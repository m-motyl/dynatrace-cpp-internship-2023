# Creating a list of objects with the largest date difference in descending order

## The Task

Using C++17, write a command-line application that will extract a given number of operating systems with the longest support period from the provided JSON file.

Input format: `<JSON file name>` `<COUNT>`

Output format: descending-ordered (by the support period) `COUNT`-element long list of new-line separated rows, with elements split with spaces, in the format:
```
<OS name> <OS cycle> <support period in days>
``` 

## The Solution
The RapidJSON was used to solve the task. Library supports both SAX and DOM styles for parsing JSON and the first approach has been used. This one is better suited for reading large files where speed and memory efficient are crucial, but therefore less readable. In the SAX style, the parser reads the document sequentially and triggers events for each element, making it harder to keep track of the overall structure of the document.

Main elements of SAX parser:
* `Handler` - provides the SAX event handler that parser will call during parsing,
* `Parser` - methods for parsing a JSON stream using the SAX interface,
* `Reader` - resposible for reading the input stream and generating SAX events.

Handler class must containt the following member functions which consumes the events (via function calls) from the `Reader`:
```
class Handler {
    bool Null();
    bool Bool(bool b);
    bool Int(int i);
    bool Uint(unsigned i);
    bool Int64(int64_t i);
    bool Uint64(uint64_t i);
    bool Double(double d);
    bool RawNumber(const Ch* str, SizeType length, bool copy);
    bool String(const Ch* str, SizeType length, bool copy);
    bool StartObject();
    bool Key(const Ch* str, SizeType length, bool copy);
    bool EndObject(SizeType memberCount);
    bool StartArray();
    bool EndArray(SizeType elementCount);
};
```
The returned values indicates whether the elements have been properly handled.

### Example
Structure is given:
```
[
    {
        "name": "debian",
        "os": true,
        "versions": [
            {
                "cycle": "11",
                "releaseDate": "2021-08-14",
                "eol": "2024-07-01",
            }
        ]
    }
]
```
If define a functions to print out their names and their values, output will look like this:
```
StartArray()
StartObject()
Key(name, 4, true)
String(debian, 6, true)
Key(os, 2, true)
Bool(true)
Key(versions, 8, true)
StartArray()
StartObject()
Key(cycle, 5, true)
String(11, 2, true)
Key(releaseDate, 11, true)
String(2021-08-14, 10, true)
Key(eol, 3, true)
String(2024-07-01, 10, true)
EndObject(3)
EndArray(1)
EndObject(3)
EndArray(1)
```

### Results
There are two approaches to solving this problem:
1. to create a sorted list of OS names with all included cycles
2. to create a sorted list of OS names with it's longest supported cycle 

The second is set as default, but if needed just swap the appropriate commented area on the lines marked with numbers (same order as listed above) in `jsonHandler.cpp` file.

**When the OS is not displayed:**
- if versions array is empty,
- if release date is later than eol date,
- if every version object is wrong type (boolean) or incorrectly formatted (e.g. YYYY-MM).
