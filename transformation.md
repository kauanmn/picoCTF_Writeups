# Transformation
- **Tags:** Reverse Engineering
- **Author:** MADSTACKS

### Description
I wonder what this really is... [enc](https://mercury.picoctf.net/static/a757282979af14ab5ed74f0ed5e2ca95/enc) `''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])`

### Hints
1. You may find some decoders online


---

## Solution


### Step 1 - Understanding the code

As the tag suggests, we're dealing with reverse engineering. It's important to understand what's the underlying mechanism behind the code:
``` python
''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])
```

Maybe it's a little bit confusing now, but let's indent that to try understand:
``` python
''.join(
	[
		chr(
			(ord(flag[i]) << 8) + ord(flag[i + 1])
		)
		for i in range(0, len(flag), 2)
	]
)
```

Taking the flag variable as a hint, we can suppose that this code is what encrypted the flag, maybe in the file `enc` we can find in the description.

<br>

Another point to note is this part:
``` python
for i in range(0, len(flag), 2)
```
The cypher is iterating the whole flag, 2 letters at a time. But, what's it doing with the information? That's the next part of the code.

<br>

Here we have the most important part of the code:
``` python
chr(
	(ord(flag[i]) << 8) + ord(flag[i + 1])
)
```

The `chr(ord(x) + ord(y))` part of the code is combining two pieces of information (adding or concatenating). First, it converts the Unicode characters to its representing numbers with `ord()`, then combines them with `+` operator, and finally converts it to character again with `chr()` function.

The first part `(ord(flag[i]) << 8)` is converting the character in `i` position to a number, and then making a bitwise left shift operation. Let's see an example:

<img src="https://user-images.githubusercontent.com/23441506/157932707-e84ebdf6-ef7c-4685-acca-205a8d6b6139.png" width="450px">

<br>

Here's another example: let's consider the letter `A`.
``` python
ord('A') == 65          # True
bin(65)  == '0b1000001' # True
```
The letter `A` corresponds to `1000001`. Making a 2-bit left shift operation, we have `100000100`. We push 2 zeroes at the end of the number.
``` python
bin(65 << 2)  == '0b100000100' # True
```

<br>

Ok. Then `ord(flag[i] << 8)` produces a 2-part information: the last 8 bits is 8 zeroes added by the left shift operation. What is left, is the binary number that corresponds to the letter:
``` python
bin(65 << 8)  == '0b100000100000000' # True
# '1000001 | 00000000' -> note the separation between the 2 bytes. The first is 01000001 and the second is 0's
```
In other words, this part of the code produces the character (in binary) + 8 bits (of 0's)

<br>

Let's see the second part of the code:
``` python
ord(flag[i + 1])
```
It just picks the next character (remember, we iterate the flag, 2 characters at a time) and converts it to the corresponding number.

<br>

Finally, let's analyze the entire function:
``` python
chr(
	(ord(flag[i]) << 8) + ord(flag[i + 1])
)
```

This function is simply adding the 16 bits (8 bits of the first character + 8 empty bits) and 8 bits of the second character. So, it will produce 16 bits made of 2 parts:
- The first 8 bits is the first character
- The last 8 bits is the second character
Simply, the function is converting 2 characters to binary (1 byte each) and concatenating them :)

One example:
`Aa` is `01000001` (65) and `01100001` (97). The result of the operation will be: `01000001 01100001`.

You can test this by putting the entire encryption code in a python file and assigning `Aa` to the `flag` variable. The encrypted data will be this character: `䅡`.

Other way to test this is to encrypt `picoCTF{`. The result will be the same as the first 4 characters of the encrypted flag.



<br>
<br>

---

### Step 2 - Decrypting the flag

Ok, now we know that each character of the encrypted flag is actually made of 2 characters combined. We could separate each encrypted character into two decrypted character by hand, but it is not elegant enough. We'll code some lines:
``` python
def decrypt(data):
	
	decrypted_data = ''

	for i in range(len(data)):
		character   = ord(data[i])
		first_char  = chr(character >> 8)
		second_char = chr(character & ((1 << 8) - 1))

		decrypted_data = decrypted_data + first_char + second_char

	return decrypted_data
```

I'll explain the function:
- First, the function takes the encrypted word `data`
- We initialize the data with an empty string with `decrypted_data = ''`
- Now, we decrypt each character of the encrypted word inside the loop `for i in range(len(data))`
- We take the corresponding number of the encrypted character
- In the `first_char` variable, we put the 8 bits (converted back to a readable character)
- In the `second_char` variable, we put the last 8 bits (also converted back to a readable character)
- Finally, we concatenate the 2 characters we found to what we already have deciphered.

<br>

How to get the first 8 bits? Just remove the last 8 bits lmao. That's we did with `first_char = chr(character >> 8)`.

How to get the last 8 bits? We pick the last 8 bits with an `AND` operator. For example: `0110010101 & 1111 == 0101`.


<br>
<br>

---

### Step 3 - Code & Flag

The final code will be:
``` python
def decrypt(data):

	decrypted_data = ''

	for i in range(len(data)):
		character   = ord(data[i])
		first_char  = chr(character >> 8)
		second_char = chr(character & ((1 << 8) - 1))

		decrypted_data = decrypted_data + first_char + second_char

	return decrypted_data


flag = '灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸彤㔲挶戹㍽'
print(decrypt(flag))
```

The flag will be then printed: `picoCTF{16_bits_inst34d_of_8_xxxxxxxx}`
