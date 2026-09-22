
If you're preparing for Java interviews, you should learn Streams in this order:

```
1. filter()
2. map()
3. flatMap()
4. distinct()
5. sorted()
6. limit()
7. skip()
8. peek()

Terminal Operations
-------------------
9. collect()
10. forEach()
11. count()
12. findFirst()
13. findAny()
14. anyMatch()
15. allMatch()
16. noneMatch()
17. min()
18. max()

Aggregation
-----------
19. reduce()
20. sum()
21. average()
22. summaryStatistics()

Primitive Streams
-----------------
23. mapToInt()
24. mapToLong()
25. mapToDouble()
26. boxed()
27. mapToObj()
```

---

# Sample Data

```java
List<Integer> numbers =
        List.of(1, 2, 3, 4, 5, 6);

List<String> names =
        List.of("John", "Alice", "Bob");
```

---

# 1. filter()

Keeps only matching elements.

```java
List<Integer> evens =
        numbers.stream()
               .filter(n -> n % 2 == 0)
               .toList();

System.out.println(evens);
```

Output:

```java
[2,4,6]
```

SQL Equivalent:

```sql
SELECT * FROM numbers
WHERE number % 2 = 0;
```

---

# 2. map()

Transforms one object into another.

```java
List<Integer> squares =
        numbers.stream()
               .map(n -> n * n)
               .toList();
```

Output:

```java
[1,4,9,16,25,36]
```

---

# 3. flatMap()

Converts nested collections into a single stream.

```java
List<List<String>> data =
        List.of(
            List.of("A", "B"),
            List.of("C", "D")
        );

List<String> result =
        data.stream()
            .flatMap(List::stream)
            .toList();
```

Output:

```java
[A,B,C,D]
```

Without flatMap:

```java
[[A,B],[C,D]]
```

---

# 4. distinct()

Removes duplicates.

```java
List<Integer> nums =
        List.of(1,2,2,3,3,3);

nums.stream()
    .distinct()
    .forEach(System.out::println);
```

Output:

```java
1
2
3
```

---

# 5. sorted()

```java
numbers.stream()
       .sorted(Comparator.reverseOrder())
       .forEach(System.out::println);
```

Output:

```java
6
5
4
3
2
1
```

---

# 6. limit()

First N records.

```java
numbers.stream()
       .limit(3)
       .forEach(System.out::println);
```

Output:

```java
1
2
3
```

---

# 7. skip()

Skip first N records.

```java
numbers.stream()
       .skip(3)
       .forEach(System.out::println);
```

Output:

```java
4
5
6
```

---

# 8. peek()

Debugging.

```java
numbers.stream()
       .peek(System.out::println)
       .map(n -> n * 2)
       .toList();
```

Output:

```java
1
2
3
4
5
6
```

---

# 9. collect()

Convert Stream → Collection

```java
Set<Integer> set =
        numbers.stream()
               .collect(Collectors.toSet());
```

---

# 10. count()

```java
long count =
        numbers.stream()
               .count();
```

Output:

```java
6
```

---

# 11. findFirst()

```java
Optional<Integer> first =
        numbers.stream()
               .findFirst();
```

Output:

```java
Optional[1]
```

---

# 12. findAny()

Mostly useful in parallel streams.

```java
Optional<Integer> any =
        numbers.parallelStream()
               .findAny();
```

May return:

```java
Optional[4]
```

---

# 13. anyMatch()

```java
boolean result =
        numbers.stream()
               .anyMatch(n -> n > 5);
```

Output:

```java
true
```

---

# 14. allMatch()

```java
boolean result =
        numbers.stream()
               .allMatch(n -> n > 0);
```

Output:

```java
true
```

---

# 15. noneMatch()

```java
boolean result =
        numbers.stream()
               .noneMatch(n -> n < 0);
```

Output:

```java
true
```

---

# 16. min()

```java
int min =
        numbers.stream()
               .min(Integer::compareTo)
               .get();
```

Output:

```java
1
```

---

# 17. max()

```java
int max =
        numbers.stream()
               .max(Integer::compareTo)
               .get();
```

Output:

```java
6
```

---

# Aggregation Functions

Aggregation means converting multiple values into a single value.

Example:

```java
[10,20,30,40]
```

Can become:

```java
100
```

(sum)

---

# 18. reduce()

Most powerful aggregation method.

### Sum

```java
int sum =
        numbers.stream()
               .reduce(
                    0,
                    (a, b) -> a + b
               );

System.out.println(sum);
```

Output:

```java
21
```

---

### Multiplication

```java
int product =
        numbers.stream()
               .reduce(
                   1,
                   (a,b) -> a*b
               );
```

Output:

```java
720
```

---

### Maximum

```java
int max =
        numbers.stream()
               .reduce(
                    Integer.MIN_VALUE,
                    Integer::max
               );
```

---

# Primitive Streams

Java created these to avoid boxing/unboxing.

```java
IntStream
LongStream
DoubleStream
```

instead of

```java
Stream<Integer>
Stream<Long>
Stream<Double>
```

---

# 19. mapToInt()

Convert Object Stream → IntStream

```java
List<String> names =
        List.of("John", "Alice", "Bob");

int totalChars =
        names.stream()
             .mapToInt(String::length)
             .sum();

System.out.println(totalChars);
```

Output:

```java
12
```

---

# 20. sum()

Available only on primitive streams.

```java
int sum =
        numbers.stream()
               .mapToInt(Integer::intValue)
               .sum();
```

Output:

```java
21
```

---

# 21. average()

```java
double avg =
        numbers.stream()
               .mapToInt(Integer::intValue)
               .average()
               .orElse(0);
```

Output:

```java
3.5
```

---

# 22. summaryStatistics()

Gives all metrics in one pass.

```java
IntSummaryStatistics stats =
        numbers.stream()
               .mapToInt(Integer::intValue)
               .summaryStatistics();

System.out.println(stats);
```

Output:

```java
count=6
sum=21
min=1
average=3.5
max=6
```

Interviewers love this one.

---

# 23. boxed()

Convert Primitive Stream → Object Stream

```java
IntStream stream =
        IntStream.range(1, 5);

Stream<Integer> boxed =
        stream.boxed();
```

Without boxed:

```java
IntStream
```

After boxed:

```java
Stream<Integer>
```

Useful when you need collectors:

```java
List<Integer> list =
        IntStream.range(1,10)
                 .boxed()
                 .toList();
```

Output:

```java
[1,2,3,4,5,6,7,8,9]
```

---

# 24. mapToObj()

Convert primitive → object.

```java
List<String> result =
        IntStream.range(1, 5)
                 .mapToObj(i -> "User-" + i)
                 .toList();
```

Output:

```java
[
 User-1,
 User-2,
 User-3,
 User-4
]
```

---

# Difference Between map() and mapToObj()

### map()

```java
Stream<Integer>
    .map(...)
```

Input:

```java
Stream<Integer>
```

Output:

```java
Stream<String>
```

Example:

```java
numbers.stream()
       .map(n -> "Num-" + n)
```

---

### mapToObj()

Input:

```java
IntStream
```

Output:

```java
Stream<String>
```

Example:

```java
IntStream.range(1,5)
         .mapToObj(i -> "Num-" + i)
```

---

# Common Interview Chain

```java
Map<String, Double> avgSalaryByDept =
        employees.stream()
                 .collect(
                     Collectors.groupingBy(
                         Employee::getDepartment,
                         Collectors.averagingDouble(
                             Employee::getSalary
                         )
                     )
                 );
```

This single statement combines:

```java
stream()
groupingBy()
averagingDouble()
collect()
```

Understanding chains like this is usually enough to solve 80–90% of Java Stream interview questions.