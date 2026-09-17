# 314_week3__230103307
# import time
# import os
# import multiprocessing as mp
# def cpu_intensive(chunk_size):
#     acc = 0
#     for i in range(chunk_size):
#         acc += (i % 7) * (i % 11)
#     return acc
# def run_bench(workers, total_items=40_000_000):
#     chunk = total_items // workers
#     start = time.perf_counter()
#     with mp.Pool(processes=workers) as pool:
#         pool.map(cpu_intensive, [chunk] * workers)
#     return time.perf_counter() - start
# if __name__ == "__main__":
#     cores = [1, 2, 4, 8, 12, 16, 24]
#     base = run_bench(1)
#     print(f"OS Reported Logical Cores: {os.cpu_count()}")
#     print(f"Single Process Baseline Time T(1): {base:.4f}s\n")
#     print("Cores | Time(s) | Observed Speedup | Theoretical Linear")
#     print("-" * 55)
#     for c in cores:
#         t = run_bench(c)
#         speedup = base / t
#         print(f"{c:5d} | {t:7.4f} | {speedup:16.2f}x | {c:18.2f}x")

# Cores | Time(s) | Observed Speedup | Theoretical Linear
# -------------------------------------------------------
#     1 |  1.3775 |             1.02x |               1.00x
#     2 |  0.7206 |             1.95x |               2.00x
#     4 |  0.3834 |             3.66x |               4.00x
#     8 |  0.2461 |             5.70x |               8.00x
#    12 |  0.1952 |             7.19x |              12.00x
#    16 |  0.1814 |             7.74x |              16.00x
#    24 |  0.2167 |             6.48x |              24.00x



 # import threading
# import time
# ITERATIONS = 5_000_000
# def worker_adjacent(shared_list, index):
#     for _ in range(ITERATIONS):
#         shared_list[index] += 1
# def worker_padded(shared_list, index):
# #Offsetby 16integers(16 *8bytes = 128bytes > 64-byte cache line)
#     padded_idx = index * 16
#     for _ in range(ITERATIONS):
#         shared_list[padded_idx] += 1
# def run_test(target_fn, size):
#     arr = [0] * size
#     threads = [threading.Thread(target=target_fn, args=(arr, i)) for i in range(4)]
#     start = time.perf_counter()
#     for t in threads: t.start()
#     for t in threads: t.join()
#     return time.perf_counter() - start
# if __name__ == "__main__":
#     t_adjacent = run_test(worker_adjacent, size=4)
#     t_padded = run_test(worker_padded, size=64)
#     print(f"Adjacent Indices (False Sharing): {t_adjacent:.4f}s")
#     print(f"Padded Indices (Cache-Aligned): {t_padded:.4f}s")
#     print(f"Slowdown Factor: {t_adjacent / t_padded:.2f}x")


# Adjacent Indices (False Sharing): 0.5676s
# Padded Indices (Cache-Aligned): 0.5440s
# Slowdown Factor: 1.04x
# kainar@MacBook-Pro-Kainar ml python start  % /usr/bin/python3 "/Users/kainar/ml python start /test.py
# "
# Adjacent Indices (False Sharing): 0.5535s
# Padded Indices (Cache-Aligned): 0.5411s
# Slowdown Factor: 1.02x
# kainar@MacBook-Pro-Kainar ml python start  % /usr/bin/python3 "/Users/kainar/ml python start /test.py
# "
# Adjacent Indices (False Sharing): 0.5563s
# Padded Indices (Cache-Aligned): 0.5526s
# Slowdown Factor: 1.01x



# import threading
# import time
# TOTAL_OPS = 2_000_000
# NUM_THREADS = 4
# class UnsafeCounter:
#     def __init__(self): self.val = 0
#     def inc(self): self.val += 1
# class LockedCounter:
#     def __init__(self):
#         self.val = 0
#         self.lock = threading.Lock()
#     def inc(self):
#         with self.lock:
#             self.val += 1
# def bench(counter_type):
#     c = counter_type()
#     ops_per_thread = TOTAL_OPS // NUM_THREADS
#     def work():
#         for _ in range(ops_per_thread):
#             c.inc()
#     threads = [threading.Thread(target=work) for _ in range(NUM_THREADS)]
#     start = time.perf_counter()
#     for t in threads: t.start()
#     for t in threads: t.join()
#     return c.val, time.perf_counter() - start
# if __name__ == "__main__":
#     val_unsafe, t_unsafe = bench(UnsafeCounter)
#     val_locked, t_locked = bench(LockedCounter)
#     print(f"Unsafe: Value = {val_unsafe:,} / {TOTAL_OPS:,} | Time: {t_unsafe:.4f}s")
#     print(f"Locked: Value = {val_locked:,} / {TOTAL_OPS:,} | Time: {t_locked:.4f}s")
#     print(f"Contention Cost Multiplier: {t_locked / t_unsafe:.2f}x")

# Unsafe: Value = 1,565,386 / 2,000,000 | Time: 0.1165s
# Locked: Value = 2,000,000 / 2,000,000 | Time: 0.2777s
# Contention Cost Multiplier: 2.38x




# import threading
# import time

# TOTAL_OPS = 2_000_000
# NUM_THREADS = 4

# def bench_lockless():
#     results = [0] * NUM_THREADS
#     ops_per_thread = TOTAL_OPS // NUM_THREADS

#     def work(thread_id):
#         local_count = 0

#         for _ in range(ops_per_thread):
#             local_count += 1

#         results[thread_id] = local_count

#     threads = [
#         threading.Thread(target=work, args=(i,))
#         for i in range(NUM_THREADS)
#     ]

#     start = time.perf_counter()

#     for t in threads:
#         t.start()

#     for t in threads:
#         t.join()

#     final_value = sum(results)

#     elapsed = time.perf_counter() - start

#     return final_value, elapsed


# value, elapsed = bench_lockless()

# print(f"Lockless: Value = {value:,} / {TOTAL_OPS:,} | Time: {elapsed:.4f}s")

# Lockless: Value = 2,000,000 / 2,000,000 | Time: 0.0332s




import time
import multiprocessing as mp
import numpy as np
def compute_heavy(n):
    x = 1.0001
    for _ in range(n):
        x = (x * 1.000001) + 0.00001
    return x
def memory_heavy(size):
    arr = np.ones(size, dtype=np.float64)
    arr = arr * 2.0 + 1.0
    return arr[0]
def run_suite():
    print("=== Compute-Bound Suite (Register Math) ===")
    for w in [1, 2, 4]:
        t0 = time.perf_counter()
        with mp.Pool(w) as p:
            p.map(compute_heavy, [25_000_000] * w)
        print(f"Workers: {w} | Execution Time: {time.perf_counter() - t0:.4f}s")
    print("\n=== Memory-Bound Suite (DRAM Bandwidth Saturation) ===")
    for w in [1, 2, 4]:
        t0 = time.perf_counter()
        with mp.Pool(w) as p:
            p.map(memory_heavy, [50_000_000] * w)
        print(f"Workers: {w} | Execution Time: {time.perf_counter() - t0:.4f}s")
if __name__ == "__main__":
    run_suite()


#     === Compute-Bound Suite (Register Math) ===
# Workers: 1 | Execution Time: 0.5556s
# Workers: 2 | Execution Time: 0.5508s
# Workers: 4 | Execution Time: 0.5488s

# === Memory-Bound Suite (DRAM Bandwidth Saturation) ===
# Workers: 1 | Execution Time: 0.1469s
# Workers: 2 | Execution Time: 0.1764s
# Workers: 4 | Execution Time: 0.3672s
# kainar@MacBook-Pro-Kainar ml python start  % 


## Q1.1

The empirical inflection point is approximately **p* = 16 workers**. My Apple M5 Pro contains **15 physical CPU cores**, consisting of **5 Super cores and 10 Performance cores**, with no SMT/Hyper-Threading. The observed speedup increases up to **7.74x at 16 workers**, but decreases to **6.48x at 24 workers**, while execution time rises from **0.1814 s to 0.2167 s**. This shows that the processor reaches physical core saturation at approximately 15–16 workers. Beyond this point, additional worker processes increase scheduling, context-switching, cache contention, and memory-resource competition.

## Q1.2

Using the empirical measurements:

$$
T(1)=1.4041s,\quad T(4)=0.3834s
$$

$$
S(4)=\frac{1.4041}{0.3834}\approx3.662
$$

Using Amdahl’s Law:

$$
3.662=\frac{1}{(1-P)+P/4}
$$

$$
\frac{1}{3.662}=1-P+\frac{P}{4}
$$

$$
0.2731=1-\frac{3P}{4}
$$

$$
P\approx0.969
$$

Therefore, the parallel fraction is approximately **96.9%**, while about **3.1%** of the workload is serial.

## Q1.3

$$
S_{max}=\frac{1}{1-P}
$$

$$
S_{max}=\frac{1}{1-0.969}\approx32.5
$$

Therefore, the theoretical maximum speedup is approximately **32.5x**. Even on a 128-core server, the workload would not achieve 128x speedup because the remaining serial portion limits scalability. In practice, the speedup would be even lower because of IPC, process creation, cache contention, memory bandwidth, and OS scheduling overhead.

## Q2.1

On a 64-bit CPython runtime, each list slot stores an **8-byte pointer** to a Python object. With a stride of 16 elements:

$$
16\times8=128\text{ bytes}
$$

Since the assumed cache-line size is 64 bytes:

$$
128>64
$$

the worker indices are separated by more than one cache line. Therefore, neighboring threads do not access list slots located in the same 64-byte cache line, which removes the intended false-sharing condition.

## Q2.2

Initially, the cache line may be in the **Shared** state in multiple cores. When Core 0 writes to index 0, it must obtain ownership of the cache line and its copy becomes **Modified**, while copies in other cores become **Invalid**. When Core 1 later writes to index 1, it must request ownership of the same cache line, causing Core 0’s copy to become invalid. Repeated writes therefore cause the cache line to move between cores, creating coherence traffic and pipeline stalls. This is false sharing because the threads modify different logical variables that happen to occupy the same physical cache line.

## Q2.3

One common solution in C++ is `alignas(64)`, which can place frequently modified data on separate cache-line boundaries. Another example is Java’s `@Contended`, which allows the JVM to add padding around selected fields so that different threads do not repeatedly write to the same cache line. Both methods use additional memory to reduce cache-coherence traffic and improve multi-core performance.

## Q3.1

The UnsafeCounter produced **1,565,386 instead of 2,000,000**, meaning that **434,614 increments were lost**.

The increment operation conceptually consists of:

**LOAD → ADD → STORE**

If two threads load the same old value before either stores the new value, both may calculate the same result and then overwrite each other. This creates a lost update and causes the final counter value to be incorrect. The UnsafeCounter took **0.1165 s**, while the LockedCounter produced the correct result but took **0.2777 s**, giving a contention cost multiplier of approximately **2.38x**.

## Q3.2

I redesigned the accumulator so that each thread increments its own local variable and stores the final result in a separate position. After all threads finish, the main thread performs a single reduction using `sum(results)`.

The lockless implementation produced the correct value of **2,000,000** in **0.0332 s**.

$$
Speedup=\frac{0.2777}{0.0332}\approx8.36x
$$

Therefore, the lockless implementation was approximately **8.36x faster** than the LockedCounter, exceeding the required 2.0x improvement.

## Q3.3

Eliminating shared mutable state is better than merely optimizing locks because it removes the source of contention. With a shared counter, all threads repeatedly compete for the same memory location and lock, causing serialization, scheduling overhead, and cache-coherence traffic. With thread-local accumulation, each worker operates independently and synchronization is needed only once during the final reduction. This design generally scales better than using more sophisticated lock types because the hot path does not contain shared-state contention.

## Q4.1

The compute-bound workload showed almost constant execution time:

* 1 worker: **0.5556 s**
* 2 workers: **0.5508 s**
* 4 workers: **0.5488 s**

Each worker performs an independent compute-heavy task, so multiple CPU cores can execute the work concurrently.

The memory-bound workload showed degradation:

* 1 worker: **0.1469 s**
* 2 workers: **0.1764 s**
* 4 workers: **0.3672 s**

The four-worker memory time is approximately:

$$
\frac{0.3672}{0.1469}\approx2.50x
$$

the single-worker time. This happens because all workers compete for the same limited memory bandwidth. Once the memory subsystem approaches saturation, adding more workers increases contention and latency instead of improving performance.

## Q4.2

My machine has **24 GB LPDDR5 unified memory**. The Apple M5 Pro configuration used here has a theoretical unified memory bandwidth of approximately **307 GB/s**.

All CPU cores share the same memory subsystem. When several memory-intensive workers run simultaneously, they compete for this finite bandwidth. Once the memory bus becomes saturated, additional CPU cores cannot increase performance proportionally because they spend more time waiting for data. This explains why the memory-bound workload increased from **0.1469 s with one worker to 0.3672 s with four workers**.

## Q4.3

If the machine-learning embedding pipeline is already memory-bandwidth saturated, adding more CPU threads with OpenMP would likely provide limited improvement because the new threads would continue competing for the same saturated memory subsystem.

A CUDA GPU implementation would generally offer greater potential speedup for a sufficiently large parallel workload because GPUs are designed for high-throughput processing and can provide very high memory bandwidth. However, the data should remain on the GPU long enough to justify transfer costs. Therefore, for a large memory-intensive embedding pipeline, I would prefer a GPU-based design with optimized memory access rather than simply adding more CPU threads.

