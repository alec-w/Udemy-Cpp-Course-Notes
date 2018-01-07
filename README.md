# Udemy C++ Course Notes
Useful notes to refer to made whilst following John Purcell's Advanced C++ course on Udemy.

## Handling files
* Use `fstream` and when opening the file pass the flag `ios::in` or `ios::out` as the flag - this is the same effect as using `ifstream` or `ofstream`.
* Use `std::ws` (e.g. `input >> std::ws`) to remove leading whitespace when reading input (C++11 feature).
* Open binary files by passing the flag `ios::binary` so that newline characters aren't interpreted as newline characters (and possibly other characters too - they should all just be treated as raw data).
* When writing data to **binary** files ensure the data does not have **padding** (detailed below).
* When writing data to a **binary** file the address of the data to write must be passed rather than the actual data - e.g. `&something` not `something`. But `write` expects a char pointer (char \*). Therefore the pointer passed to write must be cast to a char pointer. This can be done using `(char *)&something` or `reinterpret_cast<char *>(&something)`.
* Data is read from a binary file in the same manner.

## Padding/Packing
* Objects (e.g. structs) are padded so that the amount of memory (returned by `sizeof`) aligns on 2 or 4 byte boundaries.
* When writing to a binary file this padding needs to be turned off so that the only thing written to the file is just the data in the object.
* To remove the padding enclose the declaration of the object with two preprocessor (`#pragma`) directives.
* The directive before is `#pragma pack(push, 1)` to align the following data on single byte boundaries, therefore there will be no padding.
* To undo the directive so it doesn't apply to everthing following use the directive after as `#pragma pack(pop)`.

## Vectors
* Vectors can be navigated through using a standard for loop with an index or by using an **iterator**.
* Vectors manage an array internally to store the data in.
* The `size()` method returns the number of elements currently stored.
* The `capacity()` method returns the allocated memory for the internal array.
* If a new element is inserted to the vector that takes it above capacity then a new array (typically a factor of 2 larger, implementation dependent) is created and all the elements from the old array are copied across.
* Using the `resize()` method changes the number of allocated elements (the `clear()` method reduces this to 0) - but has no effect on the `capacity`.
* The capacity can be increased by using the `reserve()` method - e.g. to immediately allocate memory for X elements to avoid resizing the array if you already know the maximum number of elements you will be adding.

## Lists
* Lists are a doubly linked list - each element is a node with a data value and pointers to the previous/next element.
* Navigating lists must be done using an **iterator**.
* Elements can be added to the beginning/end by using `push_front(value)`/`push_back(value)`.
* Elements can be added directly after the element currently accessed by an iterator `it` using the method `insert(it, value)`.
* Elements can be removed by calling `erase(it)` - this removes the element currently referenced by the iterator and moves the iterator to point at the next element.

## Maps
* Maps act like a lookup table - you store key -> value pairs.
* E.g. a map can use strings as keys and ints as values by creating as `map<string, int>`.
* Values can be added and accessed using standard array syntax (square brackets).
* Maps can be iterated over in a for loop in a similar way to vectors and strings.
* An iterator for a map is pointer to an object with properties first and second which are the keys and values respectively.
* Trying to access a key that does not exist with array type syntax will add it to the map (which may not be the desired behaviour).
* To check if a key exists in the map use the `find(key)` method - this returns an iterator which points to the element if it exists or points to the end of the map if it does not exist.
* The object pointed to by the iterator (e.g. iterator of type `map<string, int>::iterator`) is actually a pair (e.g. `pair<string, int>`), which has public members first and second.
* A pair can be directly inserted into the map using the method `insert(pair<string, int>("Name", 100))`.
* A pair can be created using the function `make_pair(first, second)` as a shorter syntax.
* When using custom objects as map values they must have a paramterless constructor.
* When the map assigns values it is using the objects implementation of the assignment constructor - by default this will be performing a **shallow copy** which may not be desirable and so your object will need the assignment operator overloading.
* If you insert values using `insert(make_pair(...))` then the copy constructor will be used, this also performs a **shallow copy** by default which may not be desired and so the copy constructor will need to be overwritten.
* To use custom objects as keys they must have their methods marked as `const` (because the iterator that points to the keys returns them as const values).
* The keys in a map are sorted, so a custom object used as a key must have the less than operator overloaded as `bool operator<(const myObject &other) const {...}`.
* The overloaded less than operator is used to determine if two keys are the same - so depending on your object's implementation of the less than operator you may find that the map will treat two keys as the same even if the underlying objects are actually different.

## Multimaps
* `multimap` allows repeated keys.
* Multimaps do not have an overloaded array syntax access operator so the `insert` method must be used.
* Multimaps can be looped through with an iterator like a normal map.
* Keys are ordered as they are for a map.
* The `find(key)` method can be used to find a particular key - but this will just point to the first appearance of that key in the map (and keys are no longer unique).
* To get all the values with a particualr key you would use `pair<multimap<int, string>::iterator, multimap<int, string>::interator> its = myMultimap.equal_range(key)` and then use a for loop of the form `for(multimap<int, string>::iterator it=its.first; it != its.second; it++)`.
* This can be simplified with use of the **c++11** `auto` type as `auto its = myMultimap.qual_range(30)`.

## Sets
* A set is an object that only stores unique values, e.g. declared as `set<int> numbers`.
* Values are inserted using the `insert()` method.
* Sets order their elements (so custom objects will need the less than operator overloading as they do to use them as map keys).
* Sets can be iterated in the same manner as vectors/maps/multimaps.
* Trying to insert the same element twice will just result in the second method call doing nothing.
* The `find(element)` method can be used to find if an element exists in the set.
* The `count(element)` method returns 0 or 1 (since the elements are unique - so this is useful to use in if statements to determine if an element exists (as an alternative to `find(element)`).

## Stacks and Queues
* **Stacks** are a "last in, first out" stucture - like a physical stack of an object.
* The memory used to store the values of local variables is itself a stack.
* Declate a stack as `stack<int> myStack` and add elements with the `push()` method (note that when adding objects to a `stack` collection the original object is destroyed and a new one created onto the stack - doing a shallow copy by default).
* The element on the top of the stack (last added) can be accessed with the `top()` method - either copy it as `Test test1 = testStack.top()` or get a reference to it with `Test &test2 = testStack.top()`, but if this object is then popped from the stack then the object is destoyed and this reference would be invalid.
* Elements can be taken off the top of the stack with the `pop()` method (this returns void and destroys the object).
* The `size()` method returns the number of elements in the stack.
* **Queues** are a "first in, first out" structure - like a physical queue of people.
* The front of the queue is accessed with `front()` (as opposed to accessing the last element of a stack with `top()`).
* The `pop()` method of a queue removes the first object added to the queue (referenced by `front()`).
* The last element added to a queue is accessed with `back()`.

## Sorting vectors, Deque and Friend
* A vector can be sorted with `sort(myVec.begin(), myVec.end())` (iterators can be supplied for a subsection of the vector to only sort a part of the vector).
* To sort a vector the elements must have the less than operator defined for them (so custom objects will need to overload it).
* Alternatively a comparison function can be passed to `sort()` as `sort(myVec.begin(), myVec.end(), myComparison)` (the last parameter here is a function pointer - passed by just providing the name of the function).
* The comparison function may need to access private members of the object, this can be achieved by using **friend**. Put the declaration of the comparison function in the class declaration and precede it with the keyword `friend`.
* **Deque** is a double ended queue, it behaves like a vector that can have elements pushed onto the front as well as the end (`push_front` and `push_back` are avaialble methods).

## Operator Overloading - Assignment
* The default implementation of the assignment operator for custom objects performs a **shallow copy** (directly copying the values.
* The assignment operator is an example of a **binary operator**.
* The assignment opearator needs to return a const reference to the object it is called on (e.g. so that it can be chained `test3 = test2 = test1` without allowing the object it was just called on to be changed.
* For a class `Test` this would be defined as `const Test &operator=(const Test &other) { .... return *this;}`.
* `test3.operator=(test2)` is the same as `test3 = test2`.
* Using `Test test2 = test1` uses the implicit copy constructor (copy initialization), this is decalred as `Test(const Test &other)`.
* If you implement an assignment operator, a copy constructor or a destructor then you ought to be sure to implement the other two (rule of 3) - because the reasons for implementing one of them will usually be reasons for needing implementations of the others as well.

## Operator Overloading - Left Bit Shift
* Ths operator (`<<`) is useful for printing objects, e.g. `cout << test1 << endl`.
* The bit shift operator has left right associativity (as if we do `(cout << test1) << endl`, although this is invalid code) as opposed to the `+` operator which has right left associativity (so `1+2+5` is the same as `1+(2+5)`).
* So in the line `cout << test1 << endl` both times the left bit shift operator has first argument `ostream` - this is means that it can't be implemented for a custom object as an overloaded operator (because then the implicit first argument would be the custom object), so we need to use friend again.
* This would be implemented as `friend ostream &operator<<(ostream &out, cosnt Test &test) {out << test.name; return out;}`.

## Operator Overloading - Plus
* *Note: this is using a custom Complex class that is a simple implementation of a complex number class.*
* Since the plus operator doesn't change either of its arguments it can be implemented as a free standing function (provided there a getter methods to provide the member values it needs) that returns a new Complex instance.
* This would be defined as `Complex operator+(const Complex &c1, const Complex &c2)`.
* The plus operator can also be overloaded to combine different types, e.g. `Complex operator+(const Complex &c1, double d)`, but this only implements c1+d, not d+c1 - so we need to also implement `Complex operator+(double d, const Complex &c1)`.

## Operator Overloading - Equality Test
* The equality operator is overloaded as `bool operator==(const myObject &other) const {...}`.
* Similar the not equal operator is overloaded as `bool operator!=(const myObject &other) const { return !(*this==other); }` - since we are likely going to want the not equal operator to just be the inverse of the equality operator.

## Operator Overloading - Dereference
* *Note: this is using a custom Complex class that is a simple implementation of a complex number class.*
* Since the * symbol is often used to denote the complex conjugate we might want to overload it for the Complex class that we created.
* This could be done as `Complex Complex::operator*() const {...}` - just in the same manner as previously for other operators.

## Template Classes and Functions
* The compiler needs to know about implementation at compile time (not linking time) - therefore the declaration and the implementation generally should go in the same header file (there are alternatives).
* Generally use single letters for template types `template<class T>`.
* Instead of `template<class T>` you can also use `template<typename T>`.
* Template functions can be overridden, e.g. declare `template<class T>print(T n){...}` and `print(int n)`. Then calling `print<int>(5)` calls the first and `print(5)` calls the latter.
* Type inference can be used if the argument list provides the type, e.g. we could use `print<>(5)` and type inference will convert it to `print<int>(5)` (if we hadn't overridden the int version of print then we would not need the empty angle brackets). But this only works if we have an argument list that provides the type to c++.

## Function Pointers and Related OO Topics
* A function pointer to a function `void test()` would be declared as `void (*pTest)() = &test` (a pointer to a function that has no arguments and returns void.
* The address symbol is not needed, because the name of a function is actually a pointer to that function, so we can use `void (*pTest)() = test`.
* To call the function through the pointer use `(*pTest)()`.
* The derefernce operator is not needed, so this can be simplified to `pTest()` to call the function pointed to by `pTest`.
* If the function was `void test(int value)` then a pointer to it would be created as `void (*pTest)(int)`.
* In the `algorithm` header is the function `count_if(...)`. So if we had a `vector` called `tests`and a function that returned true on some condition on the vector elements called `match(...)`, then we could get the number of matches within our vector by passing the start and end of the vector and a pointer to the matching function (just by its name) as `count_if(tests.begin(), tests.end(), match)`.
* If we had two classes `Parent` and `Child` (with the latter inheriting from the former) and both implemented a method `void print()` we could create a reference to the parent type that points at the child object `Child c1; Parent &p1 = c1;` and called `p1.print()` we would call the `print()` method from the `Parent` class.
* To avoid the situation above we must declare the method in the parent class as **virtual** (`virtual void print()`) for C++ to create a table of function pointers that point to the appropriate functions for the right type of object.
* Note that if a derived class does important cleanup in its destructor then it is a good idea to mark the base class' dstructor as `virtual` to ensure the derived class' destructor is always called.
* Linked to this is **object slicing** or **upcasting** - where we do something like `Parent p2 = Child()`, and slice off the derived class part and "throw it away" and only keep the base class properties/methods.
* A **pure virtual function** is a method that has a virtual method declared like `virtual void speak() = 0` (this does not set the function to 0, it is a notation that means that the method has no implementation in this class.
* A class that contains at least one **pure virtual function** is an **abstract class**, abstract classes cannot be instantiated and for a class derived from an abstract class to be instantiated it must contain implementations for all the pure virtual functions declared in the abstract base class.
* Although an abstract class cannot be instantiated - we can create pointers to an abstract base class, e.g. we could delcare `Animals *animals[5]` and then `Dog dog; animals[0] = dog; animals[0]->speak();` if `speak()` is one of the virtual functions declared in Animal.
* **Functors** are another useful tool, but largely superseded by **lambdas** in C++11 (which provide a "syntactic sugar" for functors.
* A **functor** is a class/struct that overloads the round bracket operator `()`.
* Functors are useful when you want to pass around a particular block of code - e.g. a base object `Test` and derived object `MatchTest` where the base class overloads the `()` operator as a pure virtual method, then an instance of the derived class can be passed to a function that expects a reference to the base class and used to perform some operation there.
* Functors can be beneficial over function pointers because they are objects and so can have private variables and other methods and also hold a state if needed.
