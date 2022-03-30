# Insp3ct0r
- **Tags:** Web Exploitation
- **Author:** ZARATEC/DANNY

### Description
Kishor Balan tipped us off that the following code may need inspection: `https://jupiter.challenges.picoctf.org/problem/9670/` ([link](https://jupiter.challenges.picoctf.org/problem/9670/)) or http://jupiter.challenges.picoctf.org:9670

---

## Solution

### Step 1 - Browser

The first step is simple: you must have an Internet browser (maybe you're reading this with a browser right now). Next, you'll put the link (`https://jupiter.challenges.picoctf.org/problem/9670/`) to the website in the URL bar. It will redirect you to the page.

---

### Step 2 - F12

The majority browsers offer support to the Developer Tools, which is accessible by pressing the `F12` key on the keyboard.

A window will appear, similar to the window below:

<img src="https://user-images.githubusercontent.com/23441506/157361262-8481a159-0d8e-4312-aad4-757463c04db2.png" width=500px>

It will show the HTML code of the page in the **Inspector** tab. But, a modern HTML is made of other elements than just static HTML. Modern websites are made of HTML, CSS, scripts, and other stuff. So, we're also looking for other things.

---

### Step 3 - HTML

We can look to the entire page HTML source code by pressing `Ctrl + U`. The page code is something like this:
``` HTML
<!doctype html>
<html>
  <head>
    <title>My First Website :)</title>
    <link href="https://fonts.googleapis.com/css?family=Open+Sans|Roboto" rel="stylesheet">
    <link rel="stylesheet" type="text/css" href="mycss.css">
    <script type="application/javascript" src="myjs.js"></script>
  </head>

  <body>
    <div class="container">
      <header>
	<h1>Inspect Me</h1>
      </header>

      <button class="tablink" onclick="openTab('tabintro', this, '#222')" id="defaultOpen">What</button>
      <button class="tablink" onclick="openTab('tababout', this, '#222')">How</button>
      
      <div id="tabintro" class="tabcontent">
	<h3>What</h3>
	<p>I made a website</p>
      </div>

      <div id="tababout" class="tabcontent">
	<h3>How</h3>
	<p>I used these to make this site: <br/>
	  HTML <br/>
	  CSS <br/>
	  JS (JavaScript)
	</p>
	<!-- Html is neat. Anyways have 1/3 of the flag: picoCTF{tru3_d3 -->
      </div>
      
    </div>
    
  </body>
</html>
```

At the bottom of the code, we see that there is a comment with the first part of the flag: `picoCTF{tru3_d3`

---

### Step 4 - CSS

The style of the page, like the font color or the margin size, is defined with the **CSS (Cascading Style Sheets)** files. We can find them in the **Style Editor** (Firefox-based browser) or in the **Sources** tab (Chrome-based browser).

We can find a file named `mycss.css`, which contains the style rules of the website.

``` CSS
div.container {
    width: 100%;
}

header {
    background-color: black;
    padding: 1em;
    color: white;
    clear: left;
    text-align: center;
}

body {
    font-family: Roboto;
}

h1 {
    color: white;
}

p {
    font-family: "Open Sans";
}

.tablink {
    background-color: #555;
    color: white;
    float: left;
    border: none;
    outline: none;
    cursor: pointer;
    padding: 14px 16px;
    font-size: 17px;
    width: 50%;
}

.tablink:hover {
    background-color: #777;
}

.tabcontent {
    color: #111;
    display: none;
    padding: 50px;
    text-align: center;
}

#tabintro { background-color: #ccc; }
#tababout { background-color: #ccc; }

/* You need CSS to make pretty pages. Here's part 2/3 of the flag: t3ct1ve_0r_ju5t */
```

We can find the second part of the flag at the final line of the code: `t3ct1ve_0r_ju5t`.

---

### Step 5 - JavaScript

Beside the CSS file, we can find the JS file (`myjs.js`) containing the script used in the website in the **Network** tab (Firefox-based browser) or in the **Sources** tab (Chrome-based browser).
``` JavaScript
function openTab(tabName,elmnt,color) {
    var i, tabcontent, tablinks;
    tabcontent = document.getElementsByClassName("tabcontent");
    for (i = 0; i < tabcontent.length; i++) {
	tabcontent[i].style.display = "none";
    }
    tablinks = document.getElementsByClassName("tablink");
    for (i = 0; i < tablinks.length; i++) {
	tablinks[i].style.backgroundColor = "";
    }
    document.getElementById(tabName).style.display = "block";
    if(elmnt.style != null) {
	elmnt.style.backgroundColor = color;
    }
}

window.onload = function() {
    openTab('tabintro', this, '#222');
}

/* Javascript sure is neat. Anyways part 3/3 of the flag: _lucky?2e7b23e3} */

```

Again, we can find another part of the flag at the bottom of the code: `_lucky?XXXXXXXX}`.

Combining the 3 parts of the flag, we have `picoCTF{tru3_d3t3ct1ve_0r_ju5t_lucky?XXXXXXXX}`

---

## Conclusion

It is very simple challenge that makes the beginner search for hints and use the most basic tools available. The pentester journey must begin somewhere, and this challenge is a great opportunity for new comers.


