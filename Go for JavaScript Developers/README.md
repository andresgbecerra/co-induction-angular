## GO for JavaScript Developers 
> Frontend Masters: Brenna Martenson

***

### Content

- [Introduction](#introduction)
- [Printing](#printing)
- [Basic Go Syntax](#basic-go-syntax)
- [Complex Structures](#complex-structures)
- [Go Toolkit](#go-toolkit)
- [Struts](#struts)
- [Pointers](#pointers)
- [Error Handling](#error-handling)
- [Methods](#methods)
- [Interfaces](#interfaces)
- [Web Servers](#web-servers)
- [Hitting an External API](#hitting-an-external-api)
- [Concurrency](#concurrency)

***


# Introduction

- **GO vs JS**
  
    |   | GO | JavaScript |
    | - | -- | ---------- |
    | _TYPING_ | Strongly | Dynamically |
    | _STRUCTURES_ | Structs, Pointers, Methods, Interfaces | ES6 Classes |
    | _ERROR HANDLING_ | Explicit | Built in |
    | _MULTI-TASK_ | Multi-Threaded | Single-Threaded |
    | _OPINIONATED-NESS_ | Strong Opinions | Fluid Opinions |

- **Anatomy of a GO file**
  - Every GO program needs at least a **package main**, this package is going to encompass whatever files live in the same folder, and allows us access to some of the variables and functions that only are associated with this main package.
  - The following line will be any **import** statement for any additional libraries.
  - And the actual **main function**, every go program needs both a package main and the main function, the main function is the entry point of the program, where GO starts executing the code and fires off whatever else happens in our program.
  - Running: to excecute this program, we are going to use the following command - `go run <file name>.go`
    ```go
        package main

        import "fmt"

        func main() {
            fmt.Println("Hello World!")
        }
    ```

[Back](#content)

***


# Printing

- **The fmt package**
  - Here are the most common variadic functions which are available in the package:
    - Print, Printf & Println
    - Fprint, Fprintf & Fprintln
    - Fscan, Fscanf & Fscanln
    - Sprint, Sprintf & Sprintln
    - Sscan, Sscanf & Sscanln
  - All functions starting with **F** writes or reads the string to or from the stream provided as its argument. The argument must implement io.Writer or io.Reader interface depending on the use case.
  - All functions which start with **S** returns the string it created using the format specifiers. It doesn’t print items automatically.
  - The functions which end with **ln** such as Fprintln or Fscanln adds a newline.
  - The functions with an **f** at the ends support formatting.
```go
    package main
    
    import (
        "fmt"
        "os"
    )
    
    func main() {

        // printing string - return letter
        fmt.Println("T")         // T
        // printing character - return bytes
        fmt.Println('T')         // 84
        fmt.Println(string('T')) // T
    
        v := 42
    
        // Spaces are to be added manually
        fmt.Print("This", "is", "a", 42, "string\n")        // Thisisa42string  
        
        fmt.Println("Another", "string")                    // Another string
        
        fmt.Printf("The value is %d\n", v)                  // The value is 42  
        
        // formatting support
        
        s := "formatted"
        
        // Spaces are to be added manually
        // os.Stdout is an io.Writer
        fmt.Fprint(os.Stdout, "Yet", "another", "string\n") // Yetanotherstring
        fmt.Fprintln(os.Stdout, "A", "string")              // A string
        fmt.Fprintf(os.Stdout, "A %s string\n", s)          // A formatted string
    }
```

- The stringer interface in fmt package
    - The stringer interface implementor can simply be passed to a print function as an argument and it will print in the way it is defined.
    ```go
        package main
        
        import (
            "fmt"
        )
        
        // define a struct
        type Book struct{
            Name string
            Author string
        }
        
        // implement the stringer interface by
        // implementing the String() string function
        func (b Book)String() string {
            return fmt.Sprintf("%s was written by %s\n", b.Name, b.Author)
        }
        
        func main() {
            b := Book{
                "1981",
                "Andres",
            }
            
            // pass as argument directly
            fmt.Println(b)                // 1981 was written by Andres
        }
    ```
- **Documentation**
  - To get the GO documentation from the CLI, execute the following command - `go doc fmt` this command gets the information about fmt package, but if we are going to get specific information for instance Println function, we can run `go doc fmt.Println`


[Back](#content)

***


# Basic Go Syntax

- **Type**
    | Name  | Type Name | Example |
    | ----- | --------- | ------- |
    | _INTEGER_ | int int8 int16 int32 int64 uint uin8 uint26 uint32 uint64 | 1 2 44 770 ... var age int = 21 |
    | _FLOAT_ | float32 float64 | 1.5 3.4 2100 ... var gpa float64 = 4.1 |
    | _STRING_ | string | "Andres" ... var address string = "Cll 45" |
    | _BOOLEAN_ | bool && \| \|  ! < <=  > >= == != | true false ... var isHide bool = true |

- The **reflect** package allows us to change or modify the object or any variable at the dynamic. In more technical words, it allows us to manipulate the value of objects dynamically. 
- Reflection is the way a programming language can check the type of values at runtime. 
  ```go
  package main
 
        import (
            "fmt"
            "reflect"
        )
        
        type Person struct{
            Name string
            Age int
        }
        
        func main() {
            p := Person{"Andres", 22}
            fmt.Println(reflect.TypeOf(p))      // main.Person

            fmt.Println(reflect.TypeOf("Andres"))  // string
            
            var x = 10
            fmt.Println(reflect.TypeOf(float64(x) * 5.5))  // float64

        }
  ```


- **Variables**
    - The ***var*** keyword is used to declare a variable.
    ```go
        package main
        
        import "fmt"
        
        func main() {
                var name = "Andres"               // no type declaration
                var username string = "Andres123" // type declaration
                var numberOfPosts int             // no initialization
                fmt.Println(numberOfPosts)        // prints 0
        }
    ```
    - Initialization shorthand enables us to initialize variables in a concise manner. Which means we can initialize without using var and declaring datatype of that variable.
    - Inside a function, the := short assignment statement can be used in place of a var declaration with implicit type.
    ```go
        package main
        
        import "fmt"
        
        func main() {
                name := "Andres"  // notice the colon equal syntax
        }
    ```


- **Control Structures**
  - **IF Statement:**
    - The expression need not be surrounded by parentheses ( ) but the braces { } are required.
    - The if statement can start with a short statement to execute before the condition.
    - Variables declared by the statement are only in scope until the end of the if.
    ```go
        package main

        import (
            "fmt"
            "math"
        )

        func sqrt(x float64) string {
            if x < 0 {
                return sqrt(-x) + "i"
            }
            return fmt.Sprint(math.Sqrt(x))
        }

        func pow(x, n, lim float64) float64 {
            if v := math.Pow(x, n); v < lim { // the value of the variable can be assigned in the if statement
                return v
            } else {
                fmt.Printf("%g >= %g\n", v, lim)
            }
            // can't use v here, throw an Error
            // this scope can not instantiate v
            return lim
        }

        func main() {
            fmt.Println(sqrt(2), sqrt(-4))
            fmt.Println(pow(3, 2, 10))
        }
    ```

  - **Switch Statement**
    - A switch statement is a shorter way to write a sequence of if - else statements. 
    - It runs the first case whose value is equal to the condition expression.

  ```go
  package main

    import (
        "fmt"
        "runtime"
    )

    func main() {

        ///////////////////

        fmt.Print("Go runs on ")

        switch os := runtime.GOOS; os {
        case "darwin":
            fmt.Println("OS X.")
        case "linux":
            fmt.Println("Linux.")
        default:
            // freebsd, openbsd,
            // plan9, windows...
            fmt.Printf("%s.\n", os)
        }
        ///////////////////
        // Note that you can use multiple arguments 
        // in your case statement separated by a comma.
         var city string

        switch city {
        case "Des Moines":
            fmt.Println("You live in Iowa")
        case "Minneapolis,", "St Paul":  // -
            fmt.Println("You live in Minnesota")
        case "Madison":
            fmt.Println("You live in Wisconsin")
        default:
            fmt.Println("You're not from around here.")
        }
        ///////////////////
        // no switch expression
        switch { // -
        case i > 10: fmt.Println("Greater than 10")
        case i < 10: fmt.Println("Less than 10")
        default: fmt.Println("Is 10")
        }
        /////////////////
        // Add a fallthrough keyword automatically
        // continue running the switch statement
        switch {
        case i != 10:
            fmt.Println("Does not equal 10")
            fallthrough // continue to next case
        case i < 10: fmt.Println("Less than 10")
        case i > 10: fmt.Println("Greater than 10")
        default: fmt.Println("Is 10")
        }
    }

  ```


- **FOR Loops**
  - It is similar to JavaScript FOR loop
  - The basic for loop has three components separated by semicolons:
    - the init statement: executed before the first iteration
    - the condition expression: evaluated before every iteration
    - the post statement: executed at the end of every iteration
    ```go
    package main

        import "fmt"

        func main() {
            sum := 0
            for i := 0; i < 10; i++ {
                sum += i // sum = sum + i
            }
            fmt.Println(sum) // 45

            ////////////////////
            // The init and post statements are optional.
            sum := 1
            for ; sum < 10; { // -
                sum += sum
            }
            fmt.Println(sum)
            //////////////////
            // This will behave like a while loop
            sum := 1
            for sum < 10 { // you can drop the semicolons
                sum += sum
            }
            fmt.Println(sum)
            ////////////////
            // using range
            var mySentence = "This is a sentence"

            for index, letter := range mySentence { // -
                fmt.Println("Index:", index, "Letter:", letter) // Index: 0 Letter: 84 ...
                fmt.Println("Index:", index, "Letter:", string(letter)) // Index: 0 Letter: T ...
            }


        }
    ```

> The loop that does not exist is the `do while`


[Back](#content)

***


# Complex Structures


- **Functions**
  - A function is a group of statements that exist within a program for the purpose of performing a specific task.
  - At a high level, a function takes an input and returns an output.
  ```go
  package main

    import "fmt"

    // SimpleFunction prints a message
    func SimpleFunction() {
        fmt.Println("Hello World")
    }
    //////////////
    // you can omit the type from all but the last.
    func add(x, y int) int {
	return x + y
    }   
    //////////////
    // A function can return any number of results.
    func printAge(age int) (ageOfSally int, ageOfBob int) {
        ageOfBob = 21
        ageOfSally = 35
        return
    }

    func main() {
        SimpleFunction()
        fmt.Println(printAge())
    }
  ```


- **Variadic functions** are just like other functions. 
  - The only difference is its parameter defined in a way that it allows for an unknown number of arguments to be passed when calling the function.
  - To declare a variadic function, the type of the final parameter is preceded by an ellipsis `...`
    ```go
    package main

    import "fmt"

    func main() {
        variadicExample("red", "blue", "green", "yellow")
    }

    func variadicExample(s ...string) { // -
        fmt.Println(s[0])
        fmt.Println(s[3])
    }
    ```

- The scan functions are used to take values from the standard input.
  ```go
        package main
        
        import (
            "fmt"
        )
        
        func main() {
            var a, b int
        
            // use address-of operator to get the values into the variable
            fmt.Scan(&a, &b)
        
            // print the values taken
            fmt.Println(a, b)
        }
  ```


- **Arrays**
    - An array is a data structure that consists of a collection of elements of a single type or simply you can say a special variable, which can hold more than one value at a time. 
    - The values an array holds are called its elements or items. 
    - An array holds a specific number of elements, and it cannot grow or shrink. 
    ```go
    package main

    import (
        "fmt"
        "reflect"
    )

    func main() {
        // Initialize an empty array
        var intArray [5]int
        var strArray [5]string

        fmt.Println(reflect.ValueOf(intArray).Kind()) // array
        fmt.Println(reflect.ValueOf(strArray).Kind()) // array
     
        /////////////////
        // You can initialize an array with pre-defined values using an array literal. 
        var score [5]float64 = [5]float64{9, 1.5, 22, 7, 3.6}  // Defining Values
        x := [5]int{10, 20, 30, 40, 50}   // Intialized with values
        var y [5]int = [5]int{10, 20, 30} // Partial assignment
        scores := [...]float64{9, 3, 6.4, 2, 6}

        fmt.Println(x)  //  [10 20 30 40 50]
        fmt.Println(y)  //  [10 20 30 0 0]
    }
    ```


- **Make**
  - Slices: Segments of an underlying array
  - (+ Make): Must be associated with a space in memory
  - Make: Initializes and allocates space in memory for `slice`, `map`, or `channel`.
  - Slices can be created with the built-in `make` function; 
  - This is how you create dynamically-sized arrays.
  - The `make` function allocates a zeroed array and returns a slice that refers to that array:
    ```go
        // make(type, initial length, maximum capacity)
        a := make([]int, 5)  // len(a)=5 -> [0 0 0 0 0]

        // To specify a capacity, pass a third argument to make:
        b := make([]int, 0, 5) // len(b)=0, cap(b)=5

        b = b[:cap(b)] // len(b)=5, cap(b)=5
        b = b[1:]      // len(b)=4, cap(b)=4
    ```
> **Make()** is necessary because, as seen with Arrays, Go is anticipating the need to set aside a specific length of memory for a variable being created. Since `slice` and `map` allow you to define a variable _without_ a set length, `make` helps Go establish where this variable will live and how to access it.


- **Slice**
  - `An array has a fixed size.` A slice, on the other hand, is a dynamically-sized, flexible view into the elements of an array.
  - In practice, slices are much more common than arrays.
  - Managing collections of data with slices and adding and removing elements from a slice.
  - A slice is formed by specifying two indices, a `low` and `high` bound, separated by a colon:
  - `a[low : high]`
    ```go
    package main

    import "fmt"

    func main() {
        primes := [6]int{2, 3, 5, 7, 11, 13}

        var s []int = primes[1:4]
        fmt.Println(s) // [3 5 7]

        ////////////////
        
        fruitArray := [5]string{"banana", "pear", "apple", "kumquat", "peach"}
        fmt.Println(fruitArray[3:])     // [kumquat peach]
        fmt.Println(fruitArray[:3])     // [banana pear apple]

    }
    ```
- `fruitArray[3:]` ==> Everything after (and including) index 3
- `fruitArray[:3]` ==> Everything up to (but not including) index 3

- Slices are like **references** to arrays
- A slice does not store any data, it just describes a section of an underlying array. 

  ```go
  /////////////////////
  // Slices are like references to arrays
  package main

    import "fmt"

    func main() {
        names := [4]string{
            "John",
            "Paul",
            "George",
            "Ringo",
        }
        fmt.Println(names) // [John Paul George Ringo]

        a := names[0:2]
        b := names[1:3]
        fmt.Println(a, b) // [John Paul] [Paul George]

        b[0] = "XXX" // -
        fmt.Println(a, b)       //[John XXX] [XXX George]
        fmt.Println(names)      //[John XXX George Ringo]

    }
  ```
- **append:** To add an item to the end of the slice, use the `append()` method
  ```go
    package main

    import "fmt"

    func main() {

    slice1 := []int{1, 2, 3}
    slice2 := append(slice1, 4, 5)// -

    fmt.Println(slice1, slice2)                 // [1 2 3] [1 2 3 4 5]
    fmt.Println(len(slice1), cap(slice1))       // 3 3
    fmt.Println(len(slice2), cap(slice2))       // 5 6 - the capacity is 6 because it doubles the capacity of the first array
        
    }
  ```

- `len()` (Length) This indicates the longest this slice can be.
- `cap()` (Capacity) This gets the maximum capacity that the slice can be.


> Nil slices: The zero value of a slice is nil.

```go
    func main() {
        var s []int
        fmt.Println(s, len(s), cap(s))
        if s == nil {
            fmt.Println("nil!")
        }
    }
```


- **Maps**
  - Similar to _Object_ in JavaScript
  - A map is a data structure that provides you with an unordered collection of key/value pairs
  - The strength of a map is its ability to retrieve data quickly based on the key. 
  - A key works like an index, pointing to the value you associate with that key.
  ```go
  //////////////
  // map[Key]Value - map[string]int - {"Mark": 10}
  package main
 
    import "fmt"
    
    var employee = map[string]int{"Mark": 10, "Sandy": 20}
    
    func main() {
      fmt.Println(employee)
      //////////////
      var userEmails map[int]string = make(map[int]string)
      userEmails[1] = "user1@gmail.com" // {1: "user1@gmail.com"}
      userEmails[2] = "user2@gmail.com" // {2: "user2@gmail.com"}

      fmt.Println(userEmails) // map[1:user1@gmail.com 2:user2@gmail.com]

      //////////
      m := make(map[string]int)

      m["Answer"] = 42
      fmt.Println("The value:", m["Answer"])  // The value: 42

      m["Answer"] = 48
      fmt.Println("The value:", m["Answer"])  // The value: 48

      delete(m, "Answer") // delete method
      fmt.Println("The value:", m["Answer"])  // The value: 0

      v, ok := m["Answer"]
      fmt.Println("The value:", v, "Present?", ok)    // The value: 0 Present? false
    }
  ```


[Back](#content)

***


# Go Toolkit

|  Commands  | Description |
| ---------- | ----------- |
| `go doc` |  will give you access to local help docs |
| `go run <file name>` | to compile and run our program |
| `go build` and `go install` | to compile all of your go packages and dependencies and put the executable in the current directory (when run without any flags). |
| `go install` | also put the compiled dependencies into the `pkg` directory within your go workspace. |
| `go build` | lets you build an executable file locally, which lets you test your application without having to deploy it immediately. |
| `go fmt` |  will reformat your code based on Go standards. This is often run as a linter, (like on save of a file). |
| `go list` | will list all of the packages in that current directory. |
| `go vet` | runs through your source code and yells about any weird constructs |
| `go get` | to install an external package, in Go you need to specify the location of where that executable lives. i.e, if we want to use the `golint` package, we would install it like so: `go get -u golang.org/x/lint/golint` |

 
- **go lint**
  - Go lint is concerned with style mistakes and maintaining a consistent coding style that is miantained at Google.
  - Install `golint` using the command above.
  - To find out where the library was put, run `go list -f {{.Target}} golang.org/x/lint/golint`
  - For `golint` to be used globally, make sure the path you see from the above command is included in your `$PATH` variable.
  

- **Packages**
  - Packages are directories with one or more Go source files that allow Go to reuse code across a program and across files.
  - Every Go file must belong to a package.
  - So far, the packages we've seen are `main`, the package we wrote, and `fmt` and `reflect`, which are built into the Go source code. 
  - [See more packages](https://golang.org/pkg/).
  - To import packages, you list them at the top of your file either like this:

    ```go
    import "fmt"
    import "math"
    import "reflect"
    //////////
    // or
    import (
        "fmt"
        "math"
        "reflect"
    )
    ```
  - Some of the packages that ship with Go are:
    - `strings` - simple functions to manipulate strings
      - Methods: `Contains`, `Count`, `Index`, `HasPrefix`
  
    - `math` - mathematical operations
      -  `Pow`, `Abs`, `Ceil`, `Floor` ...
  
    - `isNaN`, `NaN`
    
    - `io` - handles input/output methods related to the `os` package (like reading files)
      - Methods: `Copy`, `Reader`, `Writer`

    - `os` - methods around operating system functionality
      - Methods: `Open`, `Rename`, `CreateFile`

    - `testing` - Go's build in test suite
      - Methods: `Skip`, `Run`, `Error`
    
    - `net/http` - provides http client and server implementations
      - Methods: `Get`, `Post`, `Handle`



- **Exported vs Unexported Names**
  - When you import a package, you can only access that package's public, exported names. 
  - These must start with a **capital letter**.
  - Anything that starts with a **lowercase** letter is a **private** method and will NOT be exported. 
  - These are only visible within the same package.

    ```go
    fmt.Println()
    ```
  > Within the `fmt` package, the function `Println` is exported (starts with a capital letter) and therefore can be accessed within our `main` package.
  

- **Custom Packages**
  - To demonstrate how packages work, let's create a folder called `utils`, and within that create a file called `math.go` where we will add a few small functions.
  - In `math.go` add the following:

    ```go
    package utils

    import "fmt"

    func printNum(num int) {
        fmt.Println("Current Number: ", num)
    }

    func Add(nums ...int) int {
        total := 0
        for _, v := range nums {
            printNum(v)
            total += v
        }
        return total
    }
    ```

  - And then in our `main.go` file, lets import and use this package.
  - Note that the import name is a path that is relative to the `src` directory.
  - Also note that the file name itself isn't necessary as long as that file lives within the package directory and has its package declaration at the top of the file.

    ```go
    import {
    "fmt"
    "path/from/src/to/utils" // => ie: myproject/utils
    }

    func calculateImportantData() int {
        totalValue := utils.Add(1, 2, 3, 4, 5) // -
        return totalValue
    }

    func main() {
        total := calculateImportantData()
        fmt.Println(total)
    }
    ```

- **Testing**
  - Go includes a package, `testing`, that contains all the functions necessary to run a test suite and command to run those tests, `go test`.
  - Test file names must end with `_test.go` and the Go compiler will know to ignore these unless `go test` is run.
  - The test files need to be in the same package as the method they are testing.
  - The test name should start with `Test` followed by the name of the function you are testing.
  - The only parameter passed into the test should be `t *testing.T`.

    ```go
    // average_test.go

    package utils

    import "testing"

    // The test should always accept the same argument, t, of type *testing.T)
    func TestAverage(t *testing.T) {
    // the value you expect to get from the tested function
    expected := 4
    actual := utils.average(1, 2, 3)
    if actual != expected {
        t.Errorf("Average was incorrect! Expected: %d, Actual: %d", expected, actual)
    }
    }
    ```

 > To run tests, use the command `go test` from within the directory where the test file lives.



[Back](#content)


***


# Struts

> A struct is a collection of fields.

- Structs are the only way to create concrete user-defined types in Golang. 
- Struct types are declared by composing a fixed set of unique fields. 
- Structs can improve modularity and allow to create and pass complex data structures around the system.

    ```go
    type identifier struct{
    field1 data_type
    field2 data_type
    field3 data_type
    }
    ```

- A struct type `rectangle` is declared that has three data fields of different data-types. 
- Here, struct used without instantiate a new instance of that type.

    ```go
    package main
    
    import "fmt"
    
    type rectangle struct {
        length  float64
        breadth float64
        color   string
    }
    
    func main() {
        fmt.Println(rectangle{10.5, 25.10, "red"})
    }
    ```

- **Reading a Struct**
  - Then, in order to access the fields, we use dot notation similar to working with Objects in JS.

    ```go
    u := User{ID: 1, FirstName: "user", LastName: "test", Email: "user.test@gmail.com"}
    fmt.Println(u.FirstName) // => "user"
    ```

  - To pass a struct to a function, we simply add the struct name within the function signature as the type of argument we are passing in.

    ```go
    func describeUser(u User) string {
        desc := fmt.Sprintf("Name: %s %s, Email: %s, ID: %d", u.FirstName, u.LastName, u.Email, u.ID)
        return desc
    }

    func main() {
    u := User{ID: 1, FirstName: "user", LastName: "test", Email: "user.test@gmail.com"}
    userDescription := describeUser(u) //  call this function.
    fmt.Println(userDescription)
    }
    ```

  - Let's create another struct for a group of users (think `admin` vs `guest`).

    ```go
    type User struct {
        ID int
        FirstName, LastName, Email string
    }
    type Group struct {
        role            string
        users           []User // -
        newestUser      User   // -
        spaceAvailable  bool
    }

    // function that describes the team of users:
    func describeGroup(g Group) string {
        desc := fmt.Sprintf("The %s user group has %d users. Newest user:  %s, Accepting New Users:  %t", g.role, len(g.users), g.newestUser.FirstName, g.spaceAvailable)
        return desc
    }

    func main() {
    u1 := User{ID: 1, FirstName: "user", LastName: "test", Email: "user.test@gmail.com"}
    u2 := User{ID: 2, FirstName: "user2", LastName: "test2", Email: "user2.test2@gmail.com"}

    g := Group{role: "admin", users: []User{u1, u2}, newestUser: u2, spaceAvailable: true}

    groupDescription := describeGroup(g)

    fmt.Println(groupDescription)
    // The admin user group has 2 users. Newest user:  user2, Accepting New Users:  true
    }


    ```


- **Accessibility**
  - Note that like variables and functions, only field names that have a **capital letter** are visible outside of the package where the struct is defined.

    ```go
    type User struct {
    ID  int
    Email  string
    FirstName  string
    LastName   string
    }
    ```

- **Mutating Instances Of A Struct**
  - In Go, unless otherwise indicated, we are passing around a _COPY_ of any variable we reference to existing functions. 
  - In the case above, we pass a copy of the variable `g`, and then are asking our program to print the original value of `g`. 
  - The original value is still safely untouched in memory.
  - In order to permanently modify this variable, we need to use a special kind of variables called a `pointer`.



[Back](#content)

***


# Pointers
- A pointer variable in Go is a variable that holds the _memory location_ of that variable, instead of a copy of its value.

```go
  var name string
  var namePointer *string // - pointer

  fmt.Println(name)         // ""
  fmt.Println(namePointer) // <nil>
```
- A normal string variable (`name`) is assigned a value of `""`.
- A pointer string variable (`namePointer`), however, is assigned it's default value of `<nil>` in anticipation of a future memory location.
- To assign a variable it's address in memory, you use the `&` symbol (think "a - at - for address").

```go
  var name string
  var namePointer *string // - pointer

  fmt.Println(name)         // ""
  fmt.Println(namePointer)  // <nil> 
  fmt.Println(&name)        // 0xc000014250 - memory address
  ///////////////
  var name string = "Andres"
  var namePointer *string = &name
  var nameValue = *namePointer

  fmt.Println(name)         // Andres
  fmt.Println(namePointer)  // 0xc000014250 - memory address
  fmt.Println(nameValue)    // Andres
```
- So now we're looking at the _memory address_ of a variable, rather than the value that is stored there. 
- To get that value, we need to dig into that address and pull the value out. 
- To `read through` a pointer variable to get the actual value, you use `pointer indirection` to "deference" that variable back to its original value.

> - Pointer types are indicated with a `*` next to the _TYPE NAME_, indicates that variable will POINT TO a memory location.
> - Pointer variable values are visible with a `*` next to the _VARIABLE NAME_.
> - To read through a variable to see the pointer address, use a `&` next to the _VARIABLE NAME_.
> - ie: `var nameValue = *namePointer` => "Andres"

- **Pass By Value**
  - Function parameters represent a COPY of the value that they reference. 
  - This means that any change made to that variable within the function is modifying the copy, not the underlying value in memory. 

```go
package main

import "fmt"
import "strings"

func changeName(n string) {
    n = strings.ToUpper(n)
}

func main() {
    name := "Andres"
    changeName(name)
    fmt.Println(name) // Andres - didn't Capitalize
}

///////////////
// If we did want the `changeName` function to modify the name variable, we need to pass a pointer.

func changeName(n *string) {
	*n = strings.ToUpper(*n)
}

func main() {
	name := "Andres"
	changeName(&name)
	fmt.Println(name) // ANDRES
}

```

- **Pointers And Structs**
  - Let's take the following struct:

```go
package main

import "fmt"


type Coordinates struct {
  X, Y float64
}

var c = Coordinates{X: 120.5, Y: 300}

func main() {
  // To access a field on a struct pointer
  pointerCoords := &c   // - pointer
  (*pointerCoords).X =  200
  fmt.Println(c) // {200, 300}
}
```


[Back](#content)

***


# Error Handling
- ERROR:
  - Indicates that something bad happened, but it might be possible to continue running the program.
  - For example: A function that intentionally returns an error if somenthing goes wrong.
- PANIC:
  - Panic is a built-in function that stops the ordinary flow of control and begins panicking.
  - Happen at run time.
  - Something happened that was fatal to your program and program stop excution.
  - For example: Trying to open a file that don't exist.
- RECOVER:
  - Recover is a built-in function that regains control of a panicking goroutine. 
  - Recover is only useful inside deferred functions. 
  - During normal execution, a call to recover will return `nil` and have no other effect. 
  - If the current goroutine is panicking, a call to recover will capture the value given to panic and resume normal execution.

- **Error Type**
  - The first type of error is the built in `error` type. 
  - When this occurs, most of the time you will have a game plan for what to do instead and your program can continue to run.
  - The built in `error` type is an `interface` type, with a method called `Error()` that returns the string of the error message itself.
  - That interface looks like this:

    ```go
    type error interface {
        Error() string
    }
    ////////////
    package main

    import (
        "log" // -
        "errors" // -
        "fmt"
    )

    func isGreaterThanTen(num int) error {
        if num < 10 {
            return errors.New("something bad happened")
        }
        return nil
    }
    ////////////
    func isGreaterThanTen(num int) error {
    if num < 10 {
        return fmt.Errorf("%v is NOT GREATER THAN TEN", num)
    }
    return nil
    }

    func main() {
    err := isGreaterThanTen(9)
    if err != nil {
        // Logs can be providing code tracing, profiling, and analytics.
        log.Println(err) // - log.Println
    } else {
        fmt.Println("Carry On.")
    }
    }
    ```
    - the `fmt` package will return an error.
  
    > IMPORTANT: `nil` is an acceptable return value for an `error` type as well.

    > NOTE: Log: This is a simple logging package built in to Go. You can format the output similarly to `fmt` which prints it to the console. Log will print the date and time of each logged message. Again, check out `go doc log` for more details.


- **Exiting The Program**
  - If your program encounters an error that should halt execution, you can use `log.Fatal` from the `log` package which will log the error, and then exit the program.

```go
func main() {
  err := isGreaterThanTen(9)
  if err != nil {
    log.Fatalln(err) // - log.Fatal
  } else {
    fmt.Println("Carry On.")
  }
}
```


- **Deferred Functions Calls**
  - A defer statement is often used with paired operations like open and close, connect and disconnect, or lock and unlock to ensure that resources are released in all cases, no matter how complex the control flow. 
  - The right place for a defer statement that releases a resource is immediately after the resource has been successfully acquired.
  - Deferred Functions are run even if a runtime **panic** occurs.
  - Deferred functions are executed in LIFO order, so the above code prints: 4 3 2 1 0.
    ```go
    package main
        import "fmt"
        func main() {
            for i := 0; i < 5; i++ {
                defer fmt.Printf("%d ", i) // 4 3 2 1 0
            }
        }
    ```

- `defer`: executes a line of code last within a function.
- `panic`: called during a run time error, halts execution of the program.
- `recover`: tells go what to do after a panic.

[Back](#content)

***


# Methods
- A method is a special kind of function that acts on variable of a certain type, called the receiver, which is an extra parameter placed before the method's name, used to specify the moderator type to which the method is attached. 
- The receiver type can be anything, not only a struct type: any type can have methods, even a function type or alias types for int, bool, string or array.
  ```go
    package main
    import "fmt"

    type multiply int // -

    func (m multiply) tentimes() int { // method
        return int(m * 10)
    }

    func main() {
        var num int = 2
        mul:= multiply(num)
        fmt.Println("Ten times of number is: ",mul.tentimes()) // Ten times of number is:  20
    }

  ```
- **Advantages**
    - Encapsulation
    - Polymorphism - two different types can have a method of the same name 
    - Functions that control/modify state

[Back](#content)

***


# Interfaces
- An Interface is an abstract type.
- Interface describes all the methods of a method set and provides the signatures for each method.
- An interface contains a list of function signatures, describing the behavior of other types.
- To create interface use **interface** keyword, followed by curly braces containing a list of method names, along with any parameters or return values the methods are expected to have.
  ```go
  // Declare an Interface Type and methods does not have a body
    type Employee interface {
        PrintName() string                // Method with string return type
        PrintAddress(id int)              // Method with int parameter
        PrintSalary(b int, t int) float64 // Method with parameters and return type
    }
  ```


You can also use the empty interface type to indicate that Go should accept
anything.

```go
interface{}
```

```go
func DoSomething(v interface{}) {
  // This function will take anything as a parameter.
}
```

- Example Using Interface:

```go
package main

import "fmt"

// Describer prints out a entity description
type Describer interface {
	describe() string
}

// User is a single user type
type User struct {
	ID                         int
	FirstName, LastName, Email string
}

// Group is a group of Users
type Group struct {
	role           string
	users          []User
	newestUser     User
	spaceAvailable bool
}

func (u *User) describe() string {
	desc := fmt.Sprintf("Name: %s %s, Email: %s, ID: %d", u.FirstName, u.LastName, u.Email, u.ID)
	return desc
}

func (g *Group) describe() string {
	desc := fmt.Sprintf("The %s user group has %d users. Newest user:  %s, Accepting New Users:  %t", g.role, len(g.users), g.newestUser.FirstName, g.spaceAvailable)
	return desc
}

// DoTheDescribing can be implemented on any type that has a describe method 
func DoTheDescribing(d Describer) string {
	return d.describe()
}

func main() {
	u1 := User{ID: 1, FirstName: "user", LastName: "test", Email: "user.test@gmail.com"}
	u2 := User{ID: 1, FirstName: "user2", LastName: "test2", Email: "user2.test2@gmail.com"}
	g := Group{role: "admin", users: []User{u1, u2}, newestUser: u2, spaceAvailable: true}

	describeUser := u1.describe()
	describeGroup := g.describe()

	userDescribeWithIface := DoTheDescribing(&u1)
	groupDescribeWithIface := DoTheDescribing(&g)

	fmt.Println(describeUser)
	fmt.Println(describeGroup)

	fmt.Println(userDescribeWithIface)
	fmt.Println(groupDescribeWithIface)
}
```



[Back](#content)

***


# Web Servers
- To work with HTTP we need to import the HTTP package. It contains client and server implementation for HTTP.
- To create a basic HTTP server, we need to create an endpoint. 
- In Go, we need to use _handler functions_ that will handle different routes when accessed. 
- Here is a simple server that listens to port 5050.
  ```go
  package main
 
    import (
        "fmt"
        "net/http"
    )
    
    func main() {
            // handle route using handler function
        http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
            fmt.Fprintf(w, "Welcome to new server!")
        })
    
            // listen to port
        http.ListenAndServe(":5050", nil)
    }
  ```
- We can handle different routes just like before. 
- We can simply use different handlers for each route we want to handle. Here are some examples.
  ```go
  package main
 
    import (
        "fmt"
        "net/http"
    )
    
    func main() {
        http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
            fmt.Fprintf(w, "Welcome to new server!")
        })
    
        http.HandleFunc("/students", func(w http.ResponseWriter, r *http.Request) {
            fmt.Fprintf(w, "Welcome Students!")
        })
    
        http.HandleFunc("/teachers", func(w http.ResponseWriter, r *http.Request) {
            fmt.Fprintf(w, "Welcome teachers!")
        })
    
        http.ListenAndServe(":5050", nil)
    }
  ```
  
- **Using a server mux**
  - A mux is a multiplexer. 
  - Go has a type servemux defined in http package which is a request multiplexer. 
  - Here’s how we can use it for different paths. 
  - Here we are using the io package to send results.
  ```go
  package main
 
    import (
        "io"
        "net/http"
    )
    
    func welcome(w http.ResponseWriter, r *http.Request) {
        io.WriteString(w, "Welcome!")
    }
    
    func main() {
    
        mux := http.NewServeMux() // multiplexer
    
        mux.HandleFunc("/welcome", welcome)
    
        http.ListenAndServe(":5050", mux)
    }
  ```

- **Structs & JSON**
    - we are used to working with JSON objecst when we make HTTP requests. 
    - When working with a struct in Go, we can tell our program what we want our JSON too look like, and then encode/decode it (read: stringify/parse) back and forth into Go.
    - In order to turn this struct into a JSON object, we can use the `enconding/json` package that ships with Go.

```go
package main

import "fmt"
import "encoding/json"


type User struct {
  ID int
  FirstName string
  LastName string
  Email string
}

func main() {
  u := User{
    ID: 1,
    FirstName: "user",
    LastName: "test",
    Email: "user.test@gmail.com",
  }

  data, _ := json.Marshal(u)
  fmt.Println(string(data)) // {"ID":1,"FirstName":"user","LastName":"test","Email":"user.test@gmail.com"}
}
```

> When you print this out, the structure is ALMOST there, but the formatting isn't conventional JSON.

- To modify what we want those keys to look like , we can add an additional field to our struct called a `field tag`.
```go
type User struct {
  ID int `json:"id"`
  FirstName string `json:"firstName"`
  LastName string `json:"lastName"`
  Email string `json:"emailAddress"`
}
```

[Back](#content)

***


# Hitting an External API


- Example with Star Wars API (swapi):

```go
package main

import (
	"encoding/json"
	"fmt"
	"io/ioutil"
	"log"
	"net/http"
)

// BaseURL is the base endpoint for the star wars API
const BaseURL = "https://swapi.dev/api/"

// Planet is a planet type
type Planet struct {
	Name       string `json:"name"`
	Population string `json:"population"`
	Terrain    string `json:"terrain"`
}

// Person is a person type
type Person struct {
	Name         string `json:"name"`
	HomeworldURL string `json:"homeworld"`
	Homeworld    Planet
}

func (p *Person) getHomeworld() {
	res, err := http.Get(p.HomeworldURL)
	if err != nil {
		log.Print("Error fetching homeworld", err)
	}

	var bytes []byte
	if bytes, err = ioutil.ReadAll(res.Body); err != nil {
		log.Print("Error reading response body", err)
	}

	json.Unmarshal(bytes, &p.Homeworld)
}

// AllPeople is a collection of Person types
type AllPeople struct {
	People []Person `json:"results"`
}

func getPeople(w http.ResponseWriter, r *http.Request) {
	res, err := http.Get(BaseURL + "people")

	if err != nil {
		http.Error(w, err.Error(), http.StatusBadRequest)
		log.Print("Failed to request star wars people")
	}
	fmt.Println(res)

	bytes, err := ioutil.ReadAll(res.Body)

	if err != nil {
		http.Error(w, err.Error(), http.StatusBadRequest)
		log.Print("Failed to parse request body")
	}

	var people AllPeople

	fmt.Println(string(bytes))

	if err := json.Unmarshal(bytes, &people); err != nil {
		fmt.Println("Error parsing json", err)
	}

	fmt.Println(people)

	for _, pers := range people.People {
		pers.getHomeworld()
		fmt.Println(pers)
	}

}
func main() {
	http.HandleFunc("/people", getPeople)
	fmt.Println("Serving on :8080")
	log.Fatal(http.ListenAndServe(":8080", nil))
}

```


[Back](#content)

***


# Concurrency
- Concurrency in Golang is the ability for functions to run independent of each other. 
- Goroutines are functions that are run concurrently. 
- Golang provides Goroutines as a way to handle operations concurrently.
- New goroutines are created by the `go` statement.
- The `go` keyword makes the function call to return immediately, while the function starts running in the background as a goroutine and the rest of the program continues its execution. 
- The main function of every Golang program is started using a goroutine, so every Golang program runs at least one goroutine.
  
  ```go
    package main

    import (
        "fmt"
        "time"
    )

    func say(s string) {
        for i := 0; i < 5; i++ {
            time.Sleep(100 * time.Millisecond)
            fmt.Println(s)
        }
    }

    func main() {
        go say("world") // -
        say("hello")
    }
  ```


- **Channel**
  - Go provides a mechanism called a **channel** that is used to share data between goroutines. 
  - When you execute a concurrent activity as a goroutine a resource or data needs to be shared between goroutines, channels act as a conduit(pipe) between the goroutines and provide a mechanism that guarantees a synchronous exchange.
  
  > A channel is created by the make function, which specifies the chan keyword and a channel's element type.

    ```go
    ch <- v    // Send v to channel ch.
    v := <-ch  // Receive from ch, and
            // assign value to v.
    ```

[Back](#content)


_The End_