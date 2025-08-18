# Content
- [OpenUSD](#openusd)
    - [Compiling under windows](#compilingunderwindows)
    - [Executables and env vars](#envvars)
    - [RenderSettings, RenderProducts, RenderVars](#renderspecs)
- [Python Server](#pythonserver)

<a id="openusd"></a>
# OpenUSD

<a id="compilingunderwindows"></a>
### Compiling under windows

Currently using usd-25.08 at the time of writing this.

There is not that much more from the main docs here https://github.com/PixarAnimationStudios/OpenUSD/blob/release/README.md#windows </br>

What is a bit annoying is finding the right packages to be installed before compiling it, and I think it depends on how much you've "messed" up your current environment.

I have two systems, one has been going on for years, and it is currently a frankenstein of libraries which I admittedly have to say I don't think I can repro it entirely; the other one is a fresh Windows 11 and here is what else is needed to compile OpenUSD via its build_script:

- `Visual Studio 2022`
- `python-3.12.3`
- `pip install pyside6` which resolved in `6.9.1`
- `pip install PyOpenGL` which resolved in `3.1.10`

Then launched the build with

```
python ./build_scripts/build_usd.py --embree --openimageio --test C:\dev\usd-25.08
```
You might have an error on some rebuild, mainly in the tests, and you may want to force a `BOOST` rebuild too adding

```
--force BOOST
```

In case you need extra build-args, you can specify them like this for some specific modules

```
--build-args USD,"-DPXR_BUILD_GPU_SUPPORT=ON"
```

<a id="envvars"></a>
### Executables and env var

When the build is done, it will tell you how to set various ENV VARs.

I haven't done it as I wanted to build my executable and wanted it to be "portable", so that anyone can run it without setting any env var.

I'm not an expert in Windows bundle-app, and what I found around is pretty cumbersome.

I wanted something simple, so I've decided to split my binary into 2 executables: a launcher and the actual app.

The launcher is basically setting all env vars just the execution of the actual app (kind of like a `.bat` file, but mostly an actual binary, I never liked `.bat` files fankly, but I mostly made Linux or MacOsX apps).

The launcher is a simple `main(...)` (not even a WinAPP), and it just takes
the current folder

```
    TCHAR NPath[MAX_PATH];
    GetCurrentDirectory(MAX_PATH, NPath);
```

and it uses it to build the paths to pass to the env vars like

```
    _putenv_s("USDROOT", usdRoot.c_str());
    _putenv_s("PXR_PLUGINPATH_NAME", usdPluginPath.c_str());
```
it also takes care of "appending" to existing `PATH` and `PYTHONPATH` like

```
    // get vars
    char* origPath = getenv("PATH");
    char* origPythonPath = getenv("PYTHONPATH");
    
    // check for nullptr and append and then

    _putenv_s("PATH", newPath.c_str());
    _putenv_s("PYTHONPATH", newPythonPath.c_str());
```
In case you need to enable extra env var for some USD features, you can do this too

```
    _putenv_s("HDGP_INCLUDE_DEFAULT_RESOLVER", "True");
    _putenv_s("USDIMAGINGGL_ENGINE_ENABLE_SCENE_INDEX", "True");
```

after all of this, it then launches the actual app with

```
    system("awesomeApp.exe");
```

or you can also just make it setup a shell to work with USD (like to launch a python-server and do some cool USD web-apps!)

```
    system("cmd");
```

<a id="renderspecs"></a>
### RenderSettings, RenderProducts, RenderVars

One of the topics that I worked on for a while, to try to make actual offline-renders with `OpenUSD`.

#### What is a RenderSetting prim

soon

#### What is a RenderProduct prim

soon

#### What is a RenderVar prim

soon

#### how it currently works in OpenUSD with Renderman

soon

#### How to use usdrecord

soob

#### What you might want to lookout for when implementing your own render-delegate for offline renders

soon

#### How to use husk from Houdini/Solaris for your offline renders

soon

<a id="pythonserver"></a>
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
