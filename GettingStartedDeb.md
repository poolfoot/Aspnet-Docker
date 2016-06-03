
# Getting started with ASP.NET 5 and Docker

It is possible to run the sample apps using [Docker](https://www.docker.com/) and the [ASP.NET 5 Preview Docker Image](https://registry.hub.docker.com/u/microsoft/aspnet/).

### Get the source code
```
git clone git@github.com:aspnet/Home.git aspnet-home
cd aspnet-home/samples/latest/HelloWeb
```

### Build a docker image
```
docker build -t aspnet-home-helloweb .
```

### Run a docker container
```
docker run -it -p 5004:5004 aspnet-home-helloweb
```

### See the HelloWeb sample in action
The URL to the sample app depends on how you are running Docker.

If you are using [boot2docker](https://github.com/boot2docker/boot2docker):

```
open http://$(boot2docker ip):5004
```

Otherwise, you can access the sample app directly on localhost:

```
open http://localhost:5004
```

### Stop the container
You can stop the docker container by pressing <kbd>Enter</kbd> in the console, or by using ```docker ps``` to identify the container id followed by ```docker stop <container id>```.


### Learn more
Detailed instructions on how to use the [ASP.NET 5 Preview Docker Image](https://registry.hub.docker.com/u/microsoft/aspnet/) can be found on http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx. Please note that the ```kpm``` and ```k``` tools have been renamed since that article was published.



# Getting started with ASP.NET 5 and Linux

> Tested on: Ubuntu 14.04, Mint 17.01

This section deals with setting up a machine to run applications without the Docker image.

As with all other operating systems you need DNVM to get going with ASP.NET 5. To get it you run `curl` to download a `.sh` file and then run it. To configure a Linux machine to run an ASP.NET 5 application use the following instructions.

The steps to set up a Linux machine are:

 * Get a working version of Mono
 * Get and compile libuv (Required for the Kestrel server)
 * Get DNVM
 * Add sources to NuGet.config (For package restore)


---

### Prerequisites

* `Unzip`
* `Curl`

---

### Get Unzip

`Unzip` is needed to unpack runtime packages.

```
sudo apt-get install unzip
```

### Get Curl

`Curl` is a command line tool for transferring data specified with URL syntax.

```
sudo apt-get install curl
```

---

### Get Mono

Mono is how .NET applications can run on platforms other than Windows. Mono is an ongoing effort to port the .NET Framework to other platforms. In the process of developing ASP.NET 5 we worked with the Mono team to fix some bugs and add features that are needed to run ASP.NET applications. These changes are only in builds of mono that are greater than 4.0.1.

To get these builds you need to run:

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
echo "deb http://download.mono-project.com/repo/debian wheezy main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
sudo apt-get update
sudo apt-get install mono-complete
```

### Get libuv

[Libuv](https://github.com/libuv/libuv) is a multi-platform asynchronous IO library that is used by the [KestrelHttpServer](https://github.com/aspnet/KestrelHttpServer) that we will use to host our web applications.

To build libuv you should do the following:

```
sudo apt-get install automake libtool
curl -sSL https://github.com/libuv/libuv/archive/v1.4.2.tar.gz | sudo tar zxfv - -C /usr/local/src
cd /usr/local/src/libuv-1.4.2
sudo sh autogen.sh
sudo ./configure
sudo make 
sudo make install
sudo rm -rf /usr/local/src/libuv-1.4.2 && cd ~/
sudo ldconfig
```

**NOTE:** `make install` puts `libuv.so.1` in `/usr/local/lib`, in the above commands `ldconfig` is used to update `ld.so.cache` so that `dlopen` (see `man dlopen`) can load it. If you are getting libuv some other way or not running `make install` then you need to ensure that dlopen is capable of loading `libuv.so.1`.

### Get DNVM

Now let's get DNVM. To do this run:

```
sudo apt-get install unzip
curl -sSL https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.sh | DNX_BRANCH=dev sh && source ~/.dnx/dnvm/dnvm.sh
```

Once this step is complete you should be able to run `dnvm` and see some help text.

### Add Sources to NuGet.config

Now that we have DNVM and the other tools needed to run an ASP.NET application we need to add the development configuration sources to get nightly builds of all the ASP.NET packages.

The nightly package source is: `https://www.myget.org/F/aspnetvnext/api/v2/`

To add this to your package sources you need to edit the NuGet.config.

Edit: ~/.config/NuGet/NuGet.config

The NuGet.config file should look something like the following:
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="AspNetVNext" value="https://www.myget.org/F/aspnetvnext/api/v2/" />
    <add key="nuget.org" value="https://www.nuget.org/api/v2/" />
  </packageSources>
  <disabledPackageSources />
</configuration>
```
The important part of this is that you have a package source with aspnetvnext and nuget.org in it.
