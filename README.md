# harp

This docker image is simply aimed at making [Harp](http://harpjs.com) available via docker instead of having to set it up completely locally.  I have copied in the relevant command-line usage pages from http://harpjs.com/docs/ below and updated them to show the docker usage.  This is not meant to be comprehensive documentation on Harp, and you should refer to the official documentation for that.

## Initializing a Harp Application

> See http://harpjs.com/docs/environment/init

Harp has a command line interface for creating a new project from scratch. This is the quickest way to try Harp.

### Why?

Often, starting a project requires the same boilerplate code over and over. This command is helpful if you want to create a project with a sane starting point and common defaults.

### Usage

```console
docker run --rm -v $PWD:/myproject harp init [path]
```

### Properties

* **path** – (String) Optional, this is the file path to where you want the project to be generated. It must be empty or no files will be generated. The path also assumes your current working directory.
  _Note that this path is relative to the directory you mapped to the /myproject volume._

### Example Usage

In the following example, the default application will be created in the folder `myproject/`.

```console
docker run --rm -v myproject:/myproject harp init
```

##### Default project structure

```
myproject/
 |- _layout.jade
 |- 404.jade
 |- index.jade
 +- main.less
```

_Note the default app uses [Jade](http://harpjs.com/docs/development/jade) for writing HTML, but you may also use [EJS](http://harpjs.com/docs/development/ejs), which may be more familiar._

### Using a boilerplate

The `--boilerplate` or `-b` flag allows you to initialize a new Harp app with a boilerplate on GitHub. The following would create a new project in the current directory using the boilerplate on [github.com/kennethormandy/hb-remedy](https://github.com/kennethormandy/hb-remedy)

```console
docker run --rm -v $PWD:/myproject harp init --boilerplate kennethormandy/hb-remedy
```

You may also skip specifying a GitHub user entirely, and use one of the [default Harp boilerplates](https://github.com/harp-boilerplates).

```console
docker run --rm -v $PWD:/myproject harp init --boilerplate docs
```

#### Using any project as a boilerplate
It’s also possible to initialize a project using a GitHub repository that wasn’t even intended to be a Harp boilerplate. Because Harp serves HTML, CSS & JavaScript, any project based on web technology should work. For example, you could easily serve an Apache Cordova / PhoneGap app locally.

```console
docker run --rm -v $PWD:/myproject harp init -b phonegap/phonegap-start
docker run -it -v $PWD:/myproject -p 9000:9000 harp server www
# Your project is now being served at http://localhost:9000
```

## Harp Server

> See http://harpjs.com/docs/environment/server

Running a Harp as a `server` or `multihost` is the primary purpose of the tool. This command is designed to run and serve a single app located on your computer.

### Usage

```console
docker run -it -v $PWD:/myproject -p 9000:9000 harp server [options] [path]
```

### Options

* **port** - (Number) Optional, The port the server listens on. Defaults to port `9000`.
  _Note that if you change the port, you must change the mapping it in your `docker run` command using `-p`._

### Properties

* **path** - (String) Optional, The path you want your server to listen to.
  _Note that this path is relative to the directory you mapped to the /myproject volume._

### Example Usage

```console
docker run -it -v ~/apps/example.com:/myproject -p 3000:3000 harp server --port 3000
```

#### Visiting a running harp app.

We provide a special URL for visiting your application. As port `3000` was specified in the previous command, the Harp app will be running at:

* `http://harpdev.io:3000`

However, you can always fall back to the default URI:

* `http://127.0.0.1:3000`
* `http://localhost:3000`

If no port was specified, the app will be available at the default port of 9000:

* `http://harpdev.io:9000`
* `http://127.0.0.1:9000`
* `http://localhost:9000`

#### Using Port 80

Sometimes, it is a pain typing in the port every time you visit your locally-running site. By using port `80`, you don’t need to type it in. Running Harp on port `80` requires Admin Privileges. On OS X, this means you need to preface the command with `sudo`:

```console
sudo docker run -it -v $PWD:/myproject -p 80:80 harp server --port 80
```

If Harp clashes with something that’s already running on port `80`, you can resolve the situation [with the port conflicts guide](http://harpjs.com/docs/environment/port-conflicts).

### Production

Harp is production-ready. By specifying an environment variable, extra LRU caching is added to make your site run even faster.

```console
sudo docker run -it -v $PWD:/myproject -p 80:80 -e NODE_ENV=production harp server --port 80
```

## Multihost

> See http://harpjs.com/docs/environment/multihost

`harp multihost` is like `harp server` on steroids. This is the best way to serve multiple sites in the same folder, with a single command.

### Why?

It’s important to stay organised while working on multiple projects. Spinning up many servers on different ports is for the birds. Multihost provides all the same benefits, and more.

### Usage

```console
docker run -it -v $PWD:/myproject -p 9000:9000 harp multihost [options] [path]
```

### Options

* **port** - (Number) Optional, The port the server listens on. Defaults to port `9000`.
  _Note that if you change the port, you must change the mapping it in your `docker run` command using `-p`._

### Properties

* **path** - (String) Optional, The path you want your server to listen to.
  _Note that this path is relative to the directory you mapped to the /myproject volume._

### Example

To serve an entire folder located at ~/Sites, run the following command:

```console
docker run -it -v ~/Sites:/myproject -p 3000:3000 harp multihost --port 3000
```

With that, multihost provides a listing of all your apps at the following address:

```
http://127.0.0.1:3000/
```

![Multihost listing view](http://harpjs.com/docs/images/multihost-1.png)

Harp also maps `http://127.0.0.1` to `http://harp.nu`, so you can visit your multiple applications locally. Each one will also be available at a subdomain of [harp.nu](http://harp.nu/).

For example, if you run harp multihost on the following directory of apps:

```
myapps/
 |- mysite/
 |- myproject.com/
 +- myotherproject.harp.io/
```

Then, the they will be available in your browser at the following URLs:

* http://mysite.harp.nu:9000
* http://myproject.harp.nu:9000
* http://myotherproject.harp.nu:9000

If you’d like this local URL to have parity with your deployment URL, you can use the [Harp Platform](http://harpjs.com/docs/deployment/harp-platform) and also have your deployed applications available at a subdomain of [harp.io](http://harp.io/).

Note this will not work if your machine is offline, as you will not be able to reach http://harp.nu.

### Production

As with `harp server`, by specifying an environment variable, you can multihost in production rather than development mode. In production mode, Harp has extra LRU caching to make your site run even faster.

```console
sudo docker run -it -v $PWD:/myproject -p 80:80 -e NODE_ENV=production harp multihost --port 80
```

### Indefinite, or running multihost on port 80

It’s possible to run `harp multihost` on port `80`, making it easy to leave a folder of applications hosted for you at all times.

Using this directory of applications as an example,

```
myapps/
  |- mysite/
  |- myproject.com/
  +- myotherproject.harp.io/
```

and by running the following from inside `myapps/`:

```console
sudo docker run -d -v $PWD:/myproject -p 80:80 -e NODE_ENV=production --name harpserver harp multihost -p 80
```

The `-d` will allow you to continue using this instance of your command line. If you are on Windows, you may leave off `sudo`, but you will still need administrative privileges.

Now, your applications will be available at:

* http://mysite.harp.nu
* http://myproject.harp.nu
* http://myotherproject.harp.nu

### Ending “indefinite” multihost

If you need to end multihost on port 80 (or anything else for that matter), simply stop the container.

```console
docker stop harpserver
```

## Harp compile

> See http://harpjs.com/docs/environment/compile

Export your harp site to flat static assets – HTML / CSS / JavaScript.

Why?

With Harp, you don’t need to compile or watch your project at all times—the compile files don’t need to appear alongside your source files, cluttering everything up. Still, there are still times when you might want to compile all your preprocessed files to HTML, CSS, and JavaScript:

#### 1. Apache Cordova

Building a mobile application just got even easier. `harp compile` exports directly to an Apache Cordova / PhoneGap-friendly `www` folder.

#### 2. No Lock-In

If for whatever reason you fall out of love with Harp, just export your project and deploy it on any stack or cloud provider that serves static assets.

### Usage

```console
docker run --rm -v $PWD:/myproject harp compile [options] [projectPath] [outputPath]
```

### Mobile Example

Try creating a project called `mobileapp` by running `harp init mobileapp` and then compiling it:

```console
docker run --rm -v $PWD:/myproject harp init mobileapp
docker run --rm -v $PWD:/myproject harp compile mobileapp
```

Running the compile command would generate the assets as follows:

```
mobileapp/
  +- www/
    |- index.html
    +- main.css
```

### Backup Example

Lets say we have a project called example in our apps folder. We could export it to static assets in our Desktop/backup folder by running the following command.

```console
docker run --rm -v ~:/myproject harp compile apps/example Desktop/backup

*Note* The backup folder is automatically created and is assumed to be empty.

## Supported tags and respective Dockerfile links

* `0.20.1`, `latest` [Dockerfile](https://github.com/ezarko/docker-harp/blob/master/Dockerfile)

