# NVIDIA CUDA

![CUDA 存储单元架构](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/it/cpu_gpu.webp)

<center> <small> CUDA 存储单元架构 </small> </center>

- Global memory：Grid 范围可见。不同 Block 之间通过全局内存交换数据是常见方式（但需要正确的同步手段，通常借助 Kernel 边界或原子/协作组等）；
- Shared memory：Block 范围可见。只有同一 Block 的线程能读写同一块 Shared memory；不同 Block 彼此看不到对方的共享内存；

![CUDA 函数与变量访问权限](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/it/cpu_gpu_func_var.webp)

<center> <small> CUDA 函数与变量访问权限 </small> </center>

## [LeetGPU](https://leetgpu.com/challenges)

### Easy

#### Vector Addition

```python
import torch


# A, B, C are tensors on the GPU
def solve(A: torch.Tensor, B: torch.Tensor, C: torch.Tensor, N: int):
    torch.add(A, B, out=C)
```

```c++
#include <cuda_runtime.h>

__global__ void vector_add(const float* A, const float* B, float* C, int N) {
    int idx = blockDim.x * blockIdx.x + threadIdx.x;
    if (idx < N) {
        C[idx] = A[idx] + B[idx];
    }
}

// A, B, C are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const float* A, const float* B, float* C, int N) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N + threadsPerBlock - 1) / threadsPerBlock;

    vector_add<<<blocksPerGrid, threadsPerBlock>>>(A, B, C, N);
    cudaDeviceSynchronize();
}
```

#### Matrix Multiplication

```python
import torch


# A, B, C are tensors on the GPU
def solve(A: torch.Tensor, B: torch.Tensor, C: torch.Tensor, M: int, N: int, K: int):
    torch.matmul(A, B, out=C)
```

```c++
#include <cuda_runtime.h>

__global__ void matrix_multiplication_kernel(const float* A, const float* B, float* C, int M, int N, int K) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    int y = blockDim.y * blockIdx.y + threadIdx.y;
    if (x >= K || y >= M) {
        return;
    }

    float ans = 0.0;
    for (int i = 0; i < N; ++i) {
        ans += A[i + y * N] * B[x + i * K];
    }
    C[x + y * K] = ans;
}

// A, B, C are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const float* A, const float* B, float* C, int M, int N, int K) {
    dim3 threadsPerBlock(16, 16);
    dim3 blocksPerGrid((K + threadsPerBlock.x - 1) / threadsPerBlock.x,
                       (M + threadsPerBlock.y - 1) / threadsPerBlock.y);

    matrix_multiplication_kernel<<<blocksPerGrid, threadsPerBlock>>>(A, B, C, M, N, K);
    cudaDeviceSynchronize();
}
```

#### Matrix Transpose

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, rows: int, cols: int):
    output[:] = input.T
    # output.copy_(input.T)  # PyTorch
```

```c++
#include <cuda_runtime.h>

__global__ void matrix_transpose_kernel(const float* input, float* output, int rows, int cols) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    int y = blockDim.y * blockIdx.y  + threadIdx.y;

    if (x < cols && y < rows) {
        output[y + rows * x] = input[x + cols * y];
    }
}

// input, output are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const float* input, float* output, int rows, int cols) {
    dim3 threadsPerBlock(16, 16);
    dim3 blocksPerGrid((cols + threadsPerBlock.x - 1) / threadsPerBlock.x,
                       (rows + threadsPerBlock.y - 1) / threadsPerBlock.y);

    matrix_transpose_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, rows, cols);
    cudaDeviceSynchronize();
}
```

#### Color Inversion

```python
import torch


# image is a tensor on the GPU
def solve(image: torch.Tensor, width: int, height: int):
    image = image.view(-1,4)
    image[:, 0:3] = 255 - image[:, 0:3]
```

```c++
#include <cuda_runtime.h>

#define INV_MAGIC_NUM 0x00FFFFFF

__global__ void invert_kernel(unsigned char* image, int width, int height) {
    const size_t size = width * height;
    int idx = blockDim.x * blockIdx.x + threadIdx.x;
    unsigned int *image4 = (unsigned int *)image;  // Reverse order
    if (idx < size) {
        image4[idx] ^= INV_MAGIC_NUM;
    }
}
// image_input, image_output are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(unsigned char* image, int width, int height) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (width * height + threadsPerBlock - 1) / threadsPerBlock;

    invert_kernel<<<blocksPerGrid, threadsPerBlock>>>(image, width, height);
    cudaDeviceSynchronize();
}
```

#### Matrix Addition

```python
import torch


# A, B, C are tensors on the GPU
def solve(A: torch.Tensor, B: torch.Tensor, C: torch.Tensor, N: int):
    torch.add(A, B, out=C)
```

```c++
#include <cuda_runtime.h>

__global__ void matrix_add(const float* A, const float* B, float* C, int N) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;

    if (x < N * N) {
        C[x] = A[x] + B[x];
    }
}

// A, B, C are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const float* A, const float* B, float* C, int N) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N * N + threadsPerBlock - 1) / threadsPerBlock;

    matrix_add<<<blocksPerGrid, threadsPerBlock>>>(A, B, C, N);
    cudaDeviceSynchronize();
}
```

#### 1D Convolution

```python
import torch
import torch.nn.functional as F


# input, kernel, output are tensors on the GPU
def solve(
    input: torch.Tensor,
    kernel: torch.Tensor,
    output: torch.Tensor,
    input_size: int,
    kernel_size: int,
):
    assert input.device == kernel.device == output.device
    assert input.dtype == kernel.dtype == output.dtype == torch.float32
    assert input.numel() == input_size
    assert kernel.numel() == kernel_size
    output_size = input_size - kernel_size + 1
    assert output_size >= 1
    assert output.numel() == output_size

    x = input.view(1, 1, input_size)
    k = kernel.view(1, 1, kernel_size)

    with torch.no_grad():
        y = F.conv1d(x, k)
        output.copy_(y.view(-1))
```

```c++
#include <cuda_runtime.h>

__global__ void convolution_1d_kernel(const float *input, const float *kernel,
                                      float *output, int input_size,
                                      int kernel_size) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x >= input_size - kernel_size + 1) {
        return;
    }
    float output_kernel = 0.0;
    for (int i = 0; i < kernel_size; ++i) {
        output_kernel += kernel[i] * input[x + i];
    }
    output[x] = output_kernel;
}

// input, kernel, output are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const float* input, const float* kernel, float* output, int input_size,
                      int kernel_size) {
    int output_size = input_size - kernel_size + 1;
    int threadsPerBlock = 256;
    int blocksPerGrid = (output_size + threadsPerBlock - 1) / threadsPerBlock;

    convolution_1d_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, kernel, output, input_size,
                                                              kernel_size);
    cudaDeviceSynchronize();
}
```

#### Reverse Array

```python
import torch


# input is a tensor on the GPU
def solve(input: torch.Tensor, N: int):
    input.copy_(torch.flip(input, dims=[0]))
```

```c++
#include <cuda_runtime.h>

__global__ void reverse_array(float* input, int N) {
    int x = blockIdx.x * blockDim.x + threadIdx.x;
    if (x < N / 2) {
        float tmp = 0.0;
        tmp = input[x];
        input[x] = input[N - 1 - x];
        input[N - 1 - x] = tmp;
    }
}

// input is device pointer
extern "C" void solve(float* input, int N) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N + threadsPerBlock - 1) / threadsPerBlock;

    reverse_array<<<blocksPerGrid, threadsPerBlock>>>(input, N);
    cudaDeviceSynchronize();
}
```

#### ReLU

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int):
    torch.clamp(input, min=0, out=output)
```

```c++
#include <cuda_runtime.h>

__global__ void relu_kernel(const float* input, float* output, int N) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < N) {
        output[x] = input[x] > 0 ? input[x] : 0;
    }
}

// input, output are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const float* input, float* output, int N) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N + threadsPerBlock - 1) / threadsPerBlock;

    relu_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, N);
    cudaDeviceSynchronize();
}
```

#### Leaky ReLU

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int):
    output.copy_(torch.where(input >= 0, input, 0.01 * input))
```

```c++
#include <cuda_runtime.h>

__global__ void leaky_relu_kernel(const float* input, float* output, int N) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < N) {
        output[x] = input[x] > 0.0f ? input[x] : 0.01f * input[x];
    }
}

// input, output are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const float* input, float* output, int N) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N + threadsPerBlock - 1) / threadsPerBlock;

    leaky_relu_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, N);
    cudaDeviceSynchronize();
}
```

#### Rainbow Table

```python
import torch


def fnv1a_hash(x: torch.Tensor) -> torch.Tensor:
    FNV_PRIME = 16777619
    OFFSET_BASIS = 2166136261
    x_int = x.to(torch.int64)
    hash_val = torch.full_like(x_int, OFFSET_BASIS, dtype=torch.int64)

    for byte_pos in range(4):
        byte = (x_int >> (byte_pos * 8)) & 0xFF
        hash_val = (hash_val ^ byte) * FNV_PRIME
        hash_val = hash_val & 0xFFFFFFFF

    return hash_val.to(torch.int32)


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int, R: int):
    for _ in range(R):
        input = fnv1a_hash(input)
        output.copy_(input)
```

```c++
#include <cuda_runtime.h>

__device__ unsigned int fnv1a_hash(int input) {
    const unsigned int FNV_PRIME = 16777619;
    const unsigned int OFFSET_BASIS = 2166136261;

    unsigned int hash = OFFSET_BASIS;

    for (int byte_pos = 0; byte_pos < 4; byte_pos++) {
        unsigned char byte = (input >> (byte_pos * 8)) & 0xFF;
        hash = (hash ^ byte) * FNV_PRIME;
    }

    return hash;
}

__global__ void fnv1a_hash_kernel(const int* input, unsigned int* output, int N, int R) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < N) {
        output[x] = input[x];
        for (int i = 0; i < R; ++i) {
            output[x] = fnv1a_hash(output[x]);
        }
    }
}

// input, output are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const int* input, unsigned int* output, int N, int R) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N + threadsPerBlock - 1) / threadsPerBlock;

    fnv1a_hash_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, N, R);
    cudaDeviceSynchronize();
}
```

## 参考资料

[CUDA 春训营](https://alex-mcavoy.github.io/categories/nvidia/cuda-spring-bootcamp/)
