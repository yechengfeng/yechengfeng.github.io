- **execute() 方法（来自 Executor 接口）**

  - 异常处理：

    - 任务中的未捕获异常会传播到线程池的 UncaughtExceptionHandler

    - 默认行为：打印异常堆栈到 System.err（通过 ThreadGroup 处理）。

      ```java
      java
      executor.execute(() -> {
      throw new RuntimeException("Exception from execute()");
      });
      //结果：异常被线程的 UncaughtExceptionHandler 捕获，默认打印到控制台。
      ```

- **submit() 方法（来自 ExecutorService 接口）**

  - 异常处理：

    - 任务中的异常会被封装到 Future 对象中。

    - 异常不会立即抛出！调用 Future.get() 时才会抛出 ExecutionException（原始异常可通过 getCause() 获取）。

    - 如果未调用 Future.get()，异常会被静默吞没（无日志输出）！

      ```java
      java
      Future future = executor.submit(() -> {
      throw new RuntimeException("Exception from submit()");
      });
      try {
      future.get(); // 抛出 ExecutionException，包裹原始异常
      } catch (ExecutionException e) {
      Throwable cause = e.getCause(); // 获取原始异常
      System.out.println("Caught: " + cause.getMessage());
      }
      ```

- **关键区别**

  | **特性**         | execute()                           | submit()                            |
  | ---------------- | ----------------------------------- | ----------------------------------- |
  | **返回值**       | void                                | Future 对象                         |
  | **异常可见性**   | 直接传播到 UncaughtExceptionHandler | 封装在 Future 中，需调用 get() 触发 |
  | **异常处理位置** | 线程池的异常处理器                  | 调用 Future.get() 的代码            |
  | **静默吞没异常** | （默认打印异常）                    | （若不调用 get()，异常被忽略）      |

- #### 最佳实践：避免异常丢失

  -  使用 execute() 时为线程池设置自定义 UncaughtExceptionHandler：

  ```java
  ThreadFactory factory = r -> {	
   Thread t = new Thread(r);
   t.setUncaughtExceptionHandler((thread, ex) -> {
   System.err.println("Task failed: " + ex.getMessage());
  });
   return t;
   };
   ExecutorService executor = Executors.newFixedThreadPool(1, factory);
  
  ```

  -  使用 submit() 时：必须检查 Future 结果 或者使用CompleteFuture

    ```java
    java
    Future future = executor.submit(task);
    try {
    future.get(); // 触发异常
    } catch (ExecutionException e) {
    // 处理原始异常 e.getCause()
    }
    ```

  - 通用方案：在任务内部捕获所有异常：

    ```java
    java
    executor.submit(() -> {
    try {
    // 业务代码
    } catch (Exception e) {
    // 明确处理异常（如记录日志）
    }
    });
    ```

- **为什么 submit() 会吞没异常？**

  - submit() 将任务包装为 RunnableFuture，其 run() 方法捕获异常并存储到 Future 对象，而非直接抛出。只有通过 Future.get() 才能访问该异常。

- **总结**

  - execute()：适合不需要返回值的简单任务，异常由线程池处理器处理。

  - submit()：适合需要返回值或任务状态跟踪的场景，但必须检查 Future 避免异常丢失。

  - 关键原则：始终确保任务中的异常被显式处理（通过处理器、Future 或内部 try-catch），防止静默失败。