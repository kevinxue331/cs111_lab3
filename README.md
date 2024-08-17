# Hash Hash Hash
Project 3 is 3 different implementations of a hashtable, one single threaded and 2 multithreaded. v1 incorporates a singular mutex lock for the entire table while v2 incorporates a lock for each table entry.

## Building
```shell
make
```

## Running
```shell
./hash-table-tester -t [number_of_threads] -s [entries_added_per_thread]
```

## First Implementation
In the `hash_table_v1_add_entry` function, I added a singular mutex lock under the overarching hashtable structure, whenever a thread tries to insert a value in the table, it locks the entire table to perform the action, this forces all other threads to wait while one edits the hashtable. However, this guarentees accuracy and avoids threads writing over each other as only one thread can edit the table at a time.

### Performance
```shell
./hash-table-tester -t 4 -s 200000
Generation: 96,172 usec
Hash table base: 3,596,088 usec
  - 0 missing
Hash table v1: 5,026,827 usec
  - 0 missing
Hash table v2: 991,290 usec
  - 0 missing
```
We can see version 1 is a slower than the base version. This is due to all the threads needing to wait and check on the lock often to see when they can access the data. This bottleneck causes signifiant overhead where cpu time is needed to check on locks repeatedly as each thread waits at the bottleneck checking on the status of the lock.

## Second Implementation
In the `hash_table_v2_add_entry` function, I create a lock for each hashmap table entry instead, creating a total of 4096 locks. This meant threads could independantly run multiple functions addings values to the hashtable as long as values were going into different hashtable entries. Each entry would lock its respective hashtable entry only allowing for other entries to be edited.

### Performance
```shell
./hash-table-tester -t 4 -s 200000
Generation: 96,172 usec
Hash table base: 3,596,088 usec
  - 0 missing
Hash table v1: 5,026,827 usec
  - 0 missing
Hash table v2: 991,290 usec
  - 0 missing
```
We can see v2 is significantly faster than both v1 and the base. We would expect the best case senario with no overhead for v2 to run 4 times faster than the base implementation as the programs has 4 times the computational threads working on the task. Our v2 implementation allows for a 3.6x speedup which is reasonable as some overhead is still needed to set and check locks. But compared to v1, the individual locks in each entry causes much less thread related overhead while allowing for parallel execution with lower overhead.

## Cleaning up
```shell
make clean
```