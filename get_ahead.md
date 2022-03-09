# GET aHEAD
- **Tags:** Web Exploitation
- **Author:** MADSTACKS

### Description
Find the flag being held on this server to get ahead of the competition [http://mercury.picoctf.net:34561/](http://mercury.picoctf.net:34561/)

### Hints
1. Maybe you have more than 2 choices
2. Check out tools like Burpsuite to modify your requests and look at the responses


---

## Solution (with BurpSuite)

### Step 1 - BurpSuite

First of all, we'll install **BurpSuite Community Edition** (it's free).

<img src="https://user-images.githubusercontent.com/23441506/157356608-66ec2bb8-a9ca-4373-8dfe-e77ca0876090.png" width=500px>


After that, we'll follow the steps:
1. Select **Temporary Project** and click **Next**.

<img src="https://user-images.githubusercontent.com/23441506/157357186-12ae18ed-5180-4908-aca1-1432d4317808.png" width=500px>

2. Select **Use Burp defaults** and click **Start Burp**

<img src="https://user-images.githubusercontent.com/23441506/157357433-07b949b5-f21b-464e-8181-dc42cde7c652.png" width=500px>

3. Select the **Proxy** tab.

<img src="https://user-images.githubusercontent.com/23441506/157357773-a16ca4af-13c2-4e17-86c4-016ddf1f505e.png" width=500px>



Now, we're ready to intercept and modify HTTP requests!

---

### Step 2 - Intercept and Modify Requests and Responses

Before intercepting the server responses, we need to enable interception of responses based on rules. For that, you'll click in the **Options** tab, scroll down and check the *Intercept responses based on the following rules* box.

<img src="https://user-images.githubusercontent.com/23441506/157358026-10e0e1ea-f156-4644-bb02-e4aad528f01a.png" width=500px>

Now, we go back to the **Intercept** tab, where we intercept the requests and responses.

Next, we enable the intercept clicking on the **Intercept is off** button.

<img src="https://user-images.githubusercontent.com/23441506/157358210-65781022-2dd3-492c-af8b-4541c7f9a049.png" width=500px>


After that, we open the Burp browser and add `http://mercury.picoctf.net:34561` in the URL bar.

<img src="https://user-images.githubusercontent.com/23441506/157358436-d0f5de8f-8d4a-4167-8028-8add020ea245.png" width=500px>


A `GET` HTTP request will appear on the **Intercept** window. Here, we can modify the request.

<img src="https://user-images.githubusercontent.com/23441506/157358458-43cb98ef-3adb-482a-968d-db13ed09711a.png" width=500px>

To send the request, we click on **Forward** button. Next, we will receive the requests from the server. But nothing worth our attention.

Back to the Burp browser, we see that we landed on a red page. Switching it to blue (using the **Choose Blue** button) leads us to a new interception.

<img src="https://user-images.githubusercontent.com/23441506/157358585-cfc872bd-c10d-4722-b5bb-180d5a69e4c5.png" width=500px>

Look closely: the request have the `POST` method. The **Choose Blue** button gives a `POST` method, while the **Choose Red** gives a `GET` method. But, the first hint tells that maybe there is another choice.

The other possible method to get information is the `HEAD` method, which, not coincidentally, is in the challenge title. With this in mind, we modify the request method from `POST` to `HEAD` in the **Intercept** tab. The modified request will be like the following:

``` HTTP
HEAD /index.php HTTP/1.1
Host: mercury.picoctf.net:34561
Content-Length: 0
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://mercury.picoctf.net:34561
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.82 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://mercury.picoctf.net:34561/
Accept-Encoding: gzip, deflate
Accept-Language: pt-BR,pt;q=0.9,en-US;q=0.8,en;q=0.7
Connection: close
```

After forwarding the modified request, the Burp intercepts the reply to the `HEAD` request, which contains our flag in the contents.

``` HTTP
HTTP/1.1 200 OK
flag: picoCTF{r3j3ct_th3_du4l1ty_8f878508}
Content-type: text/html; charset=UTF-8
```


The flag is `picoCTF{r3j3ct_th3_du4l1ty_8f878508}`


---

## Conclusion

It is a great challenge to learn a new tool, the BurpSuite, and practice the fundamentals of HTTP/S. It's worth to look for other solutions and see how this can be done differently. It is a simple challenge, but create opportunities for beginners to learn more about web exploitation.
