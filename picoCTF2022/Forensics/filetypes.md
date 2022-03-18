# File Types

### Problem 

We are given a funny looking PDF file, which contains the flag.

### Solution

Inspecting the PDF file reveals that it is definitely not a PDF file. Opening it reveals a shell script that appears to be able to write a file.

Running the shell script creates a `flag` file. Using `file` on it shows that it is an ar archive. 

Extracting gives us another file which we find to be a cpio archive. Going one by one, each layer has the following compression/archive methods: 

```
1. ar archive
2. cpio archive
3. bzip2
4. gzip
5. lzip
6. lz4
7. lzma
8. lzip
```

Decompressing these one by one gives us the final flag, which is: `picoCTF{f1len@m3_m@n1pul@t10n_f0r_0b2cur17y_3c79c5ba}`
