# Comp2011 notes

> [!Note]
> These are notes that I took during class. There will be missing information and potential profanities

[Home Page](../README.md) | [Comp2012 notes](./2012%20notes.md)

---

# Entry point #

the entry point of every c++ program is:
```c++
int main(int argc, char *argv[]){...}
```
---

# I/O #


## cout ##

`cout` collect messages and then prints it (which is why we add `endl`)
 - cout is a *buffered* output
 - if an error occurs between the "printing" and "collecting", nothing is printed

> To beautify stuff, use `cout.width()` (this is "right justified")
> ```c++
> cout.width(20); cout << "name: " << name << endl;
> ```
> - note that the beautifying is applied to the first item only

## cerr ##

`cerr` immediately prints everything
 - cerr is an *unbuffered* output
 - use this to catch errors

## cin ##

`cin` has a return value
 - if there is an input, it will return true
 - if there is no input, it will return false

 - there are also some complications with cin. 
   When the value expected is an int, but there is a character it doesn't know how
   to handle, it just goes on while returning false, but the character is still in the input stream.

   To solve this we use a combinaton of `cin.fail`, `cin.clear` and `cin.ignore` 
   (note that `cin.fail` might not be completely necessary)

    ```c++
    int x;
    cin >> x; 

    while (cin.fail()){ // just "!cin" also works
        cin.clear();
        cin.ignore();
        cin >> x;
    }
    cout << x << endl;
    ```

    alternatively:
    ```c++
    int x;
    while (cin >> x){
        cin.clear();
        cin.ignore();
    }
    cout << x << endl;
    ```

`cin` ignores whitespaces, so we use `cin.getline()` when we want to read `Strings`

syntax:
> `cin.getline(char s[], int max-num-char, char terminator)`  
>> `cin.getline()` will stop when etiher:  
>>    - **(max-num-char - 1)** characters are read  
>>    - **terminator** is seen  
>  
>> The **terminator** is `\n` by default
---

# operators #

> unary -> 1 operator  
> binary -> 2 operator  
> ternary -> 3 operator (e.g. shorthand if-else)

## mod function ##
> note: must be positive in formal math

In C++: 
```
+ve % +ve -> 0 or +ve (this is the most common use)
-ve % +ve -> 0 or -ve
+ve % -ve -> 0 or +ve
-ve % -ve -> 0 or -ve
```

## shorthands ##
> (same as python):  
> `+=, -=, *=, /=, %=`

## precedence and assiciativity ##
```
precedence:                     associativity:
    - (unary minus), ++, --    |    right to left
    *, /, %                    |    left to right
    +, - (subtract)            |    left to right
    = (assignment)             |    right to left
```

## increment / decrement operator ##
increments/decrements only works for `modifiable lvalues`  
(or: `variables`) 

    pre-increment   post-increment
        ++x             x++

## shorthand if-else ##

syntax:
> `condition ? true_result : false_result`

this is the same as:
```c++
if (condition)
    x = true_result;
else 
    x = false_result;
```

---

# Data types #


`boolalpha` makes it so booleans are printed in english
> true -> 1  
> false -> 0
```c++
cout << boolalpha;
cout << true << endl;   // true
cout << false << endl;  // false
```  

`noboolalpha` makes it so booleans are printed as 0 or 1  
(this is the default)

```c++
cout << noboolalpha;
cout << true << endl;   // 1
cout << false << endl;  // 0
```

`char` can be _coerced_ to `int` before addition  
(_coercing_ -> implicit type conversion)

**3.5** is a `double` by default, but can be _coerced_ into a `float`

## Static cast ##
syntax:
> `static_cast<data-type> (value)`

`static_cast` forces a change in **data type**

## Floats ##

> floats are inaccurate!!

> try representing **decimal 0.1** in binary
> ```
> binary  0.1     = decimal 0.5
> binary  0.01    = decimal 0.25
> binary  0.001   = decimal 0.125
> binary  0.0001  = decimal 0.0625
>               ...
> ```
> we can't!

## Enumeration ##
 
note: this is technically a new datatype

syntax:
> `enum new-datatype {identifier1, identifier2, ...}`

> `enum new-datatype {identifier1 = value1, identifier2 = value2, ...}`

note: do not have to put all values
```C++
enum bloodtype { A, B, AB = 10, O };  // 0, 1, 10, 11
```

calling: 
```c++
enum shapes { TEXT, LINE, RECT, CIRCLE };
cout << "supported shapes: "
        << " TEXT = " << TEXT << " LINE = " << LINE
        << " RECT = " << RECT << " CIRCLE = " << CIRCLE << endl;

int myshape;
cin >> myshape;

switch (myshape)
{
    case TEXT:
        cout << "Call a function to print text" << endl; break;
    case LINE:
        cout << "Call a function to draw a line" << endl; break;
    case RECT:
        cout << "Call a function to draw a rectangle" << endl; break;
    case CIRCLE:
        cout << "Call a function to draw a circle" << endl; break;
    default:
        cerr << "Error: Unsupported shape" << endl; break;
}
```
---

# Initializing Variables #

initializing **non-global** variables without a value -> gibberish

---

# Expressions #

> when passing *expressions* into functions, they are evaluated **BEFORE** being passed in to the function

## return ##

> the `return` expression returns the **value** as well as **control**   
> (usually back to main, or wherever it is called)

> `void` functions only returns **control** back
---

# Reference variable #

```c++
int b;
int& a = b; 
// a and b refer to the same object (they have the same address). (a is an alias of b)
// this will NOT take up extra memory
```

- note: `&a` returns the address of `a`
    ```c++
    int a;
    cout << &a << endl; // prints the address of a
    ```


## pass-by-reference ##

```c++
void func(int& x){ ... }
```
In `func`, changing `x` changes the variable it *points to*

(e.g.)
```c++
void func(int& x) { x++; }

int main(){
    int x = 0;
    func(x);
    cout << x << endl; // prints 1
    return 0;
}
```

## pass-by-constant-reference ##

```c++
void func(cont int& x){ ... }
// ** CANNOT modify the constant value ** 
```
## return by reference ##

``` c++
int& func(){...}
```
---

# Arrays #

> **Negative indicies** do *NOT* work
>> Syntatically correct, but results in `Segmentation fault`  
>
> (the compiler doesn't check if index is out of bounds )

## Initializing arrays ##
> 1D arrays:
> ```c++
> int a[5] = {1,2,3,4,5};
> int b[5] = {1,2};   // => 1, 2, 0, 0, 0
> int c[5] = {};      // => 0, 0, 0, 0, 0
> int d[] = {1,2,3};  // => 1, 2, 3 (compiler determines that the size is 3 automaically)
> int e[3];
> e[2] = {10};            // same as: e[2] = 10
> 
> int f[5] {1,2,3,4,5};   // this also works (after c++11), but wtf
> ```
>
>
>***errors:***
>```c++
>// ** errors ** //
>e = {1,2,3}     // this does NOT work (compilation error)
>// go through the elements one by one to change value
>
>b = c;  // doesn't work either
>
>double x = 1.5, y = 2.4, x = 3.1;
>double& s[] = {x, y, z};  // cannot declare array of reference (compilation error)
>```
> ---

> 2D arrays
>> the values are added row by row to the memory  
>>  - this is called ***row-major-order***
>>  - some languages may be column by column  
>
> - initializing 2D arrays: only second size value is needed  
> - n-dimension array -> only the number in the first bracket can be omitted
>
> ```c++
> int x[][2] = {{1, 2}, {3, 4}, {5, 6}};
> int x[][2] = {1, 2, 3, 4, 5, 6};        // same as above
> ```
> ---

## indexing arrays ##
when finding a value in an array, the index bounds are not checked  
> ---
> ```c++
> int x[2][2][2] = {1,2,3,4,5,6,7,8};
> 
> cout << x[0][0][7] << endl; // prints "8"
> ```
> warning generated, but no error
>
> ---


## Arrays in functions ##


Any changes made to an array inside a function carries over
> ---
> e.g.
> ```c++
> void foo(int a[], int size){ a[0] = 1; }
> 
> int main(){
>    int a[3] = {0,1,2};
>    foo(a, 3);
>    cout << a[0] << endl; // prints 1
> }
> ```
> ---

Array variables do **NOT** carry *size* information. They are only **names**  

> In functions: 
> ```c++
> void foo(int a[], int size){ ... }
> ``` 
> - the number in `[]` doesn't matter
> - the `size` variable isn't required, but can be used to pass the size of the array

When you don't want an array to be changed in a function, use a **constant array**
> ---
> ```c++
> int func(const int x[]){...}
> int& func(const int x[]){...}
> ```
> - the array is read-only in the function, but **can be writable** *outside*
> ---

---

# Recursion #

> In theory, anything you can do with recursion, you can do with a `for` loop  

Recursion consists of: 
- escape condition
- loop

**Inefficiencies** in recursion:
```
|main on hold   |
|f(3) on hold   |
|....           |
|f(0)           |
| \/            |
|back upwards   |
```
 - many function calls on hold -> lots of info housekeeping  
    -> `for` loops are more efficient

We still use recursion because it is *easier* to write:  
> Compare recursive and non-recursive fibonancci function:
>> Recursive:
>> ```c++
>> int r_fib(int n){
>>     if (n == 0) return 0;
>>     if (n == 1) return 1;
>>     return r_fib(n-1) + r_fib(n-2);
>> }
>> ```
>
>> Non-recursive:
>> ```c++
>> int fib(int n) {
>>     if (n == 0) return 0;
>>     if (n == 1) return 1;
>> 
>>     int hold = 0, prev = 0, out = 1;
>>     for (int _ = 2; _ <= n; _++){
>>         hold = out;
>>         out += prev;
>>         prev = hold;
>>     }
>>     return out;
>> }
>> ```
> ---

# midterm #

MC, true false, short/long questions, coding questions
 - debugging
 - etc..
 
> syllabus:  
> C++ basics -> scope

---

# Scope #

> Scopes learnt:
>  - file scope
>  - function scope
>  - block scope
>  - (class scope) (near end of semester)

The same `identifier` cannot be used in the same scope

A compiler will find the closest definition of a variable
 - a **local variable** with the same identifier as a **global variable** will overshadow the **global variable**


Using an overshadowed global variable
```c++
int number = 1;

void incr(int &x){
    x++;
}

int main(){
    int number = 10; // another variable, overshadows global var.
    incr(number);    // 11
    incr(::number);  // 2 (class scope, not in midterms)
}
```


## file scope ##
 - ~ global variable  
 - intialized to ***default value***  
    - `int`, `float`, `double` : `0`

---

# Struct #

> - A collection of **heterogeneous** objects  
> - Create a new user-defined data type

> Example:
> ```c++
> /* File: point.h */
>
> struct Point {
>     double x;
>     double y;
> }; // remember the ";"
> ```
> `Point` is now a user-defined data type inside the file **'point.h'**
>
> inside another file:
> ```c++
> #include <iostream>     // < ... > for library header file
> #include <cmath>
> #include "point.h"      // " ... " for user-defined header file
> using namespace std;
> 
> void print_point(const Point& p) { cout << ’(’ << p.x << ", " << p.y << ’)’; }
> 
> double euclidean_distance(const Point& p1, const Point& p2) {
>     double x_diff = p1.x - p2.x, y_diff = p1.y - p2.y;
>     return sqrt(x_diff*x_diff + y_diff*y_diff);
> }
> 
> double print_distance(Point& p1, Point& p2) {
>     cout << "Distance between "; print_point(p1);
>     cout << " and "; print_point(p2);
>     cout << " is " << euclidean_distance(p1, p2) << endl;
> }
> 
> int main()       /* To find the length of the sides of a triangle */
> {
>     Point a, b, c;
>     cout << "Enter the co-ordinates of point A: "; cin >> a.x >> a.y;
>     cout << "Enter the co-ordinates of point B: "; cin >> b.x >> b.y;
>     cout << "Enter the co-ordinates of point C: "; cin >> c.x >> c.y;
>     cout << endl << "Results: " << endl;
>     print_distance(a, b);
>     print_distance(b, c);
>     print_distance(c, a);
>
>     return 0; 
> }
> ```

## Initialzaiton of a struct ##

initialize while it is defined
```c++
Student_Record a = { "Adam", 12345, ’M’, CSE, { 2006, 9, 1 } };
```

initialize using a function

```c++
void init_date(Date& x, unsigned int year,
    unsigned int month, unsigned int day) {
    x.year = year;
    x.month = month;
    x.day = day;
}
```

```c++
void init_student_record(Student_Record& a,
    const char name[], unsigned int id,
    char gender, Dept dept, const Date& date) {
    strcpy(a.name, name);
    a.id = id;
    a.gender = gender;
    a.dept = dept;
    a.entry = date; // struct-struct assignment
}
```

## cloning structs ##

```c++
Student_Record a = { "Adam", 1234, ’M’, CSE, {2006, 9, 1}};
Student_Record b = a;
```

or (this should be equivalent)

```c++
Student_Record a = { "Adam", 1234, ’M’, CSE, {2006, 9, 1}};
Student_Record b;
/* struct-to-struct assignment == memberwise copy */
b = a;
```
 - this is **NOT** memberwise assignment



# arrays + structs #

## Memory ##

> `structs` doesn't require the data to be stored next to each other   
> `arrays` does

# Pointers #

## variables ##

`variables` are symbolic names assigned to some memory storage
 - size depends on type of `variable`
 - `variables` are addressable while `literal constants` are not
 - `variables` have **dual roles**
    - lvalue: location of the memory storage (**read-write**)
    - rvalue: value in the storage (**read-only**)