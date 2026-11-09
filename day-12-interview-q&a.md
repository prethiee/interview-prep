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
1. Caching: Varnish, Redis and Memcached
Q1. What is Varnish, and how does it work with Drupal?

Answer:

Varnish is an HTTP reverse proxy cache that stores complete HTTP responses.

When multiple users request the same page, Varnish can serve the cached response directly instead of sending every request to Drupal.

This reduces the load on the Drupal application and improves response time.

In Drupal, Varnish is integrated by:

Installing and configuring Varnish on the server.

Configuring reverse proxy settings in settings.php.

Configuring Drupal cache headers and cache tags.

Configuring Varnish to respect cache headers and invalidation.

Q2. What is Redis, and how does Drupal use it?

Answer:

Redis is an in-memory data store that Drupal can use for application-level caching.

It stores frequently accessed data in memory, allowing Drupal to retrieve it quickly instead of repeatedly querying the database.

In Drupal, Redis is integrated by:

Installing Redis on the server.

Installing and enabling the Drupal Redis module.

Configuring the Redis connection in settings.php.

Configuring Drupal cache bins to use Redis.

Q3. What is Memcached, and how does Drupal use it?

Answer:

Memcached is an in-memory caching system that stores frequently accessed data in RAM.

Drupal can use Memcached as a cache backend to reduce repeated database queries and improve application performance.

In Drupal, Memcached is integrated by:

Installing Memcached on the server.

Installing and enabling the Drupal Memcache module.

Configuring the Memcached server connection in settings.php.

Configuring Drupal cache bins to use Memcached.

Q4. What is the difference between Varnish, Redis and Memcached?

Answer:

Varnish, Redis and Memcached are caching technologies used to improve application performance, but they operate at different layers.

Technology

Purpose

Caching layer

Varnish

Stores complete HTTP responses

HTTP layer

Redis

Stores application data in memory

Application layer

Memcached

Stores application data in memory

Application layer

Varnish reduces requests reaching Drupal by serving cached HTTP responses.

Redis and Memcached reduce repeated data retrieval by storing frequently accessed application data in memory.

They can be used together to improve overall application performance.

Q5. What is a reverse proxy?

Answer:

A reverse proxy is a server that sits between the client and the backend application server.

It receives incoming requests and forwards them to the appropriate backend server.

It can also provide caching, security and load distribution.

Varnish is an example of a reverse proxy.

Q6. How do Varnish, Redis and Memcached fit into Drupal's architecture?

Answer:

They operate at different stages of request processing.

Varnish can serve a cached HTTP response before the request reaches Drupal.

Redis and Memcached store application-level cache data that Drupal can retrieve during request processing.

If the required data is not cached, Drupal may retrieve it from the database.

Diagram:

              Browser
                 |
                 v
              Varnish
                 |
          +------+------+
          |             |
       Cache Hit     Cache Miss
          |             |
          v             v
       Response      Drupal
                        |
                   +----+----+
                   |         |
                 Redis    Database
                /Memcached
                   |
                   v
                Response
2. HTTP Request Lifecycle in Drupal
Q7. Can you explain the complete lifecycle of an HTTP request in Drupal?

Answer:

When a user sends an HTTP request from the browser, it first goes through DNS and the network to reach the server.

Depending on the infrastructure, the request may pass through a CDN, load balancer or reverse proxy such as Varnish.

The web server, such as Nginx or Apache, forwards the request to the PHP runtime, which executes Drupal's index.php.

Drupal bootstraps the application, matches the request to a route, performs authentication and access checks, and invokes the appropriate controller.

The controller uses Drupal services to execute business logic and retrieve data from the cache, database or external APIs.

Finally, Drupal generates an HTTP response, such as HTML or JSON, and sends it back to the browser.

Complete request flow:

Browser
   |
   v
DNS / Network
   |
   v
CDN / Load Balancer / Varnish
   |
   v
Web Server (Nginx / Apache)
   |
   v
PHP Runtime
   |
   v
Drupal index.php
   |
   v
Drupal Bootstrap
   |
   v
Routing
   |
   v
Authentication & Access Checks
   |
   v
Controller
   |
   v
Services / Business Logic
   |
   v
Cache / Database / External APIs
   |
   v
Response Generation
   |
   v
Browser
Q8. What is DNS, and what happens when a user enters a URL in the browser?

Answer:

DNS stands for Domain Name System. It translates a domain name into an IP address.

When a user enters a URL, the browser uses DNS to identify the server's IP address and then establishes a connection to send the HTTP request.

Q9. What is the role of Nginx or Apache in a Drupal application?

Answer:

Nginx and Apache are web servers that receive incoming HTTP requests and handle web server operations.

For Drupal requests that require PHP execution, they forward the request to the PHP runtime, which executes Drupal's index.php.

Q10. What happens when a request reaches Drupal's index.php?

Answer:

Drupal's index.php is the entry point for web requests.

It loads the Composer autoloader, creates the Drupal kernel, creates the HTTP request object and passes the request to the kernel for processing.

The kernel handles the request and generates a response, which is then sent to the browser.

Q11. Can you explain the Drupal request lifecycle, including routing, access checks, controllers and services?

Answer:

After Drupal bootstraps, its routing system identifies the route matching the requested URL.

Drupal performs authentication and access checks to determine whether the user is allowed to access the resource.

If access is granted, the corresponding controller is executed.

The controller uses Drupal services to perform business logic and retrieve the required data.

Finally, the controller returns a render array or a response object, which Drupal processes and sends to the client.

Q12. What is the role of a controller in Drupal?

Answer:

A controller handles a request for a particular route.

It executes the required application logic, retrieves data using services and returns the output.

In Drupal, a controller typically returns a render array for an HTML page or a response object for other types of responses.

Q13. How does Drupal retrieve data from the database or cache during request processing?

Answer:

Drupal first attempts to retrieve previously cached data from its configured caching backend, such as Redis or Memcached.

If the required data is not available in the cache, Drupal retrieves it from the database, processes it and may store it in the cache for future requests.

Q14. How does Drupal generate and return an HTTP response?

Answer:

After processing the request, Drupal generates an HTTP response.

For a normal page request, the controller may return a render array, which Drupal's Render API converts into HTML.

For an API request, Drupal may return a JSON response.

The response is passed back through the PHP runtime and web server to the browser.

Q15. What is the difference between an HTML response and a JSON response in Drupal?

Answer:

An HTML response contains markup that the browser can render directly as a web page.

A JSON response contains structured data that can be consumed by another application or frontend, such as React or Next.js.

For example, Drupal can return HTML for a traditional website or JSON through JSON:API for a decoupled frontend.

Q16. Does every HTTP request reach Drupal? Explain with an example of a Varnish cache hit.

Answer:

No, every request does not necessarily reach Drupal.

When a user requests a page, Varnish first checks whether it has a valid cached response.

Cache hit: Varnish returns the cached response directly to the browser without invoking Drupal.

Cache miss: Varnish forwards the request to the backend, and Drupal processes it to generate the response.

Flowchart:

       User Request
             |
             v
          Varnish
             |
             v
      Cached response?
          /       \
        Yes        No
         |          |
         v          v
    Return       Forward to
    Response      Drupal
         |          |
         |          v
         |       Process
         |       Request
         |          |
         |          v
         |       Generate
         |       Response
         |          |
         |          v
         |       Cache
         |       Response
         |          |
         +-----+----+
               |
               v
             Browser
Q17. What is the difference between Varnish and Redis in the Drupal request lifecycle?

Answer:

Varnish is an HTTP reverse proxy cache that stores complete HTTP responses. It can serve cached pages without sending every request to Drupal.

Redis is an in-memory data store that Drupal can use as a backend for application-level cache bins.

The main difference is that Varnish works at the HTTP layer, whereas Redis works at the application caching layer.

3. Quick Architecture Reference
Q18. What are the responsibilities of the different components in a Drupal application?

Answer:

Component

Responsibility

DNS

Resolves domain names into IP addresses

CDN

Distributes and potentially caches content closer to users

Load Balancer

Distributes incoming traffic across servers

Varnish

Caches HTTP responses

Nginx / Apache

Handles incoming web requests

PHP

Executes Drupal's PHP code

index.php

Entry point for Drupal web requests

Routing

Matches URLs to routes and controllers

Controller

Handles request-specific application logic

Services

Provide reusable application functionality

Redis / Memcached

Store application cache data in memory

Database

Stores persistent application data

Render API

Converts render arrays into output

END OF NOTES

These are the relevant questions from your supplied content. I've excluded the GitHub profile bio, repeated explanations, and mock-interview conversation. The diagrams are included where they help explain the architecture and request flow.

ChatGPT, there was a question where they ask the life cycle of a HTTP response, and also HTTP custom response. In HTTP response, a request hits, then it goes into the index.php, then the routing and controller goes, taxes permission, all these things, that flowchart it was included there, right? Can you prepare a question for it and answer for it?
Yes. The question you're referring to is about the Drupal HTTP Request Lifecycle, specifically how Drupal processes an incoming HTTP request and generates an HTTP response.

Let's add it to your GitHub interview notes in a question-and-answer format.

Q19. Explain the complete HTTP request and response lifecycle in Drupal.
Answer:

When a user sends an HTTP request from the browser, the request reaches the web server (Nginx or Apache), which forwards it to PHP.

PHP executes Drupal's index.php, which acts as the entry point for Drupal.

Drupal then bootstraps the application and passes the request to the Drupal kernel.

The routing system identifies the matching route based on the requested URL. Drupal performs authentication and access permission checks to determine whether the user is allowed to access the requested resource.

If access is granted, Drupal invokes the corresponding controller.

The controller executes the required business logic, uses services to retrieve or process data, and returns a response.

For a page request, the controller may return a render array, which Drupal's Render API converts into HTML. For an API request, it may return a JSON response.

Finally, Drupal sends the HTTP response through PHP and the web server back to the browser.

Flowchart:

        Browser
           |
           | HTTP Request
           v
     Web Server
    (Nginx / Apache)
           |
           v
       PHP Runtime
           |
           v
        index.php
           |
           v
    Drupal Bootstrap
           |
           v
     Drupal Kernel
           |
           v
       Routing
           |
           v
   Route Matching
           |
           v
 Authentication & 
   Access Checks
           |
           v
      Controller
           |
           v
 Services / Business
        Logic
           |
           v
   Data Retrieval
 (Cache / Database)
           |
           v
  Response Generation
           |
           v
      HTTP Response
      (HTML / JSON)
           |
           v
       Web Server
           |
           v
        Browser
Q20. What is an HTTP response in Drupal, and how do you create a custom HTTP response?
Answer:

An HTTP response is the output returned by the server to the client after processing an HTTP request.

In Drupal, we can create custom HTTP responses using Symfony's Response classes.

For example, Drupal provides:

Response – For returning a custom response.

JsonResponse – For returning JSON data.

RedirectResponse – For redirecting users to another URL.

Example: Custom HTTP response in a controller

use Symfony\Component\HttpFoundation\Response;

public function customResponse() {
  return new Response(
    'Hello from Drupal!',
    Response::HTTP_OK,
    ['Content-Type' => 'text/plain']
  );
}

Here:

Hello from Drupal! is the response content.

HTTP_OK represents the HTTP status code 200.

Content-Type specifies that the response contains plain text.

Q21. What is the difference between a render array and a Response object in Drupal?

Answer:

A render array is Drupal's structured way of defining content that needs to be rendered into HTML.

A Response object represents the actual HTTP response sent to the client.

For normal Drupal page rendering, controllers generally return render arrays. For custom responses, redirects or JSON output, we can return appropriate Response objects.

Important distinction for interviews:

HTTP request lifecycle = How Drupal receives and processes a request.

HTTP response lifecycle = How Drupal generates and sends the response.

Custom HTTP response = How we programmatically return a specific response using Symfony Response classes.

These three concepts are related, but they are not the same interview question.

The expert recommends focusing on the following topics during preparation for the call: external API, integration with Drupal, best practices, cache (Varnish, Redis, differences between them) + performance ( tools, best practices). Now can you prepare all the answers we discussed so far and along with the content that I gave now for the, like I have a mock interview at 4:30, a GitHub note of, like, question and answers with the flowchart and code block and everything included. Can you give it to me?

I've prepared your GitHub-ready Drupal Interview Preparation Notes covering the topics discussed and the additional areas recommended by the interviewer.

Drupal Interview Preparation

Markdown · 47 interview questions · Code examples · Flowcharts

Ready
Download GitHub Markdown Notes

What's included
Section

Coverage

HTTP lifecycle

Request, response, routing, controllers

Custom responses

Response, JsonResponse, RedirectResponse

External APIs

Guzzle, dependency injection, authentication

API best practices

Error handling, timeouts, caching, queues

Caching

Varnish, Redis, Memcached

Drupal cache

Bins, tags, contexts, max-age

Performance

Profiling, monitoring, database optimization

Scenarios

4 practical troubleshooting scenarios

My recommendation before your 4:30 mock interview
Don't try to memorize all 47 answers.

Prioritize these five areas:

External API integration using Guzzle and dependency injection.

API error handling, authentication and best practices.

Varnish versus Redis and Drupal caching.

Cache tags, contexts and max-age.

Performance troubleshooting and optimization tools.

One important thing: Be prepared to explain an actual API integration you've worked on. Interviewers often move from theoretical questions to implementation details.

You've got the notes. Now focus on being able to explain the concepts clearly rather than memorizing paragraphs.


drupal_interview_prep_api_caching_performance.md
Document
Drupal Interview Preparation: APIs, Caching, Performance & HTTP Lifecycle
Focus areas for the mock interview: external API integration with
Drupal, integration best practices, Varnish and Redis, caching
differences, performance tools and performance best practices.

This note consolidates the topics discussed so far and adds practical
interview-ready answers and examples for the newly identified focus
areas. Examples use modern Drupal practices (Drupal 10/11-style
service injection).

1. HTTP Request and Response Lifecycle
Q1. Explain the complete HTTP request and response lifecycle in Drupal.
Answer:

When a user sends an HTTP request from the browser, it reaches the web
server, such as Nginx or Apache. The web server passes PHP requests to
the PHP runtime, which executes Drupal's index.php.

Drupal loads its application, creates the request and passes it to the
Drupal kernel. The kernel bootstraps the application and the routing
system matches the requested URL to a route.

Drupal performs authentication and access checks. If access is allowed,
the corresponding controller is called. The controller uses services to
execute business logic and retrieve data from the cache, database or an
external API.

The controller returns a render array or a response object. Drupal
prepares the final HTTP response and sends it through PHP and the web
server to the browser.

Flowchart:

Browser
   |
   | HTTP request
   v
Web server (Nginx / Apache)
   |
   v
PHP runtime
   |
   v
Drupal entry point: index.php
   |
   v
Drupal kernel / bootstrap
   |
   v
Routing: match the URL to a route
   |
   v
Authentication and access checks
   |
   v
Controller
   |
   v
Services / business logic
   |
   +----------+------------+
   |          |            |
   v          v            v
  Cache    Database    External API
   |          |            |
   +----------+------------+
              |
              v
       Render / response
              |
              v
       HTTP response
         (HTML / JSON)
              |
              v
       Web server / PHP
              |
              v
           Browser
Remember: Infrastructure components such as a CDN, load balancer or
Varnish may sit in front of the web server. A Varnish cache hit can be
served without invoking Drupal.

Q2. What happens when a request reaches Drupal's index.php?
Answer:

index.php is the entry point for Drupal web requests. It loads
Composer's autoloader, creates the HTTP request and boots the Drupal
application through the Drupal kernel. The kernel handles the request
and produces an HTTP response.

Q3. What is the role of routing in Drupal?
Answer:

Routing maps an incoming URL and HTTP method to the code that should
handle the request. A route definition can specify a path, a controller,
allowed methods, requirements and other route options. Drupal matches
the request to the route and checks access before invoking the
controller.

Q4. What is the role of a controller in Drupal?
Answer:

A controller handles a request for a particular route. It coordinates
the work needed for that request, usually by calling services, and
returns a render array or a Symfony response object. Business logic that
needs to be reused should generally live in services rather than being
placed entirely in the controller.

Q5. What is the difference between a render array and a Response object?
Answer:

A render array is Drupal's structured representation of content that
Drupal's Render API can turn into output, commonly HTML. A Response
object represents the HTTP response itself, including its content,
status code and headers.

For a normal Drupal page, a controller commonly returns a render array.
For custom text, JSON or redirects, it can return an appropriate Symfony
response object, such as Response, JsonResponse or
RedirectResponse.

Q6. What is an HTTP response, and how do you create a custom HTTP response in Drupal?
Answer:

An HTTP response is what the server sends back to the client after
processing a request. It contains a status code, headers and a response
body.

Drupal controllers can return Symfony response objects.

Example: custom plain-text response:

use Symfony\Component\HttpFoundation\Response;

public function customResponse(): Response {
  return new Response(
    'Hello from Drupal!',
    Response::HTTP_OK,
    ['Content-Type' => 'text/plain']
  );
}
Here, the body is Hello from Drupal!, the status is 200 OK, and the
Content-Type header identifies the body as plain text.

Q7. How do you return JSON from a Drupal controller?
Answer:

Use Symfony's JsonResponse. It serializes the supplied data as JSON
and sets the appropriate content type.

use Symfony\Component\HttpFoundation\JsonResponse;

public function getData(): JsonResponse {
  $data = [
    'status' => 'success',
    'message' => 'Data returned from Drupal',
  ];

  return new JsonResponse($data, JsonResponse::HTTP_OK);
}
Q8. What is the difference between an HTML response and a JSON response?
Answer:

An HTML response contains markup that a browser can render as a web
page. A JSON response contains structured data that another application
or frontend can consume. For example, a traditional Drupal site may
return HTML, while a decoupled frontend such as React or Next.js may
consume JSON from Drupal's JSON:API or a custom endpoint.

Q9. Does every HTTP request reach Drupal?
Answer:

No. A reverse proxy or CDN may serve a cached response before the
request reaches Drupal. For example, if Varnish has a valid cached
response, it can return that response directly. If the response is not
cached or is no longer valid, Varnish forwards the request to the
backend.

             Incoming request
                    |
                    v
                 Varnish
                    |
             Is response cached?
                /       \
              Yes        No
               |          |
               v          v
        Return cached   Forward to
          response       Drupal
               |          |
               |          v
               |      Process request
               |          |
               |          v
               |      Generate response
               |          |
               +----+-----+
                    |
                    v
                 Browser
2. External API Integration with Drupal
Q10. How do you integrate an external API with Drupal?
Answer:

I would generally use Drupal's HTTP Client service, which is based on
Guzzle, to communicate with the external API.

I would create a custom Drupal service responsible for making the API
request, handling the response and exposing the required data to the
rest of the application. I would inject that service into a controller,
form or other service rather than making HTTP calls directly from a
controller or a hook.

The integration typically involves:

Understand the API documentation, authentication method, endpoints,
request parameters and response format.
Configure the base URL and credentials securely.
Use dependency injection to access Drupal's HTTP Client.
Make the request with an appropriate HTTP method, headers, query
parameters or request body.
Handle the response and decode JSON when applicable.
Handle HTTP errors, network failures and timeouts.
Validate and map the external data into the format Drupal needs.
Cache data when appropriate to avoid unnecessary API calls.
Log useful diagnostic information without exposing secrets or
sensitive data.
Typical architecture:

Drupal Controller / Form
          |
          v
   Custom API Service
          |
          v
 Drupal HTTP Client
       (Guzzle)
          |
          v
    External API
          |
          v
 HTTP Response (JSON)
          |
          v
 Validate / Decode / Map
          |
          v
 Return data to Drupal
Q11. What is Guzzle, and how is it used in Drupal?
Answer:

Guzzle is a PHP HTTP client library used to send HTTP requests to
external services. Drupal provides an HTTP Client service built on
Guzzle, so we can make API calls using dependency injection.

For example, we can use the client to send GET, POST, PUT or DELETE
requests and receive an HTTP response.

Q12. How do you use Drupal's HTTP Client service in a custom service?
Answer:

Inject the http_client service into the constructor of a custom
service. This keeps the code testable and avoids creating the HTTP
client manually.

Service definition --- mymodule.services.yml:

services:
  mymodule.external_api:
    class: Drupal\mymodule\Service\ExternalApiService
    arguments: ['@http_client']
Service class --- src/Service/ExternalApiService.php:

<?php

namespace Drupal\mymodule\Service;

use GuzzleHttp\ClientInterface;

class ExternalApiService {

  public function __construct(
    protected ClientInterface $httpClient,
  ) {}

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
https://api.example.com/items is a placeholder. Replace it with the
real endpoint. In production, configure the endpoint rather than
hardcoding environment-specific values.

Q13. How do you call your custom API service from a controller?
Answer:

Inject the custom service into the controller through its constructor.
Then call the service method from the controller.

<?php

namespace Drupal\mymodule\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\mymodule\Service\ExternalApiService;
use Symfony\Component\DependencyInjection\ContainerInterface;

class ApiController extends ControllerBase {

  public function __construct(
    protected ExternalApiService $externalApi,
  ) {}

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
The controller delegates the API communication to the service. This
separation makes the integration easier to maintain and test.

Q14. Which HTTP methods are commonly used when integrating APIs?
Answer:

GET retrieves data.
POST creates a resource or submits data for processing.
PUT generally replaces or updates a resource.
PATCH partially updates a resource.
DELETE removes a resource.
The exact behavior depends on the API's contract and documentation.

Q15. How do you handle authentication when integrating an external API?
Answer:

It depends on the authentication mechanism supported by the external
API.

Common approaches include:

API key: send the key in the header or in the way specified by the
API.
Basic authentication: send a username and password over HTTPS.
Bearer token: send an access token in the Authorization header.
OAuth 2.0: obtain and refresh access tokens according to the
provider's flow.
Credentials and tokens should be stored securely, not hardcoded in
source code or committed to Git. HTTPS should be used for API
communication.

Example: bearer token:

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
Q16. How do you handle errors when an external API fails?
Answer:

I would handle API failures gracefully so that an external service
problem does not cause an uncontrolled failure in the Drupal
application.

I would consider:

HTTP status codes such as 400, 401, 403, 404, 429 and 500.
Connection failures and DNS errors.
Request timeouts.
Invalid or unexpected JSON responses.
Expired or invalid authentication tokens.
I would catch the relevant exceptions, log useful diagnostic details,
and return a controlled result or display an appropriate message. I
would avoid logging credentials, tokens or sensitive response data.

Guzzle normally throws exceptions for unsuccessful HTTP responses unless
configured otherwise. The code should handle those exceptions
deliberately.

Example:

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
    // Log the failure without exposing credentials.
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
In production code, inject Drupal's logger service as well, rather
than using the static \Drupal::logger() call. This example keeps the
error-handling idea visible in a compact snippet.

Q17. What are the best practices for external API integration in Drupal?
Answer:

Use Drupal's HTTP Client service and dependency injection.
Keep API communication in a dedicated custom service.
Store credentials securely and use HTTPS.
Set connection and request timeouts.
Handle HTTP errors, exceptions and invalid responses.
Validate external data before using or saving it.
Cache responses when the data does not need to be refreshed on every
request.
Use pagination for large API responses.
Respect API rate limits and handle HTTP 429 responses.
Log failures without exposing secrets or personal data.
Write unit or kernel tests using mocked HTTP responses.
Consider queues or background processing for slow or large
integrations.
Q18. How would you improve the performance of an external API integration?
Answer:

I would avoid making the same API request repeatedly. I would cache
responses when appropriate, set timeouts, request only the required
data, and use pagination for large datasets.

For long-running or bulk operations, I would consider Drupal's Queue API
or a background process instead of making users wait for the entire
operation during a page request.

I would also monitor API response times, failures and rate limits.

3. Caching in Drupal
Q19. What is caching, and why is it important in Drupal?
Answer:

Caching means temporarily storing data or a generated response so that
it can be reused instead of being generated or retrieved repeatedly.

Drupal uses caching to reduce database queries, avoid repeated
processing and improve page response times.

Caching can happen at different levels, including the browser, CDN,
reverse proxy, Drupal application and database infrastructure.

Q20. What is Varnish, and how does it work with Drupal?
Answer:

Varnish is an HTTP reverse proxy cache. It sits in front of the web
server and caches complete HTTP responses.

When a user requests a page, Varnish checks whether a valid cached
response exists. If it does, Varnish serves the response directly
without sending the request to Drupal. Otherwise, it forwards the
request to the backend.

In a Drupal setup, Varnish is installed and configured at the
infrastructure level. Drupal's reverse proxy settings and cache headers
must also be configured correctly so that Varnish can cache and
invalidate responses appropriately.

Q21. What is Redis, and how does Drupal use it?
Answer:

Redis is an in-memory data store that Drupal can use as an
application-level cache backend.

It stores frequently accessed cache data in memory, reducing the need to
retrieve the same data repeatedly from the database.

Redis must be installed and configured on the server. Drupal's Redis
integration is then configured to connect to it and use it for the
appropriate cache bins.

Q22. What is Memcached, and how does Drupal use it?
Answer:

Memcached is an in-memory caching system. Drupal can use it as a cache
backend to store frequently accessed application data in RAM.

It reduces repeated database access and can improve performance. The
Memcached service must be installed and configured on the server, and
Drupal's Memcache integration must be configured to use it.

Q23. What is the difference between Varnish, Redis and Memcached?
Answer:

Technology What it stores Layer Main purpose

Varnish Complete HTTP HTTP / reverse Serve cached
responses proxy responses before
Drupal

Redis Application cache Application / Fast in-memory
data and other data store data access
supported data
structures

Memcached Application cache Application cache Fast temporary
data as key-value data access
entries
Varnish works in front of Drupal and can serve a complete cached
response without invoking the application.

Redis and Memcached are used by the application to store and retrieve
cache data. They can reduce repeated database operations.

Q24. What is a reverse proxy?
Answer:

A reverse proxy is a server that sits in front of backend application
servers. It receives requests from clients and forwards them to the
appropriate backend.

It can also provide caching, security controls and traffic distribution.
Varnish is an example of a reverse proxy.

Q25. How do you configure Varnish, Redis and Memcached with Drupal?
Answer:

They require both infrastructure-level setup and Drupal-side
configuration.

Varnish: Install and configure Varnish on the server, configure
Drupal's reverse proxy settings in settings.php, and ensure cache
headers and invalidation are handled correctly.
Redis: Install Redis, enable the Drupal Redis integration,
configure the connection in settings.php and configure the
required cache bins.
Memcached: Install Memcached, enable the Drupal Memcache
integration, configure the server connection and configure Drupal
cache bins to use it.
Installing a Drupal module alone is not enough. The corresponding server
service must also be installed, running and reachable.

Q26. What are cache bins in Drupal?
Answer:

Cache bins are logical collections of cached data in Drupal. Different
types of cached data are stored in different bins.

For example, Drupal has bins for data such as render output, dynamic
page cache, discovery information and data. Cache bins use a cache
backend to store and retrieve cached items.

Q27. What are cache tags, cache contexts and cache max-age?
Answer:

These are three important parts of Drupal's cache metadata.

Cache tags: Identify the data a cached item depends on. When
that data changes, the related cache can be invalidated. For
example, node:10 can identify a dependency on node 10.
Cache contexts: Vary the cached output based on a request
context, such as user permissions, language or URL query arguments.
Cache max-age: Defines how long the cached item can remain
valid, in seconds. A max-age of 0 means the item should not be
cached.
Example:

return [
  '#markup' => 'Welcome!',
  '#cache' => [
    'tags' => ['node:10'],
    'contexts' => ['user.permissions'],
    'max-age' => 3600,
  ],
];
This example declares a dependency on node 10, varies the output by user
permissions and sets a maximum cache age of one hour.

Q28. What is the difference between cache invalidation and cache expiration?
Answer:

Cache invalidation means marking cached data as no longer valid because
something it depends on has changed. Drupal commonly uses cache tags for
targeted invalidation.

Cache expiration means cached data becomes invalid after a defined
period, often controlled by max-age or a backend's expiration settings.

Q29. What is the difference between Internal Page Cache and Dynamic Page Cache in Drupal?
Answer:

Internal Page Cache is primarily intended to cache complete pages for
anonymous users.

Dynamic Page Cache can cache page components and responses for both
anonymous and authenticated users, while taking cache contexts and
permissions into account.

Both help reduce the amount of work Drupal performs when serving
repeated requests.

4. Performance Optimization in Drupal
Q30. How do you improve the performance of a Drupal application?
Answer:

I approach performance optimization by identifying the bottleneck first
rather than making random changes.

My main areas of focus are:

Caching: Configure Drupal cache bins, cache tags, contexts and
max-age correctly. Use suitable infrastructure caching such as
Varnish and Redis where appropriate.
Database optimization: Review slow queries, add appropriate
indexes and avoid unnecessary entity loads or repeated queries.
Code optimization: Avoid redundant processing, unnecessary loops
and expensive operations during every page request.
API optimization: Cache external API responses, set timeouts and
avoid unnecessary calls.
Frontend optimization: Optimize images, reduce unnecessary
JavaScript and CSS, and enable aggregation where appropriate.
Infrastructure: Use appropriate PHP, web server, database and
server configurations.
Monitoring: Profile the application and measure response times
before and after changes.
Q31. Which tools do you use to identify performance issues in Drupal?
Answer:

I would use a combination of Drupal-specific tools, PHP profiling tools
and infrastructure monitoring.

Tool Purpose

Drupal Devel Development debugging and
inspection

Webprofiler Inspects requests, queries,
rendering and performance details
during development

Xdebug Debugging and profiling PHP code

Blackfire Profiling PHP applications and
identifying performance bottlenecks

New Relic Application performance monitoring

MySQL EXPLAIN Examines how database queries are
executed

Drush Runs Drupal maintenance and
diagnostic commands

Browser DevTools Inspects network requests,
JavaScript, CSS and frontend
performance
The exact tools depend on the environment. I would use profiling and
monitoring data to identify the actual bottleneck.

Q32. How do you optimize database performance in Drupal?
Answer:

I would first identify slow or frequently executed queries using
profiling and database monitoring tools.

Then I would:

Review query execution plans using EXPLAIN.
Add appropriate indexes where necessary.
Avoid unnecessary entity loads and repeated queries.
Use EntityQuery or Drupal's database API appropriately.
Retrieve only the fields and records required.
Use caching to avoid repeated database access.
Review custom code and Views that generate expensive queries.
Q33. How do you optimize Drupal frontend performance?
Answer:

I would optimize images, reduce unnecessary JavaScript and CSS, enable
CSS and JavaScript aggregation where appropriate, and minimize
render-blocking resources.

I would also use responsive images, lazy loading where suitable, browser
caching and a CDN for static assets when the infrastructure supports it.

Q34. What is the difference between performance monitoring and profiling?
Answer:

Performance monitoring tracks application performance over time, such as
response times, error rates, throughput and resource usage.

Profiling examines a specific request or execution to identify where
time or resources are being consumed, such as slow functions, database
queries or expensive operations.

Monitoring helps identify that a problem exists; profiling helps
investigate why it exists.

Q35. How would you troubleshoot a slow Drupal page?
Answer:

I would first reproduce the issue and determine whether it affects one
page, a particular user type or the entire application.

Then I would:

Check the browser Network tab for response time and slow assets.
Review application monitoring data.
Profile the request to identify slow PHP functions and database
queries.
Check cache hit rates and cache configuration.
Inspect external API calls and their response times.
Review database query execution plans.
Check server resources such as CPU, memory and PHP-FPM capacity.
Apply the required optimization and measure the result again.
Q36. What are common performance mistakes in Drupal?
Answer:

Common mistakes include:

Incorrect or missing cache metadata.
Repeated database queries inside loops.
Loading entities unnecessarily.
Making external API calls on every page request.
Performing expensive processing inside hooks or controllers.
Not using pagination for large datasets.
Poorly optimized Views and database queries.
Loading unnecessary JavaScript, CSS and large images.
Using cache contexts that are too broad or too specific without
understanding their impact.
5. Practical Scenarios
Q37. An external API is taking five seconds to respond. How would you handle it?
Answer:

I would first measure the API response time and determine whether the
delay is consistent or intermittent.

I would set appropriate connection and request timeouts, cache the
response if the data does not need to be real-time, and avoid making the
API call on every page load.

If the operation is long-running, I would consider processing it
asynchronously using Drupal's Queue API. I would also log failures and
monitor response times.

Q38. An external API is temporarily unavailable. How would you prevent it from affecting the Drupal website?
Answer:

I would handle connection failures and HTTP errors gracefully, use
timeouts, and avoid allowing the exception to break the entire page.

If appropriate, I would serve previously cached data or a fallback
response. For repeated failures, I would consider retry policies with
backoff and asynchronous processing. I would also log the failure and
monitor the integration.

Q39. A Drupal page is accessed by thousands of anonymous users. How would you improve its performance?
Answer:

I would make sure Drupal's Internal Page Cache and Dynamic Page Cache
are configured appropriately. I would also consider Varnish or a CDN to
serve cached responses closer to users and reduce requests reaching
Drupal.

I would verify cache metadata, optimize database queries, and monitor
cache hit rates, response times and server resources.

Q40. A page displays different content depending on the logged-in user. How would you cache it correctly?
Answer:

I would use the appropriate cache contexts to vary the output based on
the relevant user-specific information, such as user permissions or user
identity, depending on the requirement.

I would use cache tags to invalidate the output when its underlying data
changes and set an appropriate max-age.

I would avoid using a single shared cache entry for content that differs
between users.

6. Quick Revision Tables
Q41. Summarize the main components involved in a Drupal HTTP request.
Answer:

Component Responsibility

Browser Sends the HTTP request and receives the response
DNS Resolves the domain name to an IP address
CDN Distributes and may cache content
Load balancer Distributes traffic across backend servers
Varnish Caches HTTP responses
Nginx / Apache Handles incoming web requests
PHP Executes Drupal's PHP code
index.php Entry point for Drupal web requests
Drupal kernel Bootstraps and handles the request
Routing Matches the URL to a route
Access checks Determines whether the request is allowed
Controller Handles request-specific application logic
Services Provide reusable application functionality
Redis / Memcached Store application cache data in memory
Database Stores persistent application data
Render API Converts render arrays into output

Q42. Summarize the main caching concepts in Drupal.
Answer:

Concept Meaning

Cache bin Logical collection of cached data

Cache tag Identifies a dependency for
invalidation

Cache context Varies cached output based on
request context

Cache max-age Defines how long cached data
remains valid

Varnish Reverse proxy that caches HTTP
responses

Redis In-memory data store usable as a
Drupal cache backend

Memcached In-memory caching system usable as
a Drupal cache backend

Internal Page Cache Primarily caches pages for
anonymous users

Dynamic Page Cache Caches dynamic page responses and
components
Q43. Summarize the recommended practices for external API integration.
Answer:

Use Drupal's HTTP Client service.
Use dependency injection.
Keep API calls in a dedicated custom service.
Store credentials securely.
Use HTTPS.
Configure timeouts.
Handle exceptions and HTTP status codes.
Validate and map API responses.
Cache responses where appropriate.
Respect rate limits.
Log failures safely.
Test API integrations using mocked responses.
Use queues for long-running operations.
7. Last-Minute Interview Revision
Q44. Explain the difference between Varnish and Redis in one sentence.
Answer:

Varnish caches complete HTTP responses before they reach Drupal, whereas
Redis stores application-level data in memory for Drupal to retrieve
quickly.

Q45. How do you integrate an external API with Drupal in one sentence?
Answer:

I use Drupal's HTTP Client service through dependency injection,
encapsulate the API communication in a custom service, and handle
authentication, responses, errors, timeouts and caching appropriately.

Q46. What are the three most important Drupal cache metadata concepts?
Answer:

Cache tags identify when cached data should be invalidated, cache
contexts determine when cached output should vary, and cache max-age
defines how long the cached data remains valid.

Q47. What is your approach to Drupal performance optimization?
Answer:

I identify the bottleneck through monitoring and profiling, then
optimize caching, database queries, application code, external API
calls, frontend assets and infrastructure based on the measurements.

8. Important Commands for Revision
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
9. Topics to Be Ready to Explain Verbally
Explain an external API integration you have implemented.
Explain why you would use a custom service instead of putting API
logic in a controller.
Explain how dependency injection works in Drupal.
Explain how you handle API authentication and failures.
Explain how you prevent repeated API calls.
Explain Varnish versus Redis.
Explain cache tags, contexts and max-age with an example.
Explain Internal Page Cache versus Dynamic Page Cache.
Explain how you investigate a slow Drupal page.
Explain which tools you use for profiling and monitoring.
Explain how you optimize database queries.
Explain the complete HTTP request and response lifecycle.
Interview reminder: Give the direct answer first, then explain the
implementation or provide an example. For questions about your own
experience, use a real project example rather than claiming experience
with a tool or implementation you have not personally used.







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
