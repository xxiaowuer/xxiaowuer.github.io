---
title: FreeRTOS Tutorial
date: 2024-04-11 15:03:41 +0800
categories: [CS, OS]
tags: [freertos, tutorial]    # TAG names should always be **lowercase**
---

## 说明

本文档主要参考正点原子的公开教程，以及FreeRTOS源码

## FreeRTOS配置文件详解

相关的宏大致可以分为三类

`INCLUDE`：配置FreeRTOS中可选的API函数

`config`：完成FreeRTOS的功能配置和裁剪

其它配置项：`PendSV宏定义、SVC宏定义`

正点原子提供的配置文件示例：

``` c
extern uint32_t SystemCoreClock;

/* 基础配置项 */
#define configUSE_PREEMPTION                            1                       /* 1: 抢占式调度器, 0: 协程式调度器, 无默认需定义 */
#define configUSE_PORT_OPTIMISED_TASK_SELECTION         1                       /* 1: 使用硬件计算下一个要运行的任务, 0: 使用软件算法计算下一个要运行的任务, 默认: 0 */
#define configUSE_TICKLESS_IDLE                         0                       /* 1: 使能tickless低功耗模式, 默认: 0 */
#define configCPU_CLOCK_HZ                              SystemCoreClock         /* 定义CPU主频, 单位: Hz, 无默认需定义 */
//#define configSYSTICK_CLOCK_HZ                          (configCPU_CLOCK_HZ / 8)/* 定义SysTick时钟频率，当SysTick时钟频率与内核时钟频率不同时才可以定义, 单位: Hz, 默认: 不定义 */
#define configTICK_RATE_HZ                              1000                    /* 定义系统时钟节拍频率, 单位: Hz, 无默认需定义 */
#define configMAX_PRIORITIES                            32                      /* 定义最大优先级数, 最大优先级=configMAX_PRIORITIES-1, 无默认需定义 */
#define configMINIMAL_STACK_SIZE                        128                     /* 定义空闲任务的栈空间大小, 单位: Word, 无默认需定义 */
#define configMAX_TASK_NAME_LEN                         16                      /* 定义任务名最大字符数, 默认: 16 */
#define configUSE_16_BIT_TICKS                          0                       /* 1: 定义系统时钟节拍计数器的数据类型为16位无符号数, 无默认需定义 */
#define configIDLE_SHOULD_YIELD                         1                       /* 1: 使能在抢占式调度下,同优先级的任务能抢占空闲任务, 默认: 1 */
#define configUSE_TASK_NOTIFICATIONS                    1                       /* 1: 使能任务间直接的消息传递,包括信号量、事件标志组和消息邮箱, 默认: 1 */
#define configTASK_NOTIFICATION_ARRAY_ENTRIES           1                       /* 定义任务通知数组的大小, 默认: 1 */
#define configUSE_MUTEXES                               1                       /* 1: 使能互斥信号量, 默认: 0 */
#define configUSE_RECURSIVE_MUTEXES                     1                       /* 1: 使能递归互斥信号量, 默认: 0 */
#define configUSE_COUNTING_SEMAPHORES                   1                       /* 1: 使能计数信号量, 默认: 0 */
#define configUSE_ALTERNATIVE_API                       0                       /* 已弃用!!! */
#define configQUEUE_REGISTRY_SIZE                       8                       /* 定义可以注册的信号量和消息队列的个数, 默认: 0 */
#define configUSE_QUEUE_SETS                            1                       /* 1: 使能队列集, 默认: 0 */
#define configUSE_TIME_SLICING                          1                       /* 1: 使能时间片调度, 默认: 1 */
#define configUSE_NEWLIB_REENTRANT                      0                       /* 1: 任务创建时分配Newlib的重入结构体, 默认: 0 */
#define configENABLE_BACKWARD_COMPATIBILITY             0                       /* 1: 使能兼容老版本, 默认: 1 */
#define configNUM_THREAD_LOCAL_STORAGE_POINTERS         0                       /* 定义线程本地存储指针的个数, 默认: 0 */
#define configSTACK_DEPTH_TYPE                          uint16_t                /* 定义任务堆栈深度的数据类型, 默认: uint16_t */
#define configMESSAGE_BUFFER_LENGTH_TYPE                size_t                  /* 定义消息缓冲区中消息长度的数据类型, 默认: size_t */

/* 内存分配相关定义 */
#define configSUPPORT_STATIC_ALLOCATION                 0                       /* 1: 支持静态申请内存, 默认: 0 */
#define configSUPPORT_DYNAMIC_ALLOCATION                1                       /* 1: 支持动态申请内存, 默认: 1 */
#define configTOTAL_HEAP_SIZE                           ((size_t)(10 * 1024))   /* FreeRTOS堆中可用的RAM总量, 单位: Byte, 无默认需定义 */
#define configAPPLICATION_ALLOCATED_HEAP                0                       /* 1: 用户手动分配FreeRTOS内存堆(ucHeap), 默认: 0 */
#define configSTACK_ALLOCATION_FROM_SEPARATE_HEAP       0                       /* 1: 用户自行实现任务创建时使用的内存申请与释放函数, 默认: 0 */

/* 钩子函数相关定义 */
#define configUSE_IDLE_HOOK                             0                       /* 1: 使能空闲任务钩子函数, 无默认需定义  */
#define configUSE_TICK_HOOK                             0                       /* 1: 使能系统时钟节拍中断钩子函数, 无默认需定义 */
#define configCHECK_FOR_STACK_OVERFLOW                  0                       /* 1: 使能栈溢出检测方法1, 2: 使能栈溢出检测方法2, 默认: 0 */
#define configUSE_MALLOC_FAILED_HOOK                    0                       /* 1: 使能动态内存申请失败钩子函数, 默认: 0 */
#define configUSE_DAEMON_TASK_STARTUP_HOOK              0                       /* 1: 使能定时器服务任务首次执行前的钩子函数, 默认: 0 */

/* 运行时间和任务状态统计相关定义 */
#define configGENERATE_RUN_TIME_STATS                   0                       /* 1: 使能任务运行时间统计功能, 默认: 0 */
#if configGENERATE_RUN_TIME_STATS
#include "./BSP/TIMER/btim.h"
#define portCONFIGURE_TIMER_FOR_RUN_TIME_STATS()        ConfigureTimeForRunTimeStats()
extern uint32_t FreeRTOSRunTimeTicks;
#define portGET_RUN_TIME_COUNTER_VALUE()                FreeRTOSRunTimeTicks
#endif
#define configUSE_TRACE_FACILITY                        1                       /* 1: 使能可视化跟踪调试, 默认: 0 */
#define configUSE_STATS_FORMATTING_FUNCTIONS            1                       /* 1: configUSE_TRACE_FACILITY为1时，会编译vTaskList()和vTaskGetRunTimeStats()函数, 默认: 0 */

/* 协程相关定义 */
#define configUSE_CO_ROUTINES                           0                       /* 1: 启用协程, 默认: 0 */
#define configMAX_CO_ROUTINE_PRIORITIES                 2                       /* 定义协程的最大优先级, 最大优先级=configMAX_CO_ROUTINE_PRIORITIES-1, 无默认configUSE_CO_ROUTINES为1时需定义 */

/* 软件定时器相关定义 */
#define configUSE_TIMERS                                1                               /* 1: 使能软件定时器, 默认: 0 */
#define configTIMER_TASK_PRIORITY                       ( configMAX_PRIORITIES - 1 )    /* 定义软件定时器任务的优先级, 无默认configUSE_TIMERS为1时需定义 */
#define configTIMER_QUEUE_LENGTH                        5                               /* 定义软件定时器命令队列的长度, 无默认configUSE_TIMERS为1时需定义 */
#define configTIMER_TASK_STACK_DEPTH                    ( configMINIMAL_STACK_SIZE * 2) /* 定义软件定时器任务的栈空间大小, 无默认configUSE_TIMERS为1时需定义 */

/* 可选函数, 1: 使能 */
#define INCLUDE_vTaskPrioritySet                        1                       /* 设置任务优先级 */
#define INCLUDE_uxTaskPriorityGet                       1                       /* 获取任务优先级 */
#define INCLUDE_vTaskDelete                             1                       /* 删除任务 */
#define INCLUDE_vTaskSuspend                            1                       /* 挂起任务 */
#define INCLUDE_xResumeFromISR                          1                       /* 恢复在中断中挂起的任务 */
#define INCLUDE_vTaskDelayUntil                         1                       /* 任务绝对延时 */
#define INCLUDE_vTaskDelay                              1                       /* 任务延时 */
#define INCLUDE_xTaskGetSchedulerState                  1                       /* 获取任务调度器状态 */
#define INCLUDE_xTaskGetCurrentTaskHandle               1                       /* 获取当前任务的任务句柄 */
#define INCLUDE_uxTaskGetStackHighWaterMark             1                       /* 获取任务堆栈历史剩余最小值 */
#define INCLUDE_xTaskGetIdleTaskHandle                  1                       /* 获取空闲任务的任务句柄 */
#define INCLUDE_eTaskGetState                           1                       /* 获取任务状态 */
#define INCLUDE_xEventGroupSetBitFromISR                1                       /* 在中断中设置事件标志位 */
#define INCLUDE_xTimerPendFunctionCall                  1                       /* 将函数的执行挂到定时器服务任务 */
#define INCLUDE_xTaskAbortDelay                         1                       /* 中断任务延时 */
#define INCLUDE_xTaskGetHandle                          1                       /* 通过任务名获取任务句柄 */
#define INCLUDE_xTaskResumeFromISR                      1                       /* 恢复在中断中挂起的任务 */

/* 中断嵌套行为配置 */
#ifdef __NVIC_PRIO_BITS
    #define configPRIO_BITS __NVIC_PRIO_BITS
#else
    #define configPRIO_BITS 4
#endif

#define configLIBRARY_LOWEST_INTERRUPT_PRIORITY         15                  /* 中断最低优先级 */
#define configLIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY    5                   /* FreeRTOS可管理的最高中断优先级 */
#define configKERNEL_INTERRUPT_PRIORITY                 ( configLIBRARY_LOWEST_INTERRUPT_PRIORITY << (8 - configPRIO_BITS) )
#define configMAX_SYSCALL_INTERRUPT_PRIORITY            ( configLIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY << (8 - configPRIO_BITS) )
#define configMAX_API_CALL_INTERRUPT_PRIORITY           configMAX_SYSCALL_INTERRUPT_PRIORITY

/* FreeRTOS中断服务函数相关定义 */
#define xPortPendSVHandler                              PendSV_Handler
#define vPortSVCHandler                                 SVC_Handler

/* 断言 */
#define vAssertCalled(char, int) printf("Error: %s, %d\r\n", char, int)
#define configASSERT( x ) if( ( x ) == 0 ) vAssertCalled( __FILE__, __LINE__ )
```

## 基本变量类型

```c
/* Type definitions. */
#define portCHAR		char
#define portFLOAT		float
#define portDOUBLE		double
#define portLONG		long
#define portSHORT		short
#define portSTACK_TYPE	uint32_t
#define portBASE_TYPE	long

typedef portSTACK_TYPE StackType_t;
typedef long BaseType_t;
typedef unsigned long UBaseType_t;


#if( configUSE_16_BIT_TICKS == 1 )
	typedef uint16_t TickType_t;
	#define portMAX_DELAY ( TickType_t ) 0xffff
#else
	typedef uint32_t TickType_t;
	#define portMAX_DELAY ( TickType_t ) 0xffffffffUL
#endif


#define pdFALSE                                  ( ( BaseType_t ) 0 )
#define pdTRUE                                   ( ( BaseType_t ) 1 )

#define pdPASS                                   ( pdTRUE )
#define pdFAIL                                   ( pdFALSE )
#define errQUEUE_EMPTY                           ( ( BaseType_t ) 0 )
#define errQUEUE_FULL                            ( ( BaseType_t ) 0 )

/* FreeRTOS error definitions. */
#define errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY    ( -1 )
#define errQUEUE_BLOCKED                         ( -4 )
#define errQUEUE_YIELD                           ( -5 )
```

## 任务创建和删除

### 总述：任务创建和删除相关API函数

`xTaskCreate()`-动态方式创建任务：任务的任务控制块以及任务的栈空间所需的内存，均由 FreeRTOS 从 FreeRTOS 管理的堆中分配 ；

`xTaskCreateStatic()`-静态方式创建任务：任务的任务控制块以及任务的栈空间所需的内存，需用户分配提供；

`vTaskDelete()`-删除任务：

### 动态创建任务

#### 函数签名

```c
BaseType_t xTaskCreate ( 	
    TaskFunction_t 						pxTaskCode,		/* 指向任务函数的指针 */				
 	const char * const 					pcName, 		/* 任务名字，最大长度configMAX_TASK_NAME_LEN */
	const 	configSTACK_DEPTH_TYPE 		usStackDepth, 	/* 任务堆栈大小，注意字为单位 */
	void * const 						pvParameters,	/* 传递给任务函数的参数 */
	UBaseType_t 						uxPriority,		/* 任务优先级，范围：0 ~ configMAX_PRIORITIES - 1 */
	TaskHandle_t * const 				pxCreatedTask 	/* 任务句柄，就是任务的任务控制块 */
)

// 返回值
// pdPASS:任务创建成功
// errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY:任务创建失败
```

此函数创建的任务会**立刻进入就绪态**，由任务调度器调度运行

#### 动态任务创建流程

使用只需三步：

1. 将宏`configSUPPORT_DYNAMIC_ALLOCATION` 配置为 1 
2. 定义函数入口参数
3. 编写任务函数

#### 动态创建任务函数内部实现

1. 申请堆栈内存&任务控制块内存
2. TCB结构体成员赋值
3. 添加新任务到就绪列表中

**深入探究：**

##### 任务控制块结构体

注意：每个任务都有属于自己的任务控制块，类似身份证

任务栈栈顶，在任务切换时的任务上下文保存、任务恢复息息相关

```c
typedef struct tskTaskControlBlock       
{
    	volatile StackType_t 		*pxTopOfStack; 								/* 任务栈栈顶，必须为TCB的第一个成员 */
   		ListItem_t 					xStateListItem;           					/* 任务状态列表项 */      
		ListItem_t 					xEventListItem;								/* 任务事件列表项 */     
    	UBaseType_t 				uxPriority;                					/* 任务优先级，数值越大，优先级越大 */
    	StackType_t 				*pxStack;									/* 任务栈起始地址 */
    	char 						pcTaskName[ configMAX_TASK_NAME_LEN ]; 		/* 任务名字 */		
	// ...
	// 省略很多条件编译的成员
} tskTCB;

/* The old tskTCB name is maintained above then typedefed to the new TCB_t name
 * below to enable the use of older kernel aware debuggers. */
typedef tskTCB TCB_t;
```

#### 使用示例

``` c
#include "FreeRTOS.h"
#include "task.h"

/************************************************************/
/* START_TASK 任务 配置
 * 包括: 任务句柄 任务优先级 堆栈大小 创建任务
 */
#define START_TASK_PRIO         1
#define START_TASK_STACK_SIZE   128
TaskHandle_t    start_task_handler;
void start_task( void * pvParameters );

/* TASK1 任务 配置
 * 包括: 任务句柄 任务优先级 堆栈大小 创建任务
 */
#define TASK1_PRIO         2
#define TASK1_STACK_SIZE   128
TaskHandle_t    task1_handler;
void task1( void * pvParameters );

/* TASK2 任务 配置
 * 包括: 任务句柄 任务优先级 堆栈大小 创建任务
 */
#define TASK2_PRIO         3
#define TASK2_STACK_SIZE   128
TaskHandle_t    task2_handler;
void task2( void * pvParameters );

/* TASK3 任务 配置
 * 包括: 任务句柄 任务优先级 堆栈大小 创建任务
 */
#define TASK3_PRIO         4
#define TASK3_STACK_SIZE   128
TaskHandle_t    task3_handler;
void task3( void * pvParameters );
/************************************************************/

int main( void ) {
    // ...
    // 使能相关硬件
    //-----------创建任务-----------------
    
    // 创建一个初始任务，由该任务创建其它任务，并删除自己
    xTaskCreate( start_task, "start_task", START_TASK_STACK_SIZE, NULL, 1, NULL );
    
    // 开启FreeRTOS的调度器，开始执行任务
    vTaskStartScheduler();

    return 0;
}

void start_task( void * pvParameters )
{
    taskENTER_CRITICAL();               /* 进入临界区 */
    xTaskCreate((TaskFunction_t         )   task1,
                (char *                 )   "task1",
                (configSTACK_DEPTH_TYPE )   TASK1_STACK_SIZE,
                (void *                 )   NULL,
                (UBaseType_t            )   TASK1_PRIO,
                (TaskHandle_t *         )   &task1_handler );
                
    xTaskCreate((TaskFunction_t         )   task2,
                (char *                 )   "task2",
                (configSTACK_DEPTH_TYPE )   TASK2_STACK_SIZE,
                (void *                 )   NULL,
                (UBaseType_t            )   TASK2_PRIO,
                (TaskHandle_t *         )   &task2_handler );
                
    xTaskCreate((TaskFunction_t         )   task3,
                (char *                 )   "task3",
                (configSTACK_DEPTH_TYPE )   TASK3_STACK_SIZE,
                (void *                 )   NULL,
                (UBaseType_t            )   TASK3_PRIO,
                (TaskHandle_t *         )   &task3_handler );
    vTaskDelete(NULL);
    taskEXIT_CRITICAL();                /* 退出临界区 */
}

/* 任务一 */
void task1( void * pvParameters )
{
    while(1)
    {
        printf("task1正在运行！！！\r\n");

        vTaskDelay(500);
    }
}

/* 任务二 */
void task2( void * pvParameters )
{
    while(1)
    {
        printf("task2正在运行！！！\r\n");
        vTaskDelay(500);
    }
}

/* 任务三，删除task1 */
void task3( void * pvParameters )
{
    while(1)
    {
        printf("task3正在运行！！！\r\n");
        if (task1_handler != NULL) {
            printf("删除task1\r\n");
            vTaskDelete(task1_handler);
            task1_handler = NULL;
        }
        vTaskDelay(10);
    }
}

```

**执行结果**

如果**不加临界区**，结果为先执行task1然后task2然后task3

这是因为创建start_task任务后，开启了调度器，此时执行start_task，它先创建了task1,task1的优先级比start_task高，所以在start_task创建task2之前就抢占了cpu运行task1，在task1阻塞期间让出了cpu回到start_task继续创建task2，后面的过程相似。

如果**加了临界区**，则依次创建三个任务，退出临界区之后，从优先级最高的任务开始执行

#### 函数源码

```c
#if ( configSUPPORT_DYNAMIC_ALLOCATION == 1 )

    BaseType_t xTaskCreate( TaskFunction_t pxTaskCode,
                            const char * const pcName, /*lint !e971 Unqualified char types are allowed for strings and single characters only. */
                            const configSTACK_DEPTH_TYPE usStackDepth,
                            void * const pvParameters,
                            UBaseType_t uxPriority,
                            TaskHandle_t * const pxCreatedTask )
    {
        TCB_t * pxNewTCB;
        BaseType_t xReturn;

        /* If the stack grows down then allocate the stack then the TCB so the stack
         * does not grow into the TCB.  Likewise if the stack grows up then allocate
         * the TCB then the stack. */
        // 这里是根据栈空间增长方式确定TCB的位置，确保不会因为栈增长而破坏PCB
        #if ( portSTACK_GROWTH > 0 )
            {
                /* Allocate space for the TCB.  Where the memory comes from depends on
                 * the implementation of the port malloc function and whether or not static
                 * allocation is being used. */
                pxNewTCB = ( TCB_t * ) pvPortMalloc( sizeof( TCB_t ) );

                if( pxNewTCB != NULL )
                {
                    /* Allocate space for the stack used by the task being created.
                     * The base of the stack memory stored in the TCB so the task can
                     * be deleted later if required. */
                    pxNewTCB->pxStack = ( StackType_t * ) pvPortMallocStack( ( ( ( size_t ) usStackDepth ) * sizeof( StackType_t ) ) ); /*lint !e961 MISRA exception as the casts are only redundant for some ports. */

                    if( pxNewTCB->pxStack == NULL )
                    {
                        /* Could not allocate the stack.  Delete the allocated TCB. */
                        vPortFree( pxNewTCB );
                        pxNewTCB = NULL;
                    }
                }
            }
        #else /* portSTACK_GROWTH */
            {
                StackType_t * pxStack;

                /* Allocate space for the stack used by the task being created. */
                pxStack = pvPortMallocStack( ( ( ( size_t ) usStackDepth ) * sizeof( StackType_t ) ) ); /*lint !e9079 All values returned by pvPortMalloc() have at least the alignment required by the MCU's stack and this allocation is the stack. */

                if( pxStack != NULL )
                {
                    /* Allocate space for the TCB. */
                    pxNewTCB = ( TCB_t * ) pvPortMalloc( sizeof( TCB_t ) ); /*lint !e9087 !e9079 All values returned by pvPortMalloc() have at least the alignment required by the MCU's stack, and the first member of TCB_t is always a pointer to the task's stack. */

                    if( pxNewTCB != NULL )
                    {
                        /* Store the stack location in the TCB. */
                        pxNewTCB->pxStack = pxStack;
                    }
                    else
                    {
                        /* The stack cannot be used as the TCB was not created.  Free
                         * it again. */
                        vPortFreeStack( pxStack );
                    }
                }
                else
                {
                    pxNewTCB = NULL;
                }
            }
        #endif /* portSTACK_GROWTH */

        if( pxNewTCB != NULL )
        {
            #if ( tskSTATIC_AND_DYNAMIC_ALLOCATION_POSSIBLE != 0 ) /*lint !e9029 !e731 Macro has been consolidated for readability reasons. */
                {
                    /* Tasks can be created statically or dynamically, so note this
                     * task was created dynamically in case it is later deleted. */
                    pxNewTCB->ucStaticallyAllocated = tskDYNAMICALLY_ALLOCATED_STACK_AND_TCB;
                }
            #endif /* tskSTATIC_AND_DYNAMIC_ALLOCATION_POSSIBLE */

            prvInitialiseNewTask( pxTaskCode, pcName, ( uint32_t ) usStackDepth, pvParameters, uxPriority, pxCreatedTask, pxNewTCB, NULL );
            prvAddNewTaskToReadyList( pxNewTCB );
            xReturn = pdPASS;
        }
        else
        {
            xReturn = errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY;
        }

        return xReturn;
    }

#endif /* configSUPPORT_DYNAMIC_ALLOCATION */
```

**详解**

* 首先定义这个宏定义`#dedfine configSUPPORT_DYNAMIC_ALLOCATION 1`

* 创建了任务控制块`TCB_t * pxNewTCB`和返回值`BaseType_t xReturn`

* 判断栈空间增长方式确定TCB的位置，确保不会因为栈增长而破坏PCB，两种情况TCB和栈空间创建顺序相反，以常用的栈向下生长为例
  *   创建**堆栈指针**`StackType_t * pxStack`，实际上这个指针指向的空间是**堆空间**，然后从这里面选一块作为**栈空间**，所以这个指针为**堆空间的起始地址**
  * 通过`pvPortMallocStack()`函数分配栈空间，注意此函数接受的参数单位为**字节**，而动态创建函数传入的栈空间大小单位为**字**，所以要×4：`( ( size_t ) usStackDepth ) * sizeof( StackType_t )`
  * 栈空间分配成功，开始创建任务控制块TCB，分配相关内存给TCB，将栈顶指针赋值给TCB
  
* 然后通过`prvInitialiseNewTask()`函数初始化任务控制块其它成员
  * 根据堆栈指针将堆栈空间设置为全部写为`a5`，即写入脏数据
  * 将堆栈空间的最高位地址做8字节对齐，然后赋值给TCB，作为堆栈空间中**栈空间**的栈顶地址
  * 将传入的任务名称赋值给TCB的`pcTaskName`成员，名称有字符数量限制；如果没有名称则将该变量置`NULL`
  * 检查传入的任务优先级是否合法，在0~31，然后赋值给`uxPriority`变量
  * 通过`vListInitialiseItem()`初始化任务的状态列表项，`vListInitialiseItem()`函数初始化任务的事件列表项
  * 将状态列表项归属为TCB，设置事件列表项的优先级（32 - 任务优先级），并将该事件列表项归属为TCB
  * 调用`pxPortInitialiseStack()`函数初始化栈空间内容，通过向下调节栈顶指针的地址，依次写入：`xPSR、PC、R12、R3、R2、R1、R0、R11、R4`寄存器的值，然后将新的栈顶指针返回给栈顶指针；其中`pc`寄存器中存储的是该任务的**任务函数指针**
  * 将初始化后的**TCB**通过强转赋值给**任务句柄**：`*pxCreatedTask = ( TaskHandle_t ) pxNewTCB;`
  
* 通过`prvAddNewTaskToReadyList()`函数将该任务添加到就绪链表中
  
  > ``` c
  > static void prvAddNewTaskToReadyList( TCB_t * pxNewTCB )
  > {
  >     /* Ensure interrupts don't access the task lists while the lists are being
  >      * updated. */
  >     // 首先进入临界区
  >     taskENTER_CRITICAL();
  >     {
  >         uxCurrentNumberOfTasks++;	// 任务数量+1
  > 		
  >         // 判断是不是第一个任务：pxCurrentTCB指向优先级最高的任务，若无NULL则表示第一个任务
  >         if( pxCurrentTCB == NULL )
  >         {
  >             /* There are no other tasks, or all the other tasks are in
  >              * the suspended state - make this the current task. */
  >             pxCurrentTCB = pxNewTCB;
  > 
  >             if( uxCurrentNumberOfTasks == ( UBaseType_t ) 1 )
  >             {
  >                 /* This is the first task to be created so do the preliminary
  >                  * initialisation required.  We will not recover if this call
  >                  * fails, but we will report the failure. */
  >                 prvInitialiseTaskLists();
  >             }
  >             else
  >             {
  >                 mtCOVERAGE_TEST_MARKER();
  >             }
  >         }
  >         else
  >         {
  >             /* If the scheduler is not already running, make this task the
  >              * current task if it is the highest priority task to be created
  >              * so far. */
  >             if( xSchedulerRunning == pdFALSE )
  >             {
  >                 if( pxCurrentTCB->uxPriority <= pxNewTCB->uxPriority )
  >                 {
  >                     pxCurrentTCB = pxNewTCB;
  >                 }
  >                 else
  >                 {
  >                     mtCOVERAGE_TEST_MARKER();
  >                 }
  >             }
  >             else
  >             {
  >                 mtCOVERAGE_TEST_MARKER();
  >             }
  >         }
  > 
  >         uxTaskNumber++;
  > 
  >         #if ( configUSE_TRACE_FACILITY == 1 )
  >         {
  >             /* Add a counter into the TCB for tracing only. */
  >             pxNewTCB->uxTCBNumber = uxTaskNumber;
  >         }
  >         #endif /* configUSE_TRACE_FACILITY */
  >         traceTASK_CREATE( pxNewTCB );
  > 
  >         prvAddTaskToReadyList( pxNewTCB );
  > 
  >         portSETUP_TCB( pxNewTCB );
  >     }
  >     taskEXIT_CRITICAL();
  > 
  >     if( xSchedulerRunning != pdFALSE )
  >     {
  >         /* If the created task is of a higher priority than the current task
  >          * then it should run now. */
  >         if( pxCurrentTCB->uxPriority < pxNewTCB->uxPriority )
  >         {
  >             taskYIELD_IF_USING_PREEMPTION();
  >         }
  >         else
  >         {
  >             mtCOVERAGE_TEST_MARKER();
  >         }
  >     }
  >     else
  >     {
  >         mtCOVERAGE_TEST_MARKER();
  >     }
  > }
  > ```
  
  * 首先`taskENTER_CRITICAL()`进入临界区
  
  * `uxCurrentNumberOfTasks++;`任务数量+1
  
  * 判断是否为第一个任务，若为第一个则将任务控制块赋值给`pxCurrentTCB`，通过`prvInitialiseTaskLists()`函数初始化任务列表
    * 首先初始化**就绪列表**（0~31，共有32个），初始化**两个延时列表**，初始化**等待就绪列表**（当需要挂起所有任务时才会使用）；如果使能了删除任务，则会初始化删除任务列表，若使能了任务挂起，则会初始化任务挂起列表
  
      > ``` c
      > static void prvInitialiseTaskLists( void )
      > {
      >     UBaseType_t uxPriority;
      > 
      >     for( uxPriority = ( UBaseType_t ) 0U; uxPriority < ( UBaseType_t ) configMAX_PRIORITIES; uxPriority++ )
      >     {
      >         vListInitialise( &( pxReadyTasksLists[ uxPriority ] ) ); // 初始化就绪列表，最大是32个
      >     }
      > 	
      >     // 初始化两个延时列表
      >     vListInitialise( &xDelayedTaskList1 );
      >     vListInitialise( &xDelayedTaskList2 );
      >     // 初始化**等待就绪列表**
      >     vListInitialise( &xPendingReadyList );
      > 
      >     #if ( INCLUDE_vTaskDelete == 1 )
      >     {
      >         // 若使能删除任务则初始化等待删除列表
      >         vListInitialise( &xTasksWaitingTermination );
      >     }
      >     #endif /* INCLUDE_vTaskDelete */
      > 
      >     #if ( INCLUDE_vTaskSuspend == 1 )
      >     {
      >         // 若使能了任务挂起，则初始化挂起列表
      >         vListInitialise( &xSuspendedTaskList );
      >     }
      >     #endif /* INCLUDE_vTaskSuspend */
      > 
      >     /* Start with pxDelayedTaskList using list1 and the pxOverflowDelayedTaskList
      >      * using list2. */
      >     pxDelayedTaskList = &xDelayedTaskList1;
      >     pxOverflowDelayedTaskList = &xDelayedTaskList2;
      > }
      > ```
  
  * 若不是第一个任务，首先判断**调度器**是否已经启动，若未启动调度器，则会判断当前运行的任务优先级是否**小于等于**新创建任务的优先级，若成立，则将新创建的任务控制块赋值给当前任务控制块。
  
  * 然后将新任务添加到就绪列表
  
  * 退出临界区
  
  * 若已开启任务调度器，会判断当前运行的任务优先级是否**小于**新创建任务的优先级，若成立，则会执行**任务切换**
  
* 结束，返回

### 静态创建任务

#### 函数签名

此函数创建的任务会立刻进入就绪态，由任务调度器调度运行

``` c
TaskHandle_t xTaskCreateStatic( 
		TaskFunction_t			pxTaskCode,				/* 指向任务函数的指针 */
    	const char * const		pcName,					/* 任务函数名 */
    	const uint32_t			ulStackDepth, 			/* 任务堆栈大小注意 字 为单位 */
    	void * const			pvParameters, 			/* 传递的任务函数参数 */
    	UBaseType_t				uxPriority, 			/* 任务优先级 */
    	StackType_t * const		puxStackBuffer, 		/* 任务堆栈，一般为数组，由用户分配 */
    	StaticTask_t * const	pxTaskBuffer			/* 任务控制块指针，由用户分配 */
) {}

// 返回值
// NULL:失败
// 其它值：任务句柄，任务创建成功
```

> 注意：双字（double world）、字（world）、字节（byte）、半字（half-world）区别
>
> 首先字的长度在不同硬件上是不一样的，大小取决于**数据总线**是多少位的，一般为16、32、64位（bit）
>
> 字节的长度为8位（bit）
>
> 半字的长度为字的一半
>
> 所以在ARM中：
>
> 16位系统：16bit = 1 world = 2 half-world = 2 byte = 0.5 double world
>
> 32位系统：32bit = 1 world = 2 half-world = 4 byte = 0.5 double world
>
> 64为系统：64bit = 1 world = 2 half-world = 8 byte = 0.5 double world

#### 静态任务创建流程

使用只需5步

* 需将宏`configSUPPORT_STATIC_ALLOCATION `配置为 1 
* 定义空闲任务&定时器任务的任务堆栈及TCB
* 手动实现两个接口函数：空闲任务内存分配：`vApplicationGetIdleTaskMemory( ) `&软件定时器任务内存分配：`vApplicationGetTimerTaskMemory ( )`
* 定义函数入口参数
* 编写任务函数

#### 静态创建内部实现

* TCB结构体成员赋值（静态和动态任务创建的任务控制块内容是相似的，可以一一对应）
* 添加新任务到就绪列表中

#### 函数源码

``` c
typedef struct tskTaskControlBlock * TaskHandle_t;


#if ( configSUPPORT_STATIC_ALLOCATION == 1 )

    TaskHandle_t xTaskCreateStatic( TaskFunction_t pxTaskCode,
                                    const char * const pcName, /*lint !e971 Unqualified char types are allowed for strings and single characters only. */
                                    const uint32_t ulStackDepth,
                                    void * const pvParameters,
                                    UBaseType_t uxPriority,
                                    StackType_t * const puxStackBuffer,
                                    StaticTask_t * const pxTaskBuffer )
    {
        TCB_t * pxNewTCB;
        TaskHandle_t xReturn;

        configASSERT( puxStackBuffer != NULL );
        configASSERT( pxTaskBuffer != NULL );

        #if ( configASSERT_DEFINED == 1 )
            {
                /* Sanity check that the size of the structure used to declare a
                 * variable of type StaticTask_t equals the size of the real task
                 * structure. */
                volatile size_t xSize = sizeof( StaticTask_t );
                configASSERT( xSize == sizeof( TCB_t ) );
                ( void ) xSize; /* Prevent lint warning when configASSERT() is not used. */
            }
        #endif /* configASSERT_DEFINED */
		
        // 检测是否分配堆栈内存，未分配跳过返回NULL表示失败
        if( ( pxTaskBuffer != NULL ) && ( puxStackBuffer != NULL ) )
        {
            /* The memory used for the task's TCB and stack are passed into this
             * function - use them. */
            // 获取任务控制块和堆栈内存地址
            pxNewTCB = ( TCB_t * ) pxTaskBuffer; /*lint !e740 !e9087 Unusual cast is ok as the structures are designed to have the same alignment, and the size is checked by an assert. */
            pxNewTCB->pxStack = ( StackType_t * ) puxStackBuffer;

            #if ( tskSTATIC_AND_DYNAMIC_ALLOCATION_POSSIBLE != 0 ) /*lint !e731 !e9029 Macro has been consolidated for readability reasons. */
                {
                    /* Tasks can be created statically or dynamically, so note this
                     * task was created statically in case the task is later deleted. */
                    pxNewTCB->ucStaticallyAllocated = tskSTATICALLY_ALLOCATED_STACK_AND_TCB;
                }
            #endif /* tskSTATIC_AND_DYNAMIC_ALLOCATION_POSSIBLE */
			
            // 给任务控制块赋值
            prvInitialiseNewTask( pxTaskCode, pcName, ulStackDepth, pvParameters, uxPriority, &xReturn, pxNewTCB, NULL );
            // 初始化就绪列表
            prvAddNewTaskToReadyList( pxNewTCB );
        }
        else
        {
            xReturn = NULL;
        }

        return xReturn;
    }

#endif /* SUPPORT_STATIC_ALLOCATION */
```

**详细流程**

* 获取控制块内存（首地址）
* 获取堆栈内存（首地址）
* 标记使用的静态的方式申请的TCB和堆栈内存
* 调用`prvInitialiseNewTask() `初始化任务块，并将控制块信息返回给任务句柄，以便后续返回句柄信息
* 调用`prvAddNewTaskToReadyList() `添加新创建任务到就绪列表中

### 删除任务

用于删除已创建的任务，被删除的任务将从就绪态任务列表、阻塞态任务列表、挂起态任务列表和事件列表中移除

注意：

* 当传入的参数为NULL，则代表删除任务自身（当前正在运行的任务）
* 空闲任务会负责释放被删除任务中由系统分配的内存，但是由用户在任务删除前申请的内存， 则需要由用户在任务被删除前提前释放，否则将导致内存泄露 

#### 函数签名

``` c
void vTaskDelete( TaskHandle_t xTaskToDelete )
// 形参：xTaskToDelete - 待删除任务的任务句柄
```

#### 删除任务流程

使用只需2步：

* 使用删除任务函数，需将宏INCLUDE_vTaskDelete 配置为 1 
* 入口参数输入需要删除的任务句柄（NULL代表删除本身）

#### 删除任务内部实现

* 获取所要删除任务的控制块：通过传入的任务句柄，判断所需要删除哪个任务，NULL代表删除自身
* 将被删除任务，移除所在列表：将该任务在所在列表中移除，包括：就绪、阻塞、挂起、事件等列表
* 判断所需要删除的任务：删除任务自身，需先添加到等待删除列表，内存释放将在空闲任务执行；删除其他任务，释放内存，任务数量减1
* 更新下个任务的阻塞时间：更新下一个任务的阻塞超时时间，以防被删除的任务就是下一个阻塞超时的任务

#### 函数源码

``` c
#if ( INCLUDE_vTaskDelete == 1 )

    void vTaskDelete( TaskHandle_t xTaskToDelete )
    {
        TCB_t * pxTCB;

        taskENTER_CRITICAL();
        {
            /* If null is passed in here then it is the calling task that is
             * being deleted. */
            // 找到要删除任务的任务控制块
            pxTCB = prvGetTCBFromHandle( xTaskToDelete );
			
            // 在相关的状态列表中移除任务
            /* Remove task from the ready/delayed list. */
            if( uxListRemove( &( pxTCB->xStateListItem ) ) == ( UBaseType_t ) 0 )
            {
                // 判断对应优先级的就绪列表是否为0，若未空则清零对应的标志位（bit位）
                taskRESET_READY_PRIORITY( pxTCB->uxPriority );
            }
            else
            {
                mtCOVERAGE_TEST_MARKER();
            }
			
            // 判断是否有事件列表项在等待某件事
            /* Is the task waiting on an event also? */
            if( listLIST_ITEM_CONTAINER( &( pxTCB->xEventListItem ) ) != NULL )
            {
                // 若没有，则移除
                ( void ) uxListRemove( &( pxTCB->xEventListItem ) );
            }
            else
            {
                mtCOVERAGE_TEST_MARKER();
            }

            /* Increment the uxTaskNumber also so kernel aware debuggers can
             * detect that the task lists need re-generating.  This is done before
             * portPRE_TASK_DELETE_HOOK() as in the Windows port that macro will
             * not return. */
            uxTaskNumber++;
			
            // 若要删除的任务是当前任务
            if( pxTCB == pxCurrentTCB )
            {
                /* A task is deleting itself.  This cannot complete within the
                 * task itself, as a context switch to another task is required.
                 * Place the task in the termination list.  The idle task will
                 * check the termination list and free up any memory allocated by
                 * the scheduler for the TCB and stack of the deleted task. */
                // 不能直接删除，先将当前任务插入到等待删除列表中
                vListInsertEnd( &xTasksWaitingTermination, &( pxTCB->xStateListItem ) );

                /* Increment the ucTasksDeleted variable so the idle task knows
                 * there is a task that has been deleted and that it should therefore
                 * check the xTasksWaitingTermination list. */
                ++uxDeletedTasksWaitingCleanUp;

                /* Call the delete hook before portPRE_TASK_DELETE_HOOK() as
                 * portPRE_TASK_DELETE_HOOK() does not return in the Win32 port. */
                traceTASK_DELETE( pxTCB );

                /* The pre-delete hook is primarily for the Windows simulator,
                 * in which Windows specific clean up operations are performed,
                 * after which it is not possible to yield away from this task -
                 * hence xYieldPending is used to latch that a context switch is
                 * required. */
                portPRE_TASK_DELETE_HOOK( pxTCB, &xYieldPending );
            }
            else
            {	
                // 不是当前任务，直接删除
                --uxCurrentNumberOfTasks;
                traceTASK_DELETE( pxTCB );

                /* Reset the next expected unblock time in case it referred to
                 * the task that has just been deleted. */
                // 更新下一个任务的阻塞超时时间，防止被删除的任务就是下一个阻塞超时任务
                prvResetNextTaskUnblockTime();
            }
        }
        taskEXIT_CRITICAL();

        /* If the task is not deleting itself, call prvDeleteTCB from outside of
         * critical section. If a task deletes itself, prvDeleteTCB is called
         * from prvCheckTasksWaitingTermination which is called from Idle task. */
        if( pxTCB != pxCurrentTCB )
        {
            // 被删除的任务不是当前任务，则直接释放任务堆栈
            prvDeleteTCB( pxTCB );
        }

        /* Force a reschedule if it is the currently running task that has just
         * been deleted. */
        if( xSchedulerRunning != pdFALSE )
        {
            // 若开启了调度器，且删除的是任务自身，则先切换成其他任务，后续将会在 空闲任务 中将资源回收
            if( pxTCB == pxCurrentTCB )
            {
                configASSERT( uxSchedulerSuspended == 0 );
                portYIELD_WITHIN_API();
            }
            else
            {
                mtCOVERAGE_TEST_MARKER();
            }
        }
    }

#endif /* INCLUDE_vTaskDelete */
```



## 任务调度方式

1. 抢占式调度

​	主要针对**不同优先级**的任务，高优先级任务可以抢占低优先级的任务；

​	高优先级任务不停止，低优先级任务无法执行；

​	被抢占的任务会进入**就绪态。**

2. 时间片调度

​	针对**相同优先级**的任务，多个任务的优先级相同时，任务调度器会在每一次系统时钟节拍到达的时候切换任务，**一个时间片大小，取决为滴答定时器中断周期**。

​	注意没有用完的时间片不会再使用，下一次该任务得到执行还是按照一个时间片的时钟节拍运行

## 任务状态

1. 共有**四种状态**，除了运行态，其他三种任务状态的任务都有其对应的**任务状态列表**

   ![task state transition diagram](../images/2024-04-11-FreeRTOS Tutorial.assets/任务状态转换图.png)

* 运行态
  * 正在执行的任务，该任务就处于运行态，注意在STM32中，**同一时间仅一个任务处于运行态**
  * 只有就绪态的任务才能转为运行态，即任务必须先进入就绪列表中，才有可能会得到执行

* 就绪态
  * 如果该任务已经能够被执行，但当前还未被执行，那么该任务处于就绪态

* 阻塞态
  * 如果一个任务因延时或等待外部事件发生，那么这个任务就处于阻塞态

* 挂起态
  * 类似暂停，调用函数 `vTaskSuspend()` 进入挂起态，需要调用解挂函数`vTaskResume()`才可以进入就绪态

2. 

1. 1. 7. 

## 参考资料

1. [正点原子B站教程](https://www.bilibili.com/video/BV19g411p7UT?spm_id_from=333.788.videopod.episodes&vd_source=c5a893fba261848de2cb21259f6988d8)
2. [FreeRTOS官网](https://www.freertos.org/)
3. [FreeRTOS源码](https://sourceforge.net/projects/freertos/files/FreeRTOS/)