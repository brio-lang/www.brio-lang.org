---
hide:
    - navigation
title: Welcome
description: Welcome to Brio Lang, an interpreted, general purpose, object-oriented programming language.
---
![Brio Logo](images/brio.png)
# Welcome to Brio Lang (0.6.0 alpha) 
## Overview

Brio Lang is an interpreted, general purpose, object-oriented programming language. It includes modules, exceptions, dynamic typing, high level data types, and classes. Brio Lang is portable and runs on many Unix varients including Linux and macOS, as well as Windows Subsystem for Linux. With its interpreted nature and easy to understand syntax, Brio Lang is an ideal language for scripting and application development.

Brio Lang is in its early stages, currently available in alpha with version 0.6.0. All feedback and contributions are extremely welcome. To view or download the source code, please visit us on [GitHub](https://github.com/brio-lang/brio).

## Code Examples
### Hello World
Getting started with Brio Lang is easy! Every program begins execution in the `main` method.
```brio
method main(){
    print("Hello world ✋")
}
```
```
$ brio app.brio
Hello world ✋
```

### Fibonacci Series
```brio
# Fibonacci series up to n
method fib(n){
    let a = 0
    let b = 1
    while (a < n){
        print(a, ',')
        let c = a + b
        a = b
        b = c
    }
}

method main(){
    fib(1000)
}
```
```
$ brio app.brio
0,1,1,2,3,5,8,13,21,34,55,89,144,233,377,610,987
```

### Iterate Over Values
```brio
method main(){
    let numbers = range(5)  # [0, 1, 2, 3, 4]
    let sum = 0

    each (let n : numbers){
        sum += n
    }

    print(sum)
}
```
```
$ brio app.brio
10
```

You can iterate over strings, arrays, dictionaries, as well as user-defined objects.

```brio
method main(){
    let airports = {
        "SFO": {"id": 1, "city": "San Francisco"},
        "FRA": {"id": 2, "city": "Frankfurt"},
        "YOW": {"id": 3, "city": "Ottawa"},
    }

    each (let key : airports){
        print(airports[key]["city"])
    }
}
```
```
$ brio app.brio
San Francisco
Frankfurt
Ottawa
```