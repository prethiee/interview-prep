# Drupal Interview Questions and Answers

## 1. Performance and Caching

### 1. How would you troubleshoot a slow Drupal website?
**Answer:** First, I identify whether the bottleneck is in the frontend, backend, database, or server. I inspect network timings, logs, database queries, Views, PHP code, and cache configuration. Then I optimise the identified bottleneck and measure performance again.

**Memory trick:** Identify → Investigate → Optimise → Measure.

### 2. How would you improve a Drupal View that takes eight seconds to display 5,000 records?
**Answer:** I would configure pagination, inspect the generated SQL query, review indexes, joins, filters, and sorting, and check caching configuration.

**Memory trick:** Pagination → Query → Indexes → Cache.

### 3. Should a custom block displaying the current user's name vary its cache by user?
**Answer:** Yes. I would add the `user` cache context so each user's personalised output is cached separately. I would also configure appropriate cache tags and max-age.

**Memory trick:** Personalised output = User cache context.

### 4. What are cache tags, contexts, and max-age?
**Answer:**
- Cache tags: Identify when cached data should be invalidated.
- Cache contexts: Identify variations in cached output.
- Cache max-age: Defines how long cached data remains valid.

**Memory trick:** Tags = When to clear; Contexts = What varies; Max-age = How long.

### 5. Internal Page Cache vs Dynamic Page Cache?
**Answer:** Internal Page Cache caches complete pages for anonymous users. Dynamic Page Cache caches rendered pages for anonymous and authenticated users while respecting cache contexts.

**Memory trick:** Internal = Anonymous; Dynamic = Both.

### 6. Cache tags vs cache contexts?
**Answer:** Cache tags invalidate cached data when something changes. Cache contexts create separate cache variations based on conditions such as user or language.

**Memory trick:** Tags = Changes; Contexts = Variations.

### 7. What is cache invalidation?
**Answer:** Cache invalidation means marking cached data as no longer valid so Drupal can regenerate it with fresh data.

**Memory trick:** Old cache is no longer valid.

### 8. Cache invalidation vs `drush cr`?
**Answer:** Cache invalidation marks specific cached data as invalid. `drush cr` rebuilds Drupal caches, including routing and plugin discovery caches.

**Memory trick:** Invalidation = Targeted; `drush cr` = Rebuild.

### 9. `cache_get()` vs `cache_set()`?
**Answer:** `cache_get()` retrieves cached data, while `cache_set()` stores data in cache. These are legacy Drupal 7-style functions; modern Drupal uses the Cache API service.

**Memory trick:** Get = Retrieve; Set = Store.

### 10. `max-age: 0` vs permanent cache?
**Answer:** Max-age 0 means the output must not be reused from cache. `Cache::PERMANENT` represents data without time-based expiry.

**Memory trick:** 0 = Don't cache; Permanent = No time expiry.

### 11. Render Cache vs Dynamic Page Cache?
**Answer:** Render Cache stores individual components such as blocks and entities. Dynamic Page Cache stores rendered pages while respecting cache contexts.

**Memory trick:** Render = Components; Dynamic = Pages.

### 12. Internal Page Cache vs Render Cache?
**Answer:** Internal Page Cache stores complete pages for anonymous users. Render Cache stores individual rendered components that can be reused when building pages.

**Memory trick:** Internal = Full page; Render = Component.

## 2. Entities and Database

### 13. `accessCheck(TRUE)` vs `accessCheck(FALSE)`?
**Answer:** `accessCheck(TRUE)` applies entity access checks to query results. `accessCheck(FALSE)` skips those checks and should only be used when access is handled safely elsewhere.

**Memory trick:** TRUE = Check access; FALSE = Skip access checks.

### 14. `load()` vs `loadMultiple()`?
**Answer:** `load($id)` loads one entity by ID. `loadMultiple($ids)` loads multiple entities by their IDs.

**Memory trick:** Load = One; Load Multiple = Many.

### 15. `entityTypeManager()` vs `entityQuery()`?
**Answer:** `entityTypeManager()` manages entity types and provides storage handlers to load, create, and save entities. `entityQuery()` builds queries to find entities matching conditions.

**Memory trick:** Manager = Manage entities; Query = Find entities.

### 16. Content Entity vs Configuration Entity?
**Answer:** Content entities store site content, such as nodes, users, and taxonomy terms. Configuration entities store site structures and settings, such as Views and content types, which can be exported as YAML.

**Memory trick:** Content = Website data; Configuration = Website setup.

### 17. `hook_entity_presave()` vs `hook_entity_insert()`?
**Answer:** `hook_entity_presave()` runs before an entity is saved, whether new or existing. `hook_entity_insert()` runs after a new entity is inserted.

**Memory trick:** Presave = Before; Insert = After creation.

### 18. `hook_entity_update()` vs `hook_entity_insert()`?
**Answer:** `hook_entity_update()` runs after an existing entity is updated. `hook_entity_insert()` runs after a new entity is created.

**Memory trick:** Update = Existing; Insert = New.

### 19. Content Type vs Taxonomy Vocabulary?
**Answer:** A content type defines the structure and fields of content. A taxonomy vocabulary groups terms used to categorise content.

**Memory trick:** Content type = What you create; Taxonomy = How you categorise.

### 20. Node vs Entity?
**Answer:** An entity is a general Drupal data object, such as a node, user, or taxonomy term. A node is a specific content entity.

**Memory trick:** Every node is an entity, but not every entity is a node.

### 21. Field Widget vs Field Formatter?
**Answer:** A field widget controls how users enter field values. A field formatter controls how those values are displayed.

**Memory trick:** Widget = Input; Formatter = Output.

### 22. Field Definition vs Field Value?
**Answer:** Field definition describes the field's structure and settings. Field value is the actual data stored in that field.

**Memory trick:** Definition = Structure; Value = Data.

## 3. Hooks, Forms and Configuration

### 23. `hook_form_alter()` vs `hook_form_FORM_ID_alter()`?
**Answer:** `hook_form_alter()` can alter any form. `hook_form_FORM_ID_alter()` targets a specific form directly.

**Memory trick:** General form vs Specific form.

### 24. `#access` vs `#disabled`?
**Answer:** `#access` controls whether a form element is displayed or accessible. `#disabled` displays the element but prevents normal user interaction.

**Memory trick:** Access = Show or hide; Disabled = Enable or disable.

### 25. `#default_value` vs `#value`?
**Answer:** `#default_value` provides an initial value that users can usually change. `#value` sets the element's value programmatically.

**Memory trick:** Default = Starting value; Value = Programmatically set.

### 26. FormBase vs ConfigFormBase?
**Answer:** `FormBase` is used for general-purpose forms. `ConfigFormBase` is used for administrative forms that read and save Drupal configuration.

**Memory trick:** FormBase = General; ConfigFormBase = Settings.

### 27. Hook vs Event Subscriber?
**Answer:** A hook allows modules to extend or alter Drupal functionality at predefined extension points. An event subscriber listens for dispatched events and responds to them.

**Memory trick:** Hook = Extension point; Subscriber = Event listener.

### 28. `hook_init()` in Drupal 7 vs Drupal 10/11 request handling?
**Answer:** Drupal 7 used `hook_init()` during page initialisation. Modern Drupal uses the Symfony HTTP kernel, middleware, and events for request handling.

**Memory trick:** Drupal 7 = hook_init; Modern Drupal = Symfony lifecycle.

### 29. `hook_cron()` vs Queue Worker?
**Answer:** `hook_cron()` runs scheduled tasks when Drupal cron executes. Queue workers process queued jobs individually or in batches.

**Memory trick:** Cron = Trigger; Queue = Process jobs.

### 30. `hook_update_N()` vs Configuration Management?
**Answer:** `hook_update_N()` performs numbered database or system updates. Configuration Management exports and imports site configuration between environments.

**Memory trick:** Update hook = Database/system update; Config = Site setup.

### 31. `drush updb` vs `drush cim`?
**Answer:** `drush updb` runs pending database updates. `drush cim` imports configuration from the configured sync directory.

**Memory trick:** UPDB = Database; CIM = Configuration import.

## 4. REST, APIs and Security

### 32. REST vs JSON:API in Drupal?
**Answer:** Drupal REST allows us to configure resources, endpoints, HTTP methods, and supported formats. JSON:API follows a standard specification and provides structured endpoints for Drupal entities, including filtering, pagination, and relationships.

**Memory trick:** REST = More configuration; JSON:API = Standardised entity endpoints.

### 33. What are the main HTTP methods?
**Answer:**
- GET = Retrieve data.
- POST = Create data.
- PATCH = Partially update data.
- PUT = Replace a resource.
- DELETE = Delete data.

**Memory trick:** GET, POST, PATCH, PUT, DELETE.

### 34. How do you secure an API in Drupal?
**Answer:** I use appropriate authentication, such as OAuth 2.0 with access tokens or Basic Authentication over HTTPS where supported. I configure permissions and access checks, validate input, and avoid exposing sensitive information.

**Memory trick:** Authentication = Who are you? Permissions = What can you do?

## 5. Dependency Injection, Services and Plugins

### 35. What is Dependency Injection in Drupal?
**Answer:** Dependency injection is a technique where a class receives the services it needs instead of retrieving them directly. In Drupal, we use the service container and pass dependencies through the constructor, usually using the `create()` method.

**Memory trick:** Container → Create → Constructor.

### 36. Dependency Injection vs `\Drupal::service()`?
**Answer:** Dependency injection passes required services into a class through its constructor. `\Drupal::service()` retrieves a service directly from the container wherever it is called. Dependency injection improves testability and maintainability.

**Memory trick:** DI = Receive; Static call = Fetch directly.

### 37. Interface vs Abstract Class vs Trait in PHP?
**Answer:**
- Interface defines a contract that implementing classes must follow.
- Abstract class provides a base class with implemented and abstract methods.
- Trait allows code reuse across multiple classes without inheritance.

**Memory trick:** Interface = Contract; Abstract = Blueprint; Trait = Reusable code.

### 38. Service vs Plugin in Drupal?
**Answer:** A service provides reusable functionality and is managed by Drupal's service container. A plugin is a component that follows a particular plugin type and is managed by a plugin manager.

**Memory trick:** Service = Reusable functionality; Plugin = Component.

### 39. What is a Plugin Manager?
**Answer:** A plugin manager discovers plugins, creates plugin instances, and manages plugins of a particular type.

**Memory trick:** Discover → Create → Manage.

### 40. Service Container vs Plugin Manager?
**Answer:** The service container manages reusable services and makes them available for dependency injection. A plugin manager discovers and creates plugins of a particular type, such as blocks or queue workers.

**Memory trick:** Container = Services; Manager = Plugins.

## 6. Theming and Modules

### 41. Custom Module vs Custom Theme?
**Answer:** A custom module adds or extends website functionality, mainly business logic and backend features. A custom theme controls the website's presentation, layouts, templates, CSS, and JavaScript.

**Memory trick:** Module = Functionality; Theme = Presentation.

### 42. `hook_theme()` vs Twig Templates?
**Answer:** `hook_theme()` registers a theme hook and tells Drupal which template to use. Twig defines how the output is rendered.

**Memory trick:** Theme hook = Register; Twig = Display.

### 43. `hook_preprocess()` vs `hook_theme()`?
**Answer:** `hook_theme()` registers a theme hook and template. `hook_preprocess()` prepares or modifies variables before they are passed to Twig.

**Memory trick:** Theme = Register; Preprocess = Prepare.

## 7. Composer and Drush

### 44. `composer install` vs `composer update`?
**Answer:** `composer install` installs dependencies using the exact versions specified in `composer.lock`. `composer update` resolves newer compatible versions and updates the lock file.

**Memory trick:** Install = Follow lock file; Update = Resolve newer versions.

### 45. `composer.json` vs `composer.lock`?
**Answer:** `composer.json` defines project dependencies and allowed version constraints. `composer.lock` records the exact resolved package versions.

**Memory trick:** JSON = Requirements; Lock = Exact versions.

## 8. Additional Practical Questions

### 46. What is a Drupal service?
**Answer:** A service is a reusable piece of functionality managed by Drupal's service container. It can be injected into other classes.

**Memory trick:** Service = Reusable functionality.

### 47. What is a Drupal plugin?
**Answer:** A plugin is a reusable component that follows a particular plugin type and is discovered and managed by a plugin manager.

**Memory trick:** Plugin = Reusable component with a defined type.

### 48. What is the difference between `drush cr`, `drush updb`, and `drush cim`?
**Answer:**
- `drush cr` rebuilds Drupal caches.
- `drush updb` runs pending database updates.
- `drush cim` imports configuration.

**Memory trick:** CR = Cache; UPDB = Database; CIM = Configuration.

### 49. What is Composer?
**Answer:** Composer is PHP's dependency manager. It installs and manages project libraries and their dependencies using `composer.json` and `composer.lock`.

**Memory trick:** Composer = PHP package manager.

### 50. What is Drush?
**Answer:** Drush is a command-line tool used to manage Drupal sites, run database updates, rebuild caches, import configuration, and perform other administrative tasks.

**Memory trick:** Drush = Drupal command line.


### MISC
Below is the complete document in Markdown, with numbered questions, bold formatting, properly boxed ASCII diagrams, arrows, decision branches, comparison tables and PHP/YAML code examples.

Copy everything inside the code block into your `drupal-interview-preparation.md` file.


# Drupal Interview Preparation
## External API Integration, Caching, Performance and HTTP Lifecycle

**Interview Focus Areas:**
- HTTP request and response lifecycle in Drupal
- Custom HTTP responses
- External API integration using Guzzle
- Dependency injection and custom services
- API authentication and error handling
- Varnish, Redis and Memcached
- Drupal caching mechanisms
- Performance optimization and monitoring
- Practical troubleshooting scenarios

---

# 1. HTTP Request and Response Lifecycle in Drupal

## Q1. Can you explain the complete lifecycle of an HTTP request and response in Drupal?

**Answer:**

When a user sends an HTTP request from the browser, it first reaches the web server, such as Nginx or Apache.

The web server forwards the request to the PHP runtime, which executes Drupal's `index.php`.

Drupal bootstraps the application through the Drupal kernel. The routing system identifies the matching route based on the requested URL.

Drupal then performs authentication and access permission checks. If access is granted, the corresponding controller is invoked.

The controller uses Drupal services to execute business logic and retrieve data from the cache, database or external APIs.

The controller returns a render array or a response object. Drupal processes the output and generates an HTTP response, such as HTML or JSON, which is sent back through the web server to the browser.

**HTTP Request and Response Flowchart:**

```text
┌──────────────────────────────────┐
│ 1. Browser                       │
│ User sends HTTP request          │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 2. Web Server                    │
│ Nginx / Apache                   │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 3. PHP Runtime                   │
│ Executes Drupal application      │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 4. index.php                     │
│ Drupal entry point               │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 5. Drupal Bootstrap / Kernel     │
│ Initializes Drupal               │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 6. Routing                       │
│ Matches requested URL to route   │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 7. Authentication and            │
│    Access Permission Checks      │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 8. Controller                    │
│ Handles the request              │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 9. Services / Business Logic     │
│ Executes application logic       │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 10. Data Retrieval               │
│ Cache / Database / External API  │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 11. Response Generation          │
│ Render Array / HTML / JSON       │
└────────────────┬─────────────────┘
                 │
                 ▼
┌──────────────────────────────────┐
│ 12. HTTP Response                │
│ Sent back to the browser         │
└──────────────────────────────────┘
````

## Q2. What happens when a request reaches Drupal's index.php?

Answer:

`index.php` is the entry point for Drupal web requests.

It loads Composer's autoloader, creates the HTTP request object and initializes the Drupal application through the Drupal kernel.

The kernel bootstraps the application, processes the request and generates the appropriate HTTP response.

## Q3. What is the role of routing in Drupal?

Answer:

Routing maps an incoming URL and HTTP method to the appropriate controller or callback.

Drupal identifies the matching route based on the requested URL and performs the required access checks before invoking the controller.

Routes are generally defined in a module's `.routing.yml` file.

## Q4. What is the role of a controller in Drupal?

Answer:

A controller handles a request for a particular route.

It coordinates the required application logic, calls services to retrieve or process data, and returns the output.

A controller typically returns a render array for an HTML page or a Symfony Response object for custom responses.

Business logic that needs to be reused should generally be implemented in services rather than placed entirely inside controllers.

## Q5. What is the difference between a render array and a Response object in Drupal?

Answer:

A render array is Drupal's structured representation of content that needs to be rendered into output, usually HTML.

A Response object represents the actual HTTP response sent to the client. It contains the response body, HTTP status code and headers.

For normal Drupal page rendering, controllers commonly return render arrays.

For custom responses, JSON output or redirects, controllers can return appropriate Symfony Response objects.

## Q6. What is an HTTP response, and how do you create a custom HTTP response in Drupal?

Answer:

An HTTP response is the output returned by the server to the client after processing an HTTP request.

It generally contains three important components:

1. Status code: Indicates the result of the request, such as 200, 404 or 500.
2. Headers: Provide metadata about the response, such as Content-Type.
3. Body: Contains the actual response content.

Drupal controllers can return Symfony Response objects to create custom HTTP responses.

Example: Custom HTTP Response

```
use Symfony\Component\HttpFoundation\Response;

public function customResponse(): Response {
  return new Response(
    'Hello from Drupal!',
    Response::HTTP_OK,
    ['Content-Type' => 'text/plain']
  );
}
```

Explanation:

- `Hello from Drupal!` is the response body.
- `Response::HTTP_OK` represents HTTP status code 200.
- `Content-Type` specifies that the response contains plain text.

## Q7. How do you return JSON from a Drupal controller?

Answer:

We can use Symfony's `JsonResponse` class to return JSON data from a Drupal controller.

It automatically converts the supplied data into JSON and sets the appropriate Content-Type header.

Example:

```
use Symfony\Component\HttpFoundation\JsonResponse;

public function getData(): JsonResponse {
  $data = [
    'status' => 'success',
    'message' => 'Data returned from Drupal',
  ];

  return new JsonResponse(
    $data,
    JsonResponse::HTTP_OK
  );
}
```

## Q8. What is the difference between an HTML response and a JSON response?

Answer:

An HTML response contains markup that the browser can render directly as a web page.

A JSON response contains structured data that can be consumed by another application or frontend.

For example, a traditional Drupal website may return HTML, whereas a decoupled frontend built using React or Next.js may consume JSON through Drupal's JSON:API or a custom API endpoint.

## Q9. Does every HTTP request reach Drupal?

Answer:

No. Every HTTP request does not necessarily reach Drupal.

A CDN or reverse proxy such as Varnish may serve a cached response before the request reaches Drupal.

- Cache hit: Varnish finds a valid cached response and returns it directly to the browser.
- Cache miss: Varnish forwards the request to the backend, where Drupal processes it and generates the response.

Varnish Cache Hit and Cache Miss Flowchart:

```
          ┌───────────────────────┐
          │ 1. Incoming Request   │
          └───────────┬───────────┘
                      │
                      ▼
          ┌───────────────────────┐
          │ 2. Varnish Cache      │
          └───────────┬───────────┘
                      │
                      ▼
              ┌───────────────┐
              │ 3. Cache Hit? │
              └───────┬───────┘
                      │
                ┌─────┴─────┐
                │           │
               YES          NO
                │           │
                ▼           ▼
       ┌──────────────┐  ┌────────────────┐
       │ 4. Return    │  │ 5. Forward     │
       │ Cached       │  │ Request to     │
       │ Response     │  │ Drupal         │
       └──────┬───────┘  └───────┬────────┘
              │                  │
              │                  ▼
              │          ┌────────────────┐
              │          │ 6. Drupal      │
              │          │ Processes      │
              │          │ Request        │
              │          └───────┬────────┘
              │                  │
              │                  ▼
              │          ┌────────────────┐
              │          │ 7. Generate    │
              │          │ Response       │
              │          └───────┬────────┘
              │                  │
              │                  ▼
              │          ┌────────────────┐
              │          │ 8. Cache       │
              │          │ Response       │
              │          │ If Cacheable   │
              │          └───────┬────────┘
              │                  │
              └────────┬─────────┘
                       │
                       ▼
             ┌───────────────────┐
             │ 9. Return Response│
             │    to Browser     │
             └───────────────────┘
```

# 2. External API Integration with Drupal

## Q10. How do you integrate an external API with Drupal?

Answer:

I generally use Drupal's HTTP Client service, which is based on Guzzle, to communicate with external APIs.

I create a custom Drupal service responsible for making API requests, handling responses and processing the returned data.

I then inject that service into the controller or other components that require the API data.

The general integration process involves:

1. Understanding the external API documentation, endpoints and authentication mechanism.
2. Configuring the API base URL and credentials securely.
3. Using Drupal's HTTP Client service through dependency injection.
4. Making HTTP requests using appropriate methods such as GET, POST, PUT or DELETE.
5. Processing the API response, usually JSON.
6. Handling HTTP errors, network failures and timeouts.
7. Validating and mapping the external data.
8. Caching API responses where appropriate.
9. Logging errors without exposing sensitive information.

External API Integration Flowchart:

```
┌────────────────────────────────┐
│ 1. Drupal Controller / Form     │
└───────────────┬────────────────┘
                │
                ▼
┌────────────────────────────────┐
│ 2. Custom API Service           │
│ Contains API integration logic │
└───────────────┬────────────────┘
                │
                ▼
┌────────────────────────────────┐
│ 3. Drupal HTTP Client           │
│ Guzzle                         │
└───────────────┬────────────────┘
                │
                ▼
┌────────────────────────────────┐
│ 4. External API                 │
│ Sends requested data            │
└───────────────┬────────────────┘
                │
                ▼
┌────────────────────────────────┐
│ 5. HTTP Response                │
│ JSON / Status Code / Headers    │
└───────────────┬────────────────┘
                │
                ▼
┌────────────────────────────────┐
│ 6. Validate and Decode JSON     │
└───────────────┬────────────────┘
                │
                ▼
┌────────────────────────────────┐
│ 7. Map / Process Data           │
└───────────────┬────────────────┘
                │
                ▼
┌────────────────────────────────┐
│ 8. Return Data to Drupal        │
└───────────────┬────────────────┘
                │
                ▼
┌────────────────────────────────┐
│ 9. Display or Store Data        │
└────────────────────────────────┘
```

## Q11. What is Guzzle, and how is it used in Drupal?

Answer:

Guzzle is a PHP HTTP client library used to send HTTP requests to external services.

Drupal provides an HTTP Client service built on Guzzle, which allows developers to communicate with external APIs using dependency injection.

We can use it to send GET, POST, PUT, PATCH and DELETE requests and process the responses.

## Q12. How do you use Drupal's HTTP Client service in a custom service?

Answer:

We inject Drupal's `http_client` service into the constructor of our custom service.

This allows us to make HTTP requests without manually creating the HTTP client.

Step 1: Define the service in `mymodule.services.yml`.

```
services:
  mymodule.external_api:
    class: Drupal\mymodule\Service\ExternalApiService
    arguments: ['@http_client']
```

Step 2: Create the service class.

File: `src/Service/ExternalApiService.php`

```
<?php

namespace Drupal\mymodule\Service;

use GuzzleHttp\ClientInterface;

class ExternalApiService {

  protected ClientInterface $httpClient;

  public function __construct(ClientInterface $http_client) {
    $this->httpClient = $http_client;
  }

  public function fetchData(): array {

    $response = $this->httpClient->request(
      'GET',
      'https://api.example.com/items',
      [
        'headers' => [
          'Accept' => 'application/json',
        ],
        'timeout' => 10,
      ]
    );

    $data = json_decode(
      $response->getBody()->getContents(),
      TRUE,
      512,
      JSON_THROW_ON_ERROR
    );

    return is_array($data) ? $data : [];
  }

}
```

Explanation:

- `http_client` is Drupal's HTTP Client service.
- `request()` sends the HTTP request.
- `GET` specifies the HTTP method.
- `headers` specifies the request headers.
- `timeout` limits how long the request can take.
- `json_decode()` converts the JSON response into a PHP array.

The example endpoint is a placeholder and should be replaced with the actual API endpoint.

## Q13. How do you call your custom API service from a controller?

Answer:

We inject our custom API service into the controller using dependency injection.

The controller calls the service method to retrieve the external API data.

Example:

```
<?php

namespace Drupal\mymodule\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\mymodule\Service\ExternalApiService;
use Symfony\Component\DependencyInjection\ContainerInterface;

class ApiController extends ControllerBase {

  protected ExternalApiService $externalApi;

  public function __construct(ExternalApiService $external_api) {
    $this->externalApi = $external_api;
  }

  public static function create(ContainerInterface $container): static {
    return new static(
      $container->get('mymodule.external_api')
    );
  }

  public function showData(): array {

    $data = $this->externalApi->fetchData();

    return [
      '#theme' => 'item_list',
      '#items' => array_map(
        static fn ($item) => $item['name'] ?? '',
        $data
      ),
    ];
  }

}
```

The controller delegates API communication to the custom service.

This separation improves code reusability, maintainability and testability.

## Q14. Which HTTP methods are commonly used when integrating APIs?

Answer:

| HTTP Method | Purpose                            |
| ----------- | ---------------------------------- |
| GET         | Retrieves data                     |
| POST        | Creates a resource or submits data |
| PUT         | Replaces or updates a resource     |
| PATCH       | Partially updates a resource       |
| DELETE      | Deletes a resource                 |

The exact behavior depends on the external API's documentation and implementation.

## Q15. How do you handle authentication when integrating an external API?

Answer:

The authentication mechanism depends on what the external API supports.

Common authentication methods include:

- API Key: A key is sent in the request header or as specified by the API.
- Basic Authentication: A username and password are sent using HTTP authentication over HTTPS.
- Bearer Token: An access token is sent through the Authorization header.
- OAuth 2.0: Access tokens are obtained and refreshed through an authorization flow.

Credentials and tokens should be stored securely and should never be hardcoded in source code or committed to Git.

HTTPS should always be used for API communication.

Example: Bearer Token Authentication

```
$response = $this->httpClient->request(
  'GET',
  $endpoint,
  [
    'headers' => [
      'Authorization' => 'Bearer ' . $accessToken,
      'Accept' => 'application/json',
    ],
    'timeout' => 10,
  ]
);
```

## Q16. How do you handle errors when an external API fails?

Answer:

I handle API failures gracefully to ensure that an external service problem does not cause an uncontrolled failure in the Drupal application.

I consider the following scenarios:

- HTTP errors such as 400, 401, 403, 404, 429 and 500.
- Connection failures and DNS errors.
- Request timeouts.
- Invalid or unexpected JSON responses.
- Expired or invalid authentication tokens.

I catch the relevant exceptions, log useful diagnostic information and return a controlled result or appropriate error message.

I also avoid logging API credentials, access tokens or sensitive response data.

Example:

```
use GuzzleHttp\Exception\GuzzleException;

public function fetchData(): array {

  try {

    $response = $this->httpClient->request(
      'GET',
      'https://api.example.com/items',
      [
        'timeout' => 10,
      ]
    );

    $data = json_decode(
      $response->getBody()->getContents(),
      TRUE,
      512,
      JSON_THROW_ON_ERROR
    );

    return is_array($data) ? $data : [];

  }
  catch (GuzzleException $e) {

    \Drupal::logger('mymodule')->error(
      'External API request failed: @message',
      ['@message' => $e->getMessage()]
    );

    return [];

  }
  catch (\JsonException $e) {

    \Drupal::logger('mymodule')->error(
      'External API returned invalid JSON.'
    );

    return [];

  }

}
```

Note: In production, inject Drupal's logger service rather than using the static `\Drupal::logger()` call.

## Q17. What are the best practices for external API integration in Drupal?

Answer:

The important best practices are:

1. Use Drupal's HTTP Client service.
2. Use dependency injection.
3. Keep API communication in a dedicated custom service.
4. Store API credentials securely.
5. Use HTTPS.
6. Configure appropriate connection and request timeouts.
7. Handle exceptions and HTTP status codes.
8. Validate external API responses.
9. Cache responses where appropriate.
10. Use pagination for large datasets.
11. Respect API rate limits.
12. Log errors without exposing sensitive information.
13. Write tests using mocked HTTP responses.
14. Use Drupal's Queue API for long-running or bulk operations.

## Q18. How would you improve the performance of an external API integration?

Answer:

I would avoid making the same API request repeatedly.

I would cache API responses when the data does not need to be real-time, configure appropriate timeouts and request only the required data.

For large datasets, I would use pagination.

For long-running operations, I would consider Drupal's Queue API or background processing instead of making users wait for the entire operation during a page request.

I would also monitor API response times, failures and rate limits.

# 3. Caching in Drupal

## Q19. What is caching, and why is it important in Drupal?

Answer:

Caching means temporarily storing data or generated responses so they can be reused instead of being generated or retrieved repeatedly.

Drupal uses caching to:

- Reduce repeated database queries.
- Avoid unnecessary application processing.
- Improve page response times.
- Reduce server load.
- Improve scalability.

Caching can happen at different levels, including the browser, CDN, reverse proxy, Drupal application and database infrastructure.

## Q20. What is Varnish, and how does it work with Drupal?

Answer:

Varnish is an HTTP reverse proxy cache that sits in front of the web server and caches complete HTTP responses.

When a user requests a page, Varnish checks whether a valid cached response exists.

If it does, Varnish serves the response directly without sending the request to Drupal.

If the response is not available in the cache, Varnish forwards the request to the backend, where Drupal processes it.

In Drupal, Varnish is integrated through infrastructure configuration and Drupal's reverse proxy and cache header settings.

## Q21. What is Redis, and how does Drupal use it?

Answer:

Redis is an in-memory data store that Drupal can use as an application-level caching backend.

It stores frequently accessed data in memory, allowing Drupal to retrieve it quickly instead of repeatedly querying the database.

Redis needs to be installed and configured on the server. Drupal's Redis integration is then configured to connect to it and use it for the appropriate cache bins.

## Q22. What is Memcached, and how does Drupal use it?

Answer:

Memcached is an in-memory caching system that stores frequently accessed data in RAM.

Drupal can use Memcached as a cache backend to reduce repeated database access and improve application performance.

The Memcached service must be installed on the server, and Drupal's Memcache integration must be configured to use it.

## Q23. What is the difference between Varnish, Redis and Memcached?

Answer:

| Feature            | Varnish                         | Redis                                              | Memcached                    |
| ------------------ | ------------------------------- | -------------------------------------------------- | ---------------------------- |
| Type               | Reverse proxy cache             | In-memory data store                               | In-memory cache              |
| Layer              | HTTP layer                      | Application / data layer                           | Application layer            |
| Stores             | Complete HTTP responses         | Key-value data and other supported data structures | Key-value data               |
| Main purpose       | Reduce requests reaching Drupal | Fast application data access                       | Fast application data access |
| Drupal integration | Reverse proxy configuration     | Redis integration                                  | Memcache integration         |

Interview explanation:

Varnish works at the HTTP layer and caches complete HTTP responses. It can serve cached pages without sending requests to Drupal.

Redis and Memcached work at the application caching layer. They store frequently accessed data in memory and reduce repeated database operations.

They can be used together to improve application performance.

## Q24. What is a reverse proxy?

Answer:

A reverse proxy is a server that sits between the client and the backend application server.

It receives incoming requests and forwards them to the appropriate backend server.

It can also provide caching, security and traffic distribution.

Varnish is an example of a reverse proxy.

## Q25. How do you configure Varnish, Redis and Memcached with Drupal?

Answer:

They require both infrastructure-level setup and Drupal-side configuration.

Varnish:

1. Install and configure Varnish on the server.
2. Configure Drupal's reverse proxy settings in `settings.php`.
3. Configure cache headers and cache tags.
4. Ensure Varnish handles cache invalidation correctly.

Redis:

1. Install Redis on the server.
2. Install and enable Drupal's Redis integration.
3. Configure the Redis connection in `settings.php`.
4. Configure the required Drupal cache bins.

Memcached:

1. Install Memcached on the server.
2. Install and enable Drupal's Memcache integration.
3. Configure the Memcached server connection.
4. Configure Drupal cache bins to use Memcached.

Important: Installing a Drupal module alone is not sufficient. The corresponding server service must also be installed, running and reachable.

## Q26. What are cache bins in Drupal?

Answer:

Cache bins are logical collections of cached data in Drupal.

Different types of cached information are stored in different bins.

For example, Drupal has cache bins for render output, dynamic page cache, discovery information and general application data.

Each cache bin uses a configured cache backend to store and retrieve cached items.

## Q27. What are cache tags, cache contexts and cache max-age?

Answer:

These are three important concepts in Drupal's cache metadata system.

1\. Cache Tags

Cache tags identify the data that a cached item depends on.

When the underlying data changes, Drupal can invalidate the related cached items using their tags.

For example, `node:10` identifies a dependency on node 10.

2\. Cache Contexts

Cache contexts determine when cached output should vary based on request context.

Examples include:

- User permissions.
- Current language.
- URL query arguments.
- User identity.

3\. Cache Max-Age

Cache max-age defines how long a cached item remains valid, in seconds.

A max-age of `0` means the item should not be cached.

Example:

```
return [
  '#markup' => 'Welcome!',
  '#cache' => [
    'tags' => ['node:10'],
    'contexts' => ['user.permissions'],
    'max-age' => 3600,
  ],
];
```

In this example:

- `node:10` identifies the content dependency.
- `user.permissions` varies the output based on permissions.
- `3600` means the cache is valid for one hour.

## Q28. What is the difference between cache invalidation and cache expiration?

Answer:

Cache invalidation means marking cached data as no longer valid because the underlying data has changed.

Drupal commonly uses cache tags to invalidate related cached items.

Cache expiration means cached data becomes invalid after a defined period, usually controlled by max-age or the cache backend's expiration settings.

## Q29. What is the difference between Internal Page Cache and Dynamic Page Cache in Drupal?

Answer:

Internal Page Cache is primarily intended to cache complete pages for anonymous users.

Dynamic Page Cache caches page responses and components for both anonymous and authenticated users, while taking cache contexts and permissions into account.

Both help reduce the amount of processing Drupal needs to perform when serving repeated requests.

# 4. Performance Optimization in Drupal

## Q30. How do you improve the performance of a Drupal application?

Answer:

I approach performance optimization by identifying the actual bottleneck first rather than making random changes.

My main areas of focus are:

1\. Caching

- Configure Drupal cache bins correctly.
- Use cache tags, contexts and max-age appropriately.
- Use Varnish and Redis where appropriate.
- Avoid unnecessary cache invalidation.

2\. Database Optimization

- Identify slow queries.
- Review query execution plans.
- Add appropriate database indexes.
- Avoid unnecessary entity loads and repeated queries.

3\. Code Optimization

- Avoid redundant processing.
- Avoid expensive operations during every page request.
- Use efficient data retrieval methods.
- Keep reusable business logic in services.

4\. API Optimization

- Cache external API responses.
- Configure timeouts.
- Avoid unnecessary API calls.
- Use queues for long-running operations.

5\. Frontend Optimization

- Optimize images.
- Reduce unnecessary JavaScript and CSS.
- Enable aggregation where appropriate.
- Use lazy loading where suitable.

6\. Infrastructure Optimization

- Review PHP configuration.
- Monitor web server and database performance.
- Ensure adequate CPU and memory resources.

7\. Monitoring and Profiling

- Measure response times.
- Identify bottlenecks.
- Profile expensive operations.
- Compare performance before and after changes.

## Q31. Which tools do you use to identify performance issues in Drupal?

Answer:

I use a combination of Drupal-specific debugging tools, PHP profiling tools, database tools and application monitoring tools.

| Tool             | Purpose                                                             |
| ---------------- | ------------------------------------------------------------------- |
| Devel            | Development debugging and inspection                                |
| Webprofiler      | Inspects requests, queries and rendering performance                |
| Xdebug           | PHP debugging and profiling                                         |
| Blackfire        | Profiles PHP applications and identifies bottlenecks                |
| New Relic        | Application performance monitoring                                  |
| MySQL EXPLAIN    | Examines database query execution plans                             |
| Drush            | Drupal maintenance and diagnostic commands                          |
| Browser DevTools | Inspects network requests, JavaScript, CSS and frontend performance |

The tools depend on the project environment. I use profiling and monitoring data to identify the actual bottleneck.

## Q32. How do you optimize database performance in Drupal?

Answer:

I first identify slow or frequently executed queries using profiling and database monitoring tools.

Then I:

1. Review query execution plans using `EXPLAIN`.
2. Add appropriate indexes where necessary.
3. Avoid unnecessary entity loads.
4. Avoid repeated queries inside loops.
5. Use EntityQuery or Drupal's Database API appropriately.
6. Retrieve only the required fields and records.
7. Use caching to avoid repeated database access.
8. Review custom code and Views that generate expensive queries.

## Q33. How do you optimize Drupal frontend performance?

Answer:

I optimize images, reduce unnecessary JavaScript and CSS, enable CSS and JavaScript aggregation where appropriate, and minimize render-blocking resources.

I also use responsive images, lazy loading where suitable, browser caching and a CDN for static assets when supported by the infrastructure.

## Q34. What is the difference between performance monitoring and profiling?

Answer:

Performance monitoring tracks application performance over time, including response times, error rates, throughput and resource usage.

Profiling examines a specific request or execution to identify where time and resources are being consumed, such as slow PHP functions, database queries or expensive operations.

Monitoring helps identify that a problem exists, while profiling helps investigate why it exists.

## Q35. How would you troubleshoot a slow Drupal page?

Answer:

I would first reproduce the issue and determine whether it affects one page, a particular user type or the entire application.

Then I would:

1. Check the browser Network tab for response times and slow assets.
2. Review application monitoring data.
3. Profile the request to identify slow PHP functions and database queries.
4. Check cache configuration and cache hit rates.
5. Inspect external API calls and their response times.
6. Review database query execution plans.
7. Check server resources such as CPU, memory and PHP-FPM capacity.
8. Apply the required optimization.
9. Measure performance again to verify the improvement.

## Q36. What are common performance mistakes in Drupal?

Answer:

Common performance mistakes include:

- Incorrect or missing cache metadata.
- Repeated database queries inside loops.
- Loading entities unnecessarily.
- Making external API calls on every page request.
- Performing expensive processing inside hooks or controllers.
- Not using pagination for large datasets.
- Poorly optimized Views and database queries.
- Loading unnecessary JavaScript, CSS and large images.
- Using inappropriate cache contexts or invalidation strategies.

# 5. Practical Interview Scenarios

## Q37. An external API is taking five seconds to respond. How would you handle it?

Answer:

I would first measure the API response time and determine whether the delay is consistent or intermittent.

I would configure appropriate connection and request timeouts, cache the response if the data does not need to be real-time, and avoid making the API call on every page load.

If the operation is long-running, I would consider processing it asynchronously using Drupal's Queue API.

I would also monitor API response times and failures.

## Q38. An external API is temporarily unavailable. How would you prevent it from affecting the Drupal website?

Answer:

I would handle connection failures and HTTP errors gracefully.

I would use appropriate timeouts and ensure that an API exception does not break the entire page.

If appropriate, I would serve previously cached data or a fallback response.

For repeated failures, I would consider retry policies with backoff and asynchronous processing.

I would also log the failure and monitor the integration.

## Q39. A Drupal page is accessed by thousands of anonymous users. How would you improve its performance?

Answer:

I would ensure Drupal's Internal Page Cache and Dynamic Page Cache are configured appropriately.

I would also consider Varnish or a CDN to serve cached responses and reduce requests reaching Drupal.

Additionally, I would verify cache metadata, optimize database queries and monitor cache hit rates, response times and server resources.

## Q40. A page displays different content depending on the logged-in user. How would you cache it correctly?

Answer:

I would use the appropriate cache contexts to vary the output based on the relevant user-specific information, such as user permissions or user identity.

I would use cache tags to invalidate the output when its underlying data changes and configure an appropriate max-age.

I would avoid using a single shared cache entry for content that differs between users.

# 6. Quick Revision Tables

## Q41. Summarize the main components involved in a Drupal HTTP request.

Answer:

| Component         | Responsibility                             |
| ----------------- | ------------------------------------------ |
| Browser           | Sends HTTP requests and receives responses |
| DNS               | Resolves domain names into IP addresses    |
| CDN               | Distributes and may cache content          |
| Load Balancer     | Distributes traffic across servers         |
| Varnish           | Caches HTTP responses                      |
| Nginx / Apache    | Handles incoming web requests              |
| PHP               | Executes Drupal's PHP code                 |
| index.php         | Entry point for Drupal web requests        |
| Drupal Kernel     | Bootstraps and handles the request         |
| Routing           | Matches URLs to routes                     |
| Access Checks     | Determines whether access is allowed       |
| Controller        | Handles request-specific application logic |
| Services          | Provide reusable application functionality |
| Redis / Memcached | Store application cache data in memory     |
| Database          | Stores persistent application data         |
| Render API        | Converts render arrays into output         |

## Q42. Summarize the main caching concepts in Drupal.

Answer:

| Concept             | Meaning                                                   |
| ------------------- | --------------------------------------------------------- |
| Cache Bin           | Logical collection of cached data                         |
| Cache Tag           | Identifies a dependency for invalidation                  |
| Cache Context       | Varies cached output based on request context             |
| Cache Max-Age       | Defines how long cached data remains valid                |
| Varnish             | Reverse proxy that caches HTTP responses                  |
| Redis               | In-memory data store usable as a Drupal cache backend     |
| Memcached           | In-memory caching system usable as a Drupal cache backend |
| Internal Page Cache | Primarily caches pages for anonymous users                |
| Dynamic Page Cache  | Caches dynamic page responses and components              |

## Q43. Summarize the recommended practices for external API integration.

Answer:

- Use Drupal's HTTP Client service.
- Use dependency injection.
- Keep API calls in a dedicated custom service.
- Store credentials securely.
- Use HTTPS.
- Configure timeouts.
- Handle exceptions and HTTP status codes.
- Validate and map API responses.
- Cache responses where appropriate.
- Respect rate limits.
- Log failures safely.
- Test API integrations using mocked responses.
- Use queues for long-running operations.

# 7. Last-Minute Interview Revision

## Q44. Explain the difference between Varnish and Redis in one sentence.

Answer:

Varnish caches complete HTTP responses before they reach Drupal, whereas Redis stores application-level data in memory for Drupal to retrieve quickly.

## Q45. How do you integrate an external API with Drupal in one sentence?

Answer:

I use Drupal's HTTP Client service through dependency injection, encapsulate the API communication in a custom service, and handle authentication, responses, errors, timeouts and caching appropriately.

## Q46. What are the three most important Drupal cache metadata concepts?

Answer:

Cache tags identify when cached data should be invalidated, cache contexts determine when cached output should vary, and cache max-age defines how long the cached data remains valid.

## Q47. What is your approach to Drupal performance optimization?

Answer:

I identify the bottleneck through monitoring and profiling, then optimize caching, database queries, application code, external API calls, frontend assets and infrastructure based on the measurements.

# 8. Important Drush Commands

## Q48. Which Drush commands are useful for Drupal maintenance?

Answer:

```
# Check Drupal status.
drush status

# Clear Drupal caches.
drush cr

# Check enabled modules.
drush pml --status=enabled

# Run database updates.
drush updb

# Export configuration.
drush cex

# Import configuration.
drush cim
```

# 9. Important Topics to Explain Verbally

Prepare to explain these topics with practical examples from your project experience:

1. Explain an external API integration you have implemented.
2. Explain why you would use a custom service instead of putting API logic in a controller.
3. Explain how dependency injection works in Drupal.
4. Explain how you handle API authentication and failures.
5. Explain how you prevent repeated API calls.
6. Explain Varnish versus Redis.
7. Explain cache tags, contexts and max-age with an example.
8. Explain Internal Page Cache versus Dynamic Page Cache.
9. Explain how you investigate a slow Drupal page.
10. Explain which tools you use for profiling and monitoring.
11. Explain how you optimize database queries.
12. Explain the complete HTTP request and response lifecycle.

Final Interview Reminder:

Give the direct answer first, then explain the implementation or provide an example.

For questions about your own experience, use a real project example rather than claiming experience with a tool or implementation you have not personally used.

```

**One technical note:** The diagrams use ordinary box-drawing characters and arrows, not Mermaid. They will remain readable when pasted into GitHub Markdown. For best alignment, keep them inside fenced `text` code blocks.
```
---

## Quick Revision: Commands to Remember

| Command | Purpose |
|---|---|
| `drush cr` | Rebuild cache |
| `drush updb` | Run database updates |
| `drush cim` | Import configuration |
| `drush cex` | Export configuration |
| `drush status` | Check site status |
| `drush uli` | Generate one-time login link |
| `drush watchdog:show` | View recent log messages |
| `composer install` | Install locked dependencies |
| `composer update` | Update dependencies |
| `drush sql:query` | Execute SQL query |

## Final PHP Concepts

- Interface = Contract.
- Abstract class = Base class.
- Trait = Reusable methods.
- Dependency Injection = Pass dependencies into a class.
- Constructor = Initialises an object.
- Parameter = Variable declared in a function definition.
- Argument = Actual value passed when calling a function.
- Encapsulation = Restrict direct access to internal data.
- Inheritance = Reuse and extend another class.
- Polymorphism = Different classes implement the same interface differently.
