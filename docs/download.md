---
hide:
    - navigation
title: Download
description: Download the latest version of Brio Lang.
---
# Download Brio Lang
Brio Lang is available for download on [GitHub](https://github.com/brio-lang/brio), and is offered under the modified BSD [license](license.md). To get started, simply clone the repository to your machine.
```sh
$ git clone https://github.com/brio-lang/brio
```

## Build the Interpreter
First, install the dependencies:
```sh
$ apt-get -y install make g++ libcurl4-openssl-dev libfcgi-dev
```

Next, build the project:
```sh
$ make all
```

## Run the Interpreter

Running Brio Lang without any arguments, or with `-i`, will start the interactive read-eval-print loop.
```sh
$ ./bin/brio -i
```

The Brio Lang REPL can also be launched using Docker.
```sh
$ docker build . -t brio-lang:0.6.0
$ docker run -it brio-lang:0.6.0
```

To execute a Brio Lang program, simply provide the path to your program as an argument.
```sh
$ ./bin/brio ./path/to/code.brio
```

You may use the `--help` argument to see a full list of supported arguments.
```sh
$ ./bin/brio --help
```
```
usage: ./brio [-h] [-v] [-i] [-t] [-gv] [-sym] [-ast] [-fcgi] FILE.brio

optional arguments:
  -h, --help                  Prints the help information
  -v, --version               Prints the version
  -i                          Runs in interactive mode (read-eval-print loop)
  -t                          Prints the tokens from the lexer
  -gv                         Outputs a DOT file for visualizing the AST
  -sym                        Prints the symbol table globals 
  -ast                        Prints each node type in the AST
  -fcgi                       Starts FastCGI listener, must be called from spawn-fcgi
```

## Visual Studio Code

There is an extension available for VS Code that provides language support including syntax highlighting, and the ability to define a Brio interpreter and execute your code.

* [Download Brio for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=brio-lang.brio)  (or view on [GitHub](https://github.com/brio-lang/vscode-brio))

![Brio for VS Code](https://brio-lang-static-files.s3-us-west-2.amazonaws.com/vscode-brio/code_example.gif)