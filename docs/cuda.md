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

#### Matrix Copy

```python
import torch


# A, B are tensors on the GPU
def solve(A: torch.Tensor, B: torch.Tensor, N: int):
    B.copy_(A)
```

```c++
#include <cuda_runtime.h>

__global__ void copy_matrix_kernel(const float* A, float* B, int N) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < N * N) {
        B[x] = A[x];
    }

}

// A, B are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const float* A, float* B, int N) {
    int total = N * N;
    int threadsPerBlock = 256;
    int blocksPerGrid = (total + threadsPerBlock - 1) / threadsPerBlock;
    copy_matrix_kernel<<<blocksPerGrid, threadsPerBlock>>>(A, B, N);
    cudaDeviceSynchronize();
}
```

#### Simple Inference

```python
import torch
import torch.nn as nn


# input, model, and output are on the GPU
def solve(input: torch.Tensor, model: nn.Module, output: torch.Tensor):
    with torch.no_grad():
        output.copy_(model(input))
```

#### Count Array Element

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int, K: int):
    output.copy_(torch.sum(input == K))
```

```c++
#include <cuda_runtime.h>

/******************************************************************************/
__global__ void count_equal_kernel(const int* input, int* output, int N, int K) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < N) {
        if (input[x] == K) {
            atomicAdd(output, 1);
        }
    }
}
/******************************************************************************/
__global__ void count_equal_kernel(const int* input, int* output, int N, int K) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    int tid = threadIdx.x;
    __shared__ int block_cnt[256];
    int local_cnt = 0;
    if (x < N && input[x] == K) {
        local_cnt = 1;
    }
    block_cnt[tid] = local_cnt;
    __syncthreads();

    for (int stride = blockDim.x / 2; stride > 0; stride >>= 1) {
        if (tid < stride) {
            block_cnt[tid] += block_cnt[tid + stride];
        }
        __syncthreads();
    }

    if (tid == 0 && block_cnt[0] > 0) {
        atomicAdd(output, block_cnt[0]);
    }
}
/******************************************************************************/
__device__ __forceinline__ int warp_reduce_sum(int val) {
    for (int offset = 16; offset > 0; offset >>= 1) {
        val += __shfl_down_sync(0xFFFFFFFF, val, offset);
    }

    return val;
}

__global__ void count_equal_kernel(const int* input, int* output, int N, int K) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    int tid = threadIdx.x;
    int lane = tid % 32;
    int warp_id = tid / 32;

    int local_cnt = (x < N && input[x] == K) ? 1 : 0;
    local_cnt = warp_reduce_sum(local_cnt);
    __shared__ int warp_cnt[8];  // 256 / 32 = 8 warps
    if (lane == 0) {
        warp_cnt[warp_id] = local_cnt;
    }
    __syncthreads();

    if (warp_id == 0) {
        int val = (lane < 8) ? warp_cnt[lane] : 0;
        val = warp_reduce_sum(val);

        if (lane == 0 && val > 0) {
            atomicAdd(output, val);
        }
    }
}
/******************************************************************************/

// input, output are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const int* input, int* output, int N, int K) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N + threadsPerBlock - 1) / threadsPerBlock;

    count_equal_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, N, K);
    cudaDeviceSynchronize();
}
```

#### Count 2D Array Element

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int, M: int, K: int):
    output.copy_(torch.sum(input.flatten() == K))
```

```c++
#include <cuda_runtime.h>

__global__ void count_2d_equal_kernel(const int* input, int* output, int N, int M, int K) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    int y = blockDim.y * blockIdx.y + threadIdx.y;
    if (y < N && x < M && input[y*M + x] == K) {
        atomicAdd(output, 1);
    }
}

// input, output are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const int* input, int* output, int N, int M, int K) {
    dim3 threadsPerBlock(16, 16);
    dim3 blocksPerGrid((M + threadsPerBlock.x - 1) / threadsPerBlock.x,
                       (N + threadsPerBlock.y - 1) / threadsPerBlock.y);

    count_2d_equal_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, N, M, K);
    cudaDeviceSynchronize();
}
```

#### Sigmoid Linear Unit

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int):
    torch.mul(input, torch.sigmoid(input), out=output)
```

```c++
#include <cuda_runtime.h>

__global__ void silu_kernel(const float* input, float* output, int N) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < N) {
        output[x] = input[x] / (1 + expf(-input[x]));
    }
}

// input, output are device pointers
extern "C" void solve(const float* input, float* output, int N) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N + threadsPerBlock - 1) / threadsPerBlock;

    silu_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, N);
    cudaDeviceSynchronize();
}
```

#### Swish-Gated Linear Unit

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int):
    x1, x2 = input[:N//2], input[N//2:]
    torch.mul(x1 * torch.nn.functional.sigmoid(x1), x2, out=output)
```

```c++
#include <cuda_runtime.h>

__global__ void swiglu_kernel(const float* input, float* output, int halfN) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < halfN) {
        float silu = input[x] * (1 / (1 + expf(-input[x])));
        output[x] = silu * input[x+ halfN];
    }
}

// input, output are device pointers
extern "C" void solve(const float* input, float* output, int N) {
    int halfN = N / 2;
    int threadsPerBlock = 256;
    int blocksPerGrid = (halfN + threadsPerBlock - 1) / threadsPerBlock;

    swiglu_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, halfN);
    cudaDeviceSynchronize();
}
```

#### Value Clipping

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, lo: float, hi: float, N: int):
    torch.clamp(input, lo, hi, out=output)
```

```c++
#include <cuda_runtime.h>

__global__ void clip_kernel(const float* input, float* output, float lo, float hi, int N) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < N) {
        output[x] = fmaxf(fminf(input[x], hi), lo);
    }
}

// input, output are device pointers
extern "C" void solve(const float* input, float* output, float lo, float hi, int N) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N + threadsPerBlock - 1) / threadsPerBlock;

    clip_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, lo, hi, N);
    cudaDeviceSynchronize();
}
```

#### Interleave Arrays

```python
import torch


# A, B, output are tensors on the GPU
def solve(A: torch.Tensor, B: torch.Tensor, output: torch.Tensor, N: int):
    # output[0::2] = A
    # output[1::2] = B
    output.data = torch.stack([A, B], dim=1).flatten()
```

```c++
#include <cuda_runtime.h>

__global__ void interleave_kernel(const float* A, const float* B, float* output, int N) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < N) {
        output[x * 2] = A[x];
        output[2 * x + 1] = B[x];
    }
}

// A, B, output are device pointers (i.e. pointers to memory on the GPU)
extern "C" void solve(const float* A, const float* B, float* output, int N) {
    int threadsPerBlock = 256;
    int blocksPerGrid = (N + threadsPerBlock - 1) / threadsPerBlock;

    interleave_kernel<<<blocksPerGrid, threadsPerBlock>>>(A, B, output, N);
    cudaDeviceSynchronize();
}
```

#### Gaussian Error Gated Linear Unit

```python
import torch
import torch.nn.functional as F

# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int):
    output.copy_(input[:N//2] * F.gelu(input[N//2:]))
```

```c++
#include <cuda_runtime.h>

__global__ void geglu_kernel(const float* input, float* output, int halfN) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < halfN){
        output[x] = input[x] * 0.5f * input[x + halfN] * (1.0f + erff(input[x + halfN] * 0.7071067811865475f));
    }
}

// input, output are device pointers
extern "C" void solve(const float* input, float* output, int N) {
    int halfN = N / 2;
    int threadsPerBlock = 256;
    int blocksPerGrid = (halfN + threadsPerBlock - 1) / threadsPerBlock;

    geglu_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, halfN);
    cudaDeviceSynchronize();
}
```

### RGB to Grayscale

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, width: int, height: int):
    output[:] = 0.299 * input[0::3] + 0.587 * input[1::3] + 0.114 * input[2::3]
```

```c++
#include <cuda_runtime.h>

__global__ void rgb_to_grayscale_kernel(const float* input, float* output, int width, int height) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;
    if (x < width * height) {
        int x3 = x * 3;
        output[x] = input[x3] * 0.299 + input[x3 + 1] * 0.587 + input[x3 + 2] * 0.114;
    }
}

// input, output are device pointers
extern "C" void solve(const float* input, float* output, int width, int height) {
    int total_pixels = width * height;
    int threadsPerBlock = 256;
    int blocksPerGrid = (total_pixels + threadsPerBlock - 1) / threadsPerBlock;

    rgb_to_grayscale_kernel<<<blocksPerGrid, threadsPerBlock>>>(input, output, width, height);
    cudaDeviceSynchronize();
}
```

## Medium

### Reduction

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int):
    output.copy_(input.sum())
```

```c++
#include <cuda_runtime.h>

#define BLOCK_SIZE 256
#define ELEMENTS_PER_THREAD 8

__device__ __forceinline__ float warpReduceSum(float val) {
    val += __shfl_down_sync(0xFFFFFFFF, val, 16);
    val += __shfl_down_sync(0xFFFFFFFF, val, 8);
    val += __shfl_down_sync(0xFFFFFFFF, val, 4);
    val += __shfl_down_sync(0xFFFFFFFF, val, 2);
    val += __shfl_down_sync(0xFFFFFFFF, val, 1);

    return val; // lane 0
}

__global__ __launch_bounds__(BLOCK_SIZE)
void reduceKernel(const float* __restrict__ input, float* __restrict__ output, int N) {
    __shared__ float warpSums[BLOCK_SIZE / 32];  // 8 float
    
    float sum = 0.0f;
    int base = blockIdx.x * (BLOCK_SIZE * ELEMENTS_PER_THREAD) + threadIdx.x;
    #pragma unroll
    for (int i = 0; i < ELEMENTS_PER_THREAD; ++i) {
        int idx = base + i * BLOCK_SIZE;
        if (idx < N) {
            sum += input[idx];
        }
    }
    sum = warpReduceSum(sum);
    int lane = threadIdx.x & 31;
    int wid  = threadIdx.x >> 5;
    if (lane == 0) {
        warpSums[wid] = sum;
    }
    __syncthreads();

    if (wid == 0) {
        sum = (threadIdx.x < (BLOCK_SIZE / 32)) ? warpSums[lane] : 0.0f;
        sum = warpReduceSum(sum);

        if (threadIdx.x == 0) {
            output[blockIdx.x] = sum;
        }
    }
}

__global__ __launch_bounds__(BLOCK_SIZE)
void reduceFinalKernel(const float* __restrict__ input, float* __restrict__ output, int N) {
    __shared__ float warpSums[BLOCK_SIZE / 32];

    float sum = 0.0f;
    for (int i = blockIdx.x * BLOCK_SIZE + threadIdx.x; i < N; i += blockDim.x * gridDim.x) {
        sum += input[i];
    }

    sum = warpReduceSum(sum);

    int lane = threadIdx.x & 31;
    int wid  = threadIdx.x >> 5;
    if (lane == 0) {
        warpSums[wid] = sum;
    }
    __syncthreads();

    if (wid == 0) {
        sum = (threadIdx.x < (BLOCK_SIZE / 32)) ? warpSums[lane] : 0.0f;
        sum = warpReduceSum(sum);

        if (threadIdx.x == 0) {
            atomicAdd(output, sum);
        }
    }
}

extern "C" void solve(const float* input, float* output, int N) {
    if (N <= 0) {
        cudaMemset(output, 0, sizeof(float));
        return;
    }

    int elemsPerBlock = BLOCK_SIZE * ELEMENTS_PER_THREAD;     // 256 * 8 = 2048
    int numBlocks = (N + elemsPerBlock - 1) / elemsPerBlock;  // Round up

    if (numBlocks == 1) {
        reduceKernel<<<1, BLOCK_SIZE>>>(input, output, N);
    } else {
        float* partial = nullptr;
        cudaMalloc(&partial, numBlocks * sizeof(float));

        reduceKernel<<<numBlocks, BLOCK_SIZE>>>(input, partial, N);
        int finalBlocks = (numBlocks + elemsPerBlock - 1) / elemsPerBlock;
        if (finalBlocks <= 1) {
            reduceKernel<<<1, BLOCK_SIZE>>>(partial, output, numBlocks);
        } else {
            cudaMemset(output, 0, sizeof(float));
            reduceFinalKernel<<<finalBlocks, BLOCK_SIZE>>>(partial, output, numBlocks);
        }

        cudaFree(partial);
    }
}
```

### Softmax

```python
import torch


# input, output are tensors on the GPU
def solve(input: torch.Tensor, output: torch.Tensor, N: int):
    output.copy_(torch.nn.functional.softmax(input, dim=-1))
```

```c++
#include <cuda_runtime.h>
#include <math.h>

#define BLOCK_SIZE 256
#define NUM_WARPS (BLOCK_SIZE / 32)

__device__ __forceinline__ float warpReduceMax(float val) {
    val = fmaxf(val, __shfl_down_sync(0xFFFFFFFF, val, 16));
    val = fmaxf(val, __shfl_down_sync(0xFFFFFFFF, val, 8));
    val = fmaxf(val, __shfl_down_sync(0xFFFFFFFF, val, 4));
    val = fmaxf(val, __shfl_down_sync(0xFFFFFFFF, val, 2));
    val = fmaxf(val, __shfl_down_sync(0xFFFFFFFF, val, 1));

    return val;
}

__device__ __forceinline__ float warpReduceSum(float val) {
    val += __shfl_down_sync(0xFFFFFFFF, val, 16);
    val += __shfl_down_sync(0xFFFFFFFF, val, 8);
    val += __shfl_down_sync(0xFFFFFFFF, val, 4);
    val += __shfl_down_sync(0xFFFFFFFF, val, 2);
    val += __shfl_down_sync(0xFFFFFFFF, val, 1);

    return val;
}

// ============ Kernel 1: Parallel Max Reduction ============
__global__ __launch_bounds__(BLOCK_SIZE)
void reduceMaxKernel(const float* __restrict__ input, float* __restrict__ output, int N) {
    __shared__ float sdata[NUM_WARPS];

    float maxVal = -INFINITY;
    for (int i = blockIdx.x * blockDim.x + threadIdx.x; i < N; i += blockDim.x * gridDim.x) {
        maxVal = fmaxf(maxVal, input[i]);
    }

    maxVal = warpReduceMax(maxVal);

    int lane = threadIdx.x & 31;
    int wid  = threadIdx.x >> 5;
    if (lane == 0) sdata[wid] = maxVal;
    __syncthreads();

    if (wid == 0) {
        maxVal = (lane < NUM_WARPS) ? sdata[lane] : -INFINITY;
        maxVal = warpReduceMax(maxVal);
        if (lane == 0) output[blockIdx.x] = maxVal;
    }
}

// ============ Kernel 2: exp(x - max) and partial sum ============
__global__ __launch_bounds__(BLOCK_SIZE)
void expAndSumKernel(const float* __restrict__ input, float* __restrict__ output,
                     const float* __restrict__ d_max, float* __restrict__ partialSums, int N) {
    __shared__ float sdata[NUM_WARPS];
    float maxVal = d_max[0];

    float localSum = 0.0f;
    for (int i = blockIdx.x * blockDim.x + threadIdx.x; i < N; i += blockDim.x * gridDim.x) {
        float val = expf(input[i] - maxVal);
        output[i] = val;
        localSum += val;
    }

    localSum = warpReduceSum(localSum);

    int lane = threadIdx.x & 31;
    int wid  = threadIdx.x >> 5;
    if (lane == 0) sdata[wid] = localSum;
    __syncthreads();

    if (wid == 0) {
        localSum = (lane < NUM_WARPS) ? sdata[lane] : 0.0f;
        localSum = warpReduceSum(localSum);
        if (lane == 0) partialSums[blockIdx.x] = localSum;
    }
}

// ============ Kernel 3: Reduce partial sums ============
__global__ __launch_bounds__(BLOCK_SIZE)
void reduceSumKernel(const float* __restrict__ input, float* __restrict__ output, int N) {
    __shared__ float sdata[NUM_WARPS];

    float sum = 0.0f;
    for (int i = blockIdx.x * blockDim.x + threadIdx.x; i < N; i += blockDim.x * gridDim.x) {
        sum += input[i];
    }

    sum = warpReduceSum(sum);

    int lane = threadIdx.x & 31;
    int wid  = threadIdx.x >> 5;
    if (lane == 0) sdata[wid] = sum;
    __syncthreads();

    if (wid == 0) {
        sum = (lane < NUM_WARPS) ? sdata[lane] : 0.0f;
        sum = warpReduceSum(sum);
        if (lane == 0) output[0] = sum;
    }
}

// ============ Kernel 4: Normalize ============
__global__ __launch_bounds__(BLOCK_SIZE)
void normalizeKernel(float* __restrict__ data, const float* __restrict__ d_sum, int N) {
    float invSum = 1.0f / d_sum[0];
    for (int i = blockIdx.x * blockDim.x + threadIdx.x; i < N; i += blockDim.x * gridDim.x) {
        data[i] *= invSum;
    }
}

//XXX Unused
__global__ void softmax_kernel(const float* input, float* output, int N) {}

extern "C" void solve(const float* input, float* output, int N) {
    if (N <= 0) return;

    int numBlocks = min((N + BLOCK_SIZE - 1) / BLOCK_SIZE, 1024);

    float *d_partial, *d_scalar;
    cudaMalloc(&d_partial, numBlocks * sizeof(float));
    cudaMalloc(&d_scalar, sizeof(float));

    // Step 1: Find max(input)
    reduceMaxKernel<<<numBlocks, BLOCK_SIZE>>>(input, d_partial, N);
    reduceMaxKernel<<<1, BLOCK_SIZE>>>(d_partial, d_scalar, numBlocks);

    // Step 2: Compute exp(x_i - max) → output, and partial sums
    expAndSumKernel<<<numBlocks, BLOCK_SIZE>>>(input, output, d_scalar, d_partial, N);

    // Step 3: Reduce partial sums → total sum
    reduceSumKernel<<<1, BLOCK_SIZE>>>(d_partial, d_scalar, numBlocks);

    // Step 4: Normalize output[i] /= sum
    normalizeKernel<<<numBlocks, BLOCK_SIZE>>>(output, d_scalar, N);

    cudaFree(d_partial);
    cudaFree(d_scalar);
}

```

### Softmax Attention

## 参考资料

[CUDA 春训营](https://alex-mcavoy.github.io/categories/nvidia/cuda-spring-bootcamp/)
