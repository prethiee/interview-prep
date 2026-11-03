# Day 10 – Coding Exercises and Troubleshooting

## 1. PHP Coding Fundamentals

| Concept        | Meaning                                                                    |
| -------------- | -------------------------------------------------------------------------- |
| Variable       | Stores a value                                                             |
| Parameter      | Variable declared in a function or method definition                       |
| Argument       | Actual value passed when calling a function                                |
| Array          | Stores multiple values                                                     |
| Function       | Reusable block of code                                                     |
| Class          | Blueprint for creating objects                                             |
| Object         | Instance of a class                                                        |
| Interface      | Defines a contract that implementing classes must follow                   |
| Abstract class | Cannot be instantiated directly; can contain abstract and concrete methods |
| Trait          | Reuses methods across multiple classes                                     |
| Inheritance    | Allows a class to extend another class                                     |
| Encapsulation  | Controls access to an object's internal data                               |
| Polymorphism   | Allows different implementations through a common interface or parent type |

### Parameter vs Argument

```
function greet($name) {
  return "Hello " . $name;
}

greet("Preethi");
```

- `$name` is the parameter.
- `"Preethi"` is the argument.

---

## 2. PHP Coding Exercises

### Exercise 1: Reverse a String

**Question:** Write a PHP function to reverse a string without using `strrev()`.

```
function reverseString(string $input): string {
  $reversed = '';

  for ($i = strlen($input) - 1; $i >= 0; $i--) {
    $reversed .= $input[$i];
  }

  return $reversed;
}

echo reverseString('Drupal');
```

**Output:**

```
larpuD
```

### Exercise 2: Check Whether a String Is a Palindrome

```
function isPalindrome(string $input): bool {
  $input = strtolower($input);

  return $input === strrev($input);
}

var_dump(isPalindrome('Madam'));
```

**Output:**

```
bool(true)
```

### Exercise 3: Find Duplicate Values in an Array

```
function findDuplicates($arr): array {
  $seen = [];
  $duplicate = [];

  foreach ($arr as $val) {
    if (in_array($val, $seen)) {
      if (!in_array($val, $duplicate)) {
        $duplicate[] = $val;
      }
    }
    else {
      $seen[] = $val;
    }
  }

  return $duplicate;
}

print_r(findDuplicates([1, 2, 3, 2, 4, 1, 5]));
```

**Output:**

```
Array
(
    [0] => 2
    [1] => 1
)
```

### Exercise 4: Find the Second-Largest Number

```
function findSecondLargest($arr): ?int {
  $arr = array_unique($arr);

  if (count($arr) < 2) {
    return null;
  }

  $new_arr = [];

  foreach ($arr as $val) {
    if ($val != max($arr)) {
      $new_arr[] = $val;
    }
  }

  return max($new_arr);
}

echo findSecondLargest([10, 20, 40, 30, 40]);
```

**Output:**

```
30
```

### Exercise 5: Count Character Frequency

```
function charAppearance($str): array {
  $arr = str_split($str);
  $new_arr = [];

  foreach ($arr as $char) {
    if (isset($new_arr[$char])) {
      $new_arr[$char]++;
    }
    else {
      $new_arr[$char] = 1;
    }
  }

  return $new_arr;
}

print_r(charAppearance("hello"));
```

**Output:**

```
Array
(
    [h] => 1
    [e] => 1
    [l] => 2
    [o] => 1
)
```

### Exercise 6: Find the Largest Number in an Array

```
function findLargest(array $arr): ?int {
  if (empty($arr)) {
    return null;
  }

  $largest = $arr[0];

  foreach ($arr as $num) {
    if ($num > $largest) {
      $largest = $num;
    }
  }

  return $largest;
}

echo findLargest([10, 25, 5, 40, 15]);
```

**Output:**

```
40
```

### Exercise 7: Remove Duplicate Values

```
function removeDuplicates(array $numbers): array {
  return array_values(array_unique($numbers));
}

print_r(removeDuplicates([1, 2, 2, 3, 4, 4]));
```

**Output:**

```
Array
(
    [0] => 1
    [1] => 2
    [2] => 3
    [3] => 4
)
```

### Exercise 8: Check Whether a Number Is Prime

```
function isPrime(int $number): bool {
  if ($number < 2) {
    return FALSE;
  }

  for ($i = 2; $i <= sqrt($number); $i++) {
    if ($number % $i === 0) {
      return FALSE;
    }
  }

  return TRUE;
}

var_dump(isPrime(7));
```

**Output:**

```
bool(true)
```

### Exercise 9: Find the Missing Number in a Sequence

```
function findMissingNumber(array $numbers, int $n): int {
  $expectedSum = ($n * ($n + 1)) / 2;
  $actualSum = array_sum($numbers);

  return (int) ($expectedSum - $actualSum);
}

echo findMissingNumber([1, 2, 4, 5], 5);
```

**Output:**

```
3
```

### Exercise 10: Find the First Non-Repeating Character

```
function firstNonRepeatingCharacter(string $input): ?string {
  $frequency = [];

  for ($i = 0; $i < strlen($input); $i++) {
    $character = $input[$i];
    $frequency[$character] = ($frequency[$character] ?? 0) + 1;
  }

  foreach (str_split($input) as $character) {
    if ($frequency[$character] === 1) {
      return $character;
    }
  }

  return NULL;
}

echo firstNonRepeatingCharacter('swiss');
```

**Output:**

```
w
```

---

## 3. PHP OOP Coding Exercises

### Exercise 11: Create a Class and Object

```
class Employee {

  public string $name;

  public function __construct(string $name) {
    $this->name = $name;
  }

  public function getName(): string {
    return $this->name;
  }

}

$employee = new Employee('Preethi');

echo $employee->getName();
```

**Output:**

```
Preethi
```

### Exercise 12: Demonstrate Inheritance

```
class Animal {

  public function makeSound(): string {
    return 'Animal sound';
  }

}

class Dog extends Animal {

  public function makeSound(): string {
    return 'Bark';
  }

}

$dog = new Dog();

echo $dog->makeSound();
```

**Output:**

```
Bark
```

### Exercise 13: Demonstrate an Interface

```
interface PaymentInterface {
  public function pay(float $amount): string;
}

class CreditCardPayment implements PaymentInterface {

  public function pay(float $amount): string {
    return "Paid $amount using credit card";
  }

}

$payment = new CreditCardPayment();

echo $payment->pay(500);
```

### Exercise 14: Demonstrate Dependency Injection

```
interface LoggerInterface {
  public function log(string $message): void;
}

class FileLogger implements LoggerInterface {

  public function log(string $message): void {
    echo $message;
  }

}

class OrderService {

  public function __construct(
    private LoggerInterface $logger
  ) {}

  public function createOrder(): void {
    $this->logger->log('Order created');
  }

}

$logger = new FileLogger();
$order = new OrderService($logger);

$order->createOrder();
```

---

## 4. Drupal Coding Exercises

### Exercise 15: Create a Custom Drupal Service

**File:** `my_module.services.yml`

```
services:
  my_module.greeting:
    class: Drupal\my_module\Service\GreetingService
```

**File:** `src/Service/GreetingService.php`

```
<?php

namespace Drupal\my_module\Service;

class GreetingService {

  public function getGreeting(string $name): string {
    return 'Hello ' . $name;
  }

}
```

**Usage:**

```
$greeting = \Drupal::service('my_module.greeting')
  ->getGreeting('Preethi');
```

### Exercise 16: Inject a Service into a Controller

**File:** `src/Controller/GreetingController.php`

```
<?php

namespace Drupal\my_module\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\my_module\Service\GreetingService;
use Symfony\Component\DependencyInjection\ContainerInterface;

class GreetingController extends ControllerBase {

  public function __construct(
    protected GreetingService $greetingService
  ) {}

  public static function create(ContainerInterface $container): static {
    return new static(
      $container->get('my_module.greeting')
    );
  }

  public function greeting(): array {
    return [
      '#markup' => $this->greetingService->getGreeting('Preethi'),
    ];
  }

}
```

### Exercise 17: Load a Drupal Node

```
$node = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->load(1);

if ($node) {
  $title = $node->getTitle();
}
```

### Exercise 18: Create a Node Programmatically

```
$node = \Drupal\node\Entity\Node::create([
  'type' => 'article',
  'title' => 'My First Article',
  'status' => 1,
]);

$node->save();
```

### Exercise 19: Query Drupal Entities

```
$nids = \Drupal::entityQuery('node')
  ->accessCheck(TRUE)
  ->condition('type', 'article')
  ->condition('status', 1)
  ->execute();
```

### Exercise 20: Create a Custom Route

**File:** `my_module.routing.yml`

```
my_module.hello:
  path: '/hello'
  defaults:
    _controller: '\Drupal\my_module\Controller\GreetingController::greeting'
    _title: 'Hello'
  requirements:
    _permission: 'access content'
```

Rebuild Drupal caches:

```
drush cr
```

---

## 5. Drupal Troubleshooting Scenarios

### Scenario 1: A Drupal Page Returns a 500 Internal Server Error

**Possible causes:**

- PHP fatal error.
- Missing service.
- Invalid dependency injection.
- Incorrect YAML configuration.
- Database connection problem.
- Incompatible module or PHP version.

**Troubleshooting:**

1. Check Drupal logs.
2. Check PHP and web server logs.
3. Review recent code deployments.
4. Verify service definitions and class namespaces.
5. Check database connectivity.
6. Reproduce the issue in a development environment.

### Scenario 2: A Custom Route Returns a 404 Error

**Possible causes:**

- Incorrect route path.
- Invalid YAML syntax.
- Route not discovered.
- Incorrect controller namespace.
- Missing cache rebuild.

**Troubleshooting:**

```
drush cr
drush router:debug
```

### Scenario 3: A Custom Service Cannot Be Found

**Possible causes:**

- Incorrect service ID.
- Invalid services YAML.
- Incorrect class namespace.
- Class file in the wrong directory.
- Cache not rebuilt.

**Troubleshooting:**

- Verify `my_module.services.yml`.
- Verify the service class namespace.
- Verify the service ID used by the caller.
- Run `drush cr`.
- Inspect Drupal logs.

### Scenario 4: A Form Is Not Submitting

**Possible causes:**

- Validation errors.
- Incorrect submit handler.
- Form API definition issue.
- CSRF token problem.
- JavaScript errors.
- Permission restrictions.

**Troubleshooting:**

- Inspect form validation errors.
- Verify the submit handler.
- Check the browser console.
- Check Drupal logs.
- Verify form structure and access requirements.

### Scenario 5: A Node Is Not Appearing in a View

**Possible causes:**

- Node is unpublished.
- View filters exclude it.
- Content type is incorrect.
- Access restrictions apply.
- Relationships or contextual filters are incorrect.
- Cache is stale.

**Troubleshooting:**

- Verify the node's publication status.
- Review View filters and relationships.
- Check contextual filters.
- Check user access.
- Review View caching configuration.

### Scenario 6: A Drupal Page Is Slow

**Possible causes:**

- Missing cache metadata.
- Slow database queries.
- N+1 entity loading.
- Expensive Views.
- External API calls.
- Large images or frontend assets.

**Troubleshooting:**

1. Measure response time.
2. Inspect browser network activity.
3. Review slow database queries.
4. Check render and page caching.
5. Investigate external API calls.
6. Optimize the actual bottleneck.

### Scenario 7: A Twig Template Change Is Not Visible

**Possible causes:**

- Wrong template name.
- Incorrect template suggestion.
- Template in the wrong theme directory.
- Twig cache.
- Theme not active.

**Troubleshooting:**

```
drush cr
```

Enable Twig debugging in development and inspect the template suggestions in the page source.

### Scenario 8: A Configuration Change Is Missing After Deployment

**Possible causes:**

- Configuration was not exported.
- Configuration was not committed.
- Configuration import was not performed.
- Configuration dependencies are missing.
- Different configuration is active in the target environment.

**Troubleshooting:**

```
drush config:status
drush config:export
drush config:import
```

Review configuration changes before importing them, particularly in production.

### Scenario 9: A User Can Edit Content They Should Not Be Able to Edit

**Possible causes:**

- Excessive role permissions.
- Missing entity access checks.
- Custom controller bypassing access checks.
- Incorrect custom access handler.

**Troubleshooting:**

- Review role permissions.
- Check entity access.
- Check route access requirements.
- Verify server-side authorization before performing updates.

### Scenario 10: A Deployment Causes a Fatal Error

**Possible causes:**

- Missing Composer dependencies.
- PHP version mismatch.
- Missing database updates.
- Incompatible module versions.
- Incorrect configuration deployment.

**Troubleshooting:**

1. Review deployment logs.
2. Check Composer dependencies.
3. Verify PHP compatibility.
4. Check database update requirements.
5. Review configuration imports.
6. Roll back safely if the incident affects production.

---

## 6. Practical Coding Interview Approach

When given a coding problem, follow this structure:

1. **Understand the problem:** Clarify the input and expected output.
2. **Identify edge cases:** Empty arrays, one element, duplicate values, negative numbers, NULL values, and unexpected input types.
3. **Explain the approach:** Describe the logic before writing code.
4. **Write clean code:** Use meaningful variable names, small functions, appropriate types, and consistent formatting.
5. **Test the code:** Try normal input and edge cases.
6. **Explain complexity:** Understand how the solution performs as the input grows.

### Common Time Complexities

| Complexity | Meaning           |
| ---------- | ----------------- |
| O(1)       | Constant time     |
| O(log n)   | Logarithmic time  |
| O(n)       | Linear time       |
| O(n log n) | Linearithmic time |
| O(n²)      | Quadratic time    |

Examples:

- One array traversal: O(n).
- Two nested loops over the same array: typically O(n²).
- Sorting: typically O(n log n).

---

## 7. Additional Interview Questions

### Q1. What is the difference between `==` and `===` in PHP?

- `==` compares values after type conversion where applicable.
- `===` compares both value and data type.

```
var_dump(5 == '5');  // true
var_dump(5 === '5'); // false
```

### Q2. What is the difference between `isset()` and `empty()`?

- `isset()` checks whether a variable is set and not NULL.
- `empty()` checks whether a value is empty-like, including `0`, `'0'`, `FALSE`, `NULL`, and an empty array.

### Q3. What is the difference between `include` and `require`?

- `include` produces a warning if the file cannot be loaded and execution may continue.
- `require` produces a fatal error if the file cannot be loaded.

### Q4. What is the difference between `array_merge()` and the `+` array operator?

- `array_merge()` combines arrays and renumbers numeric keys.
- The `+` operator uses array union: keys from the left array take precedence when keys overlap.

### Q5. What is Composer?

Composer is PHP's dependency manager. It installs and manages packages and their dependencies using `composer.json` and `composer.lock`.

### Q6. What is PSR-4 autoloading?

PSR-4 defines a standard way of mapping PHP namespaces to directory structures so classes can be loaded automatically.

Drupal uses PSR-4 autoloading for module and core classes.

### Q7. What is the difference between a service and a plugin in Drupal?

- A service is a reusable object managed by Drupal's service container.
- A plugin is a component discovered and managed by a plugin manager, usually implementing a specific Drupal extension point such as blocks, field formatters, or queue workers.

### Q8. Why is dependency injection important?

It reduces tight coupling, improves testability, and makes dependencies explicit.

### Q9. What is a Drupal hook?

A hook allows modules to interact with or alter Drupal functionality at defined extension points.

### Q10. What is a Drupal event subscriber?

An event subscriber listens for dispatched events and executes code when those events occur. It is based on Symfony's event system.

### Q11. What is the difference between a Drupal form and a controller?

- A controller handles a route request and returns a response or render array.
- A Form API class provides structured form building, validation, submission, and form-token handling.

### Q12. What is a queue worker?

A queue worker processes queued tasks, usually in manageable batches. It is useful for long-running or asynchronous work.

### Q13. How would you write testable Drupal code?

I would separate business logic into services, inject dependencies, avoid unnecessary static service calls, and write unit or kernel tests depending on what needs to be verified.

### Q14. What is the difference between a unit test and a kernel test?

- Unit tests test isolated classes and logic, usually with mocked dependencies.
- Kernel tests bootstrap a Drupal kernel and can test services, entities, database interactions, and other integrated functionality.

### Q15. What is a functional test?

A functional test checks application behavior through simulated HTTP requests and responses, often including routing, forms, and access behavior.

---

## 8. Coding Practice Checklist

### PHP Exercises

- Reverse a string.
- Check whether a string is a palindrome.
- Find duplicate values in an array.
- Find the second-largest distinct number.
- Count character frequency.
- Find the largest number.
- Remove duplicate values.
- Check whether a number is prime.
- Find a missing number in a sequence.
- Find the first non-repeating character.

### Drupal Exercises

- Create a custom service.
- Inject a service into a controller.
- Load a node using Entity API.
- Create a node programmatically.
- Query published articles using EntityQuery.
- Create a custom route.
- Add permission requirements to a route.
- Create a custom form with validation.
- Create a custom block plugin.
- Explain how you would troubleshoot a slow page.

---

## 9. Day 10 Completion Checklist

- Write PHP array and string programs without assistance.
- Explain the logic before coding.
- Understand edge cases.
- Explain time complexity.
- Practise OOP concepts.
- Write a custom Drupal service.
- Implement constructor dependency injection.
- Work with Drupal Entity API and EntityQuery.
- Create and troubleshoot custom routes.
- Understand Drupal logging and Drush debugging.
- Troubleshoot forms, Views, services, and configuration.
- Understand Drupal testing approaches.
- Practise explaining production incidents.

**Day 10 objective:** Be comfortable solving basic PHP coding problems, writing practical Drupal code, and explaining how you investigate and resolve real-world Drupal issues.


Add explanations below every solutionAdd unsolved practice questions first
