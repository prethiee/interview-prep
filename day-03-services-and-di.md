# Day 3: Drupal Services and Dependency Injection

## 1. What Is a Service in Drupal?

A **service** is a reusable object that provides specific functionality. Drupal uses services for tasks such as logging, database access, sending HTTP requests, and managing configuration.

Examples of core services:

| Service ID | Purpose |
|---|---|
| `logger.factory` | Creates logger channels for recording messages |
| `database` | Provides access to Drupal's database connection |
| `http_client` | Provides an HTTP client for external requests |
| `current_user` | Provides information about the current user account |
| `entity_type.manager` | Manages entity type definitions and storage handlers |
| `config.factory` | Provides access to configuration objects |

Services are managed by Drupal's **service container**.

### Why use services?

- Reuse functionality across multiple classes.
- Avoid duplicating business logic.
- Improve maintainability and testability.
- Separate business logic from controllers and other classes.

---

## 2. What Is the Service Container?

The **service container** manages the creation and retrieval of services and their dependencies.

Instead of manually creating every service object, Drupal's container provides the required service.

Example:

```php
$logger_factory = \Drupal::service('logger.factory');
```

Here, `logger.factory` is the service ID used to retrieve the logger factory service.

For modern Drupal code, constructor injection is generally preferred over repeatedly retrieving services through `\Drupal::service()`.

---

## 3. What Is Dependency Injection (DI)?

**Dependency Injection means providing a class with the services or objects it needs instead of making the class fetch those dependencies itself.**

For example, a controller needs a logger service to record messages.

Without dependency injection:

```php
$logger = \Drupal::logger('mymodule');
$logger->notice('Products page accessed.');
```

With dependency injection, Drupal supplies the logger factory to the controller through its constructor.

### Benefits of dependency injection

1. **Testability:** Dependencies can be replaced with mocks or test doubles.
2. **Maintainability:** Dependencies are explicit.
3. **Loose coupling:** The class does not need to retrieve dependencies directly from the global container.
4. **Reusability:** The same service can be supplied to different classes.

### Interview answer

"Dependency Injection means providing a class with the services it needs instead of making it fetch those services itself. In Drupal, we commonly use constructor injection. This improves testability, maintainability, and loose coupling."

---

## 4. Constructor Injection in a Drupal Controller

There are three important parts:

| Method | Responsibility |
|---|---|
| `create()` | Retrieves dependencies from the service container and passes them to the constructor |
| `__construct()` | Receives and stores the dependencies |
| Controller method | Uses the injected dependencies to perform its work |

### Example: Injecting the logger factory

```php
<?php

namespace Drupal\mymodule\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\Core\Logger\LoggerChannelFactoryInterface;
use Symfony\Component\DependencyInjection\ContainerInterface;

class ProductController extends ControllerBase {

  public function __construct(
    protected LoggerChannelFactoryInterface $loggerFactory,
  ) {}

  public static function create(
    ContainerInterface $container,
  ): static {
    return new static(
      $container->get('logger.factory'),
    );
  }

  public function list(): array {
    $this->loggerFactory
      ->get('mymodule')
      ->notice('Products page accessed.');

    return [
      '#markup' => 'Welcome to our products page!',
    ];
  }

}
```

### How it works

1. Drupal creates the controller through its controller creation mechanism.
2. Drupal calls `create()` to supply the container.
3. `create()` retrieves the `logger.factory` service.
4. The service is passed to `__construct()`.
5. The constructor stores the service in `$loggerFactory`.
6. The `list()` method uses the injected service to log a message.

**Remember:** `create()` retrieves and passes the dependency; `__construct()` receives and stores it.

---

## 5. What Is `MODULE.services.yml`?

A custom service must be registered so Drupal's container knows how to create it.

For a module named `mymodule`, create:

`mymodule.services.yml`

Place this file in the module's root directory.

### Example service definition

```yaml
services:
  mymodule.greeting:
    class: Drupal\mymodule\Service\GreetingService
```

Explanation:

- `services:` begins the service definitions.
- `mymodule.greeting` is the service ID.
- `class` identifies the PHP class Drupal should instantiate.

After adding or changing a service definition, rebuild Drupal's cache:

```bash
vendor/bin/drush cr
```

---

## 6. Creating a Custom Drupal Service

Suppose multiple controllers need to generate a welcome message. We can put that reusable logic in a custom service.

### Step 1: Create the service class

File: `src/Service/GreetingService.php`

```php
<?php

namespace Drupal\mymodule\Service;

class GreetingService {

  public function getGreeting(string $name): string {
    return 'Welcome, ' . $name . '!';
  }

}
```

Example:

```php
$greeting = new GreetingService();
echo $greeting->getGreeting('Preethi');
```

Output:

```text
Welcome, Preethi!
```

The direct `new` example demonstrates the class's functionality. In Drupal application code, retrieve the registered service from the container or inject it into another class.

### Step 2: Register the service

File: `mymodule.services.yml`

```yaml
services:
  mymodule.greeting:
    class: Drupal\mymodule\Service\GreetingService
```

The service ID is `mymodule.greeting`.

---

## 7. Injecting a Custom Service into a Controller

File: `src/Controller/GreetingController.php`

```php
<?php

namespace Drupal\mymodule\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\mymodule\Service\GreetingService;
use Symfony\Component\DependencyInjection\ContainerInterface;

class GreetingController extends ControllerBase {

  public function __construct(
    protected GreetingService $greetingService,
  ) {}

  public static function create(
    ContainerInterface $container,
  ): static {
    return new static(
      $container->get('mymodule.greeting'),
    );
  }

  public function page(): array {
    return [
      '#markup' => $this->greetingService
        ->getGreeting('Preethi'),
    ];
  }

}
```

The controller receives the service through its constructor and uses it to generate the welcome message.

### Request flow

1. Drupal matches a URL to a route.
2. Drupal creates the controller.
3. The controller receives the custom service.
4. The controller calls `getGreeting()`.
5. The controller returns a render array.

The route must also be defined in `mymodule.routing.yml` for the controller method to be accessible through a URL.

---

## 8. Service vs Interface vs Trait

| Concept | When to use it | Key idea |
|---|---|---|
| **Service** | To encapsulate reusable functionality or business logic | Reusable functionality |
| **Interface** | To define methods a class must implement | Contract |
| **Trait** | To share existing method implementations across classes | Code reuse |

### Quick memory trick

- **Service:** What functionality can I reuse?
- **Interface:** What methods must a class provide?
- **Trait:** What existing code can I share between classes?

These concepts are not mutually exclusive. A service class can implement an interface and use a trait.

---

## 9. Service vs Hook vs Plugin

| Concept | Purpose | Example |
|---|---|---|
| Service | Provides reusable functionality | Custom greeting service |
| Hook | Allows a module to participate in or alter a defined Drupal extension point | `hook_form_alter()` |
| Plugin | Provides a discoverable component following a plugin type's rules | Custom block plugin |

Choose the mechanism based on the requirement. They solve different problems.

---

## 10. Why Create a Custom Service Instead of Duplicating Code?

Imagine two controllers need the same welcome message.

Instead of copying the logic into both controllers, put it in a custom service and inject that service into both.

Benefits:

- **Reusability:** Both controllers use the same implementation.
- **Maintainability:** Update the business logic in one place.
- **Testability:** Test the service independently.
- **Consistency:** Avoid slightly different implementations across controllers.

### Interview answer

"I create custom services to encapsulate reusable business logic that can be shared across multiple controllers or classes. This reduces duplication and improves maintainability and testability."

If the service implementation changes while its interface and behavior remain compatible, the consuming controllers generally do not need changes.

---

## 11. How Does Dependency Injection Help Testing?

Suppose a service depends on an external API client.

With dependency injection, a test can supply a mock client instead of making a real HTTP request.

This allows you to test the class without depending on the external API.

### Interview answer

"Dependency Injection makes testing easier because dependencies can be replaced with mocks or test doubles. This lets us test a class in isolation without relying on real external services."

---

## 12. Common Interview Questions and Answers

### Q1. What is a service in Drupal?

A service is a reusable object that provides specific functionality and is managed by Drupal's service container.

### Q2. What is Dependency Injection?

Dependency Injection is the practice of supplying a class with the dependencies it needs rather than having it retrieve them itself.

### Q3. What is the purpose of `create()`?

It retrieves the required services from the container and passes them to the constructor.

### Q4. What is the purpose of `__construct()`?

It receives the dependencies and stores them for use by the class.

### Q5. Where do you register a custom service?

In the module's `MODULE.services.yml` file.

### Q6. Why use constructor injection?

It makes dependencies explicit and improves testability, maintainability, and loose coupling.

### Q7. Can a service be used by multiple controllers?

Yes. A registered service can be injected into multiple controllers or other classes.

### Q8. What is the difference between a service and an interface?

A service provides functionality; an interface defines a contract that implementing classes must follow.

### Q9. What is the difference between a service and a trait?

A service encapsulates functionality in an object that can be injected. A trait shares method implementations across classes.

### Q10. What command rebuilds Drupal's cache after changing service YAML?

```bash
vendor/bin/drush cr
```

### Q11. Why avoid relying heavily on `\Drupal::service()`?

It introduces direct dependence on the global service locator and can make dependencies less explicit and testing harder. Constructor injection is generally preferred in modern Drupal application code.

### Q12. If a service's implementation changes, must every controller be updated?

Not necessarily. If the service's public contract and expected behavior remain compatible, consumers can continue using it without changes.

---

## 13. Day 3 Revision Checklist

Before the interview, make sure you can explain:

- [ ] What a Drupal service is
- [ ] What the service container does
- [ ] What Dependency Injection means
- [ ] The difference between `create()` and `__construct()`
- [ ] How to inject a core service into a controller
- [ ] How to define a custom service in `MODULE.services.yml`
- [ ] How to inject a custom service into a controller
- [ ] The difference between a service, interface, trait, hook, and plugin
- [ ] Why DI improves testing and maintainability
- [ ] When to use `\Drupal::service()` versus constructor injection

## Final memory summary

**Service:** Reusable functionality.

**Container:** Manages services and their dependencies.

**Dependency Injection:** Supplies a class with its dependencies.

**`create()`:** Retrieves and passes dependencies.

**`__construct()`:** Receives and stores dependencies.

**`MODULE.services.yml`:** Registers custom services.

**`drush cr`:** Rebuilds Drupal's caches.

**Interview goal:** Be able to write a simple custom service, register it, inject it into a controller, and explain why this is better than duplicating logic.
