# 06 - Memory Efficiency

Writing memory efficient code is all about avoiding data hazards.

**Cache miss** - a piece of data accessed by an instruction but not in cache.

**Data hazards** - pipeline stall cycles due to cache misses.

## Bandwidth vs. Latency Bounded

### Classifying Cache Misses

If a value has been accessed before, we call it a **capacity miss** -  If we had an infinitely large cache, we could have no capacity misses.. Otherwise, we call it a **compulsory miss** - there is nothing you can do about this.

### Classifying Code 

We call code **fully memory bound** if all stall cycles are due to data hazards.

If the memory bus is fully utilised, we call it **memory bandwidth bound**. This is because if you had a faster memory bus, more data could go through it and the number of stall cycles would be reduced.

If the memory bus is *not* fully utilised, we call it **memory latency bound**. This is because you could be transferring data, but you're not. The stalls are happening because it's taking too long to get the data into the registers.

### Measures

- If if it bandwidth bound: increase cache-line utilisation
- If it is latency bound: prefetch
- If it is capacity bound: reduce the footprint/hot dataset

## Compulsory Cache Misses

### Compulsory Read Cache Misses

```C
struct tuple { int x; int y; int z;};
int sumIt(tuple* input, long size) {
	int sum = 0;
    for (size_t i = 0; i < size; i++)
    	sum += input[i].x;
    return sum;
}
```

This piece of code is a sequential access, and therefore would cause $\frac{\text{datasize}}{\text{cache line size}}$ cache misses - $s\_trav(w=3,u=1)$ where $w$ is the width (in integers) and $u$ is the number of data words you are using (which is 1 since we are only accessing the `x` attribute).

### Asynchronous Processing

#### Hardware Prefetching

Caches speculatively load the next expected cache line. They do this by recognising patterns: looking at adjacent cache lines, strides. *If a cache line has been accessed, we speculate the next cache line will be accessed too.* However, if you had a very wide data type you would be potentially skipping cache lines. In this case, adjacent cache line prefetching would actually be harmful because you're loading data that you never end up accessing.

The *generic cost model* distinguishes between sequential and random access for this reason: because the cost for sequential accesses are much lower.

This works well for regular memory access, but breaks for irregular access - e.g. data-dependent accesses. For example:

```C
int sumIt(tuple* input, long size, tuple* input2) {
    int sum = 0;
    for (size_t i = 0; i < size; i++) 
        sum += input2[input[i].x].y;
    return sum;
}
```

The CPU needs help, so what can we do?

#### Software Prefetching

We use an intrinsic to provide prefetching hints. We provide an address to load. This is useful for random access, but not for sequential access (because the CPU can figure that out easily). There are cases where this is not an option - for example when all the previous computation is necessary to find out where you are going next.

```c
int sumIt(tuple* input, long size, tuple* input2) {
    int sum = 0;
    for (size_t i = 0; i < size; i++) {
        sum += input2[input[i].x].y;
        __builtin_prefetch(&input2[input[i + 16].x]);
    }
    return sum;
}
```

#### Comparison

Building a software prefetcher is easier because the burden lies with the user to determine is prefetching is beneficial. However, they still need to determine what cache level should be prefetched to, if the cache line should replace a non-prefetched line, and how long it should remain.

Hardware prefetchers need to decide how aggresively they should prefetch cache lines since every false-positive causes bus traffic and causes lines to be evicted.

Factors include current bus traffic, recognised access pattern, access time of least-recently-used cache line,

### Increasing Cache-line utilisation


$$
\text{Cache-line utilisation} = \frac{\text{Data requested by instruction}}{\text{Data loaded into cache}}
$$
So to increase cache-line utilisation we can change the data layout in memory.

BAD:

```C
struct tuple { int x; int y; int z;};
int sumIt(tuple* input, long size, tuple* input2) {
    int sum = 0;
    for (size_t i = 0; i < size; i++) 
        sum += input2[input[i].x].y;
    return sum;
}
```

GOOD:

```C
struct tuple { int* x; int y; int z;};
int sumIt(tuple input, long size, tuple input2) {
    int sum = 0;
    for (size_t i = 0; i < size; i++) 
        sum += input2.y[input.x[i]];
    return sum;
}
```

We use an *struct of arrays* rather than an *array of structs*. Before we had $s\_trav(w=3,u=1)$, and now we have $s\_trav(w=1,u=1)$. Therefore, cache-line utilisation is 100%.

### Compulsory Write Cache Misses

```C
int* output = getOutput();
for (size_t i = 0; i < size; i++) 
    output[i] = 0;
```

This piece of code causes $\frac{2\times \text{datasize}}{\text{cache line size}}$ data hazards in the worst case because you need to 1. read data and then 2. write it. 

#### Lazy Processing

When allocating memory, we can either:

- Allocate as much as we could possibly need, but we may be overallocating
- Allocate a small array, and only `realloc` when we need more space in the output buffer.

It turns out that the second option is much slower than the first despite the fact you are allocating less memory, because you have to (1) check if you need to reallocate on each iteration and (2) reallocation cannot be vectorised.

#### Address Translation

Virtual addresses need to be translated to physical addresses using the page table. This is performed by the OS and hardware. The OS uses a trick called **copy-on-write**, with a bunch of entries pointing to this page. When a process wants to write to it, it is copied and the copy is written to. So when using `malloc`, the page table is populated, but that doesn't mean that amount of physical memory is allocated. This is **lazy allocation**. 

A **Translation Lookaside Buffer** is a cache that is dedicated to the page table. A TLB miss means that the address needs to be recalculated.

## Capacity Misses

### Thrashing

**Thrashing** occurs when a computer's memory is overused, leading to a constant state of paging and page faults. A classic example of this is matrix multiplication in a nested loop. **Loop tiling** is a solution to this.

- Cache lines on the right are never thrashed.
- Repeated access to cache lines keep them hot.

### Multipass Algorithms

Sometimes multiple passes can be beneficial even though you do more work because you avoid thrashing so it's more CPU efficient e.g. radix sorting.

### Multicore Effects

If a memory region is being accessed by one core, and then needs to be accessed by another core, QPI can be used to move it directly from one cache to another instead of flushing it to memory. MESI is a protocol used by caches to determine whether they need to bring in a cache line.

#### False Sharing

This is the final control-flow hazard, and only occurs in multicore systems. It happens when two cores attempt to access data that will never be altered by the other, but those data are within the same cache line. This causes high *cache coherence traffic* for no reason, since they never modify the same memory. Hence *false sharing* since they are not actually sharing anything. This can occur when there are two counters on the same cache line.

## Partitioning

Partitioning is restricing a given address to be cached in a subset of the available slots. This reduces the number of slots that need to be considered when retrieving a cache line. Reducing the size of a cache has the same effect.