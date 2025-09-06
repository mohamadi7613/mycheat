
# Numpy

```py
import numpy as np
```


### Creating Arrays

```py
np.array([1, 2, 3])                  # From list
np.zeros((2, 3))                     # 2x3 zeros
np.ones((2, 3))                      # 2x3 ones
np.full((2, 3), 7)                   # 2x3 filled with 7
np.eye(3)                            # Identity matrix (3x3)
np.arange(0, 10, 2)                  # [0, 2, 4, 6, 8]
np.linspace(0, 1, 5)                 # 5 numbers between 0 and 1
np.random.rand(2, 3)                 # Uniform random [0,1)
np.random.randn(2, 3)                # Normal distribution
np.random.randint(0, 10, (2, 3))     # Random integers
```


### Inspecting Arrays

```py
arr.shape         # Dimensions
arr.ndim          # Number of dimensions
arr.size          # Total elements
arr.dtype         # Data type
arr.itemsize      # Bytes per element
arr.nbytes        # Total bytes
```

### Indexing & Slicing

```py
arr[0]            # First element
arr[-1]           # Last element
arr[1:4]          # Slice
arr[:2, 1:3]      # 2D slice
arr[:, 0]         # First column
arr[::2]          # Step slicing
arr[arr > 5]      # Boolean indexing
```



### Reshaping & Combining

```py
arr.reshape(2, 3)             # Reshape
arr.flatten()                 # Flatten
np.ravel(arr)                 # Flatten view
np.vstack([a, b])             # Vertical stack
np.hstack([a, b])             # Horizontal stack
np.concatenate([a, b], axis=0)# Concatenate
```



### Math Operations

```py
arr + 2                       # Add scalar
arr * 3                       # Multiply scalar
arr1 + arr2                   # Elementwise add
arr1 * arr2                   # Elementwise multiply
np.dot(arr1, arr2)            # Matrix multiplication
np.matmul(arr1, arr2)         # Matrix multiplication
arr.T                         # Transpose
np.linalg.inv(arr)            # Inverse
np.linalg.det(arr)            # Determinant
```

### Aggregations

```py
np.min(arr)                   # Min
np.max(arr)                   # Max
np.sum(arr)                   # Sum
np.mean(arr)                  # Mean
np.std(arr)                   # Standard deviation
np.var(arr)                   # Variance
np.median(arr)                # Median
np.argmin(arr)                # Index of min
np.argmax(arr)                # Index of max
np.cumsum(arr)                # Cumulative sum
```


### Useful Functions

```py
np.unique(arr)                # Unique values
np.sort(arr)                  # Sort
np.argsort(arr)               # Indices of sorted order
np.where(arr > 0, 1, 0)       # Conditional
np.clip(arr, 0, 10)           # Limit values
np.all(arr > 0)               # Check all True
np.any(arr > 0)               # Check any True
```

### Broadcasting

```py
a = np.array([1,2,3])
b = np.array([[10],[20],[30]])
a + b
# [[11,12,13],
#  [21,22,23],
#  [31,32,33]]
```

### Saving & Loading

```py
np.save('arr.npy', arr)       # Save binary
np.load('arr.npy')            # Load binary
np.savetxt('arr.txt', arr)    # Save text
np.loadtxt('arr.txt')         # Load text
```










