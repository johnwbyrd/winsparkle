About
-------

This is a fork of the WinSparkle project by vslavik, specifically to
support Visual Studio 2017.  This project may be useful for those who wish
to do source-level debugging or improvement on the WinSparkle libraries,
while using a modern development environment.

WinSparkle wishes to be a plug-and-forget software update library for Windows
applications. It is heavily inspired by the Sparkle framework for OS X
written by Andy Matuschak and others, to the point of sharing the same 
updates format (appcasts) and having very similar user interface.

The WinSparkle build environment depends on numerous third party
libraries, including but not limited to wxWindows, openssl, openssl-windows,
expat, and a bunch of other stuff written by vslavik.  It also depends
on bakefile, which is a utility also written by vslavik.  Unfortunately, he stopped
developing these projects in about 2016, and thus they don't support
VS2017 correctly.

Bakefile itself doesn't have a sufficiently modern build of itself to
run correctly on Windows.  So, I ran Bakefile from sources on Linux, and
edited the .bkl files in this project to provide VS2017 support.  The
resulting files were tested by hand using a current version of Visual Studio
2017.

 Building from sources
-----------------------
This repository depends heavily on git submodules, some of which in turn depend on other git
submodules.

Check the sources out and initialize the submodules:

    $ git clone git://github.com/johnwbyrd/winsparkle.git
    $ cd winsparkle
    $ git submodule update --init --recursive

To compile the library, just open `WinSparkle2017.sln` solution and build it from within
Visual Studio 2017.

 DSA signatures
---------------

WinSparkle uses exactly same mechanism for signing and signature verification
as [Sparkle Project](https://sparkle-project.org/documentation/#dsa-signatures)
does. Its tools and verification methods are fully compatible.

You may use any compatible way to sign your update.
To achieve this, you need to sign SHA1 (in binary form) of your update file
with DSA private key, using SHA1 digest.

WinSparkle provides tools to generate keys and sign the update using OpenSSL.

You need `openssl.exe` available on Windows to use those tools (available as
[precompiled binary][OpenSSL binaries]).

Alternatively, you can generate keys and sign your updates even on macOS or Linux,
using [tools provided by Sparkle project](https://github.com/sparkle-project/Sparkle/tree/master/bin).

#### Prepare signing with DSA signatures:

 - First, make yourself a pair of DSA keys. This needs to be done only once.
 WinSparkle includes a tool to help: `bin\generate_keys.bat`
 - Back up your private key (dsa_priv.pem) and keep it safe. You don’t want
 anyone else getting it, and if you lose it, you may not be able to issue any
 new updates.
 - Add your public key (dsa_pub.pem) to your project either as Windows resource,
 or any other suitable way and provide it using WinSparkle API.

#### Sign your update

When your update is ready (e.g. `Updater.exe`), sign it and include signature
to your appcast file:

 - Sign: `bin\sign_update.bat Updater.exe dsa_priv.pem`
 - Add standard output of previous command as `sparkle:dsaSignature` attribute
 of `enclosure` node of your appcast file.
 Alternatively `sparkle:dsaSignature` can be a child node of `enclosure`.
