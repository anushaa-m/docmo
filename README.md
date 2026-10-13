# docmo
ambulance tracking app
DAA LAB ASSIGNMENT — ALL 18 PROGRAMS
====================================

This file contains all 18 numbered assignment programs.
- Programs 1–12 use Python.
- Programs 13–15 use C with MPI.
- Programs 16–18 use C with OpenMP.
- The first three assignment questions each contain the two algorithms listed on
  the assignment sheet.
- MPI requires an MPI implementation such as Open MPI.
- OpenMP requires a compiler with OpenMP support, such as GCC.
- The code is for laboratory learning; check your instructor's exact input/output
  format before submission.


============================================================
Q1. MODIFIED BUBBLE SORT AND LINEAR SEARCH (PYTHON)
============================================================

def modified_bubble_sort(a):
    n = len(a)
    for i in range(n - 1):
        swapped = False
        for j in range(n - 1 - i):
            if a[j] > a[j + 1]:
                a[j], a[j + 1] = a[j + 1], a[j]
                swapped = True
        if not swapped:
            break
    return a

def linear_search(a, key):
    for i, value in enumerate(a):
        if value == key:
            return i
    return -1

arr = list(map(int, input("Enter array elements: ").split()))
print("Sorted array:", modified_bubble_sort(arr.copy()))

key = int(input("Enter element to search: "))
index = linear_search(arr, key)
if index == -1:
    print("Element not found")
else:
    print("Element found at index:", index)

Notes:
- Modified Bubble Sort stops early if a pass makes no swaps.
- Bubble Sort worst-case time: O(n^2); best case with this modification: O(n).
- Linear Search time: O(n).


============================================================
Q2. QUICK SORT AND BINARY SEARCH (PYTHON)
============================================================

def quick_sort(a):
    if len(a) <= 1:
        return a
    pivot = a[len(a) // 2]
    left = [x for x in a if x < pivot]
    equal = [x for x in a if x == pivot]
    right = [x for x in a if x > pivot]
    return quick_sort(left) + equal + quick_sort(right)

def binary_search(a, key):
    low, high = 0, len(a) - 1
    while low <= high:
        mid = (low + high) // 2
        if a[mid] == key:
            return mid
        elif a[mid] < key:
            low = mid + 1
        else:
            high = mid - 1
    return -1

arr = list(map(int, input("Enter array elements: ").split()))
sorted_arr = quick_sort(arr)
print("Sorted array:", sorted_arr)

key = int(input("Enter element to search: "))
index = binary_search(sorted_arr, key)
print("Index:", index if index != -1 else "Element not found")

Notes:
- Binary Search requires sorted input.
- Quick Sort average time: O(n log n); worst case: O(n^2).
- This concise Quick Sort creates lists, so its extra memory use is higher than
  an in-place implementation.


============================================================
Q3. MERGE SORT AND BINARY SEARCH (PYTHON)
============================================================

def merge_sort(a):
    if len(a) <= 1:
        return a
    mid = len(a) // 2
    left = merge_sort(a[:mid])
    right = merge_sort(a[mid:])
    result = []
    i = j = 0

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    result.extend(left[i:])
    result.extend(right[j:])
    return result

def binary_search(a, key):
    low, high = 0, len(a) - 1
    while low <= high:
        mid = (low + high) // 2
        if a[mid] == key:
            return mid
        if a[mid] < key:
            low = mid + 1
        else:
            high = mid - 1
    return -1

arr = list(map(int, input("Enter array elements: ").split()))
sorted_arr = merge_sort(arr)
print("Sorted array:", sorted_arr)

key = int(input("Enter element to search: "))
index = binary_search(sorted_arr, key)
print("Index:", index if index != -1 else "Element not found")

Notes:
- Merge Sort time: O(n log n); extra space: O(n).
- Binary Search works on the sorted array.


============================================================
Q4. PRIM'S MINIMUM SPANNING TREE (PYTHON)
============================================================

def prim(graph):
    n = len(graph)
    visited = [False] * n
    visited[0] = True
    mst = []
    total = 0

    for _ in range(n - 1):
        best = float("inf")
        u = v = -1
        for i in range(n):
            if visited[i]:
                for j in range(n):
                    if not visited[j] and graph[i][j] != 0:
                        if graph[i][j] < best:
                            best = graph[i][j]
                            u, v = i, j
        if v == -1:
            print("Graph is disconnected")
            return
        mst.append((u, v, best))
        total += best
        visited[v] = True

    print("MST edges:", mst)
    print("Total cost:", total)

graph = [
    [0, 2, 3, 0],
    [2, 0, 1, 4],
    [3, 1, 0, 5],
    [0, 4, 5, 0]
]
prim(graph)

Expected total cost: 7

Notes:
- Matrix uses 0 to mean no edge, so this representation cannot encode zero-weight
  edges.
- This straightforward adjacency-matrix implementation takes O(V^3).


============================================================
Q5. KRUSKAL'S MINIMUM SPANNING TREE (PYTHON)
============================================================

def find(parent, x):
    if parent[x] != x:
        parent[x] = find(parent, parent[x])
    return parent[x]

def kruskal(edges, n):
    edges = sorted(edges, key=lambda edge: edge[2])
    parent = list(range(n))
    mst = []
    total = 0

    for u, v, weight in edges:
        ru, rv = find(parent, u), find(parent, v)
        if ru != rv:
            mst.append((u, v, weight))
            total += weight
            parent[rv] = ru
            if len(mst) == n - 1:
                break

    if len(mst) != n - 1:
        print("Graph is disconnected")
        return
    print("MST edges:", mst)
    print("Total cost:", total)

edges = [(0, 1, 2), (0, 2, 3), (1, 2, 1),
         (1, 3, 4), (2, 3, 5)]
kruskal(edges, 4)

Expected total cost: 7

Notes:
- Edges are (u, v, weight).
- Union-Find prevents cycles by checking whether endpoints have different roots.


============================================================
Q6. DIJKSTRA'S SHORTEST PATH (PYTHON)
============================================================

def dijkstra(graph, source):
    n = len(graph)
    dist = [float("inf")] * n
    visited = [False] * n
    dist[source] = 0

    for _ in range(n):
        u = -1
        best = float("inf")
        for i in range(n):
            if not visited[i] and dist[i] < best:
                best, u = dist[i], i

        if u == -1:
            break

        visited[u] = True
        for v in range(n):
            if not visited[v] and graph[u][v] != 0:
                new_dist = dist[u] + graph[u][v]
                if new_dist < dist[v]:
                    dist[v] = new_dist

    print("Shortest distances:", dist)

graph = [
    [0, 2, 3, 0],
    [2, 0, 1, 4],
    [3, 1, 0, 5],
    [0, 4, 5, 0]
]
dijkstra(graph, 0)

Expected distances from vertex 0: [0, 2, 3, 6]

Notes:
- Dijkstra requires nonnegative edge weights.
- 0 in the matrix means no edge.


============================================================
Q7. FRACTIONAL KNAPSACK (PYTHON)
============================================================

def fractional_knapsack(values, weights, capacity):
    if len(values) != len(weights):
        raise ValueError("Values and weights must have equal lengths")
    if capacity < 0 or any(w <= 0 for w in weights):
        raise ValueError("Capacity must be nonnegative; weights must be positive")

    items = sorted(
        [(v / w, v, w) for v, w in zip(values, weights)],
        reverse=True
    )
    total = 0.0

    for ratio, value, weight in items:
        if capacity == 0:
            break
        if weight <= capacity:
            total += value
            capacity -= weight
        else:
            total += ratio * capacity
            capacity = 0

    print("Maximum value:", total)

fractional_knapsack([60, 100, 120], [10, 20, 30], 50)

Expected maximum value: 240.0

Time complexity: O(n log n), due to sorting.


============================================================
Q8. BELLMAN-FORD SHORTEST PATH
(ASSIGNMENT SHEET GROUPS THIS UNDER DYNAMIC PROGRAMMING)
============================================================

def bellman_ford(n, edges, source):
    dist = [float("inf")] * n
    dist[source] = 0

    for _ in range(n - 1):
        changed = False
        for u, v, weight in edges:
            if dist[u] != float("inf") and dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                changed = True
        if not changed:
            break

    for u, v, weight in edges:
        if dist[u] != float("inf") and dist[u] + weight < dist[v]:
            print("Negative-weight cycle detected")
            return

    print("Shortest distances:", dist)

edges = [(0, 1, 4), (0, 2, 5), (1, 2, -2), (2, 3, 3)]
bellman_ford(4, edges, 0)

Expected distances: [0, 4, 2, 5]

Notes:
- Relaxes all edges up to V-1 times.
- The extra pass checks for a negative cycle reachable from the source.


============================================================
Q9. 0/1 KNAPSACK USING DYNAMIC PROGRAMMING (PYTHON)
============================================================

def knapsack(values, weights, capacity):
    if len(values) != len(weights):
        raise ValueError("Values and weights must have equal lengths")
    if capacity < 0 or any(w <= 0 for w in weights):
        raise ValueError("Capacity must be nonnegative; weights must be positive")

    n = len(values)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]

    for i in range(1, n + 1):
        value, weight = values[i - 1], weights[i - 1]
        for w in range(capacity + 1):
            dp[i][w] = dp[i - 1][w]
            if weight <= w:
                dp[i][w] = max(
                    dp[i][w],
                    value + dp[i - 1][w - weight]
                )

    print("Maximum value:", dp[n][capacity])

knapsack([60, 100, 120], [10, 20, 30], 50)

Expected maximum value: 220

Time and space complexity: O(nW), where W is the capacity.


============================================================
Q10. N-QUEENS USING BACKTRACKING (PYTHON)
============================================================

def solve_n_queens(n):
    if n < 1:
        print("No solution exists")
        return

    board = [-1] * n

    def safe(row, col):
        for r in range(row):
            if board[r] == col:
                return False
            if abs(board[r] - col) == abs(r - row):
                return False
        return True

    def backtrack(row):
        if row == n:
            print(board)
            return True

        for col in range(n):
            if safe(row, col):
                board[row] = col
                if backtrack(row + 1):
                    return True
                board[row] = -1
        return False

    if not backtrack(0):
        print("No solution exists")

solve_n_queens(4)

One solution: [1, 3, 0, 2]

This version prints the first solution only.


============================================================
Q11. SUM OF SUBSETS USING BACKTRACKING (PYTHON)
============================================================

def sum_subsets(nums, target):
    if any(x < 0 for x in nums):
        raise ValueError("This pruning version requires nonnegative numbers")

    def backtrack(i, total, subset):
        if total == target:
            print(subset)
            return
        if i == len(nums) or total > target:
            return

        backtrack(i + 1, total + nums[i], subset + [nums[i]])
        backtrack(i + 1, total, subset)

    backtrack(0, 0, [])

sum_subsets([3, 4, 5, 2], 9)

Expected subsets:
[3, 4, 2]
[4, 5]

Worst-case time: O(2^n).


============================================================
Q12. 0/1 KNAPSACK USING LC BRANCH AND BOUND (PYTHON)
============================================================

from queue import PriorityQueue

def calculate_bound(i, profit, weight, capacity, items):
    if weight >= capacity:
        return profit

    b = profit
    j = i + 1

    while j < len(items) and weight + items[j][1] <= capacity:
        weight += items[j][1]
        b += items[j][0]
        j += 1

    if j < len(items):
        b += (capacity - weight) * items[j][0] / items[j][1]

    return b

def knapsack_branch_bound(values, weights, capacity):
    if len(values) != len(weights):
        raise ValueError("Values and weights must have equal lengths")
    if capacity < 0 or any(w <= 0 for w in weights):
        raise ValueError("Capacity must be nonnegative; weights must be positive")

    items = sorted(zip(values, weights),
                   key=lambda x: x[0] / x[1], reverse=True)

    # PriorityQueue is a min-priority queue; negative bound gives max-bound first.
    pq = PriorityQueue()
    root_bound = calculate_bound(-1, 0, 0, capacity, items)
    pq.put((-root_bound, -1, 0, 0))
    best = 0

    while not pq.empty():
        neg_bound, level, profit, weight = pq.get()

        if -neg_bound <= best:
            continue

        i = level + 1
        if i >= len(items):
            continue

        value, item_weight = items[i]

        # Branch 1: include the item
        if weight + item_weight <= capacity:
            new_profit = profit + value
            new_weight = weight + item_weight
            best = max(best, new_profit)
            b = calculate_bound(i, new_profit, new_weight, capacity, items)
            if b > best:
                pq.put((-b, i, new_profit, new_weight))

        # Branch 2: exclude the item
        b = calculate_bound(i, profit, weight, capacity, items)
        if b > best:
            pq.put((-b, i, profit, weight))

    print("Maximum value:", best)

knapsack_branch_bound([60, 100, 120], [10, 20, 30], 50)

Expected maximum value: 220

Notes:
- The bound may use a fraction only to estimate an optimistic upper bound.
- Actual selected items are never fractional.
- Worst-case time is exponential.


============================================================
Q13. MPI ARRAY SUM USING C
============================================================

Save as mpi_sum.c

#include <mpi.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv) {
    MPI_Init(&argc, &argv);

    int rank, size;
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Comm_size(MPI_COMM_WORLD, &size);

    const int n = 8;
    int data[8] = {1, 2, 3, 4, 5, 6, 7, 8};
    int valid = (n % size == 0);
    MPI_Bcast(&valid, 1, MPI_INT, 0, MPI_COMM_WORLD);

    if (!valid) {
        if (rank == 0)
            fprintf(stderr, "Error: array size must be divisible by process count\n");
        MPI_Finalize();
        return 1;
    }

    int local_n = n / size;
    int *local = malloc((size_t)local_n * sizeof(int));
    if (local == NULL) {
        fprintf(stderr, "Process %d: allocation failed\n", rank);
        MPI_Abort(MPI_COMM_WORLD, 1);
    }

    MPI_Barrier(MPI_COMM_WORLD);
    double start = MPI_Wtime();

    MPI_Scatter(data, local_n, MPI_INT,
                local, local_n, MPI_INT, 0, MPI_COMM_WORLD);

    int local_sum = 0;
    for (int i = 0; i < local_n; i++)
        local_sum += local[i];

    int total = 0;
    MPI_Reduce(&local_sum, &total, 1, MPI_INT, MPI_SUM, 0, MPI_COMM_WORLD);

    MPI_Barrier(MPI_COMM_WORLD);
    double elapsed = MPI_Wtime() - start;
    double max_elapsed = 0.0;
    MPI_Reduce(&elapsed, &max_elapsed, 1, MPI_DOUBLE, MPI_MAX, 0,
               MPI_COMM_WORLD);

    if (rank == 0) {
        printf("Total sum: %d\n", total);
        printf("Processes: %d\n", size);
        printf("Runtime: %.8f seconds\n", max_elapsed);
    }

    free(local);
    MPI_Finalize();
    return 0;
}

Compile and run:
    mpicc mpi_sum.c -o mpi_sum
    mpirun -np 4 ./mpi_sum

Expected total: 36
Try -np 1, -np 2, -np 4 and -np 8 to compare runtimes.


============================================================
Q14. MPI ARRAY MAXIMUM USING C
============================================================

Save as mpi_max.c

#include <mpi.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv) {
    MPI_Init(&argc, &argv);

    int rank, size;
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Comm_size(MPI_COMM_WORLD, &size);

    const int n = 8;
    int data[8] = {12, 45, 7, 89, 23, 56, 34, 18};
    int valid = (n % size == 0);

    if (!valid) {
        if (rank == 0)
            fprintf(stderr, "Error: array size must be divisible by process count\n");
        MPI_Finalize();
        return 1;
    }

    int local_n = n / size;
    int *local = malloc((size_t)local_n * sizeof(int));
    if (local == NULL) {
        fprintf(stderr, "Process %d: allocation failed\n", rank);
        MPI_Abort(MPI_COMM_WORLD, 1);
    }

    MPI_Barrier(MPI_COMM_WORLD);
    double start = MPI_Wtime();

    MPI_Scatter(data, local_n, MPI_INT,
                local, local_n, MPI_INT, 0, MPI_COMM_WORLD);

    int local_max = local[0];
    for (int i = 1; i < local_n; i++)
        if (local[i] > local_max)
            local_max = local[i];

    int global_max = 0;
    MPI_Reduce(&local_max, &global_max, 1, MPI_INT, MPI_MAX, 0,
               MPI_COMM_WORLD);

    MPI_Barrier(MPI_COMM_WORLD);
    double elapsed = MPI_Wtime() - start;
    double max_elapsed = 0.0;
    MPI_Reduce(&elapsed, &max_elapsed, 1, MPI_DOUBLE, MPI_MAX, 0,
               MPI_COMM_WORLD);

    if (rank == 0) {
        printf("Maximum element: %d\n", global_max);
        printf("Processes: %d\n", size);
        printf("Runtime: %.8f seconds\n", max_elapsed);
    }

    free(local);
    MPI_Finalize();
    return 0;
}

Compile and run:
    mpicc mpi_max.c -o mpi_max
    mpirun -np 4 ./mpi_max

Expected maximum: 89
Try -np 1, -np 2, -np 4 and -np 8 to compare runtimes.


============================================================
Q15. MPI ARRAY PRODUCT USING C
============================================================

Save as mpi_product.c

#include <mpi.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv) {
    MPI_Init(&argc, &argv);

    int rank, size;
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Comm_size(MPI_COMM_WORLD, &size);

    const int n = 8;
    int data[8] = {1, 2, 3, 4, 5, 6, 7, 8};
    int valid = (n % size == 0);

    if (!valid) {
        if (rank == 0)
            fprintf(stderr, "Error: array size must be divisible by process count\n");
        MPI_Finalize();
        return 1;
    }

    int local_n = n / size;
    int *local = malloc((size_t)local_n * sizeof(int));
    if (local == NULL) {
        fprintf(stderr, "Process %d: allocation failed\n", rank);
        MPI_Abort(MPI_COMM_WORLD, 1);
    }

    MPI_Barrier(MPI_COMM_WORLD);
    double start = MPI_Wtime();

    MPI_Scatter(data, local_n, MPI_INT,
                local, local_n, MPI_INT, 0, MPI_COMM_WORLD);

    long long local_product = 1;
    for (int i = 0; i < local_n; i++)
        local_product *= local[i];

    long long global_product = 1;
    MPI_Reduce(&local_product, &global_product, 1, MPI_LONG_LONG,
               MPI_PROD, 0, MPI_COMM_WORLD);

    MPI_Barrier(MPI_COMM_WORLD);
    double elapsed = MPI_Wtime() - start;
    double max_elapsed = 0.0;
    MPI_Reduce(&elapsed, &max_elapsed, 1, MPI_DOUBLE, MPI_MAX, 0,
               MPI_COMM_WORLD);

    if (rank == 0) {
        printf("Product: %lld\n", global_product);
        printf("Processes: %d\n", size);
        printf("Runtime: %.8f seconds\n", max_elapsed);
    }

    free(local);
    MPI_Finalize();
    return 0;
}

Compile and run:
    mpicc mpi_product.c -o mpi_product
    mpirun -np 4 ./mpi_product

Expected product: 40320

Note: integer overflow is possible for larger products, even with long long.


============================================================
Q16. OPENMP ARRAY SUM USING C
============================================================

Save as omp_sum.c

#include <stdio.h>
#include <omp.h>

int main(void) {
    int arr[] = {10, 20, 30, 40, 50, 60, 70, 80};
    int n = (int)(sizeof(arr) / sizeof(arr[0]));
    int sum = 0, threads;

    printf("Enter number of threads: ");
    if (scanf("%d", &threads) != 1 || threads < 1) {
        printf("Invalid number of threads\n");
        return 1;
    }

    double start = omp_get_wtime();

    #pragma omp parallel for num_threads(threads) reduction(+:sum)
    for (int i = 0; i < n; i++)
        sum += arr[i];

    double elapsed = omp_get_wtime() - start;

    printf("Array sum: %d\n", sum);
    printf("Threads: %d\n", threads);
    printf("Runtime: %.8f seconds\n", elapsed);
    return 0;
}

Compile and run:
    gcc -fopenmp omp_sum.c -o omp_sum
    ./omp_sum

Expected sum: 360
Try 1, 2 and 4 threads and record the measured runtimes.


============================================================
Q17. OPENMP ARRAY MINIMUM USING C
============================================================

Save as omp_min.c

#include <stdio.h>
#include <omp.h>

int main(void) {
    int arr[] = {12, 45, 7, 89, 23, 56, 34, 18};
    int n = (int)(sizeof(arr) / sizeof(arr[0]));
    int minimum = arr[0], threads;

    printf("Enter number of threads: ");
    if (scanf("%d", &threads) != 1 || threads < 1) {
        printf("Invalid number of threads\n");
        return 1;
    }

    double start = omp_get_wtime();

    #pragma omp parallel for num_threads(threads) reduction(min:minimum)
    for (int i = 0; i < n; i++) {
        if (arr[i] < minimum)
            minimum = arr[i];
    }

    double elapsed = omp_get_wtime() - start;

    printf("Minimum element: %d\n", minimum);
    printf("Threads: %d\n", threads);
    printf("Runtime: %.8f seconds\n", elapsed);
    return 0;
}

Compile and run:
    gcc -fopenmp omp_min.c -o omp_min
    ./omp_min

Expected minimum: 7
Try 1, 2 and 4 threads and record the measured runtimes.


============================================================
Q18. OPENMP MATRIX MULTIPLICATION USING C
============================================================

Save as omp_matrix.c

#include <stdio.h>
#include <omp.h>

#define N 3

int main(void) {
    int A[N][N] = {
        {1, 2, 3},
        {4, 5, 6},
        {7, 8, 9}
    };
    int B[N][N] = {
        {9, 8, 7},
        {6, 5, 4},
        {3, 2, 1}
    };
    int C[N][N] = {0};
    int threads;

    printf("Enter number of threads: ");
    if (scanf("%d", &threads) != 1 || threads < 1) {
        printf("Invalid number of threads\n");
        return 1;
    }

    double start = omp_get_wtime();

    #pragma omp parallel for num_threads(threads)
    for (int i = 0; i < N; i++) {
        for (int j = 0; j < N; j++) {
            int sum = 0;
            for (int k = 0; k < N; k++)
                sum += A[i][k] * B[k][j];
            C[i][j] = sum;
        }
    }

    double elapsed = omp_get_wtime() - start;

    printf("Product matrix:\n");
    for (int i = 0; i < N; i++) {
        for (int j = 0; j < N; j++)
            printf("%d ", C[i][j]);
        printf("\n");
    }

    printf("Threads: %d\n", threads);
    printf("Runtime: %.8f seconds\n", elapsed);
    return 0;
}

Compile and run:
    gcc -fopenmp omp_matrix.c -o omp_matrix
    ./omp_matrix

Expected product matrix:
30 24 18
84 69 54
138 114 90

Notes:
- The matrix dimensions are fixed and compatible in this example.
- For user-entered matrices, validate dimensions before multiplying.
- The outer loop is parallelized; each thread writes to separate rows.
- Compare runtimes using 1, 2 and 3 or 4 threads.


============================================================
END OF ALL 18 ASSIGNMENT PROGRAMS
============================================================

Quick API reminder:
- MPI: MPI_Init, MPI_Comm_rank, MPI_Comm_size, MPI_Scatter, MPI_Reduce,
  MPI_Barrier, MPI_Wtime, MPI_Finalize.
- OpenMP: #pragma omp parallel for, reduction, omp_get_wtime.
