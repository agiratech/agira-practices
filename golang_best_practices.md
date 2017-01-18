  This blog is designed to serve as a practical guide to [Go](https://golang.org/) best practices and design patterns.  You should have already read the [Go Tutorial](https://tour.golang.org/welcome/1) and [Effective Go](https://golang.org/doc/effective_go.html).

  A best practice is a method or technique that has consistently shown results superior
to those achieved with other means

Techniques to write Go code that is
simple,
readable,
Maintainable.

## Table of Contents

* [Installing to a custom location](#installing-to-a-custom-location)
* [Use gofmt](#use-gofmt)
* [Avoid nesting by handling errors first](#avoid-nesting-by-handling-errors-first)
* [Error Strings](#error-strings)
* [Handle errors](#handle-errors)
* [Avoid repetition when possible](#avoid-repetition-when-possible)
* [Variable Names](#variable-names)
* [Type switch to handle special cases](#type-switch-to-handle-special-cases)
* [Type switch with short variable declaration](#type-switch-with-short-variable-declaration)
* [Important code goes first](#important-code-goes-first)
* [Import Dot](#import-dot)
* [Document your code](#document-your-code)
* [Comment sentences](#comment-sentences)
* [Package Names](#package-names)
* [Shorter is better](#shorter-is-better)
* [Packages with multiple files](#packages-with-multiple-files)
* [Directly use the struct](#directly-use-the-struct)
* [Declaring Empty Slices](#declaring-empty-slices)
* [Named Result Parameters](#named-result-parameters)
* [Receiver Names](#receiver-names)
* [Receiver Type](#receiver-type)

## Installing to a custom location

The Go binary distributions assume they will be installed in /usr/local/go, but it is possible to install the Go tools to a different location. In this case you must set the GOROOT environment variable to point to the directory in which it was installed.

For example, if you installed Go to your home directory you should add the following commands to $HOME/.profile:

```
export GOROOT=$HOME/go
export PATH=$PATH:$GOROOT/bin 
```

**Note:** GOROOT must be set only when installing to a custom location.

Add /usr/local/go/bin to the PATH environment variable. You can do this by adding this line to your /etc/profile (for a system-wide installation) or $HOME/.profile:

```
export PATH=$PATH:/usr/local/go/bin
```
This allows you to easily run binaries you get via go get, and makes the (preferred) go install mechanism of building code easier to work with.

## Use gofmt

Run gofmt on your code to automatically fix the majority of mechanical style issues. Almost all Go code in the wild uses gofmt.

gofmt read the go program and show the result after indentation, vertical alignment and even re formats comments too.

**commands and options**
```gofmt filename``` : This will print reformatted code. 

```gofmt -w filename``` : This will reformat the code and updates the file. 

```gofmt -r 'rule' filename``` : Apply the rewrite rule to the source before reformatting. 

```gofmt /path/to/package``` : This will format the whole package.

Here is small example for gofmt

**filename:** demo.go

```
package main
          import "fmt"
// this is demo to format code
            // with gofmt command
 var a int=2;
             var b int=5;
                            var c string= `hello world`;
       func print(){
                   fmt.Println("Value for a,b and c is : ");
                        fmt.Println(a);
                                 fmt.Println((b));
                                         fmt.Println(c);
                         }
                         
```
**Command:** gofmt demo.go

**Output:**

```
package main
 
import "fmt"
 
// this is demo to format code
// with gofmt command
var a int = 2
var b int = 5
var c string = `hello world`
 
func print() {
        fmt.Println("Value for a,b and c is : ")
        fmt.Println(a)
        fmt.Println((b))
        fmt.Println(c)
}
```
