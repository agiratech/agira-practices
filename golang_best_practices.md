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

## Avoid nesting by handling errors first
```
err := request()
if err != nil {
    // error handling
} else {
    // normal code
}
```
Instead, write:

```
err := request()

if err != nil {
  // error handling
  return // or continue, etc.
}
// proceed to further
```

Less nesting means less cognitive load on the reader

If the if statement has an initialization statement that, such as:

```
if x, err := f(); err != nil {
    // error handling
    return
} else {
    // use x
}
```
then this may require moving the short variable declaration to its own line:

```
x, err := f()
if err != nil {
    // error handling
    return
}
// use x
```

## Error Strings

Error strings should not be capitalized (unless beginning with proper nouns or acronyms) or end with punctuation, since they are usually printed following other context. That is, use 
`fmt.Errorf("something bad")` not `fmt.Errorf("Something bad")`, 
so that 
`log.Printf("Reading %s: %v", filename, err)` formats without a spurious capital letter mid-message. This does not apply to logging, which is implicitly line-oriented and not combined inside other messages.

## Handle errors

Do not discard errors using _ variables. If a function returns an error, check it to make sure the function succeeded. Handle the error, return it, or, in truly exceptional situations, panic.

**Don't use panic errors**

Don't use panic for normal error handling. Use error and multiple return values.

## Avoid repetition when possible

suppose if you want use structures in controllers as well as models make it structures in common file

## Variable Names

Variable names in Go should be short rather than long. This is especially true for local variables with limited scope. Prefer `c` to `lineCount`. Prefer `i` to `sliceIndex`.

**The basic rule:** the further from its declaration that a name is used, the more descriptive the name must be. For a method receiver, one or two letters is sufficient. Common variables such as loop indices and readers can be a single letter (i, r). More unusual things and global variables need more descriptive names.

## Type switch to handle special cases

```
func Write(v interface{}) {
  switch v.(type) {
  case string:
    s := v.(string)
    fmt.Printf("%T\n",s)
  case int:
    i := v.(int)
    fmt.Printf("%T\n",i)
  }
}
```

## Type switch with short variable declaration

```
func Write(v interface{}) {
  switch x := v.(type) {
  case string:
    fmt.Printf("%T\n",x)
  case int:
    fmt.Printf("%T\n",x)
  }
}
```

## Important code goes first

License information, build tags, package documentation.
Import statements, related groups separated by blank lines.

Imports are organized in groups, with blank lines between them. The standard library packages are in the first group.
```
import (
    "fmt"
    "io"
    "log"

    "golang.org/x/net/websocket"
)
```
The rest of the code starting with the most significant types, and ending 
with helper function and types.

## Import Dot

The import . form can be useful in tests that, due to circular dependencies, cannot be made part of the package being tested:

package foo_test
```
import (
    "bar/testutil" // also imports "foo"
    . "foo"
)
```
In this case, the test file cannot be in package foo because it uses bar/testutil, which imports foo. So we use the `import .` form to let the file pretend to be part of package foo even though it is not. Except for this one case, do not use `import .` in your programs. It makes the programs much harder to read because it is unclear whether a name like Quux is a top-level identifier in the current package or in an imported package.


## Document your code

Package name, with the associated documentation before.
```
// Package playground registers an HTTP handler at "/compile" that
// proxies requests to the golang.org playground service.
package playground
```
Exported identifiers appear in godoc, they should be documented correctly.
```
// Author represents the person who wrote and/or is presenting the document.
type Author struct {
    Elem []Elem
}
```

```
// TextElem returns the first text elements of the author details.
// This is used to display the author' name, job title, and company
// without the contact details.
func (p *Author) TextElem() (elems []Elem) {
```

## Comment sentences

Comments documenting declarations should be full sentences, even if that seems a little redundant. This approach makes them format well when extracted into godoc documentation. Comments should begin with the name of the thing being described and end in a period:
```
// Request represents a request to run a command.
type Request struct { ...
```

```
// Encode writes the JSON encoding of req to w.
func Encode(w io.Writer, req *Request) { ... and so on.
```

## Package Names

All references to names in your package will be done using the package name, so you can omit that name from the identifiers. For example, if you are in package `chubby`, you don't need to type `ChubbyFile`, which clients will write as `chubby.ChubbyFile`. Instead, name the type `File`, which clients will write as `chubby.File`.

## Shorter is better

or at least longer is not always better.

Try to find the shortest name that is self explanatory.
- Prefer `MarshalIndent` to `MarshalWithIndentation`.
Don't forget that the package name will appear before the identifier you chose.
- In package `encoding/json` we find the type `Encoder`, not `JSONEncoder`.
- It is referred as `json.Encoder`.

## Packages with multiple files

Should you split a package into multiple files?
- Avoid very long files
The `net/http` package from the standard library contains `15734` lines in `47` files.
- Separate code and tests
`net/http/cookie.go` and `net/http/cookie_test.go` are both part of the http package.
Test code is compiled only at test time.

## Directly use the struct

While constructor functions are sometimes needed for Go libraries, it’s strongly preferred to support users directly using your `struct{}` object. For example, the Go standard library’s `http` `client` is used by directly instantiating it. The advantages of direct struct initialization vs constructor functions is worthy of its own post, but the primary reasons are local clarity and simplicity of code.
```
// preferred.go
client := smitego.Client{
  DevID:   123,
  AuthKey: "AuthKey123",
}
```

```
// discouraged.go
client := smitego.NewClient(123, "AuthKey123")
```

## Declaring Empty Slices

When declaring a slice, use
```
var t []string
```
rather than
```
t := []string{}
```
The former avoids allocating memory if the slice is never appended to.


## Named Result Parameters

Consider what it will look like in godoc. Named result parameters like:
```
func (n *Node) Parent1() (node *Node)
func (n *Node) Parent2() (node *Node, err error)
```
will stutter in godoc; better to use:
```
func (n *Node) Parent1() *Node
func (n *Node) Parent2() (*Node, error)
```
On the other hand, if a function returns two or three parameters of the same type, or if the meaning of a result isn't clear from context, adding names may be useful. For example:
```
func (f *Foo) Location() (float64, float64, error)
```
is less clear than:
```
// Location returns f's latitude and longitude.
// Negative values mean south and west, respectively.
func (f *Foo) Location() (lat, long float64, err error)
```
Naked returns are okay if the function is a handful of lines. Once it's a medium-sized function, be explicit with your return values. Corollary: it's not worth it to name result parameters just because it enables you to use naked returns. Clarity of docs is always more important than saving a line or two in your function.

Finally, in some cases you need to name a result parameter in order to change it in a deferred closure. That is always okay.


## Receiver Names

The name of a method's receiver should be a reflection of its identity; often a one or two letter abbreviation of its type suffices (such as `c` or `cl` for `Client`). Don't use generic names such as `me`, `this` or `self`, identifiers typical of object-oriented languages that place more emphasis on methods as opposed to functions. The name need not be as descriptive as that of a method argument, as its role is obvious and serves no documentary purpose. It can be very short as it will appear on almost every line of every method of the type; familiarity admits brevity. Be consistent, too: if you call the receiver `c` in one method, don't call it `cl` in another.


## Receiver Type

Choosing whether to use a value or pointer receiver on methods can be difficult, especially to new Go programmers. If in doubt, use a pointer, but there are times when a value receiver makes sense, usually for reasons of efficiency, such as for small unchanging structs or values of basic type. Some useful guidelines:

- If the receiver is a map, func or chan, don't use a pointer to it.
- If the receiver is a slice and the method doesn't reslice or reallocate the slice, don't use a pointer to it.
- If the method needs to mutate the receiver, the receiver must be a pointer.
- If the receiver is a struct that contains a sync.Mutex or similar synchronizing field, the receiver must be a pointer to avoid copying.
- If the receiver is a large struct or array, a pointer receiver is more efficient. How large is large? Assume it's equivalent to passing all its elements as arguments to the method. If that feels too large, it's also too large for the receiver.
- Can function or methods, either concurrently or when called from this method, be mutating the receiver? A value type creates a copy of the receiver when the method is invoked, so outside updates will not be applied to this receiver. If changes must be visible in the original receiver, the receiver must be a pointer.
- If the receiver is a struct, array or slice and any of its elements is a pointer to something that might be mutating, prefer a pointer receiver, as it will make the intention more clear to the reader.
- If the receiver is a small array or struct that is naturally a value type (for instance, something like the time.Time type), with no mutable fields and no pointers, or is just a simple basic type such as int or string, a value receiver makes sense. A value receiver can reduce the amount of garbage that can be generated; if a value is passed to a value method, an on-stack copy can be used instead of allocating on the heap. (The compiler tries to be smart about avoiding this allocation, but it can't always succeed.) Don't choose a value receiver type for this reason without profiling first.
- Finally, when in doubt, use a pointer receiver.

