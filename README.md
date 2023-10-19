# Content
- [OpenUSD](#openusd)
    - [Compiling under windows](#compilingunderwindows)
- [Python Server](#pythonserver)


# OpenUSD

### Compiling under windows


# Python Server

You can create a server running in a shell by simply calling

```
python -m http.server 8080 --cgi
```

and you can make python code in files in a sub-folder `cgi_bin` so
that you can access it from a browser via

```
http://localhost:8080/cgi_bin/hello.py
```

Using [ngrok](https://ngrok.com) you can also tunnel it and use a fixed address to access your server.

This may not be the safest way possible to test with some python-code on server-side, but it's a pretty good starting point.

Once you have generated your unique ngrok identifier, you can start the server in python and then tell ngrok to access your current local server from the unique identifier

```
ngrok http 8080
```

You can specify the unique identifier for your website to use a consider link, or use one of the automatically generated links (see output of ngrok command, and help for more options).
