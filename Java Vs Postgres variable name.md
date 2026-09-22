
Here's a practical mapping between **Java primitive/wrapper types** and their closest **PostgreSQL data types**, including **size** and **range**.

## Integer Types

|Java Type|Size|Range|PostgreSQL Type|Size|Range|
|---|---|---|---|---|---|
|`byte`|8 bits (1 byte)|-128 to 127|`SMALLINT` (smallest available)|16 bits|-32,768 to 32,767|
|`short`|16 bits (2 bytes)|-32,768 to 32,767|`SMALLINT`|16 bits|-32,768 to 32,767|
|`int`|32 bits (4 bytes)|-2,147,483,648 to 2,147,483,647|`INTEGER` / `INT`|32 bits|-2,147,483,648 to 2,147,483,647|
|`long`|64 bits (8 bytes)|-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807|`BIGINT`|64 bits|Same as Java `long`|

### Example

```java
byte age = 25;
short year = 2025;
int salary = 100000;
long population = 8000000000L;
```

```sql
CREATE TABLE employee (
    age SMALLINT,
    year SMALLINT,
    salary INTEGER,
    population BIGINT
);
```

---

## Floating Point Types

|Java Type|Size|Approx Precision|PostgreSQL Type|Size|
|---|---|---|---|---|
|`float`|32 bits|~6 decimal digits|`REAL`|4 bytes|
|`double`|64 bits|~15 decimal digits|`DOUBLE PRECISION`|8 bytes|

### Range

|Type|Range|
|---|---|
|`float`|±1.4E-45 to ±3.4E38|
|`double`|±4.9E-324 to ±1.8E308|

### Example

```java
float temperature = 36.5f;
double pi = 3.141592653589793;
```

```sql
CREATE TABLE metrics (
    temperature REAL,
    pi DOUBLE PRECISION
);
```

---

## Exact Decimal Types

For money and financial calculations.

|Java Type|PostgreSQL Type|
|---|---|
|`BigDecimal`|`NUMERIC(p,s)` / `DECIMAL(p,s)`|

### Example

```java
BigDecimal amount = new BigDecimal("123456.78");
```

```sql
amount NUMERIC(12,2)
```

Meaning:

- Total digits = 12
    
- Digits after decimal = 2
    

Range:

```text
-9999999999.99
to
 9999999999.99
```

---

## Character Types

|Java Type|PostgreSQL Type|
|---|---|
|`char`|`CHAR(1)`|
|`String`|`VARCHAR(n)`|
|`String`|`TEXT`|

### Example

```java
char gender = 'M';
String name = "Anoop";
```

```sql
gender CHAR(1),
name VARCHAR(100)
```

or

```sql
name TEXT
```

---

## Boolean

|Java Type|PostgreSQL Type|
|---|---|
|`boolean`|`BOOLEAN`|

### Example

```java
boolean active = true;
```

```sql
active BOOLEAN
```

Values:

```sql
TRUE
FALSE
NULL
```

---

## Date & Time Types

|Java Type|PostgreSQL Type|
|---|---|
|`LocalDate`|`DATE`|
|`LocalTime`|`TIME`|
|`LocalDateTime`|`TIMESTAMP`|
|`OffsetDateTime`|`TIMESTAMP WITH TIME ZONE`|
|`Instant`|`TIMESTAMP WITH TIME ZONE`|

### Example

```java
LocalDate dob = LocalDate.now();
LocalDateTime createdAt = LocalDateTime.now();
```

```sql
dob DATE,
created_at TIMESTAMP
```

---

## Binary Types

|Java Type|PostgreSQL Type|
|---|---|
|`byte[]`|`BYTEA`|

### Example

```java
byte[] image;
```

```sql
image BYTEA
```

---

## UUID

|Java Type|PostgreSQL Type|
|---|---|
|`UUID`|`UUID`|

### Example

```java
UUID userId = UUID.randomUUID();
```

```sql
user_id UUID PRIMARY KEY
```

---

## JSON

|Java Type|PostgreSQL Type|
|---|---|
|`Map<String,Object>`|`JSON`|
|`Map<String,Object>`|`JSONB` (recommended)|

### Example

```java
Map<String, Object> metadata;
```

```sql
metadata JSONB
```

---

## Most Common Mappings in Enterprise Applications

|Java|PostgreSQL|
|---|---|
|`Long id`|`BIGINT`|
|`String name`|`VARCHAR(255)`|
|`String description`|`TEXT`|
|`Integer age`|`INTEGER`|
|`BigDecimal amount`|`NUMERIC(18,2)`|
|`Boolean active`|`BOOLEAN`|
|`LocalDate dob`|`DATE`|
|`LocalDateTime createdAt`|`TIMESTAMP`|
|`UUID id`|`UUID`|
|`byte[] fileData`|`BYTEA`|
|`Map<String,Object>`|`JSONB`|

## Typical JPA Entity vs PostgreSQL Table

```java
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;

    private Integer age;

    private BigDecimal salary;

    private Boolean active;

    private LocalDate joiningDate;
}
```

```sql
CREATE TABLE employee (
    id BIGINT PRIMARY KEY,
    name VARCHAR(255),
    age INTEGER,
    salary NUMERIC(18,2),
    active BOOLEAN,
    joining_date DATE
);
```

For senior Java/PostgreSQL interviews, the most commonly used types are:

- `BIGINT` ↔ `Long`
    
- `INTEGER` ↔ `Integer`
    
- `VARCHAR/TEXT` ↔ `String`
    
- `NUMERIC(18,2)` ↔ `BigDecimal`
    
- `BOOLEAN` ↔ `Boolean`
    
- `TIMESTAMP` ↔ `LocalDateTime`
    
- `DATE` ↔ `LocalDate`
    
- `UUID` ↔ `UUID`
    
- `JSONB` ↔ `Map<String,Object>` / DTO object serialized as JSON.