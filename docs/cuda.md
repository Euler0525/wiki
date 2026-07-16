# NVIDIA CUDA

![CUDA 存储单元架构](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/it/cpu_gpu.webp)

<center> <small> CUDA 存储单元架构 </small> </center>

- Global memory：Grid 范围可见。不同 Block 之间通过全局内存交换数据是常见方式（但需要正确的同步手段，通常借助 Kernel 边界或原子/协作组等）；
- Shared memory：Block 范围可见。只有同一 Block 的线程能读写同一块 Shared memory；不同 Block 彼此看不到对方的共享内存；

|   内存   |       Scope       | 生命周期               | 物理位置/特点            | 典型用途               |
| :------: | :---------------: | ---------------------- | ------------------------ | ---------------------- |
| Register |      Thread       | 当前线程执行期间       | SM，最快                 | 局部变量、累加器       |
|  Local   |      Thread       | 当前 Kernel/线程       | 实际通常在 Device Memory | Register spill、大数组 |
|  Shared  |       Block       | Block 生命周期         | SM                       | Block 内数据共享       |
|  Global  |     所有线程      | allocation/application | Device DRAM              | Tensor、输入输出       |
| Constant | Grid/所有线程读取 | Context/Application    | Device + Constant Cache  | 小型只读常量           |

![CUDA 函数与变量访问权限](https://cdn.jsdelivr.net/gh/Euler0525/tube@master/it/cpu_gpu_func_var.webp)

<center> <small> CUDA 函数与变量访问权限 </small> </center>

## History

| 时间 |        架构         |                  代表产品                   | 技术变化                                                     |        主要方向         |
| :--: | :-----------------: | :-----------------------------------------: | ------------------------------------------------------------ | :---------------------: |
| 2006 |   **Tesla / G80**   |        GeForce 8800 GTX、早期 Tesla         | **统一着色器架构**，GPU 从固定图形流水线走向通用并行处理，为 CUDA/GPGPU 奠定基础 |     图形 → 通用计算     |
| 2010 |      **Fermi**      |       GTX 480/580、Tesla C2050/M2050        | L1/L2 Cache、ECC、大幅增强 FP64、并发 Kernel，更像“通用并行处理器” |           HPC           |
| 2012 |     **Kepler**      |      GTX 680、Titan、Tesla K20/K40/K80      | **SMX、性能/瓦大幅提升、Hyper-Q、Dynamic Parallelism、GPU Boost** |       HPC + 游戏        |
| 2014 |     **Maxwell**     |     GTX 750 Ti、GTX 980、Tesla M40/M60      | **极度强调能效**，重新设计 SMM，提高每瓦性能                 |     游戏、推理、VDI     |
| 2016 |     **Pascal**      |    GTX 1080、Titan Xp、Tesla P100/P40/P4    | 16nm FinFET、**NVLink、HBM2、FP16、Unified Memory 改进**     |       AI 开始爆发       |
| 2017 |      **Volta**      |             Tesla V100、Titan V             | **第一代 Tensor Core**，AI 训练发生质变                      |      AI 训练 + HPC      |
| 2018 |     **Turing**      |      RTX 2080 Ti、Tesla T4、Quadro RTX      | **第一代 RT Core + 第二代 Tensor Core；INT8/INT4**           |     光追 + AI 推理      |
| 2020 |     **Ampere**      |           RTX 3090、A100/A30/A40            | **第三代 Tensor Core、TF32、BF16、结构化稀疏、MIG；第二代 RT Core** |   AI 训练/推理 + 游戏   |
| 2022 |     **Hopper**      |              H100、H200、GH200              | **Transformer Engine、FP8、第四代 Tensor Core、TMA、DPX、NVLink 4** |         LLM/HPC         |
| 2022 |  **Ada Lovelace**   |     RTX 4090、RTX 6000 Ada、L4/L40/L40S     | **第三代 RT Core、第四代 Tensor Core、SER、DLSS 3、AV1**     |     游戏/图形/推理      |
| 2024 |    **Blackwell**    |        B100/B200、GB200、RTX 50 系列        | **第五代 Tensor Core、FP4/NVFP4、第二代 Transformer Engine、NVLink 5；第四代 RT Core** | 生成式 AI / AI Factory  |
| 2025 | **Blackwell Ultra** |              B300、GB300 NVL72              | Blackwell 强化版：**288GB HBM3E、更强 NVFP4、Attention/推理优化** |      Reasoning AI       |
| 2026 |      **Rubin**      | Rubin GPU、Vera Rubin NVL72、DGX Rubin NVL8 | **HBM4、NVLink 6、第三代 Transformer Engine、更强长上下文/Agent 推理** | Agentic AI / AI Factory |

## Programming Guide

### Error Checking

```c++
#define CUDA_CHECK(expr_to_check) do {            \
    cudaError_t result  = expr_to_check;          \
    if(result != cudaSuccess)                     \
    {                                             \
        fprintf(stderr,                           \
                "CUDA Runtime Error: %s:%i:%d = %s\n", \
                __FILE__,                         \
                __LINE__,                         \
                result,\
                cudaGetErrorString(result));      \
    }                                             \
} while(0)
```

### Timer

```c++
cudaEvent_t start, stop;
cudaEventCreate(&start);
cudaEventCreate(&stop);

cudaEventRecord(start);
cudaEventQuery(start);

// ...

cudaEventRecord(stop);
cudaEventSynchronize(stop);

float slapsed_time;
cudaEventElapsedTime(&elapsed_time, start, stop);

cudaEventDestroy(start);
cudaEventDestroy(stop);
```

## [LeetGPU](https://leetgpu.com/challenges)

### Easy

#### [Matrix Multiplication](http://euler0525.github.io/blogs/posts/e94de802/)

```python
import torch


# A, B, C are tensors on the GPU
def solve(A: torch.Tensor, B: torch.Tensor, C: torch.Tensor, M: int, N: int, K: int):
    torch.matmul(A, B, out=C)
```

```c++
#include <cuda_runtime.h>

__global__ void matrix_multiplication_kernel(const float* A, const float* B, float* C, int M, int N, int K) {
    int x = blockDim.x * blockIdx.x + threadIdx.x;  // col of C
    int y = blockDim.y * blockIdx.y + threadIdx.y;  // row if C
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

上面这种访问内存的方法，

- 对于单个线程内部，`x,y` 固定，`i` 变化，则 `A` 的访问缓存友好；

- 在 CUDA 硬件中，`threadIdx.x` 是变化最快的维度，因此同一个 Warp 内的线程，其 `x` 坐标必然是连续（或分段连续）的，而 `y` 坐标相对稳定。分析合并访问时，对于 Warp 级别，同一循环迭代（`i` 相同），`A` 访问的地址相同（广播），`B` 访问的地址连续（合并访问）

`A` 的内存访问方式，依赖 L1 缓存广播，如果 N 过大，`A` 的一行可能超出 L1 缓存，导致缓存抖动，修改为下面这种共享内存的访问方式，

```c++
#include <cuda_runtime.h>

#define TILE_SIZE 16

__global__ void matrix_multiplication_kernel(const float* A, const float* B, float* C, int M, int N, int K) {
    int row = blockDim.y * blockIdx.y + threadIdx.y;  // [0, M)
    int col = blockDim.x * blockIdx.x + threadIdx.x;  // [0, K)

    __shared__ float As[TILE_SIZE][TILE_SIZE + 1];
    __shared__ float Bs[TILE_SIZE][TILE_SIZE + 1];

    float acc = 0.0f;
    int num_tiles = (N + TILE_SIZE - 1) / TILE_SIZE;
    for (int i = 0; i < num_tiles; ++i) {
        if (row < M && i * TILE_SIZE + threadIdx.x < N) {
            As[threadIdx.y][threadIdx.x] = A[row * N + i * TILE_SIZE + threadIdx.x];
        } else {
            As[threadIdx.y][threadIdx.x] = 0.0f;  // Boundary Filling
        }

        if (col < K && i * TILE_SIZE + threadIdx.y < N) {
            Bs[threadIdx.y][threadIdx.x] = B[(i * TILE_SIZE + threadIdx.y) * K + col];
        } else {
            Bs[threadIdx.y][threadIdx.x] = 0.0f;  // Boundary Filling
        }

        __syncthreads();

        #pragma unroll
        for (int k = 0; k < TILE_SIZE; ++k) {
            acc += As[threadIdx.y][k] * Bs[k][threadIdx.x];
        }

        __syncthreads();
    }

    if (row < M && col < K) {
        C[row * K + col] = acc;
    }
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
// 共享内存 + 并行规约
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
// warp 内部 Shuffle 规约
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

### Medium

#### Histogramming

每个线程处理一个元素，总共需要执行 $N$ 次全局内存原子加法；实现简单，访问 `input` 时线程连续，内存访问合并良好。但是很多输入相等时，多个线程修改相同地址，发生竞争，导致操作串行化。

```c++
#include <cuda_runtime.h>

#define BLOCK_SIZE 256

__global__ void histogram_kernel(const int *__restrict__ input,
                                 int *__restrict__ histogram, int N,
                                 int num_bins) {
    int idx = blockDim.x * blockIdx.x + threadIdx.x;
    if (idx >= N) {
        return;
    }
    int bin = input[idx];
    // 0 <= bin < num_bins
    if (static_cast<unsigned int>(bin) < static_cast<unsigned int>(num_bins)) {
        atomicAdd(histogram + bin, 1);
    }
}

// input, histogram are device pointers
extern "C" void solve(const int *input, int *histogram, int N, int num_bins) {
    int block_size = BLOCK_SIZE;
    int grid_size = (N + BLOCK_SIZE - 1) / BLOCK_SIZE;

    histogram_kernel<<<grid_size, block_size>>>(input, histogram, N, num_bins);
}
```

每个线程处理多个元素，并且限制块的数量。

```c++
#include <cuda_runtime.h>
#include <stdio.h>
#define BLOCK_SIZE 256
#define BLOCKS_PER_SM 4

__global__ void histogram_kernel(const int *__restrict__ input,
                                 int *__restrict__ histogram, int N,
                                 int num_bins) {
    int idx = blockDim.x * blockIdx.x + threadIdx.x;
    int stride = blockDim.x * gridDim.x;

    for (; idx < N; idx += stride) {
        int bin = input[idx];
        // 0 <= bin < num_bins
        if (static_cast<unsigned int>(bin) <
            static_cast<unsigned int>(num_bins)) {
            atomicAdd(histogram + bin, 1);
        }
    }
}

// input, histogram are device pointers
extern "C" void solve(const int *input, int *histogram, int N, int num_bins) {
    if (N <= 0 || num_bins <= 0) {
        return;
    }

    int device = 0;
    cudaGetDevice(&device);

    cudaDeviceProp properties{};
    cudaGetDeviceProperties(&properties, device);

    int required_blocks = static_cast<int>(
        (static_cast<long long>(N) + BLOCK_SIZE - 1) / BLOCK_SIZE);

    int preferred_blocks = properties.multiProcessorCount * BLOCKS_PER_SM;
    int grid_size =
        required_blocks < preferred_blocks ? required_blocks : preferred_blocks;

    histogram_kernel<<<grid_size, BLOCK_SIZE>>>(input, histogram, N, num_bins);
}
```

每个块使用共享内存直方图，输入数据先累加到共享内存，块内处理结束后，再将局部结果合并到全局直方图。

```c++
#include <cuda_runtime.h>
#include <stdio.h>
#define BLOCK_SIZE 256
#define BLOCKS_PER_SM 4

__global__ void histogram_kernel(const int *__restrict__ input,
                                 int *__restrict__ histogram, int N,
                                 int num_bins) {
    int idx = blockDim.x * blockIdx.x + threadIdx.x;
    int stride = blockDim.x * gridDim.x;
    extern __shared__ int local_histogram[];
    for (int bin = threadIdx.x; bin < num_bins; bin += blockDim.x) {
        local_histogram[bin] = 0;
    }
    __syncthreads();

    for (; idx < N; idx += stride) {
        int bin = input[idx];
        // 0 <= bin < num_bins
        if (static_cast<unsigned int>(bin) <
            static_cast<unsigned int>(num_bins)) {
            atomicAdd(local_histogram + bin, 1);
        }
    }
    __syncthreads();

    for (int bin = threadIdx.x; bin < num_bins; bin += blockDim.x) {
        int count = local_histogram[bin];
        if (count != 0) {
            atomicAdd(histogram + bin, count);
        }
    }
}

// input, histogram are device pointers
extern "C" void solve(const int *input, int *histogram, int N, int num_bins) {
    if (N <= 0 || num_bins <= 0) {
        return;
    }

    int device = 0;
    cudaGetDevice(&device);

    cudaDeviceProp properties{};
    cudaGetDeviceProperties(&properties, device);
    size_t shared_bytes = static_cast<size_t>(num_bins) * sizeof(int);
    if (shared_bytes > properties.sharedMemPerBlock) {
        return;
    }

    int required_blocks = static_cast<int>(
        (static_cast<long long>(N) + BLOCK_SIZE - 1) / BLOCK_SIZE);

    int preferred_blocks = properties.multiProcessorCount * BLOCKS_PER_SM;
    int grid_size =
        required_blocks < preferred_blocks ? required_blocks : preferred_blocks;

    histogram_kernel<<<grid_size, BLOCK_SIZE, shared_bytes>>>(input, histogram,
                                                              N, num_bins);
}
```

## 参考资料

[CUDA Programming Guide](https://docs.nvidia.com/cuda/cuda-programming-guide/)

[CUDA 春训营](https://alex-mcavoy.github.io/categories/nvidia/cuda-spring-bootcamp/)

[PKU HPC Wiki | CUDA 编程入门, Introduction to CUDA Programming: From Correctness to Performance](https://hpcwiki.io/gpu/cuda/)

[A history of NVidia Stream Multiprocessor](https://fabiensanglard.net/cuda/)
