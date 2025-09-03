# Comp2012 notes

> [!Note]
> These are notes that I took during class. There will be missing information and potential profanities

[Home Page](../README.md) | [Comp2011 notes](./2011%20notes.md)

---

(what is hashing and binary tree??)

Books
- Paul Deitel, Deitel & Associates (2017). C++ How to Program.
- M.A. Weiss (2014). Data Structures and Algorithm Analysis in C++. 


```
lab 10%
PAs 24%, 3 total
midterm 26%
final 40%
```

> note: f2f lab

---

## narrowing conversion

```c++
int z {6.4}; // error!
```

## for loop , range-for statements
### for loop
```c++
//
// ++i: post processing

for (int i = 0; i < 5; ++i){ 
    
    cout << i*i << endl;
}
```

### range-for
***Note:*** numbers dont need to be successive

```c++
for (int k : { 0, 1, 2, 3, 4 })
    cout << k*k << endl;
```

**References can also be used**
```c++
int range[] {0,1,2,3,4};
for (int& x : range)
    x *= 2;
```
> x is a reference to `range[0]` in first iteration\
> x is a reference to `range[1]` in second iteration\
> etc...

## class

```c++
#include <iostream> /* File: person.h */
using namespace std;
class Person
{
    private:
        // dynamic memory allocation
        char* _name;
        int _age;
        Person *_father, *_mother, *_child;

    public:
        // constructor
        Person(const char* my_name, int my_age, Person* my_father = nullptr,\
                Person* my_mother = nullptr, Person* my_child = nullptr);
        ~Person(); // this is a destructor

        // the following are constant member functions (or accessors)
        Person* father() const;
        Person* mother() const;
        Person* child() const;
        void print_age() const;
        void print_name() const;
        void print_family() const;

        // this is a mutator
        void have_child(Person* baby) ;
};
```


```c++
// part of the implementation file
// see person.cpp
void Person::have_child(Person* baby) {_child = baby;}
void Person::print_age() const { cout << _age;}
void Person::print_name() const
{
    cout << (_name ? _name : "unknown");
}
```

***Note:*** c++ relies on **name equivalence** for class types, *NOT structure equivalence*

```c++
class W { int a; };
class W { float b; }; // Error, double definition
```

```c++
class X { int a; };
class Y { int a; };

X x;
Y y;
x = y; // Error: type mismatch
```

`Data members` can be any `basic type`, or any `user-defined types` if they are ***`already declared`***.

- special case: A class name can be used inside `its own definition` for a ***`pointer`*** to an object of the class
    ```c++
    class Cell
    {
        int info;
        Cell* next;
    };
    ```
- A `forward declaration` of a class X can be used in the definition of another class Y to define a pointer to X
    ```c++
    class Cell;

    class List{
        int size;
        Cell* data;
        /* error!!! */
        Cell x; // Error: Cell not defined yet!!!!!
    };

    class Cell{
        int info;
        Cell* next;
    }
    ```

We can give default values (C++11 & afterwards)
```c++
class Complex
{
    private:
        float real = 1.3; // Note: not allowed before C++11
        float imag {0.5}; // Use either = or { } initializer
    public:
        ...
};
```

We are advised to initialize `non-static` *(i.e. not global)* data member values by
- class constructors
- class member initializer list in a constructor
- class member functions

# Inline and non-inline functions

function calls normally create **`activation records`** (activation records use memory on the stack to handle function calls and return values)

inline functions avoid activation records and directly run the whole code (essentially replacing the function call with the whole code in the compile code)

> ***NOTE:*** inline functions must be put in the **same file** 

## Inline functions *within* class body


keywork `inline` is optional here
```c++
class Person
{   /* ... */
    Person* child() const {return _child; }
    void have_child(Person* baby) { _child = baby; }
};
```

or

```c++
class Person
{   /* ... */
    inline Person* child() const {return _child; }
    inline void have_child(Person* baby) { _child = baby; }
};
```

## inline functions *outside* class body (in the same file)

keyword `inline` is mandatory
```c++
class Person
{   /* ... */
    inline Person* child();
    inline void have_child(Person* baby);
};

inline Person* Person::child() const {return _child; }
inline void Person::have_child(Person* baby) { _child = baby; }
```

# class scope & class scope operator

"`::`" is the **class scope operator**\
(this is basically tells what "belongs to" what)

use this to declare member functions outside the class

the compiler needs this to differentiate between different scopes (i.e. where the shit is defined in)

e.g.: `A::print()` and `B::print()`, same function name, but different scopes (`A` and  `B`)

another example:
```c++
int height = 10;
class Weird
{
    short height;
    Weird() { height = 5; }
};
```

- In `Weird::Weird()`, the `height` variable used is 
    ```c++
    short height
    ```
- to reference the `int` in global scope, we do
    ```c++
    ::height
    ```

# `this` pointer

***REMEMBER: '`this`' is a POINTER***

> - we don't need to explicitly write `this`, but we can

Each class member function implicitly contains a pointer of its
class type named "`this`"

the code below:
```c++
void Person::have_child(Person* baby) { 
    _child = baby; 
}
```
will be translated to a **`unique global`** function by adding a new argument:
```c++
void Person::have_child(Person* this, Person* baby){
    this->_child = baby; 
}
```
> The call
> ```c++
> becky.have_child(&eddy)
> ```
> becomes
> ```c++
> Person::have_child(&becky, &eddy)
> ```
> after being compiled


# return by value / return by reference


# const

simplest use case:
```c++
const float PI = 3.1416;
```

> old way:
> ```c++
> #define PI 3.1416;
> ```
> - issue: we don't know the type


> "`#`..." is used to give information to the compiler

## const object
they can only call `const` member functions

bad:
```c++
class Date
{
    private:
        /* ... */
    public:
        /* ... */
        void print(){
            cout << year << "/" << month << "/" << day << endl; 
        }

};
int main() {
    const Date WW2(1945, 9, 2); // World War II ending date

    WW2.print(); // error, WW2 is const, member funciton isn't
}
```

good:
```c++
class Date
{
    private:
        /* ... */
    public:
        /* ... */
        void print() const {
            cout << year << "/" << month << "/" << day << endl; 
        }

};
int main() {
    const Date WW2(1945, 9, 2); // World War II ending date

    WW2.print(); // error, WW2 is const, member funciton isn't
}
```

### const member functions & `this` pointer

> The code
> ```c++
> int Date::difference(const Date& d) const;
> ```
> Is compiled to:
> ```c++
> int Date::difference(const Date* this, const Date& d);
> ```

## const pointers

```c++
int i = 151;
i += 20; // OK

int* pi = &i;
*pi += 20; // OK

const int* pic = &i;
*pic += 20; // Error! Can’t change i through pic

pic = pi; // OK
*pic += 20; // Error! Can’t change *pi thru pic

pi = pic; // Error: Invalid conversion from ’const int*’ to ’int*’
```

## good practices
- add `const` to stuff u dont want to change
- Function arguments you don’t intend to change -> `cosnt arguments`
    ```c++
    void print_height(const Large_Obj& LO){ cout << LO.height(): }
    ```
- Class member functions that don’t change the data members -> `const member functions`
    ```c++
    int Date::get_day() const { return day; }
    ```


# Constructors and Destructors

If ***all*** data members are public, we can initialize using '`{ }`'

(note: struct is public by default)

```c++
class Word
{
    public:
        int frequency;
        const char* str;
};

int main() { Word movie = {1, "Titanic"}; }
```

```c++
class Word
{
    public:
        int frequency;
    private:
        const char* str;
};

// cant do this now
int main() { Word movie = {1, "Titanic"}; } // error!
```
## Constructors

it has ***no return type*** – not even `void`


A constructor is called whenever an object is created:
- object creation
- object passed to a function by value
- object returned from a function by value

## Default constructor
`default constructor`: don't pass `arguments`, but can change stuff (funcion parameters -> ok (see note below))
```c++
class Word
{
    private:
        int frequency;
        const char* str;
    public:
        Word() {frequency = 0; str = nullptr;} // default constructor
};

int main() { Word movie; } // no arguments => expect default constructor
```
***NOTE:*** we can use `default constructors` as `general constructors` as well (by giving default values in function parameters)
```c++
Word(int y = 0) {frequency = y; str = nullptr;}
```

### Compiler-Generated Default Constructor

Only generated when there are no constructors **at all**

- keep default values
- give necessary amount of memory (i.e. cannot trust initial values unless we give it)


```c++
class Word
{
    private:
        int frequency;
        const char* str;
};

int main() {Word movie;}
```

the default constructor
```c++
X::X() { }
```
- this gives enough memory for int and char*

## Conversion constructors

- constructor with 1 argument -> conversion constructor
- if all but one argument have default values, it is still a conversion
constructor.

```c++
class Word
{
    private: int frequency; char* str;
    public:
        Word(char c) 
            { frequency = 1; str = new char[2]; str[0] = c; str[1] = ’\0’; }
        Word(const char* s) // Assumption: s != nullptr
            { frequency = 1; str = new char [strlen(s)+1]; strcpy(str, s); }
};

int main()
{
    Word movie("Titanic"); // Explicit conversion
    Word movie2 {’A’}; // Explicit conversion
    Word movie3 = ’B’; // Implicit conversion
    Word director = "James Cameron"; // Implicit conversion
}
```

### explicit conversion constructor

prevent implicit conversion by adding `explicit` keyword

```c++
class Word{
    private: int frequency; char* str;
    public:
        explicit Word(const char* s)
            { frequency = 1; str = new char [strlen(s)+1]; strcpy(str,s); }
}

int main() {
    Word *p = new Word("action"); // Explicit conversion
    Word movie("Titanic"); // Explicit conversion
    Word director = "James Cameron"; // error, implicit conversion
}
```

## Copy constructor

```c++
X::X(const X& )
```

- MUST BE `call-by-reference` (because call-by-value needs to copy -> call copy constructor -> infinite loop)
- exactly one argument of the same class passed by its const reference
```c++
class Word
{
    private:
        int frequency; char* str;
        void set(int f, const char* s)
            { frequency = f; str = new char [strlen(s)+1]; strcpy(str,s); }
    public:
        Word(const char* s, int k = 1)
            { set(k, s); cout << "conversion\n"; }
        Word(const Word& w) // copy constuctor
            { set(w.frequency, w.str); cout << "copy\n"; }
};
```

It is called upon when:
- parameter passed to a function by value.
- initialization using the assignment syntax (though it actually is not an assignment):
    ```c++
    Word x {"Star Wars"}; Word y = x;
    ```
- object returned by a function by value.

### Explicit copy constructor
we can also use `explicit` before copy constructor 
```c++
class Word
{
    private:
        /* ... */
    public:
        Word(const char* s, int k = 1)
            { set(k, s); cout << "conversion\n"; }
        explicit Word(const Word& w) // copy constuctor
            { set(w.frequency, w.str); cout << "copy\n"; }
};
```
then `Word y = x` will **NOT** work

### Copy Elision and Return Value Optimization

- Return value optimization is a compiler optimization technique which applies copy elision in a return statement.
- It omits copy/move operation by constructing a local (temporary) object directly into the function’s return value!
- For the example, codes that are supposed to be run by ‘x’ are run directly on ‘song’.

> to remove this when compiling:\
> `g++ -std=c++11 -fno-elide-constructors file.cpp`

### Default Copy Constructor

***if no copy constuctor is given, the compiler will automatically supply a `default copy constructor`***

- this will do `memberwise copy` for each member


```c++
class Word /* File: default-copy-constructor.cpp */
{
    private: ...
    public: Word(const char* s, int k = 0) { ... };
};
int main()
{
    Word movie {"Titanic"}; // which constructor?
    Word song {movie}; // which constructor?
    Word song = movie; // which constructor?
}
```

default copy constructor: 
```c++
X(const X&) { /* memberwise copy */ }
```

----

call default constructor using `Word w;`

```c++
class Word
{
    private:
        int frequency; char* str;
    public:
        void set() const { cout << "Input the string: "; cin >> str; } // Error! (will compile, but will crash when ran (bus error))

        // cin >> str 
        // assumes str has memory
        // '[some character]' to str[0], '[some character]' to str[1]
        void set(int k) { frequency = k; }
        void set(char c) { str = new char [2]; str[0] = c; str[1] = ’\0’; }
        void set(const char* s) { str = new char [strlen(s)+1]; strcpy(str, s); }
};
```

## default arguments

Parameters without default values must be declared to the left of those with default arguments. The following is an error:
```c++
void upload(char os = LINUX, char* prog, char format = TEXT); // error!
```

## deligating constructor

```c++
Word(const Word& w) : Word(w.str, w.frequency) { cout << "copy" << endl; }
```

***the target constructor must be the only item in the MIL.***


## member initializer list

- initialize const ref
- class member that is another class
- can call constructor for member variables


```c++
class Word {
    /* ... */
}
class Word_Pair{
    private:
        Word w1;
        Word w2;
    public:
        Word_Pair() : w1(), w2("HKUST") { cout << "calling Word_Pair()" << endl;}
        // Word_Pair() { cout << "calling Word_Pair" << endl;}
}

```
- here the constructor for w1 and w2 is still called in the commented constructor

## garbage and destructors

we can only have `1 destructor`
> `X::~X(){}`

- destructor is invoked automatically when out of scope
- destructor will release memory of the object, and we can do *additional things* (i.e. housekeeping)
    - e.g manually delete dynamically allocated memory
- things **owned** by the object is ***NOT*** deleted
    - we have to use `delete` for dynamically allocated things

##  Compiler-generated Member Functions
C++11 allows you to explicitly generate or not generate them:
- to generate: `= default;`
- not to generate: `= delete;`



# Inheritance

e.g. find common stuff from 2 class -> put into a parent class

** every derived object must call constructor of base class

***each class has its own constructor***

*NOTE:* For parent class in header, need to add 
```c++
#ifndef UPERSON_H // UPERSON_H is just a variable
#define UPERSON_H // best practice same as filename

/* code */

#endif
```
this tells the compiler to ignore things between `#define` and `#endif`

public inheritance
- doesn't change privacy of parent

Derived objects can be treated as base class objects

## construction

if we don't say anyhting in the MIL of derived class, then the default constructor(s) for the parent class(es) will be used

the base class must be constructed first, even if it isn't explicitly in the MIL

> ```c++
> class B {
>     /*code*/
> }
> 
> class D: public B {
>     private:
>         int x, y;
>     public:
>         D() : x(1), y(2) { /* code */ }
> }
> ```
> when we call the constructor of class `D`, the constructor of class `B` is called, then the other `data members` of `D` are constucted


## destruction

the destruction is never inherited. if you KYS, don't kill others

## Access control

`Private members` are only available to `base class`' own member functions (methods), and not to any other classes including derived classes (except `friends`) or global functions.

`public`: accessible to
- member functions of the class (from class developer)
- any member functions of other classes (application programmers)
- any global functions (application programmers)

`protected`: accessible to
- member functions and friends of the class
- member functions and friends of its derived classes (subclasses)

    ⇒ class developer restricts what subclasses may directly use

`private`: accessible only to
- member functions and friends of the class
⇒ class developer enforces information hiding

## Polymorphism & Binding

static binding

```c++
int main() {
    UPerson uperson("Charlie Brown", CBME);
    Teacher teacher("Alan Turing", CSE, PROFESSOR, "CS Theory");
    UPerson* u; Teacher* t;

    cout << "\nUPerson object pointed by UPerson pointer:\n";
    u = &uperson; u->print();

    cout << "\nTeacher object pointed by Teacher pointer:\n";
    t = &teacher; t->print();

    cout << "\nTeacher object pointed by UPerson pointer:\n";
    u = &teacher; u->print();

    cout << "\nUPerson object pointed by Teacher pointer:\n";
    t = &uperson; t->print();
    // Error: convert base-class ptr
    //        to derived-class ptr
    
    t = static_cast<Teacher*>(&uperson); t->print(); // Ok, but ...
}
```


### dynamic binding & virtual functions
virtual functions:
- declared using the keyword `virtual` in the **class definition**, and **not** in the **method implementation**, if it is defined outside the class.
- if `virtual` in the base class, it is automatically `virtual` in all directly or indirectly derived classes (but its better to still specify again for readability) 
- cannot pass the object by value (`func(base_obj) {base_obj->virtual_mem_func()}`)


> "what if derived class func is virtual and base is not?"\
> -> base class func is not virtual
>
> if we call func from base class, it cannot see the true "identity" of the ptr


> #### Run-time Type Information
>> `RTTI` is a run-time facility that keeps track of dynamic types ⇒ program can determine an object’s type at run-time.
>
> this is how the computer knows the object's type at run time
> 
> - `static_cast()` does ***NOT*** consult `RTTI`
> - we need `dynamic_cast()`
> 
> #### typeinfo
> - we can use `typeid()` to find the identity of the object
> - we need to <span style="color:#FF3333"> dereference </span> pointers before comparing them, otherwise the type will be <span style="color:#C9DEFC">different </span>
> 
>
>
>

### dynamic_cast()
- this **will consult** `RTTI` to ensure result of `valid complete object`
- only works on `pointers` & `references` of polymorphic class (with virtual functions) types

> - If the input is a `pointer`: it returns a `pointer` to a valid complete object of the target type, otherwise, a `null pointer`.
> - If the input is a `reference`: it returns a `reference` to a valid complete object of the target type, otherwise, it is a `runtime error`


> `a = typeid(<exp>)` returns the type info of the exp\
> `a.name()` is just the name\
> we can compare directly or using name\
> - `a.name()` returns the size of the object and the name of the type
> - `typeid().name()` returns a 

if we want `typeid()` add `#include <typeinfo>`
v

### overriding
the keyword `override` is optional

- Overriding is not possible if the method is not virtual.

- parameters must be <span style="color:#FF4444"> the same </span> as the func u want to override

***NOTE:*** only need to add the keyword in the prototype when function is defined somewhere else
```c++
class B{
    /* code */
    public:
        virtual f(int t) const;
};
class D : public B{
    /* code */
    public:
        // `virtual` and `override` is optional
        virtual void f(int t) const override;
};
D::f() const { /*code*/ } // DON'T add `override`
```


we can call private function of derived class from base class ??

# stack and queue

these are basically just data structures

> `stack`: <span style="color:#FF4444"> last in first out </span>\
> `queue`: <span style="color:#FF4444"> first in first out <span>

## stack & queue
supports:
- data: an ordered list of data/items
- operations:
    - `top`: get the value of the top item
    - `push`: add a new item to the top
    - `pop`: remove an item from the top

# Abstract base class

- we cannot create objects of abstract base class
- we cannot use pass-by-value or return-by-value 

## pure virtual functions
> `virtual <return-type> <func name>(<paremeters>) = 0`

we need this when we cannot provide an implementation of the function

---

# Generic programming

## Template

```c++
#include <iostream>
using namespace std;

template <typename T> inline const t&
    larger (const T& a, const T& b) { return (a < b) ? b : a; }

int main(){
    int x = 4, y =  8;
    cout << larger(x, y) << << " is a bigger number!" >> endl;

    string a("cheetah"), b("gorilla");
    cout << larger(a, b) << << " is stronger!" >> endl;
}
```
"`typename`" keyword may be replaced by "`class`"

`template <typename T> .......` is called the template instantiation
- there is <span style="color:#ff4444"> no automatic type conversion: </span>
    ```c++
    cout << larger(4, 5.5);
    // Error: no matching function call to 'larger(int, double)'
    ```
    to fix this issue we can <span style="color:#FF4444"> explicitly instantiate </span> the `function template`
    ```c++
    cout << larger<double>(4, 5.5);
    ```


what if we need different cases? (e.g. when "<" is not well defined)

```c++
/* General case */
template <typename T>
const T& larger(const T& a, const T& b)
    { cout << "general case: "; return (a < b) ? b : a; }

/* Exceptional case */
template <>
const char* const& larger(const char* const& a, const char* const& b)
    { cout << "special case: "; return (strcmp(a, b) < 0) ? b : a; }

int main(){
    int x = 4, y =  8;
    cout << larger(x, y) << << " is a bigger number!" >> endl;

    string a("cheetah"), b("gorilla");
    cout << larger(a, b) << << " is stronger!" >> endl;

    // if we don't have the exceptional case, this wouldn't properly work
    // it will work, just not correctly
    const char* microsoft = "microsoft";
    const char* apple  = "apple";
    cout << larger(apple, microsoft) << << " is better!" >> endl;
}
```

note that we can overload the template with a non-template version, the preference will go to the non-template version
- that is
    ```c++
    /* General case */
    template <typename T>
    const T& larger(const T& a, const T& b)
        { cout << "general case: "; return (a < b) ? b : a; }

    /* non-template exceptional case */
    const char* const& larger(const char* const& a, const char* const& b)
        { cout << "non-template case: "; return (strcmp(a, b) < 0) ? b : a; }
    ```

### <span style="color:#c9defc"> templates with more than 1 formal arguments </span>

#### Don't return by reference / don't return if there are different data types that may be returned otherwise
```c++
#include <iostream> /* File: fcn-template-2arg.cpp */
using namespace std;

template <typename T1, typename T2> 
inline const T1& larger(const T1& a, const T2& b) { return (a < b) ? b : a; }

int main() {
    cout << larger(4, 5.5) << endl; // T1 is int, T2 is double
    // this is NOT ok
    // const int& 5.5 -> cannot happen!!!

    // need to return by value

    cout << larger(5.5, 4) << endl; // T1 is double, T2 is int

}

```
> `? :` returns by value when the two data types are different

return by value works since we can change double to int\

if we have various combinations of diffenrent arguments for the same template, the compiler will instantiate a function for each combination

examples of poor code:
```c++
#include <iosteam>
template <class T> T* create() { return new T; }; // correct syntax
template <class T> void f() { T a; cout << a << endl; }

int main() { create(); f(); } // BAD
// compiler cannot deduct the parameter T

// this is NOT syntax error, but error on CALLING the function
```

to fix this, we tell the function explicitly what type to use

```c++
int main(){
    int* p = create<int>();
    *p = 5;
    cout << *p << endl;
    f<double>();
}
```

## function templates

- we MUST write the datatype when making objects
- we can have nontype parameters
```c++
template <typename T> class List_Node
{
public:
    List_Node(const T& x) : data(x) { }
    List_Node* next {nullptr};
    List_Node* prev {nullptr};
    T data;
};


template<typename T, int max_num_items> class List
{
public:
    List() = default;
    bool append(const T& item) {
        if (num_items == max_num_items)
           { cerr << "List is full\n"; return false; }
        else
           { /* incomplete */ return true; }
    }
    void print() const {
        for (const List_Node<T>* p = head; p; p = p->next)
            cout << p->data << endl;
        }
// ... Other member functions
private:
    List_Node<T>* head {nullptr};
    List_Node<T>* tail {nullptr};
};

int main(){
    List<int, 10> hi;
}
```

### finding the Size of Any Array Using Nontype Parameter

```c++
template <typename T, int N>
int f(T (&x) [N]) { return N; }

int main(){
    int a[] = {10, 20, 40};
    cout << f(a) << endl;
}
```

note:
```c++
int g(int (&x)[4]){/* code */} // x references an integer array of size 4
```

# operator overloading

we may want to write the overload function as a global non-member function so that it can be commutative


pre-increment vs post-increment

```c++
Vector& operator++(); // pre-increment returns lvalue
Vector operator++(int); // post-increment takes a dummy int, returns r-value
```

***NOTE:*** 
- `pre-increment` returns a modifiable `lvalue` (by reference)
- `post-increment` returns a copy of the object, which is an `rvalue` (this is because post-increment is supposed to give back the original value)

***NOTE2:***
- for user defined objects, we can do post-increment on the temporary object we get from another post-increment
    ```c++
    Vector a(0.0,0.0);
    a++++; // is ok
    ```
- not for literal constants 
    ```c++
    int x(2);
    x++++; // BAD
    ```
# Friend class and operator

- we do NOT need forward declaration of the friend function or class
- friends are granted, not taken 
- friends are not symmetric
    > A is B's friend != B is A's friend
- friends are not transitive
    > A is B's friend, B is C's friend != A is C's friend
- friends are not inherited


issue: ( `<<` as a member function )
```c++
class Vector{
    private:
        int x;
        int y;
    public:
        /* some code*/
        ostream& operator<<(ostream& os){
            os << "(" << x << "," << y << ")"
        }
}

int main(){
    Vector a(1,2);
    a << cout; // yes, this would be the syntax to print
}
```

sub-optimal solution: global function
- problem: inefficient due to more calls

solution: `friends`!
```c++
class Vector{
    friend ostream& operator<<(ostream&os, const Vector&a);
    friend Vector operator+(const Vector& a, const Vector&b);
    private:
        int x;
        int y;
    public:
        /* some code*/
        
}
ostream& operator<<(ostream&os, const Vector&a){
    return os << "(" << x << "," << y << ")";
}
Vector operator+(const Vector& a, const Vector&b){
    return Vector(ax + b.x, a.y + b.y);
}
```

# Algorithmns
Analyzing an algorithm allows us to predict the resources that it requires. Resources include
- memory
- communication bandwidth
- computational time (usually most important)

see LN for concept stuff


traversing a binary tree:
- preorder (representing: prefix notation)
- inorder (representing: infix notation)
- postorder (representing: postfix notation)

## functors (function pointers)
```c++
#include <iostream> /* File: fp-smaller-larger.cpp */
using namespace std;

int larger(int x,int y){ return(x>y)?x:y; }
int smaller(int x,int y){ return(x>y)?y:x; }

int main() {
    int choice;
    cout << "Choice: (1 for larger; others for smaller): ";
    cin >> choice;

    int (*f)(int, int) = (choice == 1) ? larger : smaller;
    cout << f(3, 5) << endl;
    return 0;
}
```

we need the "()"
> `int *f(int, int) = ....` wrong!! (syntax error)\
> `int (*f)(int, int) = ....` good

array of function pointers:
```c++
double add(double x, double y) { return x+y; }
double subtract(double x, double y) { return x-y; }
double multiply(double x, double y) { return x*y; }
double divide(double x, double y) { return x/y; }

int main(){
    double (*f[])(double x, double y) // Array of function pointers
        = { add, subtract, multiply, divide };

    int operation; double x, y;
    cout << "Enter 0:+, 1:-, 2:*, 3:/, then 2 numbers: ";
    while (cin >> operation >> x >> y)
    {
        if (operation >= 0 && operation <= 3)
            cout << f[operation](x, y) << endl; // Call + - * /
        cout << "Enter 0:+, 1:-, 2:*, 3:/, then 2 numbers: ";
    }
    return 0;
}

```


here are some syntaxes that are correct

```c++


int larger(int x,int y){ return(x>y)?x:y; }
int smaller(int x,int y){ return(x>y)?y:x; }

int main(){
    int choice;

    int (*f)(int, int) = (choice == 1) ? larger : smaller; // correct
    int (*f)(int, int) = (choice == 1) ? &larger : &smaller; // correct

    cout << f(3, 5) << endl; // correct
    cout << (*f)(3, 5) << endl; // correct
    
    // this is quite similar to arrays, where the array is just an address
}
```

## BST

size:
- best case: $O(log(N))$
- worst case: $O(N)$

### deletion

before deleting any intermediate nodes, we have to make sure we "disconnect" its child nodes

### switch statement
when we use "`new`" with switch statement, we have to use '`{}`' in the case (create local variable)


### not using templates

if we dont use templates, then we need
- forward declaration of node
- separate definition of (most) functions using node

# static variables
global variables which are
- only created once
- lifetime of entire run of the program
- still controlled by its scope: file, function, class.
- if not explicitly initialized, will be zero-initialized for basic types
(and their arrays) and default-initialized for objects.

Static variables in a `function`:
- are initialized `only once` regardless how many times the function is
called.
- retain their values across function calls.
- can be accessed only inside the function


# Hashing
(implementation of hash table == hashing)

>data(key) --hash function--> array index

Constant time 

hash table
- search, insertion, deletion
- objects are unordered
- we cannot <span style="color:#ff4444"> quickly </span> do:
    - find_min, find_max
    - finding successor and predecessor
    - reporting data within a given range
    - listing out the data in order

there will always be collision when we dont map one key to one index

## hash function design
### function: Mod
> $h(k)$ = $k\text{ }mod\text{ }m$

anything that falls into `h(k) = 0` is in `equivalence class 0`, etc..

usually:
- $m$ is a prime number
- avoid powers of 2 or 10
    - $2^p$ will only depend on the $p$ lowest order `bits` of $k$ (in binary)
    - $10^p$ will only depend on the last $p$ digits of $k$ (in decimal)
- good values of $m$: 
    - `primes` not too close to powers of 2 

### dealing with strings
**Method 1:** adding the ASCII values\
issue: 
- *anagrams* (same characters, different words)
    - different permutation of same set of characters -> same hash values
- large tables -> poor distribution 
- e.g., $m$ = $10,007$ (a prime number) and all string keys have ≤ $8$ characters. Since ASCII values ≤ $127$, their numeric keys are in the range between 0 and 127 × 8 = $1016$
    - unused space

**Method 2:** 
> $h(key)$ = (key[0] + 27·key[1] + 272·key[2]) mod m


If the first 3 characters are random and the table size m is 10,007, then it is a reasonably equitable distribution.\
Problems:
- letters in an English word are not random;
- according to some dictionary, there are only 2,851 different combinations for the first 3 letters of English words;
- therefore, only at most 28% of the table can actually be hashed to (if m = 10,007).

**Method 3:** 
> h(key) = $($ ${\Sigma}_{i=0}^{L-1}$ $37^{(L-1-i)}$ $)$ mod $m$


## dealing with collisions
### 1: separate chaining
Use the idea of `equivalence class`, each one takes a separate chain

make an array of ptrs to linked list, the table doesn't actually use memory to store

- The hash table is more than a simple array, but a table of linked lists.
- Keys having the same hash values are chained on a separate linked list.\
(see LN for diagram)

insert -> insert at the front of linked list

### 2: open addressing
- only hash table to store objects of same/differnent key
- if a key collides with existing key, we `relocate` the new key
- need to determine the sequence of slots to be examined for key location (how?)

Key k may be stored at an entry different from $T[h(k)]$.\
Two issues arise:
- what is the relocation scheme? 
- how to search for k later?

Three common strategies for resolving collisions in open addressing
1) linear probing
2) quadratic probing
3) double hashing


To insert a key k, compute h0(k). If $T[h_0(k)]$ is empty, insert it there.\
If collision occurs, probe alternative cell in the following order: $h_1(k)$, $h_2(k)$, ..., until an empty cell is found.\
$h_i(k)$ = ($hash(k)$+$f(i)$) mod $m$, where the function $f$ determines the collision resolution strategy and $f(0)$ = $0$.
Different open addressing methods differ in the definition of the function $f$( ).

#### linear probing
> $f(i)$ = $i$\
> $h_i(k)$ = $(hash(k) + i)$ mod $m$

insertion -> increment index until empty\
search -> increment index until found or empty\
deleting -> (later)

issue: <span style="color:#FF1212"> primary clustering </span>
- A block of contiguously occupied table entries is a cluster.
- On `average`, when we insert a new key `k`, we may hit the `middle` of a cluster. Therefore, the time to insert k would be proportional to <span style="color:#FFa3a3"> half </span> the size of a cluster.
    - ⇒ the larger the cluster, the slower the performance.

see more disadvantages in LN

#### quadratic probing
> $f(i)$ = $i^2$\
> $h_i(k)$ = $(hash(k) + c_1 i+c_2 i^2)$ mod $m$

If the table size is prime, then a new key can always be inserted if the table is at least half empty (see proof in the reference book by Weiss).
> this means we need a table size double the input size to guarantee success insert


items with different `home positions` will have different <span style="color:#FF1212"> probe sequence </span>

issue: <span style="color:#FF1212"> secondary clustering </span>
- Keys that hashed to the <span style="color:#33aaff"> same </span> home position will probe the <span style="color:#33aaff"> same </span> `alternative cells`.
- Simulation results suggest that it generally causes less than an extra half probe per search.
- To avoid secondary clustering, the probe sequence need to be a function of the original key value, not the home position.

#### double hashing
> $f(i)$ = $i$ x $hash_2(k)$ \
> $h_i(k)$ = $(hash(k)$ + $i$ x $hash_2(k))$ mod $m$

m should be prime!!! ($hash_2(k)$ should be rel. prime w.r.t m)\
e.g. $hash_2(k)$ = R - k mod R, where R < m 


if the result of $hash_2$ is a factor of R then we only have a very limited number of possible slots when collision occurs


#### comparison:
- quadratic is simpler and faster
- double hashing results in better table


## Deletion in open addressing
Actual deletion cannot be performed in open addressing hash tables, otherwise the probing sequence will be broken.

### lazy deletion
Add an extra field to each table entry, and mark it as
- EMPTY 
- ACTIVE 
- DELETED

problem: even if an item is deleted, we still have to continue searching

### Re-hashing
`Load factor` $\alpha = N/m$, where $N$ is the number of actually hashed items in the hash table\
(0 < $\alpha$ < 1)

operations in the hash table we be drastically slower when $\alpha$ is large

when $\alpha$ is large, we `roughly double` the table size (cannot actually double, otherwise not prime) and `re-hash` all data items with new hash function

`Re-hashing` is an expensive function, but we do not have to frequently do it in a well-designed hash table

----

# namespace (tutorial)

we can use namespace to refer to two functions with the same & definition name but defined in different files

- the library writer needs to use `namespace`

namespaces are expandable (i.e. we can add stuff to it)
- technically, we are allowed to extend `std`, but this is ***REALLY NOT RECOMMENDED***. this is undefined behaviour cuz updates to the standard library can cause issues

---

# rvalue Reference and Move Semantics

(the reference we talk abt in 2011 is lvalue reference, which is an alias)

- A `variable` is a symbolic name assigned to some memory storage.
- variable vs literal constant: variable is addressable
    - `x = 100;` x is a var with an addr, 100 is a literal const

- variable has dual role, depending on where it is
    ```
    x = x + 1
    ```
    - lvalue: its location (read write)
    - prvalue (pure r-value) [C++11]: its value (read-only)

```
                          / prvalue
          / rvalue
expression                > xvalue (expiring)
          \ glvalue 
            (general lvalue)
                          \ lvalue
```


### unnammed objects 1: dynammically allocated objects/values
- Dynamic objects are managed by the heap.
- If you lose all pointers to a dynamic object, you lose the object — resulting in a memory leak.

### unnammed objects 2: temporary objects
1) const reference initialization
2) argument passing (e.g., type conversion)
3) function returned value (by copying)
4) evaluation of expressions (e.g., result of sub-expressions)


### holding a temp object:
- const reference (before c++11)
    > `const T& <variable> = <temporary object>;`
    - hold for a longer time
    - all the variables are managed by the `stack`

- rvalue reference (new to c++11)
    > `T&& <variable> = <temporary object>;`
    - **alias** of the temp object
    - can manipulate safely
    - **MUST** be temp obj
    - can be used as lvalue or rvalue
    - lifetime of temp obj is the same as the rvalue reference
    - once created, it is treated as a normal variable

similarities:
- both can be bound to temp value/obj
- both are references, must be initialized when they are declared

differneces:
- rvalue ref can be modified. Once created, can be used like <span style="color:#4444ff">regular var </span>
- f(const T&) can take almost any arguments: (const) rvalue/lvalue, temporary value/object, and even rvalue reference!
- f(T&&) can take only temporary value/object.
- If you have both f(const T&) and f(T&&), and the input argument is a temporary value/object -> T&&.


***NEVER*** return a reference to a local variable

## move semantics

if we have copy constructor, there will not be a free move constructor

```c++
Word& operator=(Word&& w) { // Move assignment
    if (this != &w) {       // No assignment for the same Word
        delete [] str;
        freq = w.freq; str = w.str;
        w.freq = 0; w.str = nullptr;
        cout << "move assignment: "; print();
    }
    return *this;
}
```
we need the if statement because we can "fake" a temp obj

if there is move constructor, then RBV will be done by move (AS LONG AS NOT GLOBAL, in that case then by copy)

## std::move()
- faking a temp object
- doesn't really move something