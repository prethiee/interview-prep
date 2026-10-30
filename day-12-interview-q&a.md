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
