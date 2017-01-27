# Earl
Earl is the fanciest C++ extension for Python that allows External Term Format packing and unpacking.
This library can support packing and unpacking the External Term Format made popular by Erlang in Python. Written in C++ using the Python C API, it should be
marginally usable across the various Python implementations, but for now I can guarantee CPython because that's what it was built against.

# Version
1.4 is considered the first official release. Any version below that was a testing build.

# Features
Currently Earl supports these features.

# Packing

* SMALL_INTEGER_EXT
* INTEGER_EXT
* SMALL_TUPLE_EXT
* LARGE_TUPLE_EXT
* LIST_EXT
* STRING_EXT
* MAP_EXT
* ATOM_UTF8_EXT

# Python Types to Pack Types
* Integers < 256: SMALL_INTEGER_EXT
* Integers >= 256: INTEGER_EXT
* String/Unicode: ATOM_UTF8
* Bytes: STRING_EXT (If more than 65535, LIST_EXT)
* Dictionary: MAP_EXT
* Tuple: SMALL_TUPLE_EXT/LARGE_TUPLE_EXT (Depending on Size)
* Lists/Sets: LIST_EXT

# Unpacking

* SMALL_INTEGER_EXT
* INTEGER_EXT
* SMALL_TUPLE_EXT
* LARGE_TUPLE_EXT
* LIST_EXT
* STRING_EXT
* MAP_EXT
* ATOM_UTF8_EXT
* SMALL_ATOM_UTF8_EXT
* ATOM_EXT
* BINARY_EXT

Development Notes:
* Ideally, only one item is provided to unpack, but it can handle many items.
* A collection of many items is always returned as a Python list of various item types in the order they appear in the ETF data.
* They are converted to python types according to the list above.
* Dictionaries are slow to parse because they are parsed independently in order to retain depth. I could make this faster by ignoring depth, but that defeats the purpose of a dictionary.
* Dictionary key types are retained. IE: An int key is an int key on pack and unpack.

# So how fast is this

This code is C++ accessing individual bytes, which means it is fairly fast. Overall, the speed depends on how many items you ask it to unpack or pack and how complex each item is. This is to be expected. Depending on that, the speeds can range from nanoseconds to microseconds.

## Actual Benchmarks
Benchmarks updated as of 1.5. Packing and Unpacking now include a complex example.

# Packing
```
click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl" "earl.pack(120)"
10000000 loops, best of 3: 0.176 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl" "earl.pack(12000)"
10000000 loops, best of 3: 0.183 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl" "earl.pack((1,2,3))"
1000000 loops, best of 3: 0.322 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl" "earl.pack([1,2,3])"
1000000 loops, best of 3: 0.368 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl" "earl.pack({1,2,3})"
1000000 loops, best of 3: 0.413 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl" "earl.pack({1:2, 3:4})"
1000000 loops, best of 3: 0.481 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl" "a=earl.pack({1:{'d':[1,2,3], '_trace': 12000}})"
1000000 loops, best of 3: 1.37 usec per loop
```

# Unpacking
```
click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl;a=earl.pack(120)" "earl.unpack(a)"
10000000 loops, best of 3: 0.185 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl;a=earl.pack(12000)" "earl.unpack(a)"
10000000 loops, best of 3: 0.2 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl;a=earl.pack((1,2,3))" "earl.unpack(a)"
1000000 loops, best of 3: 0.247 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl;a=earl.pack([1,2,3])" "earl.unpack(a)"
1000000 loops, best of 3: 0.279 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl;a=earl.pack({1,2,3})" "earl.unpack(a)"
1000000 loops, best of 3: 0.257 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl;a=earl.pack({1:2, 3:4})" "earl.unpack(a)"
1000000 loops, best of 3: 0.604 usec per loop

click@DESKTOP-QL2J54T C:\Users\click\Documents\GitHub\Earl
> python -m timeit --setup="import earl;a=earl.pack({1:{'d':[1,2,3], '_trace': 12000}})" "earl.unpack(a)"
1000000 loops, best of 3: 1.24 usec per loop
```
