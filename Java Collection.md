
That's a large topic. I'll focus on the **most commonly used and interview-relevant methods** for each class/interface.

# 1. StringBuilder

Used for mutable strings (better performance than String when modifying text repeatedly).

## Constructors

```java
StringBuilder sb = new StringBuilder();
StringBuilder sb = new StringBuilder("Hello");
StringBuilder sb = new StringBuilder(100);
```

---

## append()

Adds data at the end.

```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");

System.out.println(sb); // Hello World
```

---

## insert()

Insert at index.

```java
StringBuilder sb = new StringBuilder("Hllo");
sb.insert(1, "e");

System.out.println(sb); // Hello
```

---

## delete()

Remove characters.

```java
StringBuilder sb = new StringBuilder("Hello");
sb.delete(1, 4);

System.out.println(sb); // Ho
```

---

## deleteCharAt()

```java
StringBuilder sb = new StringBuilder("Hello");
sb.deleteCharAt(1);

System.out.println(sb); // Hllo
```

---

## replace()

```java
StringBuilder sb = new StringBuilder("Hello");
sb.replace(0, 5, "Java");

System.out.println(sb); // Java
```

---

## reverse()

```java
StringBuilder sb = new StringBuilder("abc");
sb.reverse();

System.out.println(sb); // cba
```

---

## charAt()

```java
StringBuilder sb = new StringBuilder("Hello");

System.out.println(sb.charAt(1)); // e
```

---

## setCharAt()

```java
StringBuilder sb = new StringBuilder("Hello");
sb.setCharAt(0, 'Y');

System.out.println(sb); // Yello
```

---

## substring()

```java
StringBuilder sb = new StringBuilder("Hello");

System.out.println(sb.substring(1, 4)); // ell
```

---

## length()

```java
System.out.println(sb.length());
```

---

## capacity()

```java
StringBuilder sb = new StringBuilder();

System.out.println(sb.capacity()); // 16
```

---

## toString()

```java
String s = sb.toString();
```

---

# 2. Queue

FIFO structure.

```java
Queue<Integer> queue = new LinkedList<>();
```

---

## add()

Throws exception if insertion fails.

```java
queue.add(10);
queue.add(20);
```

---

## offer()

Returns boolean.

```java
queue.offer(30);
```

---

## remove()

Removes head.

```java
queue.remove();
```

---

## poll()

Returns and removes head.

```java
Integer val = queue.poll();
```

Returns null if empty.

---

## element()

Peek with exception.

```java
queue.element();
```

---

## peek()

```java
queue.peek();
```

Returns null if empty.

---

## isEmpty()

```java
queue.isEmpty();
```

---

## size()

```java
queue.size();
```

---

# 3. Stack

LIFO structure.

```java
Stack<Integer> stack = new Stack<>();
```

---

## push()

```java
stack.push(10);
stack.push(20);
```

---

## pop()

```java
Integer val = stack.pop();
```

Removes top.

---

## peek()

```java
Integer val = stack.peek();
```

---

## empty()

```java
stack.empty();
```

---

## search()

1-based position from top.

```java
stack.push(10);
stack.push(20);
stack.push(30);

System.out.println(stack.search(20)); // 2
```

---

# 4. String

Most used class in Java.

```java
String s = "Hello World";
```

---

## length()

```java
s.length();
```

---

## charAt()

```java
s.charAt(0);
```

---

## substring()

```java
s.substring(1);
s.substring(1,4);
```

---

## contains()

```java
s.contains("World");
```

---

## startsWith()

```java
s.startsWith("Hel");
```

---

## endsWith()

```java
s.endsWith("ld");
```

---

## equals()

```java
s.equals("Hello");
```

---

## equalsIgnoreCase()

```java
s.equalsIgnoreCase("hello");
```

---

## compareTo()

```java
"abc".compareTo("abd");
```

---

## indexOf()

```java
s.indexOf("o");
```

---

## lastIndexOf()

```java
s.lastIndexOf("o");
```

---

## replace()

```java
s.replace("Hello", "Hi");
```

---

## replaceAll()

Regex based.

```java
s.replaceAll("\\d", "");
```

---

## split()

```java
String[] arr = s.split(" ");
```

---

## trim()

```java
" Hello ".trim();
```

---

## strip() (Java 11)

Unicode-aware trim.

```java
" Hello ".strip();
```

---

## toUpperCase()

```java
s.toUpperCase();
```

---

## toLowerCase()

```java
s.toLowerCase();
```

---

## isEmpty()

```java
s.isEmpty();
```

---

## isBlank() (Java 11)

```java
"   ".isBlank();
```

---

## toCharArray()

```java
char[] arr = s.toCharArray();
```

---

# 5. Integer

Wrapper class.

---

## parseInt()

```java
int n = Integer.parseInt("123");
```

---

## valueOf()

```java
Integer n = Integer.valueOf("123");
```

---

## toString()

```java
Integer.toString(123);
```

---

## compare()

```java
Integer.compare(10,20);
```

---

## max()

```java
Integer.max(10,20);
```

---

## min()

```java
Integer.min(10,20);
```

---

## sum()

```java
Integer.sum(10,20);
```

---

## bitCount()

Count set bits.

```java
Integer.bitCount(7);
```

Output:

```java
3
```

---

## toBinaryString()

```java
Integer.toBinaryString(10);
```

Output:

```java
1010
```

---

# 6. Character

---

## isDigit()

```java
Character.isDigit('5');
```

---

## isLetter()

```java
Character.isLetter('A');
```

---

## isLetterOrDigit()

```java
Character.isLetterOrDigit('9');
```

---

## isUpperCase()

```java
Character.isUpperCase('A');
```

---

## isLowerCase()

```java
Character.isLowerCase('a');
```

---

## toUpperCase()

```java
Character.toUpperCase('a');
```

---

## toLowerCase()

```java
Character.toLowerCase('A');
```

---

## getNumericValue()

```java
Character.getNumericValue('7');
```

Output:

```java
7
```

---

# 7. Set

No duplicates.

```java
Set<Integer> set = new HashSet<>();
```

---

## add()

```java
set.add(10);
```

---

## remove()

```java
set.remove(10);
```

---

## contains()

```java
set.contains(10);
```

---

## size()

```java
set.size();
```

---

## isEmpty()

```java
set.isEmpty();
```

---

## clear()

```java
set.clear();
```

---

## addAll()

```java
set.addAll(otherSet);
```

---

## retainAll()

Intersection.

```java
set1.retainAll(set2);
```

---

## removeAll()

Difference.

```java
set1.removeAll(set2);
```

---

# 8. HashMap

Most important collection.

```java
Map<String,Integer> map = new HashMap<>();
```

---

## put()

```java
map.put("A",100);
```

---

## get()

```java
map.get("A");
```

---

## getOrDefault()

```java
map.getOrDefault("X",0);
```

---

## putIfAbsent()

```java
map.putIfAbsent("A",200);
```

---

## containsKey()

```java
map.containsKey("A");
```

---

## containsValue()

```java
map.containsValue(100);
```

---

## remove()

```java
map.remove("A");
```

---

## replace()

```java
map.replace("A",300);
```

---

## computeIfAbsent()

Very important.

```java
map.computeIfAbsent(
    "A",
    k -> new ArrayList<>()
);
```

Grouping example:

```java
Map<String,List<Integer>> map = new HashMap<>();

map.computeIfAbsent("A", k -> new ArrayList<>())
   .add(1);
```

---

## merge()

Frequency counting.

```java
map.merge("apple",1,Integer::sum);
```

Equivalent:

```java
map.put(
  word,
  map.getOrDefault(word,0)+1
);
```

---

## keySet()

```java
map.keySet();
```

---

## values()

```java
map.values();
```

---

## entrySet()

Most efficient iteration.

```java
for(Map.Entry<String,Integer> e : map.entrySet()) {
    System.out.println(e.getKey());
    System.out.println(e.getValue());
}
```

---

# 9. ArrayDeque

Recommended replacement for Stack.

```java
Deque<Integer> dq = new ArrayDeque<>();
```

---

## addFirst()

```java
dq.addFirst(10);
```

---

## addLast()

```java
dq.addLast(20);
```

---

## offerFirst()

```java
dq.offerFirst(5);
```

---

## offerLast()

```java
dq.offerLast(25);
```

---

## pollFirst()

```java
dq.pollFirst();
```

---

## pollLast()

```java
dq.pollLast();
```

---

## peekFirst()

```java
dq.peekFirst();
```

---

## peekLast()

```java
dq.peekLast();
```

---

## push()

Stack operation.

```java
dq.push(10);
```

---

## pop()

```java
dq.pop();
```

---

# 10. PriorityQueue

Heap implementation.

Default: Min Heap.

```java
PriorityQueue<Integer> pq =
        new PriorityQueue<>();
```

---

## offer()

```java
pq.offer(10);
pq.offer(5);
pq.offer(20);
```

---

## poll()

```java
pq.poll();
```

Returns:

```java
5
```

---

## peek()

```java
pq.peek();
```

---

## remove()

```java
pq.remove();
```

---

## size()

```java
pq.size();
```

---

## isEmpty()

```java
pq.isEmpty();
```

---

## Max Heap

```java
PriorityQueue<Integer> pq =
    new PriorityQueue<>(
        Collections.reverseOrder()
    );
```

---

## Custom Comparator

```java
PriorityQueue<String> pq =
    new PriorityQueue<>(
        (a,b) -> a.length() - b.length()
    );
```

---

# Most Important Methods for Interviews

If you're preparing for Java/SDE interviews, these are the methods you should know by heart:

### String

```java
length()
charAt()
substring()
contains()
indexOf()
split()
replace()
equals()
compareTo()
```

### StringBuilder

```java
append()
insert()
delete()
reverse()
setCharAt()
```

### HashMap

```java
put()
get()
getOrDefault()
putIfAbsent()
computeIfAbsent()
merge()
entrySet()
```

### Set

```java
add()
contains()
remove()
retainAll()
removeAll()
```

### Queue

```java
offer()
poll()
peek()
```

### ArrayDeque

```java
push()
pop()
peek()
addFirst()
addLast()
pollFirst()
pollLast()
```

### PriorityQueue

```java
offer()
poll()
peek()
Comparator
reverseOrder()
```

These are the methods that appear most frequently in DSA problems, Java interviews, and production code.




---

# HashMap Sorting 

In Java, `HashMap` itself **does not maintain any order**. If you want sorted output, you need to either:

1. Sort by **Key**
    
2. Sort by **Value**
    

---

# 1. Sort HashMap by Key

## Using TreeMap

`TreeMap` automatically sorts entries based on keys.

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {

        Map<Integer, String> map = new HashMap<>();

        map.put(3, "Java");
        map.put(1, "Python");
        map.put(2, "Go");

        Map<Integer, String> sortedMap = new TreeMap<>(map);

        System.out.println(sortedMap);
    }
}
```

### Output

```text
{1=Python, 2=Go, 3=Java}
```

---

## Descending Order of Keys

```java
Map<Integer, String> sortedMap =
        new TreeMap<>(Collections.reverseOrder());

sortedMap.putAll(map);

System.out.println(sortedMap);
```

### Output

```text
{3=Java, 2=Go, 1=Python}
```

---

## Using Streams

```java
Map<Integer, String> sortedMap =
        map.entrySet()
           .stream()
           .sorted(Map.Entry.comparingByKey())
           .collect(
               LinkedHashMap::new,
               (m,e) -> m.put(e.getKey(), e.getValue()),
               LinkedHashMap::putAll
           );

System.out.println(sortedMap);
```

### Output

```text
{1=Python, 2=Go, 3=Java}
```

---

# 2. Sort HashMap by Value

Suppose:

```java
Map<Integer, String> map = new HashMap<>();

map.put(1, "Java");
map.put(2, "Python");
map.put(3, "C");
map.put(4, "Go");
```

---

## Ascending Order by Value

```java
Map<Integer, String> sortedMap =
        map.entrySet()
           .stream()
           .sorted(Map.Entry.comparingByValue())
           .collect(
               LinkedHashMap::new,
               (m,e) -> m.put(e.getKey(), e.getValue()),
               LinkedHashMap::putAll
           );

System.out.println(sortedMap);
```

### Output

```text
{3=C, 4=Go, 1=Java, 2=Python}
```

---

## Descending Order by Value

```java
Map<Integer, String> sortedMap =
        map.entrySet()
           .stream()
           .sorted(Map.Entry.comparingByValue(Comparator.reverseOrder()))
           .collect(
               LinkedHashMap::new,
               (m,e) -> m.put(e.getKey(), e.getValue()),
               LinkedHashMap::putAll
           );

System.out.println(sortedMap);
```

### Output

```text
{2=Python, 1=Java, 4=Go, 3=C}
```

---

# Sort by Value Then Key

Very common interview question.

```java
Map<Integer, String> sortedMap =
        map.entrySet()
           .stream()
           .sorted(
               Map.Entry.<Integer, String>comparingByValue()
                        .thenComparing(Map.Entry.comparingByKey())
           )
           .collect(
               LinkedHashMap::new,
               (m,e) -> m.put(e.getKey(), e.getValue()),
               LinkedHashMap::putAll
           );
```

Example:

```java
{
  3=C,
  1=Java,
  5=Java,
  2=Python
}
```

Output:

```text
{3=C, 1=Java, 5=Java, 2=Python}
```

When values are equal (`Java`), keys are used as tie-breakers.

---

# Interview-Friendly Approach (Without Streams)

### Sort by Value

```java
List<Map.Entry<Integer, String>> list =
        new ArrayList<>(map.entrySet());

list.sort(Map.Entry.comparingByValue());

for (Map.Entry<Integer, String> entry : list) {
    System.out.println(entry.getKey() + " " + entry.getValue());
}
```

---

# Most Important APIs

### Sort By Key

```java
Map.Entry.comparingByKey()
```

### Sort By Value

```java
Map.Entry.comparingByValue()
```

### Reverse Order

```java
Comparator.reverseOrder()
```

### Multiple Conditions

```java
.thenComparing(...)
```

---

# Java 8 Stream Template (Memorize)

```java
Map<K,V> sorted =
        map.entrySet()
           .stream()
           .sorted(...)
           .collect(
               LinkedHashMap::new,
               (m,e) -> m.put(e.getKey(), e.getValue()),
               LinkedHashMap::putAll
           );
```

`LinkedHashMap` is important here because it preserves the order produced by the stream sorting. If you collect back into a `HashMap`, the sorted order will be lost.