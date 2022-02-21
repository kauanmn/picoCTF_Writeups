# Easy Peasy
- **Tags:** Cryptography
- **Author:** MADSTACKS

### Description
A one-time pad is unbreakable, but can you manage to recover the flag? (Wrap with picoCTF{}) `nc mercury.picoctf.net 36981` [otp.py](https://mercury.picoctf.net/static/6920807ae08f883dbb50f6f301f92684/otp.py)

---

### otp.py
```python
#!/usr/bin/python3 -u
import os.path

KEY_FILE = "key"
KEY_LEN = 50000
FLAG_FILE = "flag"


def startup(key_location):
	flag = open(FLAG_FILE).read()
	kf = open(KEY_FILE, "rb").read()

	start = key_location
	stop = key_location + len(flag)

	key = kf[start:stop]
	key_location = stop

	result = list(map(lambda p, k: "{:02x}".format(ord(p) ^ k), flag, key))
	print("This is the encrypted flag!\n{}\n".format("".join(result)))

	return key_location

def encrypt(key_location):
	ui = input("What data would you like to encrypt? ").rstrip()
	if len(ui) == 0 or len(ui) > KEY_LEN:
		return -1

	start = key_location
	stop = key_location + len(ui)

	kf = open(KEY_FILE, "rb").read()

	if stop >= KEY_LEN:
		stop = stop % KEY_LEN
		key = kf[start:] + kf[:stop]
	else:
		key = kf[start:stop]
	key_location = stop

	result = list(map(lambda p, k: "{:02x}".format(ord(p) ^ k), ui, key))

	print("Here ya go!\n{}\n".format("".join(result)))

	return key_location


print("******************Welcome to our OTP implementation!******************")
c = startup(0)
while c >= 0:
	c = encrypt(c)
```

---

## Solution

### Step 1 - Flag size

Open Console and enter the following command: `nc mercury.picoctf.net 36981`. The output will be:
```
******************Welcome to our OTP implementation!******************
This is the encrypted flag!
5541103a246e415e036c4c5f0e3d415a513e4a560050644859536b4f57003d4c

What data would you like to encrypt?
```

The encrypted flag is in hexadecimal. Every 2 hexadecimal characters are equal to 1 byte. Then, the **flag has 32 bytes**.

---

### Step 2 - OTP fundamentals

**ONE-TIME PAD:** _One-time pad is an encryption technique that cannot be cracked, but requires the use of a single-use pre-shared key that is no smaller than the message being sent. The plaintext is paired with a random secret key (the one-time pad). Then, each bit or character of the plaintext is encrypted by combining it with the corresponding bit or character from the pad using modular addition (XOR)._

In other words, the key cannot be reused. What is not our case. Take a look at the following lines of otp.py code:
``` python
if stop >= KEY_LEN:
	stop = stop % KEY_LEN
```

This code block tells us that if our input is long enough (50000 bytes, i.e., the key length), we will end using the same key again (the key that was used to encrypt the flag).

What we will do here is to cycle the key to the first position (position 0) of the key. Then, we will leak the first 32 bytes of the key and XOR it with the encrypted flag. The result will be the decrypted file.

Why it works? If you do `(plaintext XOR key) XOR key == ciphertext XOR key`, the result will be the `plaintext`.

---

### Step 3 - Reset key position

Because the flag used the first 32 bytes of the key, we must consume `50000 - 32 == 49968 bytes` of the key to cycle it. So, we will encrypt 49968 `\x00` characters to get to position 0 again.

---

### Step 4 - Leak the key

After step 3, we will leak the same 32 bytes of the key that encrypted the flag using 32 `\x00` characters.

---

### Script of steps 3 and 4
We will paste the following script in the terminal to insert the (50000 - 32) bytes (to reset the key) of `\x00` + key leak (32 bytes of `\x00`):
```
python3 -c "print('\x00'*(50000-32)+'\n'+'\x00'*32)" | nc mercury.picoctf.net 36981
```

The output will be:
```
$ python3 -c "print('\x00'*(50000-32)+'\n'+'\x00'*32)" | nc mercury.picoctf.net 36981
******************Welcome to our OTP implementation!******************
This is the encrypted flag!
5541103a246e415e036c4c5f0e3d415a513e4a560050644859536b4f57003d4c

What data would you like to encrypt? Here ya go!
< BIG TEXT HERE >

What data would you like to encrypt? Here ya go!
6227295e455c7838375c7866375c7862355c786430635c7838665c7863365c78
```

The last line (`6227295e455c7838375c7866375c7862355c786430635c7838665c7863365c78`) is the first 32 bytes of the key we need.

---

### Step 5 - XOR encrypted flag

As we said before, if we XOR 2x the plain text with the key, we will get the plain text again. So, the final step is to XOR the encrypted flag with the leaked key part:

XOR the plain text and the key (both in hexadecimal), and the decrypted file will be in hexadecimal encoding.

![image](https://user-images.githubusercontent.com/23441506/154978059-4f69dc39-0533-4441-b415-7c4c80468c64.png)


Now, we get the ASCII flag with Python:
``` python
flag = bytes.fromhex('3766396461323966343034393961393864623232303338306135373734366134').decode('ascii')
print('picoCTF{' + flag + '}')
```

The flag is `picoCTF{7f9da29f40499a98db220380a57746a4}`


---

## Conclusion

It is a great challenge to practice your cryptography and programming knowledge. You have to analyze the code to find the vulnerable piece of code, which explores one of the essentials points of OTP technique. By the way, there are a lot of more advanced and sophisticated solutions to this challenge, but we try to keep it simple and accessible, because the most important thing of the challenges are the concepts underneath.

