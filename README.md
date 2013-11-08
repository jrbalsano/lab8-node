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
is going to expect to find a folder `~/.node\_modules` to exist, but its
actually located at `~/.local/lib/node\_modules`. So, we create a symlink - a
secondary entry in our directory structure that points to an existing location
in the directory structure:

```
cd ~
ln -s .local/lib/node_models .node_modules
```

Now one last thing. We need to configure our PATH to include our install of
node.js. The PATH is a list of directories that your shell searches whenever you
type a program you want to execute. The current directory is never in your path
- that's why when you type things like `mdb-lookup-server` the shell never finds
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
particular directories compared to the way the www.clic.cs.columbia.edu server
handles similar navigation in your README (not README.md).

### Sources

* For configuring a local install of node: http://tnovelli.net/blog/blog.2011-08-27.node-npm-user-install.html
