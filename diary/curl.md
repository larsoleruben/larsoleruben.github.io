---
layout: page
title: Curl
permalink: /diary/curl
---
## A word of warning
It is very comme to see installation scripts for various programs usign a curl reference to get the script from a remote computer's website. Never just do that, follow the url and have a look at what the script is doing. Just remember, you give the script somebody else wrote, elevated rights on your box!

## All the curl commands I can't remember, but
There is quite good documentation for all of [curl here](https://ec.haxx.se)

## Download a file from a website
```bash
curl http://some.url --output some.file
```

## Multipart formposts
With curl, you add each separate multipart with one -F (or --form) flag and you then continue and add one -F for every input field in the form that you want to send.
The above small example form has two parts, one named 'person' that is a plain text field and one named 'secret' that is a file.
Send your data to that form like this:
```bash
curl -F person=anonymous -F secret=@file.txt http://example.com/submit.cgi
```
