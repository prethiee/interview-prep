# Day 1 — PHP Fundamentals and Object-Oriented Programming

## 1. Classes and Objects

A **class** is a blueprint that defines properties and methods. An **object** is an instance of a class.

```php
class Car {
    public string $brand;

    public function drive(): void {
        echo "The car is moving";
    }
}

$car = new Car();
$car->brand = "Toyota";
$car->drive();
```

- `class Car` defines a class.
- `$brand` is a property.
- `drive()` is a method.
- `new Car()` creates an object.
- `->` accesses an object's properties and methods.

## 2. Properties and Methods

- **Property:** A variable declared inside a class that stores an object's data.
- **Method:** A function declared inside a class that defines its behavior.

```php
class Employee {
    public string $name = "Preethi";

    public function introduce(): void {
        echo "Hello, " . $this->name;
    }
}

$employee = new Employee();
$employee->introduce();
```

### What is `$this`?

`$this` refers to the current object inside a non-static instance method.

`$this->name` accesses the `name` property of the current object.

## 3. Access Modifiers

PHP provides three main visibility levels.

| Modifier    | Accessibility                                             |
| ----------- | --------------------------------------------------------- |
| `public`    | Accessible from anywhere the object or class is available |
| `protected` | Accessible inside the class and its subclasses            |
| `private`   | Accessible only inside the class that declares it         |

```php
class User {
    public string $name = "John";
    protected string $role = "Developer";
    private string $password = "secret";
}
```

Use `private` and `protected` to control access to internal implementation details.

## 4. Constructors

A constructor is a special method that runs automatically when an object is created.

```php
class Employee {
    public string $name;

    public function __construct(string $name) {
        $this->name = $name;
    }
}

$employee = new Employee("Preethi");

echo $employee->name;
```

Output:

```text
Preethi
```

The constructor initializes an object with the supplied values.

## 5. Variables, Parameters, and Arguments

These three terms are related but have different meanings.

- **Variable:** A named storage location that holds a value.
- **Parameter:** A variable declared in a function or method definition. It acts as a placeholder that receives a value.
- **Argument:** The actual value or expression passed when calling a function or method.

```php
function greet(string $name): void {
    echo "Hello, " . $name;
}

$userName = "Preethi";

greet($userName);
```

In this example:

- `$userName` is a variable.
- `$name` is a parameter.
- `$userName` passed to `greet()` is the argument expression; its value is `"Preethi"`.

Remember: **the parameter is the placeholder; the argument is what you pass to it.**

## 6. Inheritance

Inheritance allows one class to reuse and extend another class.

```php
class Animal {
    public function eat(): void {
        echo "Eating";
    }
}

class Dog extends Animal {
    public function bark(): void {
        echo "Barking";
    }
}

$dog = new Dog();
$dog->eat();
$dog->bark();
```

`Dog` inherits the `eat()` method from `Animal`.

PHP classes support single class inheritance: a class can extend only one parent class.

## 7. Method Overriding

Method overriding happens when a child class provides its own implementation of an inherited method.

```php
class Animal {
    public function makeSound(): void {
        echo "Animal sound";
    }
}

class Dog extends Animal {
    public function makeSound(): void {
        echo "Bark";
    }
}

$dog = new Dog();
$dog->makeSound();
```

Output:

```text
Bark
```

The child implementation is used when the method is called on a `Dog` object.

## 8. Interface, Abstract Class, and Trait

These are three important PHP concepts, but they serve different purposes.

### 8.1 Interface — contract

An **interface defines which public methods a class must implement, without providing their implementation**. A class can implement multiple interfaces.

```php
interface NotificationInterface {
    public function send(string $message): void;
}

class EmailNotification implements NotificationInterface {
    public function send(string $message): void {
        echo "Email: " . $message;
    }
}

class SmsNotification implements NotificationInterface {
    public function send(string $message): void {
        echo "SMS: " . $message;
    }
}
```

Both classes follow the same contract but implement it differently.

Key points:

- Use `interface` to declare the contract.
- Use `implements` when a class adopts an interface.
- A class can implement multiple interfaces.
- Interfaces can declare public method signatures without supplying their implementation.

### 8.2 Abstract class — shared foundation

An **abstract class can contain both implemented and abstract methods, as well as properties**. A class can extend only one class in PHP. The concrete subclass must implement all inherited abstract methods that are still unimplemented.

An abstract class cannot be instantiated directly; you cannot create an object from it.

```php
abstract class Notification {
    protected string $recipient;

    public function __construct(string $recipient) {
        $this->recipient = $recipient;
    }

    abstract public function send(string $message): void;

    public function getRecipient(): string {
        return $this->recipient;
    }
}

class EmailNotification extends Notification {
    public function send(string $message): void {
        echo "Sending email to " . $this->recipient;
    }
}

$email = new EmailNotification("user@example.com");
$email->send("Hello");
```

Key points:

- Use `abstract class` to define a shared foundation.
- An abstract class can have properties, constructors, concrete methods, and abstract methods.
- An abstract method declares the method signature but has no implementation in the abstract class.
- A concrete subclass must implement any inherited abstract methods that remain unimplemented.
- You cannot instantiate the abstract class directly using `new Notification()`.

### 8.3 Trait — code reuse

A **trait allows methods and properties to be reused across multiple classes without inheritance**. A class can use multiple traits, subject to resolving any naming conflicts.

```php
trait LoggerTrait {
    public function log(string $message): void {
        echo $message;
    }
}

class EmailNotification {
    use LoggerTrait;
}

class OrderService {
    use LoggerTrait;
}

$email = new EmailNotification();
$email->log("Email sent successfully");

$order = new OrderService();
$order->log("Order processed");
```

Key points:

- Use `trait` to declare reusable code.
- Use `use` inside a class to include a trait.
- Multiple traits can be used in one class.
- Traits are not standalone classes and cannot be instantiated directly.

### 8.4 Quick comparison

| Feature                          | Interface                                           | Abstract class    | Trait           |
| -------------------------------- | --------------------------------------------------- | ----------------- | --------------- |
| Main purpose                     | Contract                                            | Shared foundation | Code reuse      |
| Can contain implemented methods? | No method implementations in the interface contract | Yes               | Yes             |
| Can declare properties?          | No instance properties                              | Yes               | Yes             |
| Can be instantiated directly?    | No                                                  | No                | No              |
| How a class uses it              | `implements`                                        | `extends`         | `use`           |
| Multiple allowed?                | Multiple interfaces                                 | One parent class  | Multiple traits |

### 8.5 When to use them in Drupal

- **Interface:** Useful for defining a common contract for services or plugins. Different implementations can follow the same API.
- **Abstract class:** Useful for sharing common implementation among related plugins, often alongside Drupal's plugin base classes.
- **Trait:** Useful for reusing a focused set of methods across otherwise unrelated classes.

For example, several Drupal plugins may share a base class for common behavior, implement an interface to guarantee certain methods, and use a trait for a small reusable capability.

## 9. Static Methods and Properties

Static members belong to the class rather than an individual object.

```php
class Calculator {
    public static function add(int $a, int $b): int {
        return $a + $b;
    }
}

echo Calculator::add(10, 20);
```

Output:

```text
30
```

Key points:

- Use `static` to declare a static method or property.
- Use `ClassName::methodName()` to call a static method.
- `::` is called the scope resolution operator.
- A static method does not have an instance `$this` context.

In Drupal, services are generally accessed through dependency injection rather than by relying on static calls to service implementations.

## 10. Encapsulation

Encapsulation means controlling access to an object's internal state and exposing only the operations that should be available.

```php
class BankAccount {
    private float $balance = 0;

    public function deposit(float $amount): void {
        if ($amount <= 0) {
            throw new InvalidArgumentException(
                "Amount must be positive"
            );
        }

        $this->balance += $amount;
    }

    public function getBalance(): float {
        return $this->balance;
    }
}

$account = new BankAccount();
$account->deposit(500);

echo $account->getBalance();
```

The balance is private, so callers cannot modify it directly. The `deposit()` method validates changes before updating it.

## 11. Polymorphism

Polymorphism allows different classes to be used through the same interface or parent type while providing different behavior.

```php
interface PaymentInterface {
    public function pay(float $amount): void;
}

class CardPayment implements PaymentInterface {
    public function pay(float $amount): void {
        echo "Paid by card: " . $amount;
    }
}

class UpiPayment implements PaymentInterface {
    public function pay(float $amount): void {
        echo "Paid by UPI: " . $amount;
    }
}

function processPayment(
    PaymentInterface $payment,
    float $amount
): void {
    $payment->pay($amount);
}

processPayment(new CardPayment(), 500);
processPayment(new UpiPayment(), 300);
```

The same `processPayment()` function works with different implementations.

In Drupal, this concept is useful when working with pluggable systems and services that follow common interfaces.

## 12. Type Declarations and Return Types

Type declarations communicate what types a method expects and returns.

```php
function calculateTotal(int $price, int $quantity): int {
    return $price * $quantity;
}

echo calculateTotal(100, 3);
```

- `int $price` declares the expected parameter type.
- `int $quantity` declares the expected parameter type.
- `: int` declares the return type.

Type declarations help make code easier to understand and catch many mistakes. PHP's coercion behavior depends on the types involved and whether strict typing is enabled.

## 13. Exception Handling

Exceptions represent errors or exceptional situations that code can throw and handle.

```php
function divide(int $a, int $b): float {
    if ($b === 0) {
        throw new InvalidArgumentException(
            "Cannot divide by zero"
        );
    }

    return $a / $b;
}

try {
    echo divide(10, 0);
} catch (InvalidArgumentException $exception) {
    echo $exception->getMessage();
}
```

Key points:

- `throw` raises an exception.
- `try` contains code that may throw an exception.
- `catch` handles a matching exception.
- `getMessage()` returns the exception message.

In Drupal, use appropriate exception handling around integrations and operations that can fail. Do not silently swallow errors that should be logged or reported.

## 14. Arrays

Arrays store multiple values.

### Indexed array

```php
$fruits = ["Apple", "Banana", "Orange"];

echo $fruits[0];
```

Output: `Apple`

### Associative array

```php
$user = [
    "name" => "Preethi",
    "role" => "Developer",
];

echo $user["name"];
```

Output: `Preethi`

Indexed arrays use numeric keys; associative arrays use named or other explicit keys.

## 15. Loops

Loops repeat code.

### `foreach`

Use `foreach` to iterate over arrays or other iterable values.

```php
$numbers = [10, 20, 30];

foreach ($numbers as $number) {
    echo $number . PHP_EOL;
}
```

### `for`

Use `for` when a loop needs an initialization, condition, and update expression.

```php
for ($i = 0; $i < 3; $i++) {
    echo $i . PHP_EOL;
}
```

## 16. Functions and Methods

A function is a reusable block of code. A method is a function declared inside a class.

```php
function add(int $a, int $b): int {
    return $a + $b;
}

$result = add(10, 20);
echo $result;
```

### `echo` vs `return`

- `echo` displays output.
- `return` sends a result back to the caller.

Use `return` when a function should calculate a value for another part of the program to use.

## 17. Coding Exercise — Calculate an Array Sum

### Problem

Calculate the sum of all numbers in an array.

### Incorrect attempt

```php
$numbers = [10, 20, 30, 40];
$total = 0;

foreach ($numbers as $number) {
    $total += $total + $number;
}

echo $total;
```

The issue is that `$total` is added to itself on every iteration. This repeatedly doubles the accumulated sum instead of simply adding the next number.

### Correct solution

```php
$numbers = [10, 20, 30, 40];
$total = 0;

foreach ($numbers as $number) {
    $total += $number;
}

echo $total;
```

Output:

```text
100
```

### Reusable function

```php
function calculateSum(array $numbers): int {
    $total = 0;

    foreach ($numbers as $number) {
        $total += $number;
    }

    return $total;
}

echo calculateSum([10, 20, 30, 40]);
```

The function accepts an array, adds each number to the running total, and returns the final result.

## 18. Day 1 Revision Checklist

- [ ] Classes and objects
- [ ] Properties and methods
- [ ] `$this` and constructors
- [ ] Public, protected, and private visibility
- [ ] Inheritance and method overriding
- [ ] Interface, abstract class, and trait
- [ ] Variables, parameters, and arguments
- [ ] Static methods and properties
- [ ] Encapsulation and polymorphism
- [ ] Type declarations and return types
- [ ] Exception handling
- [ ] Indexed and associative arrays
- [ ] `foreach` and `for` loops
- [ ] Functions, methods, `echo`, and `return`
- [ ] Array-sum coding exercise

## 19. Key Interview Takeaways

1. An interface defines a contract; an abstract class provides a shared foundation; a trait enables code reuse.
2. A concrete class can extend only one class but can implement multiple interfaces and use multiple traits.
3. An abstract class cannot be instantiated directly.
4. A parameter is declared in a function definition; an argument is supplied when calling it.
5. Encapsulation protects internal state, while polymorphism allows different implementations to be used through a common type.
6. Static methods do not have an instance `$this` context.
7. Type declarations improve clarity and help catch type errors.
8. `return` provides a value to the caller; `echo` displays output.
9. Practise writing code, tracing variable values, and explaining your reasoning aloud.

---

**Day 1 status:** PHP fundamentals and OOP covered. Continue with Day 2: Drupal architecture and the request lifecycle.
