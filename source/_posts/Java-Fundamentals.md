---
title: Java Fundamentals
date: 2023-06-29 19:58:06
categories: [学习笔记]
tags: [FrontEndMasters, Java]
---

# Java Fundamentals

## Introduction

- [IntelliJ Community Edition](https://www.jetbrains.com/idea/download)
- Java 17 or later
  - IntellJ > Download JDK
- [GitHub repo](https://github.com/angiejones/frontendmasters-java-fundamentals)

## Conditions and Loops

<!-- more -->

### Java Basic Exercise

<details>
<summary>Gross Pay Calculator</summary>

```java
public class GrossPayCalculator {
    public static void main(String[] args) {

        System.out.println("How many hours did you work?");
        Scanner scanner = new Scanner(System.in);
        int hours = scanner.nextInt();

        System.out.println("What is your hourly pay rate?");
        double rate = scanner.nextDouble();
        scanner.close();

        double payRate = hours * rate;
        System.out.println("Gross pay: " + payRate);
    }
}
```

</details>

### If Statement

<details>
<summary>Salary Calculator</summary>

```java
/**
 * Salary Calculator
 * All sales people get a payment of $1000 for the week.
 * Salespeople who exceed 10 sales get an additional bonus of $250.
 */
public class SalaryCalculator {
    public static void main(String[] args) {
        System.out.println("How many sales do you exceed this week?");
        Scanner scanner = new Scanner(System.in);
        int salesCount = scanner.nextInt();
        scanner.close();

        double salary = 1000;
        double bonus = 250;
        int quota = 10;

        if(salesCount >= quota) {
            salary += bonus;
        }

        System.out.println("Your salary this week is: $" + salary);
    }
}
```

</details>

### If-Else Statement

<details>
<summary>Quota Calculator</summary>

```java
/**
 * Quota Calculator
 * All salespeople are expected to make at least 10 sales each week.
 * For those who do,
 * they receive a congratulatory message.
 * For those who don't,
 * they are informed of how many sales they were short.
 */
public class QuotaCalculator {
    public static void main(String[] args) {
        int quota = 10;

        Scanner scanner = new Scanner(System.in);
        System.out.println("How many sales this week?");
        int salesCount = scanner.nextInt();
        scanner.close();

        if (salesCount >= quota) {
            System.out.println("Congratulations! You finish the goal!");
        } else {
            System.out.println("You are still need " + (quota - salesCount) + " sales");
        }
    }
}
```

</details>

### Nested If Statement

<details>
<summary>Test Results</summary>

```java
public class TestResults {
    public static void main(String[] args) {
        System.out.println("Enter your score:");
        Scanner scanner = new Scanner(System.in);
        double score = scanner.nextDouble();
        scanner.close();

        char grade;

        if(score < 60) {
            grade = 'F';
        } else if(score < 70) {
            grade = 'D';
        } else if(score < 80) {
            grade = 'C';
        } else if(score < 90) {
            grade = 'B';
        } else  {
            grade = 'A';
        }

        System.out.println("Your grade is: " + grade);
    }
}
```

</details>

### Switch Expression

<details>
<summary>Switch Expression</summary>

```java
public class GradeMessage {
    public static void main(String[] args) {
        System.out.println("Enter your letter grade");
        Scanner scanner = new Scanner(System.in);
        String grade = scanner.next().toUpperCase();
        scanner.close();

        String message = switch (grade) {
            case "A", "B" -> "Excellent job!";
            case "C" -> {
                yield "Well done";
            }
            case "D" -> "You need to work a bit harder!";
            case "F" -> "Uh oh!";
            default -> "Error. Invalid grade.";
        };

        System.out.println(message);
    }
}
```

</details>

### Operators

- Relational Operators

| Operator | Description              | Example        |
| -------- | ------------------------ | -------------- |
| >        | Greater than             | 2 > 1 -> true  |
| >=       | Greater than or equal to | 2 >= 2 -> true |
| <        | Less than                | 1 < 2 -> true  |
| <=       | Less than or equal to    | 1 <= 1 -> true |
| ==       | Equal to                 | 1 == 1 -> true |
| !=       | Not equal to             | 1 != 2 -> true |

- Logical Operators

| Symbol | Operator | Example                 |
| ------ | -------- | ----------------------- |
| &&     | AND      | true && true -> true    |
| \|\|   | OR       | true \|\| false -> true |
| !      | NOT      | !true -> false          |

### While Loops

<details>

<summary>While Loop</summary>

```java
public class InputValidation {
    public static void main(String[] args) {
        double rate = 15;
        double maxHours = 40;

        System.out.println("How many hours did you work?");
        Scanner scanner = new Scanner(System.in);
        double workedHours = scanner.nextDouble();

        while (workedHours > maxHours || workedHours < 1) {
            System.out.println("Invalid entry. Your hours must be between 1 and 40. Try again.");
            workedHours = scanner.nextDouble();
        }
        scanner.close();
        System.out.println("Your pay is: $" + workedHours * rate + ".");
    }
}
```

</details>

### Do-While Loops

<details>
<summary>Do-While Loop</summary>

```java
public class AddNumbers {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        boolean again;

        do {
            System.out.println("Enter the first number:");
            double firstNumber = scanner.nextDouble();
            System.out.println("Enter the second number:");
            double secondNumber = scanner.nextDouble();
            double sum = firstNumber + secondNumber;
            System.out.println("The sum is: " + sum);
            System.out.println("Would you like to start over? Enter true or false");
            again = scanner.nextBoolean();
        } while(again);

        scanner.close();
    }
}
```

</details>

### For Loops

<details>
<summary>For Loop</summary>

```java
public class Cashier {
    public static void main(String[] args) {
        System.out.println("Enter the number of items you would like to scan:");
        Scanner scanner = new Scanner(System.in);
        int quantity = scanner.nextInt();

        double total = 0;

        for (int i = 0; i < quantity; i++) {
            System.out.println("Enter the cost of the item:");
            double price = scanner.nextDouble();
            total += price;
        }

        System.out.println("Your total is: $" + total);
        scanner.close();
    }
}
```

</details>

## Methods, Objects and Data Types

### Methods

<details>
<summary>Methods</summary>

```java
public class GreetUser {
    public static String askName() {
        System.out.println("What is your name?");
        Scanner scanner = new Scanner(System.in);
        String name = scanner.next();
        scanner.close();
        return name;
    }

    public static void greetUser(String name) {
        System.out.println("Hello " + name + "!");
    }

    public static void main(String[] args) {
        greetUser(askName());
    }
}
```

</details>

### Method Overloading

<details>
<summary>Method Overloading</summary>

```java
public class AreaCalculator {
    public static double area(double radius) {
        if (radius < 0) {
            return -1;
        }
        return Math.PI * radius * radius;
    }

    public static double area(double x, double y) {
        if (x < 0 || y < 0) {
            return -1;
        }
        return x * y;
    }

    public static void main(String[] args) {
        System.out.println(area(5.0));
        System.out.println(area(-1));
        System.out.println(area(5.0, 4.0));
        System.out.println(area(-1.0, 4.0));
    }
}
```

</details>

### Variable Scope

<details>
<summary>Variable Scope</summary>

```java
public class InstantCreditCheck {
    static int requiredSalary = 25000;
    static int requiredCreditScore = 700;
    static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        double salary = getSalary();
        int creditScore = getCreditScore();
        scanner.close();

        boolean qualified = isUserQualified(salary, creditScore);

        notifyUser(qualified);
    }

    public static void notifyUser(boolean qualified) {
        if(qualified) {
            System.out.println("Congrats! You've been approved.");
        } else {
            System.out.println("Sorry. You've been declined.");
        }
    }

    public static double getSalary() {
        System.out.println("Enter your salary:");
        return scanner.nextDouble();
    }

    public static int getCreditScore() {
        System.out.println("Enter your credit score:");
        return scanner.nextInt();
    }

    public static boolean isUserQualified(double salary, int creditScore) {
        return salary >= requiredSalary && creditScore >= requiredCreditScore;
    }
}
```

</details>

### Objects and Encapsulation

- Encapsulation
  - All data and behaviors is contained within the object itself.
  - Expose behavior and restrict direct access to data.

<details>

<summary>Encapsulation</summary>

```java
public class Rectangle {
    private double length;
    private double width;
    private int sides = 4;

    public static void main(String[] args) {

    }

    public double calculateArea() {
        return length * width;
    }

    public double calculatePerimeter() {
        return (length * 2) + (width * 2);
    }


    public double getLength() {
        return length;
    }

    public void setLength(double length) {
        this.length = length;
    }

    public double getWidth() {
        return width;
    }

    public void setWidth(double width) {
        this.width = width;
    }

    public int getSides() {
        return sides;
    }

    public void setSides(int sides) {
        this.sides = sides;
    }
}
```

</details>

### Constructors

<details>
<summary>Constructors</summary>

```java
public class Rectangle {
    private double length;
    private double width;
    private int sides = 4;

    public static void main(String[] args) {

    }

    public Rectangle() {
        this.length = 0;
        this.width = 0;
    }

    public Rectangle(double length, double width, int sides) {
        this.length = length;
        this.width = width;
        this.sides = sides;
    }

    public double calculateArea() {
        return length * width;
    }

    public double calculatePerimeter() {
        return (length * 2) + (width * 2);
    }
}
```

</details>

### Instantiating Objects

<details>
<summary>Instantiating Objects</summary>

```java
public class HomeAreaCalculator {
    public static void main(String[] args) {
        Rectangle room1 = new Rectangle();
        room1.setLength(25);
        room1.setWidth(50);
        double areaOfRoom1 = room1.calculateArea();

        Rectangle room2 = new Rectangle(30, 75, 4);
        double areaOfRoom2 = room2.calculateArea();

        double totalArea = areaOfRoom1 + areaOfRoom2;
        System.out.println("Area of both rooms: " + totalArea);
    }
}
```

</details>

### Sending and Receiving Objects

<details>
<summary>Sending and Receiving Objects</summary>

```java
public class HomeAreaCalculatorRedo {
    private final Scanner scanner = new Scanner(System.in);


    public static void main(String[] args) {
        HomeAreaCalculatorRedo calculator = new HomeAreaCalculatorRedo();
        Rectangle kitchen = calculator.getRoom();
        Rectangle bathroom = calculator.getRoom();
        double totalArea = calculator.calculateTotalArea(kitchen, bathroom);
        System.out.println("The total area is: " + totalArea);
        calculator.scanner.close();
    }

    public Rectangle getRoom() {
        System.out.println("What is the length of the room?");
        var length = scanner.nextDouble();
        System.out.println("What is the width of the room?");
        var width = scanner.nextDouble();

        return new Rectangle(length, width, 4);
    }

    public double calculateTotalArea(Rectangle room1, Rectangle room2) {
        return room1.calculateArea() + room2.calculateArea();
    }
}
```

</details>

### Records and Wrapper Classes

<details>
<summary>Records and Wrapper Classes</summary>

```java
public record Person(String name, int age) {
    public static void main(String[] args) {
        Person person = new Person("John", 30);
        System.out.println(person.name());
        System.out.println(person.age());
    }
}
```

```java
public class WrapperClasses {
    public static void main(String[] args) {
        int myInt = 10;
        Integer myInteger = 10;
        System.out.println(myInt);
        System.out.println(myInteger);
    }
}
```

| Primitive Type | Wrapper Class |
| -------------- | ------------- |
| byte           | Byte          |
| int            | Integer       |
| double         | Double        |
| float          | Float         |
| char           | Character     |
| boolean        | Boolean       |
| long           | Long          |
| short          | Short         |

## Arrays and Text Processing

### Arrays

```java
int[] numbers = new int[5];

int[] numbers = {1, 2, 3, 4, 5};
```

### Random Number Array

<details>
<summary>Random Number Array</summary>

```java
public class LotteryTicket {
    private static final int LENGTH = 6;
    private static final int MAX_TICKET_NUMBER = 69;

    public static void main(String[] args) {
        int[] ticket = getTicket();
        printTicket(ticket);
    }

    public static int[] getTicket() {
        return genRandomNumbers();
    }

    private static int[] genRandomNumbers() {
        int[] ticket = new int[LENGTH];

        Random random = new Random();

        for (int i = 0; i < LENGTH; i++) {
            int randomNumber;

            do {
                randomNumber = random.nextInt(1, MAX_TICKET_NUMBER + 1);
            } while (search(ticket, randomNumber));

            ticket[i] = randomNumber;
        }

        return ticket;
    }

    private static boolean search(int[] array, int number) {
        for (int value : array) {
            if (value == number) {
                return true;
            }
        }

        return false;
    }

    public static void printTicket(int[] tickets) {
       for(int ticketNumber : tickets) {
           System.out.print(ticketNumber + " | ");
       }
    }
}
```

</details>

### Strings

<details>
<summary>Strings</summary>

```java
public class WordCount {

    public static void main(String[] args) {
        String sentence = "The quick brown fox jumps over the lazy dog.";
        countWords(sentence);
    }


    public static void countWords(String sentence) {
        var words = sentence.split(" ");
        printWords(words);
    }

    public static void printWords(String[] words) {
        int wordCount = words.length;
        String message = String.format("Your sentence contains %d words:", wordCount);
        System.out.println(message);
        for (String word : words) {
            System.out.println(word);
        }
    }
}
```

</details>

### StringBuilder

<details>
<summary>StringBuilder</summary>

```java
public class BackwardsString {
    public static void main(String[] args) {
        String string = "Hello World!";
        System.out.println(reverseString(string));
    }

    public static String reverseString(String string) {
        StringBuilder reversedString = new StringBuilder();
        for (int i = string.length() - 1; i >= 0; i--) {
            reversedString.append(string.charAt(i));
        }
        return reversedString.toString();
    }
}
```

</details>

### Jumbled String

<details>

<summary>Jumbled String</summary>

```java
public class JumbledString {
    public static void main(String[] args) {
        String string = "HelloWorld!";
        System.out.println(jumbleString(string));
    }

    public static String jumbleString(String string) {
        StringBuilder jumbledString = new StringBuilder();

        for(int i = 0; i < string.length(); i++) {
            if(i != 0 && Character.isUpperCase(string.charAt(i))) {
                jumbledString.append(" ");
            }
            jumbledString.append(string.charAt(i));
        }

        return jumbledString.toString();
    }
}
```

</details>

### Text Blocks

```java
public class TextBlocks {
    public static void main(String[] args) {
        String textBlock = """
                [
                    {
                        "name": "John",
                        "age": 30
                    },
                    {
                        "name": "Jane",
                        "age": 25
                    }
                ]
                """;
        System.out.println(textBlock);
    }
}
```

## Inheritance

### Inheritance

<details>
<summary>Inheritance</summary>

```java
public class Person {
    private String name;
    private int age;
    private String gender;

    public Person() {
        this.setName("Unknown");
        this.setAge(0);
        this.setGender("Unknown");
    }

    public Person(String name, int age, String gender) {
        this.setName(name);
        this.setAge(age);
        this.setGender(gender);
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }
}

public class Employee extends Person {
    private String employeeId;
    private String title;

    public Employee() {
        super();
        this.employeeId = "Unknown";
        this.title = "Unknown";
    }

    public Employee(
            String name,
            int    age,
            String gender,
            String employeeId,
            String title
    ) {
        this.setName(name);
        this.setAge(age);
        this.setGender(gender);
        this.setEmployeeId(employeeId);
        this.setTitle(title);
    }

    public String getEmployeeId() {
        return employeeId;
    }

    public void setEmployeeId(String employeeId) {
        this.employeeId = employeeId;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }
}
```

</details>

### Overriding Parameters

<details>
<summary>Overriding Parameters</summary>

```java
public class Square extends Rectangle {
    @Override
    public void setLength(double length) {
        super.setLength(length);
        super.setWidth(length);
    }
}
```

</details>

### Overloading Methods

<details>

<summary>Overloading Methods</summary>

```java
public class Parent {
    public void print(String message) {
        System.out.println(message);
    }
}

public class Child extends Parent {
    public void print(int number) {
        System.out.println(number);
    }
}
```

</details>

### Chain of Inheritance

<details>

<summary>Chain of Inheritance</summary>

```java
public class Human {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

public class Employee extends Human {
    private String employeeId;

    public String getEmployeeId() {
        return employeeId;
    }

    public void setEmployeeId(String employeeId) {
        this.employeeId = employeeId;
    }
}

public class Manager extends Employee {
    private String department;

    public String getDepartment() {
        return department;
    }

    public void setDepartment(String department) {
        this.department = department;
    }
}
```

</details>

### Limited Access and Access Modifier

- Limited Access in Inheritance. > 继承中的有限访问。
  - constructors are not inherited. > 构造函数不会被继承。
  - public and protected members are inherited. > 公共和受保护的成员被继承。
  - private members are not inherited. > 私有成员不会被继承。
  - final members are inherited but cannot be overridden. > 继承最终成员，但不能被覆盖。

### Sealed Classes

- classes that restrict inheritance to specific classes. > 限制继承到特定类的类。

```java
public sealed class Shape permits Rectangle, Circle { }

public sealed class Rectangle extends Shape permits Square { }

public final class Square extends Rectangle { }

public non-sealed class Circle extends Shape { }
```

- Sealed classes
  - sealed classes grant inheritance permission. > 密封类授予继承权限。
  - permitted subclass must extend sealed class. > 允许的子类必须扩展密封类。
  - permitted subclass must be sealed/non-sealed or final. > 允许的子类必须是密封/非密封或最终的。

## Polymorphism & Abstraction

### Polymorphism

<details>

<summary>Polymorphism</summary>

```java
public class Animal {
    public void makeSound() {
        System.out.println("Animal making sound");
    }
}

public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Woof");
    }

    public void fetch() {
        System.out.println("Fetching...");
    }
}

public class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Meow");
    }

    public void scratch() {
        System.out.println("Scratching...");
    }
}
```

</details>

### Type Casting

```java
Animal animal = new Dog();
animal.fetch(); // error
((Dog) animal).fetch(); // works
```

### instanceof Operator

```java
Animal animal = new Dog();
if(animal instanceof Dog) {
    Dog dog = (Dog) animal;
    dog.fetch();
}
```

### instanceof Pattern Matching

```java
Animal animal = new Dog();
if(animal instanceof Dog animalTheDog) {
    animalTheDog.fetch();
}
// same as
if(animal instanceof Dog) {
    Dog dog = (Dog) animal;
    dog.fetch();
}
```

### Abstraction

<details>

<summary>Abstraction</summary>

```java
public abstract class Animal {
    public abstract void makeSound();
}

public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Woof");
    }

    public void fetch() {
        System.out.println("Fetching...");
    }
}
```

</details>

- Abstraction
  - abstract classes and methods are templates. > 抽象类和方法是模板。
  - declare using abstract reserved word. > 使用 abstract 保留字声明。
  - subclasses must implement abstract methods. > 子类必须实现抽象方法。
  - abstract classes cannot be instantiated. > 抽象类不能被实例化。

### Interfaces

- a stateless construct with abstract behaviors. > 一个没有状态的结构，具有抽象的行为。

<details>

<summary>Interfaces</summary>

```java
public interface Animal {
    public void makeSound();
}

public class Dog implements Animal {
    @Override
    public void makeSound() {
        System.out.println("Woof");
    }

    public void fetch() {
        System.out.println("Fetching...");
    }
}
```

</details>

### Multiple Inheritance

- classes can implement multiple interfaces to achieve multiple inheritance. > 类可以实现多个接口以实现多重继承。

```java
public interface Animal {
    public void makeSound();
}

public interface Hunter {
    public void hunt();
}

public class Dog implements Animal, Hunter {
    @Override
    public void makeSound() {
        System.out.println("Woof");
    }

    @Override
    public void hunt() {
        System.out.println("Hunting...");
    }

    public void fetch() {
        System.out.println("Fetching...");
    }
}
```

<Details>

<summary>Default Methods</summary>

```java
public interface Animal {
    public void makeSound();

    default void eat() {
        System.out.println("Eating...");
    }
}
```

</Details>

- Static methods

  - interface methods that contain implementation but are not inherited by implementing classes. > 接口方法包含实现但不被实现类继承。

- Interfaces
  - cannot be instantiated. > 不能被实例化。
  - implemented by classes, extended by other interfaces. > 由类实现，由其他接口扩展。
  - implementing non-abstract class must implement all abstract methods. > 实现非抽象类必须实现所有抽象方法。
  - methods are public and abstract by default. > 方法默认为 public 和 abstract。
  - default and static methods allowed. > 允许默认和静态方法。

## Data Structures

### Set

- Collections Framework

  - unified architecture that provides data structures and algorithms to manipulate them. > 统一的架构，提供数据结构和算法来操作它们。

- Common Collections

  - Set
  - List
  - Queue
  - Map

- Set
  - unordered collections of unique objects

```java
Set<String> set = new HashSet<>();
set.add("dog");
set.add("cat");
set.add("mouse");

System.out.println(set); // [cat, dog, mouse]

set.contains("cat"); // true
set.remove("mouse"); // true
set.size(); // 2

Set moreAnimals = Set.of("dog", "cat", "mouse"); // immutable
moreAnimals.add("bird"); // error
```

### List

- List
  - ordered elements allowing duplicates
  - accessible by position

```java
List<String> list = new ArrayList<>();
list.add("dog");
list.add("cat");
list.add("mouse");

System.out.println(list); // [dog, cat, mouse]
System.out.println(list.get(1)); // cat

list.set(1, "bird");
System.out.println(list); // [dog, bird, mouse]

list.remove(2);
System.out.println(list); // [dog, bird]

list.size(); // 2

List moreAnimals = List.of("dog", "cat", "mouse"); // immutable
```

### Queue

- Queue
  - ordered elements allowing duplicates
  - accessible by position
  - FIFO

```java
Queue<String> queue = new LinkedList<String>();
queue.add("dog");
queue.add("cat");
queue.add("mouse");

System.out.println(queue); // [dog, cat, mouse]
System.out.println(queue.peek()); // dog

System.out.println(queue.remove()); // dog
System.out.println(queue); // [cat, mouse]

queue.size(); // 2
```

### Map

- Map
  - unique key-value pairs
  - unordered

```java
Map<String, String> map = new HashMap<String, String>();
map.put("dog", "dog is a dog");
map.put("cat", "cat is a cat");
map.put("mouse", "mouse is a mouse");

map.putIfAbsent("mouse", "mouse is a mouse"); // won't add

System.out.println(map); // {cat=cat is a cat, dog=dog is a dog, mouse=mouse is a mouse}

System.out.println(map.get("cat")); // cat is a cat

map.remove("mouse");

System.out.println(map); // {cat=cat is a cat, dog=dog is a dog}

map.size(); // 2

Map moreAnimals = Map.of(
    "dog", "dog is a dog",
    "cat", "cat is a cat",
    "mouse", "mouse is a mouse"
); // immutable
```

### Collection Iterators

- Iterators
  - allow looping over collections
  - hasNext() and next() methods

```java
List<String> list = new ArrayList<>();
list.add("dog");
list.add("cat");
list.add("mouse");

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    System.out.println(iterator.next());
}
```

- Access to Iterator

  - Set
  - List
  - Queue

- EntrySet
  - Map

```java
Map<String, String> map = new HashMap<String, String>();
map.put("dog", "dog is a dog");
map.put("cat", "cat is a cat");
map.put("mouse", "mouse is a mouse");

Iterator<Map.Entry<String, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
    Map.Entry<String, String> entry = iterator.next();
    System.out.println(entry.getKey() + " : " + entry.getValue());
}
```

### Iterating with Enhanced For Loop

```java
for(Map.Entry<String, String> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " : " + entry.getValue());
}
```

### Iterating with forEach()

```java
map.forEach((k, v) -> System.out.println(k + " : " + v));

list.forEach(animal -> System.out.println(animal));
list.forEach(System.out::println);
```

## Functional Interfaces and Streams

### Functional Interfaces

- Functional Interfaces
  - single abstract method
  - used as assignment targets for lambda expressions or method references
  - java.util.function

```java
List countries = List.of("USA", "China");
Consumer print = c -> System.out.println(c);
countries.forEach(print);
```

```java
Consumer<String> print = c -> System.out.println(c);
print.accept("Hello world!");

Supplier<String> get = () -> "Hello world!";
System.out.println(get.get());
```

| Interface      | Description                                | Abstract Method   |
| -------------- | ------------------------------------------ | ----------------- |
| Consumer       | accepts single argument, returns no result | void accept(T t)  |
| Supplier       | no argument, returns a result              | T get()           |
| Predicate      | single argument, returns boolean           | boolean test(T t) |
| Function       | single argument, returns a result          | R apply(T t)      |
| UnaryOperator  | single argument, returns a result          | R apply(T t)      |
| BinaryOperator | two arguments, returns a result            | R apply(T t, U u) |

```java
@FunctionalInterface
public interface Function<T, R> {
    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }
}
```

```java
Function<Integer, Integer> add = x -> x + 1;
Function<Integer, Integer> multiply = x -> x * 2;

Function<Integer, Integer> addAndMultiply = add.andThen(multiply);
```

```java
@FunctionalInterface
public interface Predicate<T> {
    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }
}
```

### Streams

- Streams
  - sequence of elements supporting sequential and parallel aggregate operations
  - java.util.stream
  - lazy evaluation

```java
int[] numbers = {1, 2, 3, 4, 5, 6};
Arrays.stream(numbers)
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .forEach(System.out::println);
// 4, 16, 36
```

```java
int[] numbers = {1, 2, 3, 4, 5, 6};
Arrays.stream(numbers).parallel()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .forEach(System.out::println);
// 16, 4, 36 (order not guaranteed)
```

- Stream operations
  - Intermediate
    - Performs operation and return result stream
  - Terminal
    - return a result and close the stream

### anyMatch, allMatch and filter

```java
List<String> countries = List.of("USA", "China", "France", "Germany", "Japan");
boolean anyMatch = countries.stream().anyMatch(c -> c.equals("France"));
boolean allMatch = countries.stream().allMatch(c -> c.length() > 2);
List<String> filtered = countries.stream().filter(c -> c.length() > 2).collect(Collectors.toList());
```

```java
Map<String, String> map = new HashMap<String, String>();
map.put("dog", "dog is a dog");
map.put("cat", "cat is a cat");
map.put("mouse", "mouse is a mouse");

HashMap<String, String> filtered = map.entrySet()
    .stream()
    .filter(e -> e.getKey().length() ashMap> 3)
    .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue, (e1, e2) -> e1, HashMap::new));
```

### map and reduce

```java
List<String> countries = List.of("USA", "China", "France", "Germany", "Japan");

List<String> upperCaseCountries = countries.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);
int sum = numbers.stream().reduce(0, (a, b) -> a + b);
```

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);
int sum = numbers.stream().reduce(0, Integer::sum);
```

### collect

```java
List<String> countries = List.of("USA", "China", "France", "Germany", "Japan");
String joined = countries.stream().map(String::toUpperCase).collect(Collectors.joining(", "));
```

## Exceptions

### Handling Exceptions

```java
// ArrayIndexOutOfBoundsException
int[] numbers = {1, 2, 3};
System.out.println(numbers[3]);
```

<details>

<summary>Create a file</summary>

```java
public class CreateFile {
    public static void main(String[] args) {
        try {
            File file = new File("test.txt");
            if (file.createNewFile()) {
                System.out.println("File created: " + file.getName());
            } else {
                System.out.println("File already exists.");
            }
        } catch (IOException e) {
            System.out.println("An error occurred.");
            e.printStackTrace();
        }
    }
}
```

### Checked and Unchecked Exceptions

- Checked Exceptions
  - Exceptions that are checked at compile time

```java
public boolean createNewFile() throws IOException {
    // ...
}
```

- Unchecked Exceptions
  - Exceptions that are checked at runtime

```java
public double nextDouble() {
    // ...
}

Scanner scanner = new Scanner(System.in);
double number = scanner.nextDouble();
// if input is not a double, InputMismatchException is thrown
```

- Checked vs Unchecked Exceptions
  - Checked Exceptions
    - used when there's a possibility of recovery
  - Unchecked Exceptions
    - used when there's not anything that can be done in the event of the error

### Handling Multiple Exceptions

- Polymorphism
- Multiple catch blocks
- Catch multiple in single block

- Polymorphism

```java
// Class ArrayIndexOutOfBoundsException
/**
 * Java.lang.Object
 *   java.lang.Throwable
 *     java.lang.Exception
 *       java.lang.RuntimeException
 *         java.lang.IndexOutOfBoundsException
 *           java.lang.ArrayIndexOutOfBoundsException
 */
// All implemented interfaces:
// Serializable, Cloneable, Throwable
try {
    int[] numbers = {1, 2, 3};
    System.out.println(numbers[3]);
} catch (Exception e) { // Will catch all exceptions that are subclasses of Exception
    System.out.println("Something went wrong.");
}
```

- Multiple catch blocks

```java
public class MultipleCatchBlocks {
    public static void main(String[] args) {
        File file = new File("test.txt");
        try {
            Scanner scanner = new Scanner(file);
            while (scanner.hasNextLine()) {
                int number = Integer.parseInt(scanner.nextLine());
                System.out.println(number);
            }
            scanner.close();
        } catch (FileNotFoundException e) {
            file.createNewFile();
            System.out.println("File created.");
        } catch (IOException e) {
            System.out.println("An error occurred.");
            e.printStackTrace();
        } catch (NumberFormatException e) {
            System.out.println("Cannot parse integer.");
        } catch (Exception e) {
            System.out.println("Something went wrong.");
        } finally {
            System.out.println("Finally block.");
        }
    }
}
```

- Catch multiple in single block

```java
public class CatchMultipleErrorInSingleBlock {
    public static void main(String[] args) {
        File file = new File("test.txt");
        try {
            Scanner scanner = new Scanner(file);
            while (scanner.hasNextLine()) {
                int number = Integer.parseInt(scanner.nextLine());
                System.out.println(number);
            }
            scanner.close();
        } catch (FileNotFoundException | NumberFormatException e) {
            System.out.println("Something went wrong.");
        }
    }
}
```

### Closing the File Reader in Finally Block

```java
public class ClosingFileReader {
    public static void main(String[] args) {
        File file = new File("test.txt");
        Scanner scanner = null;
        try {
            scanner = new Scanner(file);
            while (scanner.hasNextLine()) {
                int number = Integer.parseInt(scanner.nextLine());
                System.out.println(number);
            }
        } catch (FileNotFoundException e) {
            System.out.println("File not found.");
        } finally {
            scanner.close();
            System.out.println("Finally block.");
        }
    }
}
```

```java
public class ClosingFileReaderAutomatically {
    public static void main(String[] args) {
        File file = new File("test.txt");
        try (Scanner scanner = new Scanner(file)) {
            while (scanner.hasNextLine()) {
                int number = Integer.parseInt(scanner.nextLine());
                System.out.println(number);
            }
        } catch (FileNotFoundException e) {
            System.out.println("File not found.");
        }
    }
}
```

### Print to File

```java
public class PrintToFile {
    public static void main(String[] args) {
        try (PrintWriter writer = new PrintWriter("test.txt")) {
            writer.println("Hello");
            writer.println("World");
        } catch (FileNotFoundException e) {
            System.out.println("File not found.");
        }
}
```

### Throwing Exceptions

```java
public class ThrowingExceptions {
   public static double divide(double a, double b) throws NavigateInputException {
       if (b == 0) {
           throw new NavigateInputException("Cannot divide by zero.");
       }
       if (a < 0 || b < 0) {
           throw new NavigateInputException();
       }
       return a / b;
   }
}
```

```java
public class NegativeInputException extends Exception {
    public NegativeInputException(String message) {
        super(message);
    }

    public NegativeInputException() {
        this("Input must be greater than zero.");
    }
}
```

### Re-throwing Exceptions

```java
public class RethrowingExceptions {
    public static void main(String[] args) {
        try {
            divide(10, 0);
        } catch (NavigateInputException e) {
            System.out.println(e.getMessage());
        }
    }

    public static double divide(double a, double b) throws NavigateInputException {
        if (b == 0) {
            throw new NavigateInputException("Cannot divide by zero.");
        }
        if (a < 0 || b < 0) {
            throw new NavigateInputException();
        }
        return a / b;
    }
}
```

## Wrapping up

[Angie's website](https://angiejones.tech/)
