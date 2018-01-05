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
