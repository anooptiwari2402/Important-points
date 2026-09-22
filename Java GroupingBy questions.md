
`Collectors.groupingBy()` is one of the most important collectors in Java Streams. It groups elements based on a key, similar to SQL `GROUP BY`.

## 1. Basic groupingBy

### Group Employees by Department

```java
import java.util.*;
import java.util.stream.*;

class Employee {
    String name;
    String department;

    Employee(String name, String department) {
        this.name = name;
        this.department = department;
    }

    public String getDepartment() {
        return department;
    }

    public String toString() {
        return name;
    }
}

public class Main {
    public static void main(String[] args) {

        List<Employee> employees = List.of(
                new Employee("John", "IT"),
                new Employee("Alice", "HR"),
                new Employee("Bob", "IT"),
                new Employee("David", "HR")
        );

        Map<String, List<Employee>> result =
                employees.stream()
                        .collect(Collectors.groupingBy(Employee::getDepartment));

        System.out.println(result);
    }
}
```

Output:

```java
{
 HR=[Alice, David],
 IT=[John, Bob]
}
```

---

# 2. Group By and Count

### Count employees in each department

```java
Map<String, Long> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.counting()
                ));

System.out.println(result);
```

Output:

```java
{HR=2, IT=2}
```

Equivalent SQL:

```sql
SELECT department, COUNT(*)
FROM employee
GROUP BY department;
```

---

# 3. Group By and Sum

### Sum salary department-wise

```java
class Employee {
    String name;
    String department;
    double salary;

    Employee(String name, String department, double salary) {
        this.name = name;
        this.department = department;
        this.salary = salary;
    }

    public String getDepartment() {
        return department;
    }

    public double getSalary() {
        return salary;
    }
}
```

```java
Map<String, Double> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.summingDouble(Employee::getSalary)
                ));

System.out.println(result);
```

Output:

```java
{
 IT=150000.0,
 HR=80000.0
}
```

---

# 4. Group By and Average

### Average salary per department

```java
Map<String, Double> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.averagingDouble(Employee::getSalary)
                ));
```

Output:

```java
{
 IT=75000.0,
 HR=40000.0
}
```

---

# 5. Group By and Mapping

Suppose we only want employee names.

```java
Map<String, List<String>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.mapping(
                                e -> e.name,
                                Collectors.toList()
                        )
                ));

System.out.println(result);
```

Output:

```java
{
 IT=[John, Bob],
 HR=[Alice, David]
}
```

---

# 6. Group By and Set

Remove duplicates.

```java
Map<String, Set<String>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.mapping(
                                e -> e.name,
                                Collectors.toSet()
                        )
                ));
```

Output:

```java
{
 IT=[John, Bob],
 HR=[Alice, David]
}
```

---

# 7. Group By and Max

### Highest salary employee per department

```java
Map<String, Optional<Employee>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.maxBy(
                                Comparator.comparing(Employee::getSalary)
                        )
                ));
```

Output:

```java
{
 IT=Optional[John],
 HR=Optional[Alice]
}
```

---

# 8. Group By and Min

### Lowest salary employee per department

```java
Map<String, Optional<Employee>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.minBy(
                                Comparator.comparing(Employee::getSalary)
                        )
                ));
```

---

# 9. Nested Grouping

### Department → Gender

```java
class Employee {
    String name;
    String department;
    String gender;
}
```

```java
Map<String, Map<String, List<Employee>>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.groupingBy(Employee::getGender)
                ));
```

Output:

```java
{
 IT={
      Male=[John, Bob],
      Female=[Lisa]
    },
 HR={
      Female=[Alice]
    }
}
```

Equivalent SQL:

```sql
SELECT department, gender
FROM employee
GROUP BY department, gender;
```

---

# 10. Partitioning (Special Case)

When grouping by boolean.

```java
Map<Boolean, List<Employee>> result =
        employees.stream()
                .collect(Collectors.partitioningBy(
                        e -> e.salary > 50000
                ));
```

Output:

```java
{
 true=[John, Bob],
 false=[Alice]
}
```

---

# 11. Group By Multiple Fields

Suppose we want:

```java
department + gender
```

```java
Map<String, List<Employee>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        e -> e.department + "-" + e.gender
                ));
```

Output:

```java
{
 IT-Male=[John, Bob],
 HR-Female=[Alice]
}
```

Better approach:

```java
record Key(String department, String gender) {}

Map<Key, List<Employee>> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        e -> new Key(e.department, e.gender)
                ));
```

---

# 12. Top Paid Employee per Department (Interview Favorite)

```java
Map<String, Employee> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.collectingAndThen(
                                Collectors.maxBy(
                                        Comparator.comparing(Employee::getSalary)
                                ),
                                Optional::get
                        )
                ));
```

Output:

```java
{
 IT=John,
 HR=Alice
}
```

---

# 13. Convert Grouped Result to Custom Object

```java
record DepartmentStats(
        long count,
        double totalSalary
) {}
```

```java
Map<String, DepartmentStats> result =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.collectingAndThen(
                                Collectors.toList(),
                                list -> new DepartmentStats(
                                        list.size(),
                                        list.stream()
                                                .mapToDouble(Employee::getSalary)
                                                .sum()
                                )
                        )
                ));
```

---

## Most Common Interview Questions Using groupingBy

### Count Frequency

```java
List<String> words = List.of("java", "python", "java", "go");

Map<String, Long> freq =
        words.stream()
             .collect(Collectors.groupingBy(
                     s -> s,
                     Collectors.counting()
             ));
```

Output:

```java
{
 java=2,
 python=1,
 go=1
}
```

---

### Group Strings by Length

```java
Map<Integer, List<String>> result =
        words.stream()
             .collect(Collectors.groupingBy(String::length));
```

Output:

```java
{
 2=[go],
 4=[java],
 6=[python]
}
```

---

### Group Anagrams

```java
List<String> words =
        List.of("eat", "tea", "ate", "bat", "tab");

Map<String, List<String>> result =
        words.stream()
             .collect(Collectors.groupingBy(word -> {
                 char[] arr = word.toCharArray();
                 Arrays.sort(arr);
                 return new String(arr);
             }));

System.out.println(result.values());
```

Output:

```java
[
 [eat, tea, ate],
 [bat, tab]
]
```

These are the `groupingBy` patterns most frequently asked in Java interviews, especially at companies like JP Morgan, Goldman Sachs, Walmart, Amazon, and banking projects where aggregation and reporting logic is common.