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
