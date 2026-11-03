# Day 2: Drupal Architecture and Request Lifecycle

## 1. Drupal Architecture

Drupal is built on PHP and uses Symfony components, a service container, a routing system, and a rendering system.

A simplified Drupal request flow is:

1. The browser sends an HTTP request.
2. The web server passes the request to Drupal through `index.php`.
3. Drupal bootstraps the application and processes the request.
4. The routing system matches the requested URL to a route.
5. Drupal checks whether the current user has access.
6. The controller executes if access is granted.
7. The controller returns a render array or another response.
8. Drupal renders the output and uses caching where appropriate.
9. Drupal sends the HTTP response to the browser.

**Important:** Access checks normally happen before the controller executes. If access is denied, Drupal returns an appropriate access-denied response, typically HTTP 403.

Not every controller returns HTML. A controller can also return a JSON or other HTTP response.

## 2. Important Drupal Files

| File | Purpose |
|---|---|
| `index.php` | Entry point for Drupal HTTP requests. |
| `composer.json` | Defines project dependencies, autoloading and Composer configuration. |
| `composer.lock` | Records the exact resolved dependency versions. |
| `vendor/autoload.php` | Loads Composer's autoloader for PHP classes. |
| `settings.php` | Contains site-specific settings, including database configuration and trusted host patterns. |
| `*.info.yml` | Defines module or theme metadata. |
| `*.routing.yml` | Defines routes, paths, controllers and access requirements. |
| `*.services.yml` | Defines services provided by a module. |
| `*.module` | Contains procedural hooks and related module code. |
| `*.install` | Contains installation, schema and database update hooks. |
| `*.libraries.yml` | Defines CSS and JavaScript libraries. |
| `*.permissions.yml` | Defines custom permissions. |
| `*.links.menu.yml` | Defines menu links. |
| `*.links.task.yml` | Defines local task links, such as tabs. |

### `.module` vs. `.install`

- `.module` contains procedural hooks, such as `hook_form_alter()`.
- `.install` contains installation-related code, database schema definitions and update hooks.

### Module vs. Theme

- **Module:** Adds or changes functionality.
- **Theme:** Controls presentation and the visual appearance of the site.

### Core, Contributed and Custom Modules

- **Core modules:** Distributed with Drupal core.
- **Contributed modules:** Developed and distributed by the Drupal community.
- **Custom modules:** Developed for a specific project or business requirement.

## 3. Composer and Dependencies

Composer manages PHP dependencies.

- `composer install` installs dependencies according to `composer.lock` when the lock file is present.
- `composer update` resolves newer compatible versions according to the dependency constraints and updates the lock file.
- The `vendor/` directory contains Composer-managed dependencies and related executable tools.
- `vendor/autoload.php` enables Composer's class autoloading, including configured PSR-4 autoloading.

**Interview question: What is the difference between `composer install` and `composer update`?**

`composer install` uses the locked versions to reproduce the dependency set. `composer update` resolves versions again and updates the lock file.

## 4. Routes and Controllers

A route connects a URL to a controller or another handler. It can also specify access requirements.

Example `mymodule.routing.yml`:

```yaml
mymodule.products:
  path: '/products'
  defaults:
    _controller: '\Drupal\mymodule\Controller\ProductController::list'
    _title: 'Products'
  requirements:
    _permission: 'access content'
```

Example controller at `src/Controller/ProductController.php`:

```php
<?php

namespace Drupal\mymodule\Controller;

use Drupal\Core\Controller\ControllerBase;

class ProductController extends ControllerBase {

  public function list(): array {
    return [
      '#markup' => 'Welcome to our products page!',
    ];
  }

}
```

### Route vs. Controller

- **Route:** Defines the URL, handler and access requirements.
- **Controller:** Handles the request and returns a render array or response.

The route definition belongs in the module's `.routing.yml` file. The controller is a PHP class, commonly placed in the module's `src/Controller/` directory.

After adding or changing a route, rebuild Drupal's caches:

```bash
vendor/bin/drush cr
```

If a route still returns 404, check the YAML syntax, route definition, controller class and method, and whether the route is being discovered correctly.

## 5. Hooks

A hook is an extension point that allows a module to participate in or alter Drupal behavior without modifying Drupal core.

For example, `hook_form_alter()` allows a custom module to alter an existing form.

Example in `mymodule.module`:

```php
<?php

use Drupal\Core\Form\FormStateInterface;

function mymodule_form_alter(
  array &$form,
  FormStateInterface $form_state,
  $form_id
): void {
  if ($form_id === 'user_login_form') {
    $form['name']['#description'] = 'Enter your username.';
  }
}
```

Key points:

- Implement the hook in your custom module.
- The function name begins with the module's machine name.
- `$form` is passed by reference so the form array can be modified.
- Hooks allow customization through Drupal's extension system rather than editing core code.

**Interview question: Why use a hook instead of modifying Drupal core?**

A hook allows custom functionality to integrate with Drupal at a supported extension point. This avoids direct core modifications and makes changes easier to maintain during updates.

## 6. Services and Dependency Injection

### What is a service?

A service is a reusable object that provides specific functionality. Examples include logging, HTTP requests, configuration access and database operations.

Drupal manages many services through its service container.

### What is Dependency Injection (DI)?

Dependency Injection means supplying a class with the services it needs instead of having the class fetch those services itself.

In Drupal, constructor injection is generally preferred for controllers and services because it makes dependencies explicit and improves testability and maintainability.

Example constructor:

```php
public function __construct(
  private LoggerChannelFactoryInterface $loggerFactory,
) {}
```

This illustrates receiving a dependency through a constructor. A complete Drupal controller also needs a `create()` method to retrieve the service from Drupal's service container and pass it to the constructor.

### Service Locator vs. Dependency Injection

Fetching a service directly:

```php
$logger = \Drupal::logger('mymodule');
```

Constructor injection:

```php
public function __construct(
  private LoggerChannelFactoryInterface $loggerFactory,
) {}
```

These examples serve different purposes: the first directly accesses Drupal's logger, while the second shows a dependency being supplied to a class. In a complete class, the injected logger factory can be used to create a channel logger.

Direct service access can be useful in procedural code and certain situations, but constructor injection is generally preferred in object-oriented Drupal code.

### Benefits of Dependency Injection

- Makes dependencies explicit.
- Improves unit testing by allowing dependencies to be substituted or mocked.
- Reduces direct coupling to the global service container.
- Improves maintainability.

**Interview-ready answer:**

"A service is a reusable object that provides functionality. Dependency Injection is a technique for supplying that service to a class rather than having the class fetch it directly. In Drupal, constructor injection is generally preferred because it improves testability and maintainability."

## 7. Render Arrays

A render array is a structured PHP array that describes output for Drupal's rendering system.

Example:

```php
return [
  '#theme' => 'item_list',
  '#title' => 'Products',
  '#items' => ['Laptop', 'Keyboard', 'Mouse'],
  '#cache' => [
    'max-age' => 3600,
  ],
];
```

Here:

- `#theme` selects the theme hook.
- `#title` supplies the list title.
- `#items` supplies the list items.
- `#cache` provides cache metadata, including the maximum age in seconds.

Drupal processes the render array and generates the output. Cache metadata helps Drupal determine when rendered output can be reused or must be invalidated.

**Important:** Rendering and caching are different. The rendering system produces the output; render cache can store reusable rendered output.

## 8. Quick Interview Revision

**Q1. What happens when a user requests `/products`?**

The request enters Drupal through `index.php`, the router matches the route, access is checked, the controller executes, Drupal renders the output where appropriate, and an HTTP response is returned.

**Q2. Where would you implement `hook_form_alter()`?**

In the custom module's `.module` file.

**Q3. What does `drush cr` do?**

It rebuilds Drupal caches, including route-related caches.

**Q4. What is the difference between a route and a controller?**

A route defines how a URL is handled and its access requirements. A controller handles the request and returns a response or render array.

**Q5. Why is constructor injection preferred?**

It makes dependencies explicit and improves testability, maintainability and loose coupling.

**Q6. What happens if a user does not have permission to access a route?**

Drupal denies access before the controller normally executes.

**Q7. What is a render array?**

A structured PHP array describing output for Drupal's rendering system.

## Day 2 Checklist

- [ ] Explain the Drupal request lifecycle in the correct order.
- [ ] Identify important Drupal configuration and PHP files.
- [ ] Explain Composer install vs. update.
- [ ] Distinguish routes from controllers.
- [ ] Explain hooks and `hook_form_alter()`.
- [ ] Explain services vs. Dependency Injection.
- [ ] Describe render arrays and render caching.
- [ ] Know when to use `vendor/bin/drush cr`.

# Drupal Interview Revision – SOLID, Design Patterns & Architecture

## 1. SOLID Principles

### Q1. What is SOLID?

SOLID is a set of five object-oriented programming principles that help developers write maintainable, reusable, flexible, and testable code.

| Principle | Meaning |
|---|---|
| S – Single Responsibility | A class should have one responsibility. |
| O – Open/Closed | Open for extension, closed for modification. |
| L – Liskov Substitution | Child classes should be usable wherever the parent is expected. |
| I – Interface Segregation | Prefer smaller, specific interfaces. |
| D – Dependency Inversion | Depend on abstractions rather than concrete implementations. |

### Q2. How does Drupal use SOLID principles?

Drupal supports SOLID principles through its architecture:

- Dependency injection supports Dependency Inversion.
- Services help separate responsibilities.
- Interfaces provide abstraction.
- Plugins allow functionality to be extended through defined extension points.
- Small, focused classes support Single Responsibility.

### Q3. What is the Single Responsibility Principle?

A class should have one responsibility and one main reason to change.

Example: Separate email sending, payment processing, and user registration into different services.

### Q4. What is the Open/Closed Principle?

A class should be open for extension but closed for modification.

Example: Drupal plugins allow developers to introduce new functionality without modifying existing plugin implementations.

### Q5. What is the Liskov Substitution Principle?

A child class should be usable wherever its parent class is expected without breaking the expected behaviour.

### Q6. What is the Interface Segregation Principle?

A class should not be forced to implement methods it does not need. Prefer smaller, focused interfaces.

### Q7. What is the Dependency Inversion Principle?

High-level classes should depend on abstractions rather than concrete implementations.

Example: Depend on `LoggerInterface` rather than directly creating a specific logger implementation.

---

## 2. Design Patterns Used in Drupal

### Q1. What are design patterns?

Design patterns are reusable solutions to commonly occurring software design problems.

They help improve code structure, maintainability, and reusability.

### Q2. Which design patterns are used in Drupal?

| Pattern | Drupal example |
|---|---|
| Dependency Injection | Constructor injection |
| Factory | Controller creation through `create()` |
| Plugin | Block and field formatter plugins |
| Observer | Event subscribers |
| Strategy | Different field formatter implementations |
| Decorator | Service decoration |
| Adapter | External API integration adapters |
| Singleton-like shared instance | Shared services managed by the service container |

### Q3. What is the Dependency Injection pattern?

Dependencies are provided to a class rather than the class retrieving them directly.

Example:

```php
public function __construct(LoggerInterface $logger) {
  $this->logger = $logger;
}
```

This improves loose coupling, testability, and maintainability.

### Q4. What is the Factory pattern?

A factory creates objects and hides the object creation process from the calling code.

Drupal example: Controller creation through the static `create()` method.

```php
public static function create(ContainerInterface $container): static {
  return new static(
    $container->get('logger.factory')
  );
}
```

### Q5. What is the Plugin pattern?

It allows developers to extend Drupal functionality through reusable components implementing specific extension points.

Examples:
- Block plugins.
- Field formatter plugins.
- Queue worker plugins.
- Views plugins.

### Q6. What is the Observer pattern?

It allows one component to respond to events triggered by another component without tight coupling.

Drupal example: Event subscribers.

```php
public static function getSubscribedEvents(): array {
  return [
    KernelEvents::REQUEST => ['onRequest'],
  ];
}
```

### Q7. What is the Strategy pattern?

It allows different implementations of an operation to be selected depending on the requirement.

Drupal example: Different field formatter plugins can render the same field in different ways.

### Q8. What is the Decorator pattern?

It allows developers to extend or modify an existing service's behaviour without changing its original implementation.

Drupal example:

```yaml
services:
  my_module.custom_logger:
    class: Drupal\my_module\CustomLogger
    decorates: logger.factory
    arguments: ['@my_module.custom_logger.inner']
```

### Q9. What is the Adapter pattern?

It allows two components with different interfaces to work together.

Drupal example: An integration service that adapts an external API response into a structure expected by Drupal.

### Q10. What is the Singleton pattern?

It ensures that a class has only one shared instance.

Drupal's service container generally manages services as shared instances by default, unless configured otherwise. This is similar to singleton behaviour, although the service container is not itself simply the Singleton pattern.

---

## 3. Drupal Architecture Questions

### Q1. What is the Drupal service container?

It is a dependency injection container responsible for managing and providing services.

Services are registered in YAML files and can be injected into controllers, forms, and other classes.

### Q2. What is the difference between a service and a plugin?

- Service: A reusable PHP object managed by the service container.
- Plugin: A reusable component implementing a particular extension point and managed by a plugin manager.

### Q3. What is the difference between a hook and an event subscriber?

- Hooks allow modules to interact with or alter Drupal functionality at predefined extension points.
- Event subscribers listen for dispatched events and execute code when those events occur.

### Q4. What is service decoration?

Service decoration allows us to wrap an existing service and extend or modify its behaviour without changing the original service implementation.

### Q5. What is autowiring?

Autowiring automatically resolves and injects dependencies based on their types, reducing manual service configuration.

It is supported by Symfony's dependency injection component, but should not be assumed to be automatically available for every Drupal service or class.

### Q6. What is the difference between a factory and dependency injection?

- Factory: Responsible for creating objects.
- Dependency Injection: Provides the dependencies an object needs.

A factory can use dependency injection to create an object.

### Q7. What is loose coupling?

Loose coupling means classes have minimal dependencies on specific implementations.

It makes applications easier to maintain, test, and extend.

### Q8. What is an interface in PHP?

An interface defines a contract that implementing classes must follow.

```php
interface PaymentInterface {
  public function pay(float $amount): void;
}
```

### Q9. What is an abstract class?

An abstract class cannot be instantiated directly. It can contain both abstract and concrete methods.

### Q10. What is a trait?

A trait allows methods to be reused across multiple classes without using inheritance.

---

## 4. Scenario-Based Interview Questions

### Q1. You need to add new functionality without modifying an existing service. What would you use?

I would consider service decoration if I need to extend an existing service, or a plugin if the functionality belongs to a plugin extension point.

### Q2. Multiple classes need the same business logic. How would you handle it?

I would move the common logic into a reusable service and inject it into the classes that need it.

### Q3. An external API has a different response structure from your Drupal application. What would you do?

I would create an adapter or integration service to transform the external response into the structure expected by Drupal.

### Q4. You need to execute custom logic when a Drupal request event occurs. What would you use?

I would implement an event subscriber and subscribe to the appropriate Symfony or Drupal event.

### Q5. You need multiple implementations of the same functionality. What would you use?

I would consider an interface with different implementations or a plugin architecture, depending on the use case.

### Q6. How do you make Drupal code easier to test?

- Use dependency injection.
- Separate business logic into services.
- Depend on interfaces where appropriate.
- Avoid unnecessary static service calls.
- Write unit, kernel, and functional tests as appropriate.

---

## 5. Quick Revision – Important Comparisons

| Concepts | Key difference |
|---|---|
| Service vs Plugin | Reusable object vs extension component |
| Hook vs Event Subscriber | Drupal extension hook vs event listener |
| Factory vs DI | Creates objects vs supplies dependencies |
| Interface vs Abstract Class | Contract vs partial implementation |
| Trait vs Inheritance | Method reuse vs parent-child relationship |
| Decorator vs Adapter | Extends behaviour vs translates interfaces |
| Constructor vs Setter Injection | Dependency passed during creation vs afterwards |
| Render Array vs Twig | Structured rendering definition vs presentation template |
| Preprocess vs Hook Alter | Prepares template variables vs alters data at an extension point |

---

## 6. Interview Preparation Checklist

- [ ] Explain all five SOLID principles.
- [ ] Explain Dependency Injection with a PHP example.
- [ ] Explain the Factory pattern.
- [ ] Explain the Plugin pattern.
- [ ] Explain the Observer pattern.
- [ ] Explain the Strategy pattern.
- [ ] Explain service decoration.
- [ ] Explain the Adapter pattern.
- [ ] Understand Drupal's service container.
- [ ] Understand hooks and event subscribers.
- [ ] Explain interfaces, abstract classes, and traits.
- [ ] Give practical examples from enterprise Drupal projects.

**Interview tip:** Don't just list design patterns. Explain the problem each pattern solves and give one example of how you've used it in a real Drupal project.

