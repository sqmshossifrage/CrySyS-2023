Just looking at "iterated through each character of the flag" gives me a hint of brute forcing, because we can crack these characters one by one. There are 95 printable ASCII characters, with a suffix of $2^{10}$ possibilities. The message space for each character is $2^{10}\cdot 95 < 10^5$, which can be hashed within a second for the four hash algorithms listed here. 

To identify which hash algorithm is used, just look at the [length of each hash](https://en.wikipedia.org/wiki/List_of_hash_functions). `MD5` has 128 bits, `SHA-1` has 160 bits, and so on. 

To get a string of printable characters
```python
"".join([chr(i) for i in range(32,127)])
```

 To generate the binary string suffix, for `i` in `range(1024)`, 
```python
bin(i)[2:].zfill(10)
```


There's no trick left, just loops and loops. 
