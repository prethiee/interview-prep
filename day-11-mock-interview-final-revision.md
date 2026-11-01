# Day 11 – Mock Interview and Final Revision
## 1. How to Approach a Senior Drupal Developer Interview

A senior developer interview generally evaluates five areas:

1. PHP and object-oriented programming fundamentals.
2. Drupal architecture and core APIs.
3. Practical coding and problem-solving.
4. Performance, security, debugging, and integrations.
5. Ownership, technical decision-making, and communication.

### How to structure your answers

Use this approach:

- **What:** Explain the concept.
- **Why:** Explain why it is needed.
- **How:** Explain how you implement it.
- **Example:** Give a practical Drupal example.

For scenario-based questions, use:

- Problem.
- Investigation.
- Solution.
- Result.

Avoid giving long theoretical answers when a short practical explanation is enough.

---

# 2. Introduction and Experience-Based Questions

### Q1. Tell me about yourself.

**Sample answer:**

I am a Drupal developer and technical lead with over 11 years of experience in web development, primarily focused on enterprise Drupal applications.

I have worked across Drupal 7 through Drupal 11, building and maintaining complex CMS platforms for global organizations and enterprise clients.

My core expertise is in Drupal backend development, custom module development, CMS architecture, integrations, performance optimization, security, and migrations.

I have also worked on headless and decoupled Drupal implementations, multilingual platforms, multisite architectures, and content workflows.

Along with hands-on development, I have experience coordinating with cross-functional teams, mentoring developers, and contributing to technical decisions and delivery.

### Q2. What are your strongest technical skills?

**Sample answer:**

My strongest areas are Drupal backend development, PHP, custom module development, Drupal APIs, services and dependency injection, entity management, database integration, REST APIs, and caching.

I am also comfortable troubleshooting performance issues, working on Drupal upgrades and migrations, and supporting enterprise CMS implementations.

### Q3. What versions of Drupal have you worked with?

**Sample answer:**

I have worked across Drupal 7 through Drupal 11.

My experience includes working with Drupal's evolving architecture, including the transition from procedural development in older Drupal versions to the object-oriented, service-oriented architecture introduced in Drupal 8 and continued in later versions.

### Q4. How do you approach a new Drupal project?

**Sample answer:**

I first understand the business requirements, content architecture, user roles, integrations, and expected scale.

Then I review the existing infrastructure, identify reusable contributed modules, and determine where custom development is required.

I focus on maintainability, security, performance, configuration management, and deployment processes from the beginning.

### Q5. How do you handle technical disagreements?

**Sample answer:**

I first understand the reasoning behind the different approaches.

I compare the options based on maintainability, performance, security, complexity, and project requirements. I prefer decisions backed by technical evidence rather than personal preferences.

Once a decision is made, I align with the team and document important architectural choices.

---

# 3. PHP and OOP Mock Interview

### Q6. What are the four pillars of OOP?

**Answer:**

1. Encapsulation – Bundling data and methods together while controlling access.
2. Inheritance – Allowing a class to reuse or extend another class.
3. Polymorphism – Supporting different implementations through a common interface or parent type.
4. Abstraction – Hiding implementation details and exposing essential functionality.

### Q7. What is the difference between an interface and an abstract class?

| Interface | Abstract class |
|---|---|
| Defines a contract | Provides a base class |
| A class can implement multiple interfaces | A class can extend only one class |
| Defines required methods and constants | Can contain abstract and concrete methods |
| Does not hold ordinary instance state | Can contain properties and state |

**Interview answer:**

I use interfaces when I want to define a contract that multiple classes can implement. I use abstract classes when related classes need to share common implementation or behavior.

### Q8. What is a trait?

A trait allows methods to be reused across multiple classes without inheritance.

```php
trait LoggerTrait {

  public function logMessage(string $message): void {
    echo $message;
  }

}

class UserService {
  use LoggerTrait;
}
```

### Q9. What is dependency injection?

Dependency injection is a design pattern in which a class receives its dependencies from outside rather than creating them internally.

It reduces tight coupling and improves testability.

### Q10. What is the difference between an abstract class and a concrete class?

An abstract class cannot be instantiated directly and may contain abstract methods. A concrete class can be instantiated and provides implementations for all required abstract methods.

### Q11. What is the difference between `==` and `===`?

`==` compares values with type conversion where applicable. `===` compares both value and type.

### Q12. What is the difference between `isset()` and `empty()`?

`isset()` checks whether a variable exists and is not NULL.

`empty()` checks whether a value is empty-like, including `0`, `'0'`, `FALSE`, `NULL`, and an empty array.

### Q13. What is Composer?

Composer is PHP's dependency manager. It manages packages, dependencies, and autoloading using `composer.json` and `composer.lock`.

### Q14. What is PSR-4?

PSR-4 is a PHP autoloading standard that maps namespaces to directory structures.

### Q15. What is the difference between an exception and an error in PHP?

An `Exception` represents an exceptional condition that application code can catch and handle.

An `Error` represents a PHP engine or programming-level problem, such as a type error. Both implement PHP's `Throwable` interface and can be caught with `catch (Throwable $e)` where appropriate.

---

# 4. Drupal Architecture and Core APIs

### Q16. Explain the Drupal request lifecycle.

**Answer:**

When a request reaches Drupal:

1. The web server forwards the request to Drupal's front controller, `index.php`.
2. Drupal bootstraps the application.
3. The request is processed through Symfony's HTTP kernel.
4. Routing identifies the matching route.
5. Access checks are performed.
6. The controller executes the required logic.
7. Drupal builds a response, often using render arrays.
8. The response is rendered and returned to the browser.

**Important terms:** Front controller, bootstrap, routing, access checking, controller, render array, response.

### Q17. What is the Drupal service container?

It manages reusable services and their dependencies.

Services are registered in YAML files and retrieved through dependency injection.

Example:

```yaml
services:
  my_module.greeting:
    class: Drupal\my_module\Service\GreetingService
```

### Q18. What is a service?

A service is a reusable object that provides functionality, such as database access, logging, entity management, or HTTP requests.

### Q19. What is a plugin?

A plugin is a reusable component that implements a specific extension point in Drupal.

Examples include:
- Blocks.
- Field formatters.
- Field widgets.
- Queue workers.
- Views plugins.

### Q20. What is the difference between a hook and an event subscriber?

| Hook | Event subscriber |
|---|---|
| Drupal-specific extension mechanism | Symfony event mechanism |
| Used to alter or extend Drupal behavior | Used to react to dispatched events |
| Commonly implemented as procedural functions | Implemented as classes |
| Invoked through Drupal's hook system | Registered as an event subscriber service |

### Q21. What is the difference between a controller and a service?

A controller handles an HTTP request and returns a response.

A service contains reusable business logic that can be called from controllers and other application components.

### Q22. What is the Entity API?

The Entity API provides a structured way to work with Drupal entities such as nodes, users, taxonomy terms, media, and custom entities.

It supports loading, creating, updating, deleting, and querying entities.

### Q23. What is the difference between content entities and configuration entities?

| Content entities | Configuration entities |
|---|---|
| Represent content or operational data | Represent configurable site structures |
| Commonly stored in database tables | Stored through configuration storage |
| Examples: nodes, users, taxonomy terms | Examples: content types, views, image styles |
| May support revisions | Usually configuration-managed |

### Q24. What is the difference between field storage configuration and field configuration?

**Field storage configuration** defines the field's underlying storage definition, such as field type and cardinality.

**Field configuration** attaches the field to a specific entity bundle and controls bundle-specific settings such as label, required status, and display-related configuration.

### Q25. What is the difference between hooks and plugins?

Hooks allow modules to react to or alter Drupal behavior at defined extension points.

Plugins provide structured, discoverable implementations of defined plugin types, managed by plugin managers.

### Q26. What is the difference between configuration and content?

Configuration defines how the site behaves, such as content types, Views, and image styles.

Content represents the actual information managed by the website, such as articles and pages.

---

# 5. Drupal Forms, Routing, and Access

### Q27. How do you create a custom route?

Define the route in `my_module.routing.yml` and point it to a controller or form.

Example:

```yaml
my_module.hello:
  path: '/hello'
  defaults:
    _controller: '\Drupal\my_module\Controller\HelloController::hello'
    _title: 'Hello'
  requirements:
    _permission: 'access content'
```

Rebuild caches after changing the route definition.

### Q28. Explain Drupal Form API.

Drupal Form API provides a structured way to build, validate, and submit forms.

The main methods are:
- `buildForm()`
- `validateForm()`
- `submitForm()`

It also supports form tokens, validation, and form-state management.

### Q29. How do you validate a form?

Override `validateForm()` and use the form state to inspect submitted values.

```php
public function validateForm(array &$form, FormStateInterface $form_state): void {
  $email = $form_state->getValue('email');

  if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
    $form_state->setErrorByName('email', $this->t('Enter a valid email address.'));
  }
}
```

### Q30. How do you protect a custom route?

Use appropriate route permissions, role or custom access checks, and entity access checks where applicable.

Do not rely only on hiding links or buttons in the frontend.

### Q31. What is the difference between authentication and authorization?

Authentication verifies who the user is.

Authorization determines what the user is allowed to do.

---

# 6. Drupal Caching and Performance

### Q32. Explain cache tags, contexts, and max-age.

| Cache metadata | Purpose |
|---|---|
| Cache tags | Invalidate cached output when dependencies change |
| Cache contexts | Vary cached output based on request context |
| Cache max-age | Define how long output remains cacheable |

**Memory trick:**

- Tags = Invalidation.
- Contexts = Variation.
- Max-age = Expiration.

### Q33. What is the difference between Internal Page Cache and Dynamic Page Cache?

Internal Page Cache caches complete responses for anonymous users.

Dynamic Page Cache caches pages while respecting cacheability metadata and can benefit anonymous and authenticated users.

### Q34. What is render caching?

Render caching stores the rendered output of individual components or portions of a page so Drupal does not need to rebuild them repeatedly.

### Q35. How do you troubleshoot a slow Drupal page?

**Answer:**

I would first identify whether the issue is related to the frontend, backend, database, or external services.

I would:
1. Inspect browser network activity.
2. Check Drupal and PHP logs.
3. Identify slow SQL queries.
4. Investigate N+1 entity loading.
5. Review render and page cache metadata.
6. Check external API response times.
7. Optimize the bottleneck and measure the result.

### Q36. What is a cache invalidation problem?

It occurs when cached data is not invalidated after its underlying data changes.

I would check whether the correct cache tags are attached and whether the update operation invalidates those tags.

### Q37. What is a CDN?

A Content Delivery Network distributes eligible content through geographically distributed servers, reducing latency and origin-server load.

### Q38. How do you optimize a Drupal View?

- Use appropriate filters and relationships.
- Avoid unnecessary fields and joins.
- Enable suitable caching.
- Use pagination.
- Inspect query performance.
- Review access checks and contextual filters.
- Avoid loading unnecessary entities.

---

# 7. REST APIs and Integrations

### Q39. What is REST?

REST is an architectural style for designing networked applications. It commonly uses HTTP methods to interact with resources.

### Q40. Explain common HTTP methods.

| Method | Purpose |
|---|---|
| GET | Retrieve data |
| POST | Create a resource or perform an operation |
| PUT | Replace a resource |
| PATCH | Partially update a resource |
| DELETE | Delete a resource |

### Q41. What is the difference between REST and JSON:API?

REST is an architectural style.

JSON:API is a specification for structuring API requests and responses.

Drupal provides a core JSON:API implementation that exposes entity resources through standardized endpoints.

### Q42. What is Drupal's HTTP Client?

Drupal provides an HTTP Client service based on Guzzle.

The service ID is:

```php
http_client
```

It can be injected into services and controllers to communicate with external APIs.

### Q43. How do you handle API failures?

I would:
- Configure appropriate connection and response timeouts.
- Catch relevant exceptions.
- Log failures without exposing secrets.
- Use retries with backoff for transient failures.
- Avoid retrying non-recoverable errors blindly.
- Use queues for work that should not block the page response.

### Q44. What is synchronous vs asynchronous processing?

Synchronous processing waits for an operation to finish before continuing.

Asynchronous processing allows work to happen separately, often through a queue or background worker.

### Q45. How would you integrate Drupal with a CRM?

I would first understand the data mapping, authentication, API limits, and synchronization requirements.

Then I would implement the integration using Drupal's HTTP Client, handle errors and timeouts, and use queues for large or time-consuming synchronization tasks.

I would also consider logging, retries, data validation, duplicate prevention, and monitoring.

---

# 8. Database and SQL

### Q46. What is the difference between INNER JOIN and LEFT JOIN?

INNER JOIN returns matching rows from both tables.

LEFT JOIN returns all rows from the left table and matching rows from the right table, with NULL values when no match exists.

### Q47. What is an index?

An index is a database structure that helps retrieve data efficiently.

It improves suitable read queries but adds storage and write overhead.

### Q48. What is a transaction?

A transaction groups multiple database operations into one logical unit.

It follows ACID principles: Atomicity, Consistency, Isolation, and Durability.

### Q49. What is SQL injection?

SQL injection is a vulnerability where untrusted input manipulates a SQL query.

Prevent it through parameterized queries and Drupal's database abstraction layer.

### Q50. How do you identify a slow query?

Use database slow query logs and EXPLAIN to inspect index usage, rows examined, joins, and query execution strategy.

### Q51. What is the N+1 query problem?

It happens when an application executes one query to retrieve a collection and then additional queries for each individual item.

Solutions include batching, suitable joins, and efficient entity loading.

### Q52. Why should you use Entity API instead of directly updating entity tables?

Direct database updates can bypass entity validation, hooks, field handling, revisions, and other Drupal functionality.

Use the Entity API for normal entity operations.

---

# 9. Drupal Security

### Q53. What is XSS?

Cross-Site Scripting occurs when malicious scripts are injected into content and executed in another user's browser.

Prevent it through context-aware output escaping, safe render arrays, and Twig's default auto-escaping.

### Q54. What is CSRF?

Cross-Site Request Forgery tricks an authenticated user's browser into sending an unwanted request.

Drupal provides form-token protection through Form API and CSRF mechanisms for applicable routes.

### Q55. How do you prevent SQL injection?

Use parameterized queries, Drupal's database abstraction layer, and safe allowlisting for dynamic SQL identifiers.

### Q56. How do you secure file uploads?

Use Drupal's managed file system, validate permitted extensions and file sizes, and ensure files are stored in appropriate public or private locations.

Do not trust filenames or client-provided MIME types alone.

### Q57. How do you secure an API integration?

- Use secure authentication.
- Store secrets outside source control.
- Validate incoming data.
- Use HTTPS.
- Apply access checks.
- Configure timeouts.
- Avoid exposing
