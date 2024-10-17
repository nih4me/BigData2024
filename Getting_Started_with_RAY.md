# Distributed Computing with Python's Ray Library: Summing all the Prime Numbers Between 1 and N Across Multiple Hosts

This tutorial demonstrates how to use the **Ray** library for distributed computing in Python, specifically running a function to compute the sum of the first N integers across multiple hosts in a local network.

## Prerequisites
1. **Ray Library** installed on all machines.
2. **Python 3.9+** on all machines.
3. Machines must be networked with the ability to connect via SSH.

### Step 1: Install Ray

On each machine, install the Ray library:

```bash
pip install ray
```

### Step 2: Setting Up Ray on the Local Network

#### On the **Head Node** (the main machine):

1. Initialize Ray on the head node, specifying the IP address and port for communication:
   ```bash
   ray start --head --node-ip-address=<head_node_ip> --port=6379
   ```
   Replace `<head_node_ip>` with the actual IP address of your head node.

2. Note down the connection string printed in the output, which should look like:
   ```
   ray start --address='head_node_ip:6379' --redis-password='5241590000000000'
   ```

#### On the **Worker Nodes** (the other machines):

1. Connect each worker to the head node using the connection string:
   ```bash
   ray start --address='head_node_ip:6379' --redis-password='5241590000000000'
   ```

Each machine is now part of a Ray cluster, and you can distribute tasks across them.

### Step 3: Writing the Distributed Sum Function

In this example, we will use Ray to compute the sum all the prime numbers between 1 and N by splitting the work across multiple hosts.

- Without `Ray`

```py
N = 10_000_000
host_counts = 1

batch_size = N / host_count

def is_prime(n):
    if n <= 1:
        return False
    if n == 2 or n == 3:
        return True
    if n % 2 == 0 or n % 3 == 0:
        return False
    i = 5
    while i * i <= n:
        if n % i == 0 or n % (i + 2) == 0:
            return False
        i += 6
    return True

def map(start, end):
   primes = [n for in range(start, end) if is_prime(n)]
   return primes

def reduce(...):
   pass
```









```python
import ray

# Initialize Ray; if on the head node, simply run ray.init(). For workers, pass the address.
ray.init(address="auto")

# Function to compute the sum of integers in a range
@ray.remote
def sum_range(start, end):
    return sum(range(start, end + 1))

def parallel_sum(n, num_tasks):
    # Divide the range into subranges
    ranges = []
    chunk_size = n // num_tasks
    for i in range(num_tasks):
        start = i * chunk_size + 1
        end = (i + 1) * chunk_size if i != num_tasks - 1 else n
        ranges.append((start, end))

    # Distribute the tasks across workers
    results = [sum_range.remote(start, end) for start, end in ranges]

    # Gather results from all workers
    return sum(ray.get(results))

if __name__ == "__main__":
    N = 1_000_000_000  # Sum of the first 1,000,000,000 integers
    num_workers = 4  # Number of tasks to distribute across the cluster

    total_sum = parallel_sum(N, num_workers)
    print(f"The sum of the first {N} integers is: {total_sum}")

    # Close the Ray cluster when done
    ray.shutdown()
```

### Explanation:

1. **Initialization**:  
   - The `ray.init(address="auto")` automatically connects each machine to the cluster using the connection established earlier.

2. **Remote Functions**:  
   - The `@ray.remote` decorator is used to mark the `sum_range` function as a task that can be distributed across machines. This function calculates the sum of integers in a specific range.

3. **Task Distribution**:  
   - The `parallel_sum` function splits the task of summing the first N integers into `num_tasks` chunks. Each chunk is sent to a different worker for parallel computation.

4. **Task Execution**:  
   - Ray's `remote()` function calls execute the `sum_range` function on different workers. The results are retrieved using `ray.get()`, and the final sum is calculated by aggregating the results.

### Step 4: Running the Code

1. Save the script on the head node, and make sure all worker nodes can access it (either by copying the file or using a shared drive).

2. Run the script on the head node:
   ```bash
   python sum_ray.py
   ```

   The script will distribute the sum computation across the worker nodes in your Ray cluster. The final result will be printed on the head node.

### Step 5: Shutting Down the Cluster

After the computation, you can shut down Ray on all machines by running the following command:

```bash
ray stop
```

### Conclusion

In this tutorial, we demonstrated how to use Ray to distribute the computation of the sum of the first N integers across multiple hosts in a local network. You can adapt this approach for more complex distributed tasks by leveraging Ray's capabilities for parallel and distributed execution.
