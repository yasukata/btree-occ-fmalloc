# A Persistent Multi-core Scalable B+Tree adopting Optimistic Concurrency Control (OCC) built on fmalloc

This repository retains a persistent version of [btree-occ](https://github.com/yasukata/btree-occ) (OCC B+Tree) that leverages [fmalloc](https://github.com/yasukata/fmalloc) to store the data on a file transparently.

For details about ```btree-occ``` and ```fmalloc```, please refer to the repositoies of them.

Essentially, fmalloc is a malloc implementation for memory mapped files, and it allocates memory from a virtual memory region of a memory mapped file.

```btree-occ``` contains a B+Tree implementation that adopts OCC for multi-core scalability.

This repository integrates both of them and builds a persistent multi-core scalable B+Tree.

Commit [09de3ad](https://github.com/yasukata/btree-occ-fmalloc/commit/09de3ad09bce14198e118c4a9c9670261da64247) shows how the in-memory B+Tree is transformed into a persistent data structure.

## Compilation

The ```make``` command will generate a benchmark app named ```btbench``` that involves the B+Tree implementation.

```
$ mkdir btocctest
$ cd btocctest
$ git clone https://github.com/yasukata/fmalloc.git
$ git clone https://github.com/yasukata/btree-occ-fmalloc.git
$ make -C fmalloc
$ cd btree-occ-fmalloc
$ make
```

## Run Benchmark

The following is the example command and output of ```btbench```.

```
$ dd if=/dev/zero of=./bt.dat bs=512M count=1
./1+0 records in
1+0 records out
536870912 bytes (537 MB, 512 MiB) copied, 2.01936 s, 266 MB/s

$ ./btbench -c 100000 -t 4 -f ./bt.dat -w
[main:329]: 4-thread, 100000 entries (25000 per-thread), keylen 8, vallen 8
[main:334]: B+Tree
[main:347]: PUT entries...
[__bench:237]: thread[0]: 25000 operations
[__bench:237]: thread[1]: 25000 operations
[__bench:237]: thread[2]: 25000 operations
[__bench:237]: thread[3]: 25000 operations
total 100000 entries: duration 45036887 ns, 450 ns/op, 2.220402 Mops/sec
[main:353]: GET entries...
[__bench:237]: thread[0]: 25000 operations
[__bench:237]: thread[1]: 25000 operations
[__bench:237]: thread[2]: 25000 operations
[__bench:237]: thread[3]: 25000 operations
total 100000 entries: duration 34863597 ns, 348 ns/op, 2.868321 Mops/sec

$ ./btbench -c 100000 -t 4 -f ./bt.dat
[main:329]: 4-thread, 100000 entries (25000 per-thread), keylen 8, vallen 8
[main:334]: B+Tree
[main:353]: GET entries...
[__bench:237]: thread[0]: 25000 operations
[__bench:237]: thread[1]: 25000 operations
[__bench:237]: thread[2]: 25000 operations
[__bench:237]: thread[3]: 25000 operations
total 100000 entries: duration 33315521 ns, 333 ns/op, 3.001603 Mops/sec
```

- ```-c```: number of operations
- ```-t```: number of concurrent threads
- ```-k```: key length
- ```-v```: value length
- ```-f```: file for saving data
- ```-w```: if this is specified, PUT benchmark will be executed

## Note

The size of the memory-mapped file should be large enough. Otherwise, fmalloc fails to allocate memory.
