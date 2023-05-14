---
title: "Python in the Browser? What in the world is PyScript"
datePublished: Mon Sep 12 2022 11:30:42 GMT+0000 (Coordinated Universal Time)
cuid: cl7yoouq200wzfxnv072cdyf7
slug: python-in-the-browser-what-in-the-world-is-pyscript
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ItGgnEXi48c/upload/1fb638f3fe5160ed2bb90aea47a541f2.jpeg
tags: python, web-development, backend, html5, frontend-development

---

I don't like Web Development, especially Front-end Development with HTML and CSS, so when I heard about Pyscript, my mind started wandering into a new false reality. A reality where we didn't need javascript (I never got the hang of Javascript). Today, we aim to understand the basics of PyScript and try to understand why it was even developed.

Since the creation of Javascript in 1995, it first competed with NCSA Mosaic. After a very successful launch by then Browser Giant, Netscape, it gained the trust of the developer community and, by the next year, was handed off to an international scripting organization called ECMA (European Computer Manufacturers Association), which is responsible for the development and upkeep of this language to this day.

Brendan Eich created JavaScript to fill the need for a “glue language” used by informal programmers and designers. This allowed programmers to use JavaScript to put together components and automate interactions. At this point in our JavaScript history, there were two dominating web browsers: Netscape Navigator (with JavaScript) and Internet Explorer (with Jscript). And by the time the browser world shifted and Internet Explorer became the dominant browser, JavaScript evolved into the endorsed standard for writing interactive processing run in a web browser, to the point where it is necessary to develop web apps today.

Now, almost 30 years into the active development of Javascript, a challenger approaches. Created by Anaconda, Pyscript aims to [bring development for 99%](https://pyscript.net) by allowing users to create rich Python applications in the browser using HTML's interface and the power of Pyodide, WASM, and modern web technologies. PyScript is still in heavy development, so it isn't advised to use it in a production environment. All warnings aside, let's dive deep into Python in the browser.

## Installing the Pyscript Framework

1. [Click here](https://github.com/pyscript/pyscript/archive/refs/heads/main.zip) to download the `zip` file.
    
2. Copy and paste the following into your `<head>` tag
    

```html
<link rel="stylesheet" href="path/to/pyscript.css" />
<script defer src="path/to/pyscript.js"></script>
```

OR

1. Copy and paste the commands into your `<head>` tag
    

```html
<link rel="stylesheet" href="https://pyscript.net/alpha/pyscript.css" />
<script defer src="https://pyscript.net/alpha/pyscript.js"></script>
```

Yup, it's as easy as that. You can now write Python inside your HTML file!

## Writing your first "Program" in HTML

Yes, I just proved [you](https://ischool.syr.edu/why-html-is-not-a-programming-language) wrong. We're gonna be programming in HTML. Let's write the iconic "Hello World" program into our IDE of choice.

Every IDE has a different shortcut to create the boilerplate code, so you don't end up typing it out every time. For JetBrains IDEs, we can use `Ctrl+J` to open the Template Menu and click on one of the many HTML formats available.

In the `<body>` tag, insert this little snippet of text. If you know anything about python, you'll be able to decode what this one-liner will do.

```python
<py-script> print('Hello, World!') </py-script>
```

If it wasn't clear to you, we're "printing" hello world onto the screen. Open this file using a modern browser, and after a couple of seconds of loading, you'll finally have written a program in HTML.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662214204048/8wauOQUWW.png align="center")

## Using Packages in PyScript

To use non-standard packages in Pyscript, we have to declare their use within `<py-env>` tags separating them with new lines. The simple format for declaring numpy and pandas into your HTML code would be:

```html
      <py-env>
        - numpy
        - pandas
      </py-env>
```

Remember to declare this in the head tag just below the `<script>` tag we used

Pyscript has a lot to offer, and if you look at the GitHub repository for the project, you'll see a bunch of cool examples. One of my favorites was Mario natively in the browser using PyScript. It has the entire first level (1-1) complete with sound, the iconic Mario soundtrack, and even cool fireworks at the end.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1662293579612/TkEY_WhHA.png align="center")

## Conclusion

PyScript is a cool web utility to help integrate the core values of python into the hands of web developers without using a dedicated framework like Flask or Django. Although, in my experience, it was a tad bit slow, maybe because it is in the alpha stage, or maybe because it isn't optimized to run on Chromium-based browsers (Tested on Brave). The latter seems unlikely because chromium-based browsers are the most commonly found nowadays.