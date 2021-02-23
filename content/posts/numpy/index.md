---
title: Passing numpy array to shared library
date: 2020-03-29
toc: yes
description: Here we explain how we can pass numpy arrays to C (C++ and Go) shared libraries
---

## Introduction

Imagine that we have developed a `C` or `C++` (or `rust`) library which does some operations on vectors and matrices (linear algebra).
Without talking about performances, we chose such a language for several reasons:

 - System constraints (maybe our initial target does not embed neither `java` nor `python` virtual machines)
 - Interoperability with other software (our piece of code if a sub-module of a bigger project)
 - Need to manage memory (yes it happens) ...

Testing such a library is painful. Just let us imagine that we want to test a function with input data generated from several different (and weird) distributions (writing such tests in `C` does not motivate me so much).

But we know that `python` can generate these data in one line (thanks to `numpy` or `scipy`). So the question is the following: **How to pass `numpy` array to `C` library ?**

## Our C written shared library

First let us write a `C` library which prints a basic array.
```c
// 
// main.c
// 
#include <stdio.h>

void print_array(double *v, size_t n)
{
    for (size_t i = 0; i < n; i++)
    {
        printf("%f ", v[i]);
    }
    printf("\n");
}

int main()
{
}
```

We can compile it as a shared library with `gcc` for example:
```bash
$ gcc -Wall -pedantic -shared -fPIC -o mylib.so main.c
```

The options `-Wall -pedantic` are artifacts of my first `C` lessons I attended :) That is to impose not compiling with warnings and also to respect some coding rules.

The `-shared` option is to create a shared library and the `-fPIC` flag is to make Positional Independent Code. This latter flag is a common practice while building shared library (see [https://stackoverflow.com/a/967055](https://stackoverflow.com/a/967055) for details).

## Basic call from python

`python` has many interoperability with `C`. Thanks to the built-in `ctypes` library  you can *easily* manipulate external `C` code. Let us call our printing function!

```python
# 
# test.py
# 
from ctypes import CDLL, POINTER
from ctypes import c_size_t, c_double
import numpy as np

# load the library
mylib = CDLL("mylib.so")

# C-type corresponding to numpy array 
ND_POINTER_1 = np.ctypeslib.ndpointer(dtype=np.float64, 
                                      ndim=1,
                                      flags="C")

# define prototypes
mylib.print_array.argtypes = [ND_POINTER_1, c_size_t]
mylib.print_array.restype = None

# create array X = [1 1 1 1 1]
X = np.ones(5)
# call function
mylib.print_array(X, X.size)
```

Let us detail the previous snippet. First we load the library we compiled.
Then we need to define the prototype of our function to properly call it. The problem is that we want a `numpy` array as input. 

<!-- <div class="note">
<div class="note-title">What is exactly a <code>numpy</code> array?</div>
<div class="note-text"> -->

{{< info >}}
The `numpy` library is mainly written in `C`. A `numpy` array is basically a data buffer (`char*`) with some metadata (see [https://scipy-lectures.org/advanced/advanced_numpy/](https://scipy-lectures.org/advanced/advanced_numpy/) for more information). Thus a `C` function can easily operate on its data.
{{< /info >}}
<!-- </div>
</div> -->

No problem, we can retrieve the backed data buffer with `np.ctypeslib.ndpointer`. We precise that
our array stores `np.float64` (double), it has a single dimension (array) and that the storage is row-major (this is not relevant here). 

Now in the terminal:
```bash
$ python3 test.py
1.000000 1.000000 1.000000 1.000000 1.000000
```

## Enter the matrix

It works fine! Now, what about sending matrices ? Actually this is quite the same thing since behind we still have ... an array. The indexing creates this abstraction of rows and columns, but `numpy` always manage a `char*`.

So, let us write our new printing function:

```c
void print_matrix(double *v, size_t n, size_t p)
{
    for (size_t i = 0; i < n; i++) {
        for (size_t j = 0; j < p; j++) {
            printf("%f ", v[i * n + j]);
        }
        printf("\n");
    }
    printf("\n");
}
```

As we said, the magic lies in the indexing part: the value at `(i,j)` is located at `i * n + j` in the data buffer.
To call this function, we can add these lines to our initial code:

```python
# C-type corresponding to numpy 2-dimensional array (matrix) 
ND_POINTER_2 = np.ctypeslib.ndpointer(dtype=np.float64, 
                                      ndim=2,
                                      flags="C")

# define the prototype
mylib.print_matrix.argtypes = [ND_POINTER_2, c_size_t]
mylib.print_array.restype = None
```

Here we want to send a 2-dimensional array (matrix). So we define a pointer
to such an object and we set the prototype of the `C` function. Then we can call it after creating a toy matrix.

```python
# create matrix
#     | 1 2 3 |
# M = | 4 5 6 |
#     | 7 8 9 |
M = np.arange(1, 10, 1, dtype=np.float64).reshape(3, 3, order="C")
# call function (*M.shape expands the dimensions of M)
mylib.print_matrix(M, *M.shape)
```

In the terminal it outputs:
```bash
$ python3 test.py
1.000000  2.000000  3.000000  
4.000000  5.000000  6.000000  
7.000000  8.000000  9.000000
```

### Calling `C++` or `Go` code

Until now, we have called `C` code. Actually, we can call `C++` of `Go` code in the same manner. Unfortunately or fortunately, it relies on the ability of `C++`-written and `Go`-written shared library to export their functions using the `cdecl` calling convention. In a word, it is like calling `C` code.

#### Example in `C++`

In `C++`, you can export function with the `extern "C"` declaration.

```c++
// main.cpp
#include <iostream>

extern "C"
{

    void print_array(double *array, size_t n)
    {
        for (size_t i = 0; i < n; i++)
        {
            std::cout << array[i] << " ";
        }
        std::cout << std::endl;
    }

    void print_matrix(double *array, size_t n, size_t p)
    {
        for (size_t i = 0; i < n; i++)
        {
            for (size_t j = 0; j < p; j++)
            {
                std::cout << array[i * n + j] << " ";
            }
            std::cout << std::endl;
        }
        std::cout << std::endl;
    }
}

int main() {}
```

It naturally looks like the initial `C` code, except that we use the `iostream` library. We can compile the program with `gcc`, linking with the standard `C++` library:
```bash
$ gcc -Wall -pedantic -shared -fPIC -o mycpplib.so main.cpp -lstdc++
```

The `python` code is roughly the same as before (here we must call `mycpplib.so`), so we don't rewrite it. Hopefully the results are the same!

Obviously, this example does not use the whole power of `C++`, namely object-oriented programming (OOP).
`C++` adds classes and methods which are more complex stuff than
`C` types and functions, so they cannot be exported as is. To circumvent this problem, we can basically use pointers.

#### Example in `Go`

`Go` is a more recent programming language which becomes increasingly prevalent.
It has many assets but the best one is its simplicity. The syntax has very few features, making it very easy to learn (1 week to to go through just about every aspect).
Obviously it has several other advantages but I won't detail it here.

The aptly named `C` package allows to communicate between `Go` and `C` programs. In particular, you can export functions but also manage the `C` types.

Our `Go` code can look like this:
```go
// main.go
package main

import "C"

import (
	"bytes"
	"encoding/binary"
	"fmt"
	"unsafe"
)

// SIZEOF_FLOAT64 is the number of bytes behind a float64
const SIZEOF_FLOAT64 = 8

// ToFloat64Slice converts a slice of bytes in slice of float64
func ToFloat64Slice(raw []byte) []float64 {
    // create an io.Reader from these bytes
    buffer := bytes.NewReader(raw)
    // init a slice of float64
    data := make([]float64, len(raw)/SIZEOF_FLOAT64)
    // Read bytes and copy them into the float64 slice
    if err := binary.Read(buffer, binary.LittleEndian, &data); err != nil {
        fmt.Println(err)
        return nil
    }
    return data
}

//export printSlice
func printSlice(array *float64, n int) {
    // load the raw array into a slice of bytes: []byte
    raw := C.GoBytes(unsafe.Pointer(array), C.int(n)*SIZEOF_FLOAT64)
    fmt.Println(ToFloat64Slice(raw))
}

func main() {}
```

Here the difference is that the buffer is copied into a `Go` structure.
In fact `Go` provides some idiomatic functions to convert data between `C`.
`C` arrays are handled as `Go` slices of bytes (`[]byte`) with the function `C.GoBytes`. However, it would seem that we have to copy these bytes to see them as `float64` (function `ToFloat64Slice`).

To compile this file to a shared library we have define the build mode:
```bash
$ go build -buildmode=c-shared -o mygolib.so main.go
```
It also creates a header you can include in your `C/C++` project to use
the functions.

Finally, there is a ugly trick not to copy data. We can cast the `C` array
into a `Go` array (seems not so ugly ?!).

```go
const MAX_SIZE = 1024

//export printSliceUgly
func printSliceUgly(cArray *float64, n int) {
    // cast C pointer to pointer to a Go array
    goArray := (*[MAX_SIZE]float64)(unsafe.Pointer(cArray))
    if n <= MAX_SIZE {
        // crop
        data := (*goArray)[:n]
        fmt.Println(data)
    }
}
```

The problem is the need to set the size of the array. The trick is to use
a great size (`MAX_SIZE`) and then crop the array (it turns is into a slice). Why not directly cast to a slice? A pointer to a `Go` array is the address of its first element (like in `C`), but a slice is a structure (see [https://blog.golang.org/slices-intro](https://blog.golang.org/slices-intro) and [https://golang.org/src/runtime/slice.go](https://golang.org/src/runtime/slice.go)).

```go
type slice struct {
    array unsafe.Pointer // pointer to the real buffer
    len   int            // number of elements in buffer
    cap   int            // total capacity of the buffer
}
```

Thus, a pointer to a slice is just a pointer to such a structure so the cast will not work (and we cannot create a slice by providing the pointer to its underlying buffer).


## Last few elements

### Memory

Let us talk a bit about memory. In the previous examples, we created numpy arrays and we sent their pointers to a shared library. Their memory is then allocated by `python`, so we must care about not letting other code free it.

```c
void scalar_mul(double *v, size_t n, double scalar)
{
    for (size_t i = 0; i < n; i++)
    {
        v[i] = scalar * v[i];
    }
}
```

Indeed, if we add the function above to our shared library, we see that it would modify our array.
We can check it in our `python` code:
```python
library.scalar_mul.argtypes = [ND_POINTER_1, c_size_t, c_double]
library.scalar_mul.restype = None

# X = [1 1 1 1 1]
X = np.ones(5)
library.scalar_mul(X, X.size, 3.0)
print(X)
```
It basically outputs what we expect:
```bash
$ python3 test.py
[3. 3. 3. 3. 3.]
```

If we try to free the pointer, something bad is very likely to happen. On my laptop, my terminal totally freezes for instance.

### Tools to generate wrappers

Here we have done everything by hand (but thanks to the nice features of `python`). If you have a bigger library you want to wrap, this job can be quickly laborious. Some tools exist to automatically generate these wrappers. We can mention [G-Wrap](https://www.nongnu.org/g-wrap/) and [SWIG](http://www.swig.org/).

I used the latter, it works fine with `C` and seems now to work well with `C++` (modern features are more and more supported). The advantage is that you give the function you want to wrap and it generates the `python` code. However, it create a kind of "standard" wrapper which overloads substantially all the objects. In the case of `numpy` arrays, we also ought to write extra code to make it really working since `swig` is not aware of our needs within `python`. 

Thus these tools may be relevant according to your needs but doing it manually remains a simple solution it would work in any case.