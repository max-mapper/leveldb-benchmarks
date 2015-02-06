# leveldb-benchmarks

a bunch of benchmarks testing various things with leveldb using node.js

## bulk insert

leveldb has a `batch` API that can improve bulk insert performance if used correctly. benchmarks for this are located in the following repository:

https://github.com/maxogden/level-bulk-load

takeaways from this benchmark:

- wait for a batch write to finish before writing another one
- set your `writeBufferSize` option to match the size in bytes of the data in your batches. generally values above 16MB make no difference
- it is better for throughput to write small batches earlier rather than waiting for a batch buffer to fill up and writing the batch later

## query speed for indexes

keys in leveldb sort lexicographically, and you can either get a key directly (if you know the key ahead of time) or you can create an iterator at any starting key position and iterate forward or backwards until you find the first matching key.

the follwoing repository has benchmarks exploring different approaches to indexing in leveldb:

https://github.com/maxogden/level-row-versioning-benchmarks

it turns out to be roughly twice as fast to keep a secondary index, but this obviously takes up more space on disk.

## index size

leveldb compresses data using snappy compression, and uses a keyframe-esque approach to further compress neighboring keys with a common prefix

the following benchmark inserts 10M rows (40byte keys, 1byte values) into leveldb:

https://gist.github.com/mafintosh/693319f4d357e49c5bb9

the on-disk size of the leveldb directory after running the above benchmark was 80MB.
