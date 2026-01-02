# Java Latest Features – Practice Questions

---

## Q1. You are writing a utility method that processes a collection of numeric values.

To restrict a generic type to only numeric values in Java, bounded generics are used. By specifying an upper bound using `extends Number`, the compiler ensures that only classes that are subclasses of `Number` such as Integer, Double, or Float can be passed. This provides type safety and allows the method to safely call methods defined in the Number class, such as `doubleValue()`.

The wildcard `<? extends Number>` is used when the method only needs to read values from a collection. It allows flexibility by accepting collections of any specific subclass of Number, such as List<Integer> or List<Double>. However, it does not allow adding elements (except null) because the exact subtype is unknown. A practical use case is a method that calculates the sum or average of numbers from a list.

The wildcard `<? super Integer>` is used when the method needs to write Integer values into a collection. It accepts Integer or any of its parent types, such as Number or Object. This is useful when adding elements rather than reading them. A practical use case is a method that inserts Integer values into a collection that can hold Integers or their supertypes.

```java
import java.util.List;

public class NumberUtils {

    public static double sum(List<? extends Number> numbers) {
        double total = 0;
        for (Number n : numbers) {
            total += n.doubleValue();
        }
        return total;
    }

    public static void addIntegers(List<? super Integer> list) {
        list.add(10);
        list.add(20);
    }
}
```
## Q2. Java introduced records to simplify data-carrying classes.

Records solve several problems commonly found in traditional POJOs. In a normal POJO, developers must manually write constructors, getters, equals(), hashCode(), and toString() methods, which leads to boilerplate code. Records automatically generate all these methods, making the code concise, readable, and less error-prone. They also make the intent of the class clear by explicitly stating that the class is meant to hold immutable data.

A record should not be used when mutability is required, when complex business logic is involved, or when the class needs to extend another class. Records are implicitly final and their fields are immutable, so they are not suitable for entities that require state changes or inheritance. For example, JPA entities or classes with setters are not good candidates for records.
```
public record EmployeeRecord(int id, String name, String department) {
}
```

Q3. What is a functional interface?

A functional interface is an interface that contains exactly one abstract method. It is the foundation of lambda expressions in Java. Functional interfaces enable a functional programming style and allow behavior to be passed as data. Java provides many built-in functional interfaces to reduce the need for custom interfaces.

Two commonly used built-in functional interfaces are Runnable, which represents a task with no arguments and no return value, and Predicate<T>, which represents a condition that returns a boolean value. Lambda expressions improve readability by removing boilerplate code associated with anonymous classes.
```
// Anonymous class
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running task");
    }
};
```
// Lambda expression
Runnable r2 = () -> System.out.println("Running task");


The lambda version is shorter, clearer, and focuses only on the behavior rather than the syntax.

Q4. What is a Stream in Java?

A Stream in Java represents a sequence of elements that can be processed using functional-style operations. Unlike collections, streams do not store data. Instead, they operate on data provided by a source such as a list, set, or array. Streams are designed for processing data, while collections are designed for storing data.

Intermediate operations such as filter, map, and sorted return a new stream and do not trigger execution. Terminal operations such as forEach, collect, and reduce trigger the processing of the stream and produce a result or side effect. Streams are considered lazy because intermediate operations are not executed until a terminal operation is called. This allows Java to optimize performance by processing only the required elements.
```
import java.util.Arrays;
import java.util.List;

public class StreamExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        numbers.stream()
               .filter(n -> n % 2 == 0)
               .map(n -> n * 2)
               .forEach(System.out::println);
    }
}
```
Q5. Given a list of employee objects.

To filter employees by department, the filter stream operation is used. It allows selecting only those employees whose department matches a given condition. To transform employee names to uppercase, the map operation is used to convert each employee object into a modified value or extract and transform a specific field. To group employees by department, the Collectors.groupingBy() collector is used, which organizes employees into a map where the key is the department and the value is a list of employees.
```
import java.util.*;
import java.util.stream.Collectors;

class Employee {
    String name;
    String department;

    Employee(String name, String department) {
        this.name = name;
        this.department = department;
    }
}

public class EmployeeStreamExample {
    public static void main(String[] args) {
        List<Employee> employees = List.of(
            new Employee("Rahul", "IT"),
            new Employee("Amit", "HR"),
            new Employee("Neha", "IT"),
            new Employee("Priya", "Finance")
        );

        List<Employee> itEmployees =
            employees.stream()
                     .filter(e -> e.department.equals("IT"))
                     .toList();

        List<String> upperCaseNames =
            employees.stream()
                     .map(e -> e.name.toUpperCase())
                     .toList();

        Map<String, List<Employee>> groupedByDepartment =
            employees.stream()
                     .collect(Collectors.groupingBy(e -> e.department));
    }
}


```
