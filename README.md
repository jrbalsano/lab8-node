lab8
====

Part 1: Build and Install Node.js
---------------------------------

Installing node.js on your own machine isn't too difficult, but often times
you'll find yourself in root situations, so it makes sense to figure out how to
install tools just for your user. In this part of the lab you will build and
install node.js for your user without root privileges.

To start with, download the latest node source code into your home directory

```sh
$ cd
$ git clone git@github.com:joyent/node.git
```

This will take a little while. Once its complete, create a file in your home
directory called `.npmrc`. This file will store configuration information for
the node package manager (npm). npm makes it easy to add libraries written for
node.js to your projects. Make sure the file contains the following lines,
replacing `YOUR-UNI` with your actual UNI.

```
root =    /home/YOUR-UNI/.local/lib/node_modules
binroot = /home/YOUR-UNI/.local/bin
manroot = /home/YOUR-UNI/.local/share/man
```

Once that's finished, move into the node directory. Generally, building code
from source is done in three steps. The first step is to configure the process
for the current system, ensuring the necessary dependencies exist, and generating a
Makefile. Next, `make` is run, and does just what you'd expect - it compiles all
the files in the proper order. Lastly, `make install` will copy the necessary
files into a location where you expect to find them, or where you told the
configuration step you wanted them. Since the configuration step generates the
Makefile, it can decide where these files get copied to. 

We're going to pass one parameter to the configure step, and that's a prefix.
This is the location we want all our executable files copied to when we install.
Run the following commands to configure and install node, replacing `YOUR-UNI`
with your actual UNI again. Just a warning, make is going to take a little
while.

```
cd ~/node/
./configure --prefix=/home/YOUR-UNI/.local
make
make install
```

Next, we're going to create a symlink for node. We need a symlink because node
is going to expect to find a folder `~/.node_modules` to exist, but its
actually located at `~/.local/lib/node_modules`. So, we create a symlink - a
secondary entry in our directory structure that points to an existing location
in the directory structure:

```
cd ~
ln -s .local/lib/node_models .node_modules
```

Now one last thing. We need to configure our PATH to include our install of
node.js. The PATH is a list of directories that your shell searches whenever you
type a program you want to execute. The current directory is never in your path; 
that's why when you type things like `mdb-lookup-server` the shell never finds
  your program, but when you specify the folder its in using
`./mdb-lookup-server` the shell knows its not going to find it in the
directories in PATH, but in the current directory. Anyway, to add our node
install to the PATH, add the following line your `~/.bashrc` file (or whatever
profile you're using - if you don't know what this means, just edit `~/.bashrc`)

```
export PATH=$HOME/.local/bin:$PATH
```

Once you've done this, try the following sequence of commands:

```
source ~/.bashrc
which npm
```

If the output says ~/.local/bin/npm or something equivalent then you're good to
go. If not, try logging out and back in again and then running `which npm`. If
all this is good, then congratulations, you've installed node.js!

Now before we move on, we should at least get some basic file server
funcionality out of node. So to complete this part of the assignment, install
the node package `http-server` and run it such that it serves content from
`~/html/` on a port of your choosing. Instructions for installing and using this
node package can be found at https://github.com/nodeapps/http-server.

Note that you can access a particularly clic machine via the browser via
`http://clic-machine-name.clic.cs.columbia.edu:port`.

You should now be able to access your web page for cs3157/tng/index.html. Note
the difference between the way the `http-server` package handles navigation to
particular directories compared to the way the www.clic.cs.columbia.edu server.

Once you get it running, include the following your README.txt:

* The (part of) `ps ajxfww` output that shows your node server running
* The difference you not in the navigation of directories. What does this
  difference tell you about http-server?
* The capture of a netcat session fetching the Star Trek index.html page from
  your own node http-server

Part 2: Build a simple app engine using Express
-----------------------------------------------

Express is a node framework for quickly creating web applications. In general,
it's great for making RESTful sites. For this part of the assignment, we'll just
be building a simple endpoint on top of an existing webserver, using node.js and
Express. To do this you'll start by building a basic file server with Express
and then add a single route that does some investigating into dynamic pages
using node.js and Express. 

Before we get started you should familiarize yourself with Javascript at least a
little bit. This blog post has a good introduction for programmers that is
relatively unbiased: http://www.2ality.com/2013/06/basic-javascript.html. Be
aware though that there are a lot of caveats in Javascript that are nuisances if
you discover them without knowing what they are, but once you know how to use
them to your advantage you can learn to love the language (isn't that the case
with all programming languages?) You don't have to read the whole article --
skim over the things you've already heard of to get a basic idea. Note that when
tinkering with javascript, you can now run `node` on your clic account to
have a REPL (Read Execute Print Loop) for Javascript to try out commands.

Now, create a part2 directory, and inside run `npm install -g learnyounode`. Once
this completes, run `learnyounode`. This will open a small guide to node.js to
teach you the basics. Go through the lessons for "Hello World" through "Make it
Modular." These should help you understand the basics of how node.js works.
Next, read the "Getting Started" section of the documentation for express.js at 
http://expressjs.com/guide.html. Once you have a functioning hello world, modify 
it to include the following:

1. Display a hit count. You can do this by declaring a variable outside of the
   call to create a GET route and referencing it inside the callback function.
   This is an example of using a closure! (They're really cool and also likely
   the cause of most newcomers' first javascript frustration). This should
   appear in the same route as /hello.txt.
2. Display (clearly labeled) the url for the request and the parsed version of
   the url. This information is available in the `request` object of your get 
   callback, which is an instance of `http.IncomingMessage` (more information
   at http://nodejs.org/api/http.html#http_http_incomingmessage). 
   `JSON.stringify()` may also prove useful in this endeavor.

Send the following query from your bowser to see what values are output by your
server: /hello.txt?key=abc

Include the output of the above request in your README.txt along with an
explanation/description of what each field in the parsed uri means. Commit your
server as `server.js` inside your part2 directory. You can also run this server
with `npm start`.

Part 3: mdb-lookup in node
--------------------------

For this part of the lab you're going to implement the same functionality as in
lab7. That is, make your Express application respond to /mdb-lookup and 
/mdb-loop?key=search_string URLs in the same way lab 7 did, and also let it 
server files from your public html directory.

Start by adding the functionality to serve static html files. 
http://expressjs.com/api.html#directory should help.

Next, take a dip back into the learnyounode lesson for "Time Server". The 
lesson should give you a basic idea of how the `net` module in node works. Once
you've completed this take a look at
http://nodejs.org/api/net.html#net_net_connect_options_connectionlistener to
learn how to connect on a socket. Unlike lab 7, it will be much easier for this
lab to create separate connections to the mdb-lookup-server for each query
because of the race conditions involved in non-blocking I/O. So for every
browser request sent, make a new socket connection to the mdb-lookup-server and
close the connection once you've gotten the results. 

* Remember you can always use console.log to gather debugging information. 
* Accept the port number that mdb-lookup-server runs on as the sole parameter 
  to your server.js file
* If you'd like to have an easier time generating dynamic content, look into
  using the ejs framework. Instructions for adding it to express can be found
  here: http://robdodson.me/blog/2012/05/31/how-to-use-ejs-in-express/. If
  a little confused about what's going on with the syntax for ejs, check out
  the ejs starting guide: http://embeddedjs.com/getting_started.html. Note that
  you should ONLY use this guide for understanding the syntax of ejs and what it
  does - how you use ejs with Express is explained in the first guide.

### Sources

* For configuring a local install of node: http://tnovelli.net/blog/blog.2011-08-27.node-npm-user-install.html
* For information on building source: http://www.codecoffee.com/tipsforlinux/articles/27.html
* REST Architecture: http://www.infoq.com/articles/rest-introduction 
