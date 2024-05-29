---
title: FreeRTOS
categories: 嵌入式
mathjax: true
abbrlink: 77fea114
date: 2024-10-17 17:57:28
tags:
---

# FreeRTOS

## 任务管理

> **在任何时刻，只有一个任务可以处于运行状态！**
>
> FreeRTOS 是一个 **抢占式** 的实时多任务系统，其任务调度器也是 **抢占式** 的。高优先级的任务可以打断低优先级任务的运行而取得 CPU 的使用权，这样就保证了那些紧急任务的运行，我们就可以为那些对实时性要求高的任务设置一个很高的优先级。高优先级的任务执行完成以后重新把 CPU 的使用权归还给低优先级的任务。
>

**任务函数** 一般包含一个死循环：

```c
void TaskFunction(void *pvParameters) {
    int iVariableExample = 0;  // 任务实例独属的变量
    for(; ; ) {
        /* 完成任务功能 */
    }
    
    vTaskDelete(NULL);  // 跳出死循环，删除任务(传入NULL默认删除当前任务)
}
```

### 创建任务

**`xTaskCreate()` 函数**

```c
// task.h
BaseType_t xTaskCreate( TaskFunction_t pxTaskCode,
                        const char * const pcName,
                        const configSTACK_DEPTH_TYPE usStackDepth,
                        void * const pvParameters,
                        UBaseType_t uxPriority,
                        TaskHandle_t * const pxCreatedTask ) PRIVILEGED_FUNCTION;
/*
 * @return pdTRUE  任务创建成功
		   errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY 内存堆空间不足，无法创建任务
 */
```

- `pvTaskCode`：指向任务实现函数的指针（函数名）

> 任务通常是一个永不退出的 C 语言函数，用死循环实现。

- `pcName`：（FreeRTOS 不使用）用于辅助调试
- `usStackDepth`：任务创建时，内核为它分配的栈空间大小，单位为字(word)

> 在 32 位微型处理器中，一个字由 4 字节组成。比如 32 位宽的栈空间，`usStackDepth=100`，则分配 $400B$ 的栈空间。
>
> 应用程序在 `FreeRTOSConfig.h` 文件中定义常量 `configMINIMAL_STACK_SIZE` 决定空闲任务的栈空间大小。

- `pvParameters`：传递到任务中的值
- `uxPriority`：决定执行任务的优先级，从最低 `0` 到最高 `configMAX_PRIORITIES-1`

> `configMAX_PRIORITIES` 在 `FreeRTOSConfig.h` 中定义，值越大，内核消耗的内存空间越多。
>
> - 如果 `uxPriority` 大于 `configPRIORITIES`，则自动封顶为最大合法值。
>
> - 如果多个任务优先级相同，调度器会让这些任务轮流执行，每个任务执行一个时间片，在时间片起始时刻进入运行态，在时间片结束时刻退出运行态；在 `FreeRTOSConfig.h` 文件中定义的 `configTICK_RATE_HZ` 表示 tick 的频率，时间片长度为 `1/configTICK_RATE_HZ`。

- `pxCreatedTask`：传出任务的句柄，在 API 调用中对创建出的任务进行引用（比如改变优先级、删除任务）

---

```c
void vTaskStartScheduler( void ) PRIVILEGED_FUNCTION;  // 启动任务调度器，任务开始执行
```

---

代码示例见 FreeRTOS 实时内核使用指南 P10~20.

### 任务状态

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube@master/it/rtos_task_state_machine.webp" alt="rtos_task_state_machine" width="60%" height="60%" />

<center> <small> Full task state machine </small> </center>

---

```c
/*
 * Blocking API function e.g.
 * 代替（忙等待）的空循环，让任务在延迟期间进入阻塞态，可以去调度其它任务
 * @param xTicksToDelay 延迟tick周期数 = 延迟时间 / portTICK_RATE_MS
 */
void vTaskDelay( const TickType_t xTicksToDelay ) PRIVILEGED_FUNCTION;

void xTaskDelayUntil( TickType_t * const pxPreviousWakeTime,
                                const TickType_t xTimeIncrement )
```

---

代码示例见 FreeRTOS 实时内核使用指南 P24, 28, 29~30.

**`vApplacationIdleHook` 函数**

> `FreeRTOSConfig.h` 中的常量 `configUSE_IDLE_HOOK` 定义为 1 时，Hook 函数才会被调用。

```c
void vApplacationIdleHook(void);  // 函数名固定，无参数，无返回值
```

**`vTaskPrioritySet` 函数**

用于在调度器启动后改变任务的优先级。

```c
void vTaskPrioritySet(xTaskHandle pxTask, unsigned portBASE_TYPE uxNewPriority);
```

- `pxTask`：任务句柄，`pxTask=NULL` 表示自身
- `uxNewPriority`：目标优先级

**`vTaskPriorityGet` 函数**

```c
/*
 * @return 返回被查询任务的优先级,
 */
void vTaskPriorityGet(xTaskHandle pxTask);
```

`vTaskDelete` 函数

```c
/*
 * 内核为任务分配的内存空间会在删除后自动回收，任务1自己占用的内存或资源需要应用程序显式释放
 * @param pxTaskDelete 待删除任务句柄
 */
void vTaskDelete(xTaskHandle pxTaskToDelete);
```

代码示例见 FreeRTOS 实时内核使用指南 P36~37, 39~40.

## 队列管理

### 创建队列

创建队列时，RTOS 从堆空间中分配内存空间，如果堆中空间不足，函数返回 `NULL`

```c
/*
 * @param  uxQueueLength 队列深度
 * @param  uxItemSize    队列中数据单元的长度
 * @return NULL 堆空间不足;非NULL，返回队列句柄
 */
xQueueHandle xQueueCreate(unsigned portBASE_TYPE uxQueueLength, unsigned portBASE_TYPE uxItemSize);
```

### 写入函数

```c
/*
 * @param  xQueue        目标队列句柄，xQueueCreate函数的返回值
 * @param  pvItemToQueue 发送数据的指针（按uxItemSize复制对应长度）
 * @param  xTicksToWait  阻塞超时时间，队列满时，任务处于阻塞态等待队列空间有效的最长等待时间
 *                       xTicksToWait = time / portTICK_RATE_MS
 * @return pdPass        超时前数据成功入队
 *         errQUEUE_FULL 队列已满                
 */
portBASE_TYPE xQueueSendToFront(xQueueHandle xQueue, const void * pvItemToQueue, portTickType xTicksToWait);
portBASE_TYPE xQueueSendToBack(xQueueHandle xQueue, const void * pvItemToQueue, portTickType xTicksToWait);
```

### 读出函数

```c
/*
 * 出队
 * @return pdPASS         读取成功
 *         errQUEUE_EMPTY 队列已空，读取失败  
 */
portBASE_TYPE xQueueReceive(xQueueHandle xQueue,       // 被读队列的句柄
                            const void * pvBuffer,     // 接收缓存指针（指向的内存空间等于数据单元大小）
                            portTickType xTicksToWait  // 阻塞超时时间
                           );
// 与出队不同，不删除被接收的数据单元
BaseType_t xQueuePeek(xQueueHandle xQueue, const void * pvBuffer, portTickType xTicksToWait);
```

> - *如果设定 `xTicksToWait=portMAX_DELAY`，并且在文件 `FreeRTOSConfig.h` 中设定 `INCLUDE_vTaskSuspend=1`，则阻塞等待无时间限制。*

### 查询函数

```c
/*
 * @param  xQueue 被查询队列的句柄
 * @return 返回队列中保存的数据单元的个数
 */
unsigned portBASE_TYPE uxQueueMessageWaiting(xQueueHandle xQueue);
```

---

- `taskYIELD();` 函数用于立刻进行任务切换，不必等到时间片耗尽。

例如，两个任务的优先级相同，一个任务调用了 `taskYIELD();` 函数，立刻由运行态进入就绪态，另一个任务进入运行态。

---

代码示例见 FreeRTOS 实时内核使用指南 P54~**65**

## 中断管理

### 二值信号量

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube@master/it/rtos_intr_binary_semaphore.webp" alt="rtos_intr_binary_semaphore" width="50%" height="50%"/>

<center> <small> Using a binary semaphore to synchronize a task with an interrupt </small> </center>

**创建二值信号量**

```c
/*
 * @param xSemaphore 创建的信号量，直接传入值，而不是传址
 */
void vSemaphoreCreateBinary(xSemaphoreHandle xSemaphore);
```

**`xSemaphoreTake` 函数**

```c
/*
 * @param  xSemaphore   获取的信号量
 * @param  xTicksToWait 阻塞超时时间（任务进入阻塞态等待信号量有效的最长时间）
 *                      = time / portTICK_RATE_MS
 * @return pdPASS       成功获得信号量
 *         pdFALSE      未获得信号量  
 */
portBASE_TYPE xSemaphoreTake(xSemaphoreHandle xSemaphore, portTickType xTickToWait);
```

**`xSemaphoreGiveFromISR` 函数**

除了互斥信号量外，其它类型的信号量可通过调用 `xSemaphoreGiveFromISR` 给出

```c
/*
 * @param xSemaphore                给出的信号量
 * @param pxHigherPriorityTaskWoken 调用本函数会使一个任务退出阻塞态，如果该任务优先级高于先前被中断的任务，pxHigherPriorityTaskWoken=pdTRUE，在中断退出前进行一次上下文切换
 * @return pdPASS 调用成功
 *         pdFALL 信号量已经有效，无法重复给出
 */
portBASE_TYPE xSemaphoreGiveFromISR(xSemaphoreHandle xSemaphore, portBASE_TYPE *pxHigherPriorityTaskWoken);
```

代码示例见 FreeRTOS 实时内核使用指南 P76~79.

### 计数信号量

一个二值信号量最多只可以锁存一个中断事件（0/1），在锁存的事件未被处理之前，如果还有中断发生，后续的中断事件将会丢失。采用计数的方式代替二值信号量，丢中断的情况可以避免。

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube@master/it/rtos_intr_count_semaphore.webp" alt="rtos_intr_count_semaphore" width="50%" height="50%" />

<center> <small> Using a counting semaphore to 'count' events </small> </center>

- **事件计数**：如图，每次事件发生时，中断服务例程都会 `Give` 一次信号量，计数值加 1.延迟处理任务每处理一个任务都会 `Take` 一次信号量，计数值减 1,。最终 **信号量的计数值等于已发生事件与已处理事件数目的差值**。

- **资源管理**：用信号量的计数值表示可用资源的数目，可以实现资源管理（详见下节）。

**创建计数信号量**

```c
/*
 * @param uxMaxCount     最大计数值（信号量队列的深度）
 * @param uxInitialCount 初始计数值
 * 						 - 事件计数：0  资源管理：uxMaxCount
 * @return NULL  堆空间不足，信号量分配内存失败
 *         !NULL 信号量创建成功，返回信号量句柄
 */
xSemaphoreHandle xSemaphoreCreateCounting(
    unsigned portBASE_TYPE uxMaxCount,
    unsigned portBASE_TYPE uxInitialCount
);
```

代码示例见 FreeRTOS 实时内核使用指南 P85~86

### 中断服务例程中使用队列

信号量用于事件通信，队列不仅可以事件通信，还可以传递数据。

`xQueueSendToFrontFromISR, xQueueSendToBackFromISR, xQueueReceiveFromISR` 是中断安全版本，用于中断服务例程中，函数在 ISR 中被调用时，不会等待队列空间可用或处理队列满的情况，因此不需要指定阻塞超时时间 `xTicksToWait`；相反，它会立即返回一个状态码，指示是否成功发送消息或是否需要进行上下文切换。

```c
/*
 * @param xQueue        目标队列句柄，xQueueCreate函数的返回值
 * @param pvItemToQueue 发送数据的指针（按uxItemSize复制对应长度）
 * @param pxHigherPriorityTaskWoken 调用本函数会使一个任务退出阻塞态，如果该任务优先级高于先前被中断的任务，pxHigherPriorityTaskWoken=pdTRUE，在中断退出前进行一次上下文切换
 * @return pdPass        超时前数据成功入队
 *         errQUEUE_FULL 队列已满                
 */
portBASE_TYPE xQueueSendToFrontFromISR(xQueueHandle xQueue, void * pvItemToQueue, portBASE_TYPE *pxHigherPriorityTaskWoken);
portBASE_TYPE xQueueSendToBackFromISE(xQueueHandle xQueue, void * pvItemToQueue, portBASE_TYPE *pxHigherPriorityTaskWoken);

/*
 * @return pdPASS         读取成功
 *         errQUEUE_EMPTY 队列已空，读取失败  
 */
portBASE_TYPE xQueueReceiveFromISR(
    xQueueHandle xQueue,       // 被读队列的句柄
    const void * pvBuffer,     // 接收缓存指针（指向的内存空间等于数据单元大小）
    portTickType xTicksToWait  // 阻塞超时时间
);
```

代码示例见 FreeRTOS 实时内核使用指南 P89

## 资源管理

### 临界区与挂起

**基本临界区**

临界区会关掉中断（关掉优先级低于 `configMAX_INTERRUPT_PRIORITY` 的中断）在 `ENTER` 与 `EXIT` 之间执行时不会切换到其它任务

```c
taskENTER_CRITICAL();
/* Critical Sections/Regions */

taskEXIT_CRITICAL();
```

**挂起**

如果一个临界区太长，不适合简单地关中断实现，可以考虑使用挂起调度器。

挂起可以停止上下文切换而不用关中断，如果某个中断在调度器挂起过程中要求上下文切换，则这个请求也会被挂起，直到调度器被重新唤醒。

```c
void vTaskSuspendAll(void);  // 挂起调度器

/*
 * @return 如果一个挂起的上下文切换请求在xTaskResumeAll()返回前执行，返回pdTRUE，否则返回pdFALSE
 */
portBASE_TYPE xTaskResumeAll(void);
```

### 互斥量

> *感觉类似于多线程编程中的互斥锁*
>
> 互斥量是一种特殊的二值信号量，单词 MUTEX 来源与 "MUTual EXclusion"
>
> - 用于互斥：用后必须归还；
> - 用于同步：完成同步后丢弃；

<img src="https://cdn.jsdelivr.net/gh/Euler0525/tube@master/it/rtos_mutex.webp" alt="rtos_mutex" width="90%" height="90%" />

<center> <small> Mutual exclusion implemented using a mutex </small> </center>

`xSemaphoreCreateMutex` 函数

```c
xSemaphoreHandle xSemaphoreCreateMutex(void); // 创建成功则返回互斥量的句柄，否则返回NULL
```

代码示例见 FreeRTOS 实时内核使用指南 P108~111.

### 守护任务

> **优先级反转**
>
> 低优先级任务拥有互斥量的时候，优先级会被临时提高，提高到和高优先级任务相同的优先级。如果另一个高优先级任务也想获取这个信号量，必须要等待互斥量被释放，否则高优先级任务将不能获取这个互斥量，并且那个拥有互斥量的低优先级任务也永远不会被剥夺。

为了避免优先级反转和死锁，规定守护任务是对某个资源具有唯一所有权的任务。

只有守护任务才可以直接访问其守护的资源，并且其它任务要访问时只能间接通过守护任务提供服务。

## 内存管理

> 标准的 `malloc()` 函数和 `free()` 函数可能会有以下问题：
>
> - 在小型嵌入式系统中不可用；
> - 具体实现较大，占用代码空间；
> - 不具备线程安全特性；
> - 每次调用的时间开销可能不同；
> - 可能会产生内存碎片；
> - 使链接器配置复杂；

当内核请求内存时，调用 `pvPortMalloc()` 函数而不是 `malloc()` 函数；当释放内存时，调用 `vPortFree()` 函数而不是直接调用 `free()` 函数。

代码详见 `FreeRTOS\Source\portable\MemMang\heap_1,2,3.c`，FreeRTOS 实时内核使用指南 P123~126.

## 参考资料

- FreeRTOS 实时内核使用指南
- Mastering the FreeRTOS™ Real Time Kernel

- [qq_61672347 的博客—FreeRTOS](https://blog.csdn.net/qq_61672347/category_11891132.html)
- [朱工的专栏—FreeRTOS 高级篇](https://blog.csdn.net/zhzht19861011/category_9265965.html?spm=1001.2014.3001.5482)
