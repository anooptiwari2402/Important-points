
`CompletableFuture` is one of the most important concurrency APIs introduced in Java 8.

It solves a major problem with traditional `Future`:

- `Future` can only get the result using `get()`
    
- Cannot chain tasks
    
- Cannot combine multiple async operations
    
- Cannot easily handle exceptions
    

`CompletableFuture` provides:

- Asynchronous execution
    
- Task chaining
    
- Combining multiple tasks
    
- Exception handling
    
- Parallel execution
    

---

# 1. Creating CompletableFuture

## Manual Completion

```java
CompletableFuture<String> future = new CompletableFuture<>();

new Thread(() -> {
    try {
        Thread.sleep(2000);
        future.complete("Hello");
    } catch (Exception e) {
        future.completeExceptionally(e);
    }
}).start();

System.out.println(future.get());
```

Output:

```text
Hello
```

---

# 2. runAsync()

Used when no result is returned.

```java
CompletableFuture<Void> future =
        CompletableFuture.runAsync(() -> {
            System.out.println(Thread.currentThread().getName());
        });

future.join();
```

Output:

```text
ForkJoinPool.commonPool-worker-1
```

Equivalent to:

```java
Runnable task
```

---

# 3. supplyAsync()

Used when a value must be returned.

```java
CompletableFuture<String> future =
        CompletableFuture.supplyAsync(() -> {
            return "Hello";
        });

System.out.println(future.join());
```

Output:

```text
Hello
```

Equivalent to:

```java
Supplier<String>
```

---

# 4. thenApply()

Transforms result.

```java
CompletableFuture<Integer> future =
        CompletableFuture.supplyAsync(() -> 10)
                .thenApply(x -> x * 2);

System.out.println(future.join());
```

Output:

```text
20
```

Think:

```java
input -> output
```

Like Stream.map()

```java
stream.map(...)
```

---

# 5. thenAccept()

Consumes result.

```java
CompletableFuture.supplyAsync(() -> "Java")
        .thenAccept(System.out::println)
        .join();
```

Output:

```text
Java
```

Equivalent:

```java
Consumer<T>
```

---

# 6. thenRun()

Run another task without using previous result.

```java
CompletableFuture.supplyAsync(() -> "Java")
        .thenRun(() -> {
            System.out.println("Completed");
        })
        .join();
```

Output:

```text
Completed
```

---

# 7. thenCompose()

Used for dependent async calls.

Without compose:

```java
CompletableFuture<
    CompletableFuture<String>
> future;
```

Nested future problem.

Example:

```java
CompletableFuture<String> future =
        CompletableFuture.supplyAsync(() -> "Java")
                .thenCompose(str ->
                    CompletableFuture.supplyAsync(() ->
                        str + " Developer"));

System.out.println(future.join());
```

Output:

```text
Java Developer
```

Think:

```java
flatMap()
```

Equivalent:

```java
stream.flatMap(...)
```

---

# 8. thenCombine()

Combine two independent futures.

```java
CompletableFuture<String> first =
        CompletableFuture.supplyAsync(() -> "Java");

CompletableFuture<String> second =
        CompletableFuture.supplyAsync(() -> " Spring");

CompletableFuture<String> result =
        first.thenCombine(second,
                (a,b) -> a+b);

System.out.println(result.join());
```

Output:

```text
Java Spring
```

Real Example:

```java
getUser()
getOrders()

combine result
```

---

# 9. allOf()

Wait for all futures.

```java
CompletableFuture<String> f1 =
        CompletableFuture.supplyAsync(() -> "A");

CompletableFuture<String> f2 =
        CompletableFuture.supplyAsync(() -> "B");

CompletableFuture<String> f3 =
        CompletableFuture.supplyAsync(() -> "C");

CompletableFuture.allOf(f1,f2,f3)
        .join();
```

Retrieve values:

```java
List<String> result =
        Stream.of(f1,f2,f3)
                .map(CompletableFuture::join)
                .toList();
```

Output:

```text
[A, B, C]
```

---

# 10. anyOf()

Returns first completed future.

```java
CompletableFuture<String> db =
        CompletableFuture.supplyAsync(() -> {
            sleep(3);
            return "DB";
        });

CompletableFuture<String> cache =
        CompletableFuture.supplyAsync(() -> {
            sleep(1);
            return "CACHE";
        });

Object result =
        CompletableFuture.anyOf(db, cache)
                .join();

System.out.println(result);
```

Output:

```text
CACHE
```

Useful for:

- Multiple mirrors
    
- Fastest response wins
    

---

# 11. exceptionally()

Handle exception.

```java
CompletableFuture<Integer> future =
        CompletableFuture.supplyAsync(() -> {
            return 10 / 0;
        })
        .exceptionally(ex -> {
            return -1;
        });

System.out.println(future.join());
```

Output:

```text
-1
```

---

# 12. handle()

Access both success and failure.

```java
CompletableFuture<Integer> future =
        CompletableFuture.supplyAsync(() -> {
            return 10/0;
        })
        .handle((result, ex) -> {
            if(ex != null)
                return -1;

            return result;
        });

System.out.println(future.join());
```

Output:

```text
-1
```

---

# 13. whenComplete()

Observe result without changing it.

```java
CompletableFuture<Integer> future =
        CompletableFuture.supplyAsync(() -> 100)
                .whenComplete((res, ex) -> {
                    System.out.println(res);
                });

System.out.println(future.join());
```

Output:

```text
100
100
```

Useful for:

- Logging
    
- Metrics
    
- Auditing
    

---

# 14. Async Variants

Every stage has async version.

```java
thenApply()
thenApplyAsync()

thenAccept()
thenAcceptAsync()

thenCompose()
thenComposeAsync()
```

Example:

```java
CompletableFuture.supplyAsync(() -> 10)
        .thenApplyAsync(x -> x * 2)
        .join();
```

---

# 15. Custom Thread Pool

Never use common pool in large applications.

```java
ExecutorService executor =
        Executors.newFixedThreadPool(10);

CompletableFuture<String> future =
        CompletableFuture.supplyAsync(
                () -> "Hello",
                executor);

System.out.println(future.join());

executor.shutdown();
```

---

# 16. join() vs get()

## join()

```java
future.join();
```

Throws:

```java
CompletionException
```

Unchecked exception.

---

## get()

```java
future.get();
```

Throws:

```java
InterruptedException
ExecutionException
```

Checked exceptions.

Most modern code uses:

```java
join()
```

---

# 17. Real Production Example

Suppose API needs:

```text
User Profile
Orders
Recommendations
```

Sequential:

```java
User user = getUser();
Orders orders = getOrders();
Rec rec = getRecommendations();
```

Time:

```text
1 sec + 1 sec + 1 sec
= 3 sec
```

Parallel:

```java
CompletableFuture<User> user =
        supplyAsync(this::getUser);

CompletableFuture<Orders> orders =
        supplyAsync(this::getOrders);

CompletableFuture<Rec> rec =
        supplyAsync(this::getRecommendations);

CompletableFuture.allOf(
        user,
        orders,
        rec
).join();

Dashboard dashboard =
        new Dashboard(
                user.join(),
                orders.join(),
                rec.join());
```

Time:

```text
max(1,1,1)
= 1 sec
```

---

# 18. Most Important Methods To Remember

|Method|Purpose|
|---|---|
|runAsync|Async task no return|
|supplyAsync|Async task with return|
|thenApply|Transform result|
|thenAccept|Consume result|
|thenRun|Run next task|
|thenCompose|Chain dependent futures|
|thenCombine|Combine two futures|
|allOf|Wait all futures|
|anyOf|First completed future|
|exceptionally|Error handling|
|handle|Success + error handling|
|whenComplete|Logging/auditing|
|join|Wait for result|
|get|Wait with checked exceptions|

# Interview Question

**Q: Difference between thenApply and thenCompose?**

`thenApply`

```java
T -> U
```

Transforms value.

```java
future.thenApply(x -> x * 2)
```

---

`thenCompose`

```java
T -> CompletableFuture<U>
```

Flattens nested futures.

```java
future.thenCompose(
    x -> getUserAsync(x)
)
```

A common interview answer is:

> `thenApply` is like `map()`, while `thenCompose` is like `flatMap()` in Java Streams.

That's the key distinction interviewers usually expect.