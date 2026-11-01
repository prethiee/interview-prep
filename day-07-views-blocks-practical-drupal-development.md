# Day 7 – Views, Blocks, and Practical Drupal Development

## 1. Drupal Views

### 1.1 What is Drupal Views?

Views is a powerful Drupal core module used to retrieve, display and organize content from the database without writing custom SQL queries for common listing requirements.

It allows site builders and developers to create:
- Content listings.
- User listings.
- Taxonomy listings.
- Search result pages.
- Tables.
- Grids.
- RSS feeds.
- Blocks.
- REST exports.

Views provides a user interface to configure queries, filters, sorting, relationships and display formats.

### 1.2 Why Do We Use Views?

Views reduces the need to write custom queries for common content listing requirements.

Benefits:
- No need to write SQL for standard listings.
- Supports multiple display formats.
- Provides filtering and sorting.
- Supports relationships between entities.
- Supports contextual filters.
- Supports exposed filters.
- Supports pagination.
- Supports caching.

Example:

A website needs to display the latest 10 published articles.

Instead of writing a custom database query, we can create a View that:
1. Uses Content as its base.
2. Filters by content type Article.
3. Filters by Published status.
4. Sorts by creation date in descending order.
5. Limits results to 10.

### 1.3 Creating a View

Navigate to:

Structure → Views → Add view.

Example configuration:

| Setting | Value |
|---|---|
| View name | Latest Articles |
| Show | Content |
| Content type | Article |
| Published | Yes |
| Sort | Authored on, descending |
| Items per page | 10 |
| Display | Page |

Save the View and visit the configured page.

### 1.4 Views Architecture

A View generally consists of:

1. Base table.
2. Fields.
3. Filters.
4. Sort criteria.
5. Relationships.
6. Contextual filters.
7. Display configuration.
8. Query execution.
9. Output rendering.

**Base Table**

Defines the primary data source for the View.

Examples:
- Content.
- Users.
- Taxonomy terms.
- Files.

**Fields**

Define which information should be displayed.

Examples:
- Title.
- Body.
- Author.
- Created date.
- Image.

**Filters**

Restrict which records are returned.

Example: Show only published articles.

**Sort Criteria**

Determine the order in which results are displayed.

Example: Sort articles by creation date, newest first.

**Relationships**

Allow a View to access related entity data.

Example: Display the author's username alongside article information.

**Display**

Defines how the View output is presented.

Examples:
- Page.
- Block.
- Attachment.
- Feed.
- REST export.

### 1.5 Filters vs Exposed Filters

**Filters**

Filters are configured by the developer or site builder and are generally applied automatically.

Example:

Show only published articles.

**Exposed Filters**

Exposed filters allow website visitors to select filter criteria through the frontend.

Example:

A job listing page allows visitors to filter jobs by:
- Location.
- Department.
- Employment type.

| Filters | Exposed Filters |
|---|---|
| Configured internally. | Available to users on the frontend. |
| Automatically applied. | Users choose filter values. |
| Controls the dataset. | Allows interactive filtering. |

### 1.6 Contextual Filters

Contextual filters dynamically filter View results using values supplied by the current route, URL, or another configured context.

Example:

A page displays articles belonging to a particular taxonomy term.

If the URL contains:

`/articles/technology`

The View can use the taxonomy term identifier or another configured contextual value to display only matching articles.

Contextual filters are useful for:
- User-specific content.
- Taxonomy-based listings.
- Content related to the current node.
- Dynamic pages.
- Category-based listings.

### 1.7 Exposed Filters vs Contextual Filters

| Exposed Filters | Contextual Filters |
|---|---|
| Users select filter values. | Values are supplied dynamically through context. |
| Usually displayed as form elements. | Often derived from URL, route or another context. |
| Example: Select a location. | Example: Show content for the current taxonomy term. |

**Interview Question: What is the difference between exposed filters and contextual filters?**

Exposed filters allow users to select filtering criteria through the frontend. Contextual filters dynamically filter results based on values supplied by the URL, route or another configured context.

### 1.8 Views Relationships

A relationship allows a View to access data from a related entity or table.

Example:

An Article references an Author through its author field.

A relationship can be added to access the author's information and display the author's name.

Other examples:
- Node to taxonomy term.
- Node to author.
- Content to referenced media.
- User to related entities.

### 1.9 Relationships vs Contextual Filters

A relationship provides access to related data.

A contextual filter restricts the results using a dynamic value.

Example:

A View displays articles and author names.

- Relationship: Connects articles to their authors.
- Contextual filter: Restricts articles to a particular author.

### 1.10 Views Display Types

Drupal Views supports different display types.

**Page Display**

Creates a standalone page with its own URL.

Example: `/latest-articles`

**Block Display**

Creates a reusable View block that can be placed in a theme region.

Example: Latest articles displayed in the sidebar.

**Attachment Display**

Attaches additional View output to another display.

Example: A summary or related listing attached to a page display.

**Feed Display**

Provides feed output such as RSS.

**REST Export Display**

Exposes View results through a REST-style export, depending on the configured serializer and access settings.

### 1.11 Views Pagination

Pagination divides a large result set into smaller pages.

Example:

Instead of displaying 500 articles on one page, display 10 articles per page.

Benefits:
- Reduces the number of displayed records.
- Improves usability.
- Can reduce data retrieval and rendering overhead, depending on the query.
- Makes large listings easier to navigate.

Important: Pagination alone does not guarantee good query performance. The SQL query, joins, filters and sorting must also be optimized.

### 1.12 Views Caching

Views supports caching to reduce repeated query execution and rendering.

Common caching approaches include:
- Time-based caching.
- Tag-based invalidation.

The exact available cache plugins depend on the Views display and cache configuration.

Example:

A news listing can be cached for five minutes or invalidated when relevant content changes.

### 1.13 How to Optimize a Slow View

When a View becomes slow:

1. Inspect the generated SQL query.
2. Check expensive joins and relationships.
3. Review filters and sorting.
4. Check database indexes.
5. Remove unnecessary fields.
6. Avoid loading large amounts of data.
7. Implement pagination.
8. Configure appropriate caching.
9. Measure query execution time.

### 1.14 Views vs Custom Database Query

| Views | Custom Query |
|---|---|
| Suitable for common listing requirements. | Suitable for specialized query requirements. |
| Provides a configuration interface. | Requires PHP or SQL implementation. |
| Supports filters, sorting and relationships. | Requires custom implementation of these features. |
| Easier for site builders to maintain. | Provides more control over complex logic. |

### 1.15 When Would You Use Views or Custom Code?

Use Views when:
- Creating standard content listings.
- Creating user listings.
- Building searchable directories.
- Displaying taxonomy-based content.
- Creating reusable blocks.

Use custom code when:
- Business logic is highly specialized.
- Complex calculations are required.
- The query is difficult to represent through Views.
- A specialized API or data-processing operation is needed.

**Interview Questions – Views**

**Q1. What is Drupal Views?**

Views is a Drupal core module used to retrieve, filter, sort and display content or other entity data without writing custom SQL for common listing requirements.

**Q2. What are the main components of Views?**

Base table, fields, filters, sort criteria, relationships, contextual filters, displays and caching.

**Q3. What is the difference between filters and exposed filters?**

Filters are configured internally and applied automatically. Exposed filters allow website visitors to select filtering criteria through the frontend.

**Q4. What are contextual filters?**

Contextual filters dynamically filter View results based on values supplied by the URL, route or another configured context.

**Q5. What is a relationship in Views?**

A relationship allows a View to access related data from another entity or table.

**Q6. What is the difference between a relationship and a contextual filter?**

A relationship connects related data for display or filtering. A contextual filter restricts results based on a dynamic value.

**Q7. What are the different Views display types?**

Page, Block, Attachment, Feed and REST Export.

**Q8. How do you optimize a slow View?**

I inspect the generated SQL query, review joins, filters and sorting, check database indexes, remove unnecessary fields, implement pagination and configure appropriate caching.

**Q9. When would you use Views instead of a custom query?**

I use Views for standard content listings and filtering requirements. For complex business logic or specialized data processing, I consider custom queries or application code.

**Q10. What is the difference between a Page display and a Block display?**

A Page display creates a standalone page with a URL, whereas a Block display creates reusable output that can be placed in a theme region.

**Memory Tip:** Fields = Display. Filters = Restrict. Sort = Order. Relationship = Connect. Contextual Filter = Dynamic Restriction.

---

## 2. Drupal Blocks

### 2.1 What is a Block in Drupal?

A block is a reusable component of content or functionality that can be placed in different regions of a Drupal theme.

Examples:
- Search form.
- Navigation menu.
- Login form.
- Recent articles.
- Social media links.
- Custom promotional content.

Blocks allow content and functionality to be displayed in specific regions of a website.

### 2.2 Block Regions

A Drupal theme defines regions where blocks can be placed.

Common regions include:
- Header.
- Primary menu.
- Sidebar.
- Content.
- Footer.

The available regions depend on the active theme.

### 2.3 Types of Blocks

Drupal supports several kinds of blocks.

**Custom Blocks**

Created by site administrators using the Block Content system.

Example: A promotional message or footer information.

**Plugin Blocks**

Created by modules using Drupal's Block plugin system.

Example: A custom block displaying the latest articles.

**Views Blocks**

Created using a View's Block display.

Example: A block displaying the latest published news.

### 2.4 Creating a Custom Block Plugin

File location:

`web/modules/custom/my_module/src/Plugin/Block/HelloBlock.php`

```php
<?php

namespace Drupal\my_module\Plugin\Block;

use Drupal\Core\Block\BlockBase;

/**
 * Provides a Hello World block.
 *
 * @Block(
 *   id = "hello_block",
 *   admin_label = @Translation("Hello Block")
 * )
 */
class HelloBlock extends BlockBase {

  public function build(): array {
    return [
      '#markup' => $this->t('Hello from Drupal!'),
    ];
  }

}
```

**Explanation:**

- `BlockBase`: Base class for block plugins.
- `@Block`: Declares the class as a block plugin using annotation discovery.
- `id`: Unique plugin identifier.
- `admin_label`: Administrative name of the block.
- `build()`: Returns the render array.

After creating the block:

```bash
drush cr
```

Navigate to Structure → Block layout and place the block.

Note: Drupal 11 supports PHP attributes for many plugin types. Annotation-based block plugins remain common in existing Drupal projects and are still supported.

### 2.5 Block Plugin Lifecycle

The general process is:

1. Drupal discovers the block plugin.
2. The plugin manager manages the plugin definition.
3. Drupal creates the block instance.
4. The block's `build()` method generates a render array.
5. Drupal renders the output.
6. The output can be cached according to its cache metadata.

### 2.6 Block Configuration

Blocks can have configuration that controls their behaviour.

Examples:
- Number of articles to display.
- Selected content type.
- Display heading.
- Custom text.
- Visibility conditions.

For configurable block plugins, developers can use methods such as:

- `blockForm()`
- `blockSubmit()`
- `defaultConfiguration()`

Example:

```php
public function defaultConfiguration(): array {
  return [
    'message' => 'Hello from Drupal!',
  ] + parent::defaultConfiguration();
}

public function blockForm($form, \Drupal\Core\Form\FormStateInterface $form_state): array {
  $form['message'] = [
    '#type' => 'textfield',
    '#title' => $this->t('Message'),
    '#default_value' => $this->configuration['message'],
  ];

  return $form;
}

public function blockSubmit($form, \Drupal\Core\Form\FormStateInterface $form_state): void {
  $this->configuration['message'] = $form_state->getValue('message');
}
```

The `build()` method can then use the configured value:

```php
public function build(): array {
  return [
    '#markup' => $this->configuration['message'],
  ];
}
```

### 2.7 Block Visibility Conditions

Drupal allows blocks to be displayed only under specific conditions.

Examples:
- Only on specific pages.
- Only for authenticated users.
- Only for administrators.
- Only for selected content types.
- Only for particular languages.

Visibility conditions help control where and to whom a block is displayed.

### 2.8 Block Cache Metadata

Blocks can use cache tags, contexts and max-age.

Example:

```php
public function build(): array {
  return [
    '#markup' => $this->t('Welcome!'),
    '#cache' => [
      'contexts' => ['user'],
      'tags' => ['node_list'],
      'max-age' => 3600,
    ],
  ];
}
```

This example:
- Varies the output by user.
- Depends on node listing data.
- Has a one-hour maximum cache lifetime.

The actual cache metadata should reflect the block's real dependencies.

### 2.9 Block vs Views Block

| Block Plugin | Views Block |
|---|---|
| Custom functionality implemented as a plugin. | A block generated from a View. |
| Requires PHP development for custom behaviour. | Configured through Views UI. |
| Suitable for specialized business logic. | Suitable for content listings. |
| Can have custom configuration forms. | Uses Views configuration. |

### 2.10 Block vs Region

A block is a reusable content or functionality component.

A region is a predefined location in a theme where blocks can be placed.

Example:

The header is a region, and the search form is a block placed inside that region.

**Interview Questions – Blocks**

**Q1. What is a block in Drupal?**

A block is a reusable component of content or functionality that can be placed in different regions of a Drupal theme.

**Q2. What are the different types of blocks?**

Custom blocks, plugin blocks and Views blocks.

**Q3. What is BlockBase?**

BlockBase is the base class used to create custom block plugins.

**Q4. What is the purpose of the build() method?**

It returns a render array containing the block's output.

**Q5. What is block visibility?**

Block visibility determines where and under what conditions a block is displayed, based on factors such as route, role, content type or language.

**Q6. How do you make a block configurable?**

Override methods such as `defaultConfiguration()`, `blockForm()` and `blockSubmit()` to define, display and save block configuration.

**Q7. How do you make a block user-specific?**

Add the appropriate cache context, such as `user`, to ensure the output varies by user.

**Q8. What is the difference between a block and a region?**

A block is the content or functionality component. A region is the location in the theme where the block is placed.

**Memory Tip:** Block = Component. Region = Location.

---

## 3. Drupal Practical Development

### 3.1 Understanding Drupal Module Structure

A custom module is used to add functionality to Drupal.

Typical structure:

```text
my_module/
├── my_module.info.yml
├── my_module.routing.yml
├── my_module.services.yml
├── my_module.module
├── config/
│   └── install/
├── src/
│   ├── Controller/
│   ├── Form/
│   ├── Plugin/
│   │   └── Block/
│   └── EventSubscriber/
└── templates/
```

Important files:

| File | Purpose |
|---|---|
| `.info.yml` | Module metadata |
| `.routing.yml` | Route definitions |
| `.services.yml` | Service definitions |
| `.module` | Hook implementations |
| `src/Controller/` | Controllers |
| `src/Form/` | Form classes |
| `src/Plugin/` | Plugin implementations |
| `src/EventSubscriber/` | Event subscribers |
| `config/install/` | Default configuration installed with the module |

### 3.2 Creating a Custom Module

File: `my_module.info.yml`

```yaml
name: My Custom Module
type: module
description: Custom functionality for Drupal.
core_version_requirement: ^11
package: Custom
```

Enable the module:

```bash
drush en my_module -y
```

### 3.3 Creating a Custom Controller

File: `src/Controller/HelloController.php`

```php
<?php

namespace Drupal\my_module\Controller;

use Drupal\Core\Controller\ControllerBase;

class HelloController extends ControllerBase {

  public function content(): array {
    return [
      '#markup' => $this->t('Hello from my custom module!'),
    ];
  }

}
```

Route:

```yaml
my_module.hello:
  path: '/hello'
  defaults:
    _controller: '\Drupal\my_module\Controller\HelloController::content'
    _title: 'Hello'
  requirements:
    _permission: 'access content'
```

### 3.4 Creating a Custom Service

A service provides reusable functionality that can be injected into controllers, forms and other services.

File: `my_module.services.yml`

```yaml
services:
  my_module.greeting:
    class: Drupal\my_module\GreetingService
```

File: `src/GreetingService.php`

```php
<?php

namespace Drupal\my_module;

class GreetingService {

  public function getMessage(): string {
    return 'Hello from the custom service!';
  }

}
```

### 3.5 Injecting the Service into a Controller

```php
<?php

namespace Drupal\my_module\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\my_module\GreetingService;
use Symfony\Component\DependencyInjection\ContainerInterface;

class HelloController extends ControllerBase {

  protected GreetingService $greetingService;

  public function __construct(GreetingService $greeting_service) {
    $this->greetingService = $greeting_service;
  }

  public static function create(ContainerInterface $container): static {
    return new static(
      $container->get('my_module.greeting')
    );
  }

  public function content(): array {
    return [
      '#markup' => $this->greetingService->getMessage(),
    ];
  }

}
```

This demonstrates dependency injection in Drupal.

### 3.6 Entity Loading in Custom Development

Example:

```php
$node = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->load(1);

if ($node) {
  $title = $node->getTitle();
}
```

In production object-oriented code, prefer injecting the entity type manager instead of using the global service locator.

### 3.7 Render Arrays

A render array is a structured PHP array that Drupal uses to describe content that should be rendered.

Example:

```php
$build = [
  '#theme' => 'item_list',
  '#items' => [
    'Drupal',
    'PHP',
    'MySQL',
  ],
];
```

Drupal uses render arrays to manage:
- Output.
- Theme hooks.
- Attachments.
- Cache metadata.
- Access restrictions.
- Libraries.

### 3.8 Render Array vs HTML

| Render Array | HTML |
|---|---|
| Structured PHP representation of output. | Markup language used to represent web content. |
| Processed by Drupal's rendering system. | Directly interpreted by the browser. |
| Supports cache metadata and theme integration. | Does not automatically provide Drupal render metadata. |
| Preferred for Drupal output. | Useful for final rendered markup. |

### 3.9 Attaching CSS and JavaScript Libraries

Drupal uses asset libraries defined in `.libraries.yml` files.

Example:

File: `my_module.libraries.yml`

```yaml
global:
  version: 1.x
  css:
    theme:
      css/style.css: {}
  js:
    js/script.js: {}
  dependencies:
    - core/drupal
    - core/once
```

Attach the library to a render array:

```php
$build['#attached']['library'][] = 'my_module/global';
```

This allows Drupal's asset management system to load the required CSS and JavaScript.

### 3.10 Drupal Coding Best Practices

- Follow Drupal coding standards.
- Use dependency injection.
- Avoid hardcoding configuration.
- Use Entity API instead of directly querying entity tables.
- Use Form API for forms.
- Use render arrays for output.
- Add appropriate cache metadata.
- Implement access checks.
- Use translation functions for user-facing strings.
- Avoid unnecessary database queries.
- Use services for reusable business logic.
- Keep controllers thin.
- Separate business logic from presentation.

---

## 4. Practical Interview Scenarios

### Scenario 1: Display the latest five published articles on the homepage.

Use a View with:
- Content as the base table.
- Article content type filter.
- Published status filter.
- Created date descending sort.
- Limit of five results.
- Block display.

### Scenario 2: Display articles belonging to the current taxonomy term.

Use a View with a contextual filter based on the taxonomy term identifier or another configured route context.

### Scenario 3: Display author names alongside articles.

Add a relationship between content and the author entity, then add the author name as a field.

### Scenario 4: Allow visitors to filter a job listing by location.

Create a View with an exposed filter on the location field.

### Scenario 5: Create a reusable promotional component.

Create a custom Block plugin or a reusable custom block, depending on whether the component requires custom programmatic behaviour.

### Scenario 6: Display a block only to authenticated users.

Configure block visibility based on the authenticated user role or appropriate visibility condition.

### Scenario 7: A block displays incorrect information for different users.

Review the cache contexts and add the appropriate context, such as `user`, if the output varies by user identity.

### Scenario 8: A View takes too long to load.

Inspect its SQL query, joins, filters, sorting, indexes and cache configuration. Reduce unnecessary data retrieval and apply pagination.

### Scenario 9: A custom module needs to reuse the same business logic in multiple controllers.

Create a service and inject it into the controllers using dependency injection.

### Scenario 10: You need to display custom content with proper Drupal caching.

Return a render array and add the appropriate cache tags, contexts and max-age.

---

## 5. Important Differences – Quick Revision

| Concept | Difference |
|---|---|
| Views vs Custom Query | Configurable listing system vs custom query implementation |
| Filters vs Exposed Filters | Internal filtering vs user-controlled filtering |
| Exposed vs Contextual Filters | User-selected values vs dynamically supplied values |
| Relationship vs Contextual Filter | Access related data vs dynamically restrict results |
| Page vs Block Display | Standalone page vs reusable block |
| Block vs Region | Component vs theme location |
| Custom Block vs Block Plugin | Content-managed block vs programmatic block |
| Block Plugin vs Views Block | Custom PHP component vs View-generated block |
| BlockBase vs ControllerBase | Base class for block plugins vs base class for controllers |
| Render Array vs HTML | Drupal output structure vs browser markup |
| Service vs Controller | Reusable functionality vs request handler |
| Service vs Plugin | Reusable application functionality vs plugin-type component |

---

## 6. Final Interview Revision

Remember these important points:

1. Views is used to retrieve, filter, sort and display content.
2. A View consists of a base table, fields, filters, sorting, relationships and displays.
3. Exposed filters allow visitors to filter results.
4. Contextual filters use dynamic values from routes or other configured contexts.
5. Relationships allow Views to access related entity data.
6. Views supports Page, Block, Attachment, Feed and REST Export displays.
7. Pagination divides large result sets into smaller pages.
8. Views caching reduces repeated query and rendering operations.
9. Blocks are reusable components placed in theme regions.
10. Custom block plugins extend Drupal through the Block plugin system.
11. `BlockBase` is the base class for custom block plugins.
12. `build()` returns a render array.
13. Block visibility controls where and to whom a block is displayed.
14. Block cache metadata controls variation, invalidation and expiration.
15. Custom modules are used to extend Drupal functionality.
16. Services provide reusable business logic.
17. Dependency injection improves maintainability and testability.
18. Render arrays are Drupal's preferred way of representing renderable output.
19. Asset libraries manage CSS and JavaScript.
20. Drupal coding standards, access checks and cache metadata are important for production development.

## 7. Day 7 Completion Checklist

- [ ] Understand Drupal Views architecture.
- [ ] Create a basic View.
- [ ] Understand base tables, fields, filters and sorting.
- [ ] Understand exposed filters.
- [ ] Understand contextual filters.
- [ ] Understand Views relationships.
- [ ] Understand Page and Block displays.
- [ ] Understand Views pagination and caching.
- [ ] Troubleshoot a slow View.
- [ ] Understand custom blocks.
- [ ] Create a custom Block plugin.
- [ ] Understand block configuration and visibility.
- [ ] Understand block cache metadata.
- [ ] Understand Drupal module structure.
- [ ] Create a custom module.
- [ ] Create a controller and route.
- [ ] Create and inject a service.
- [ ] Understand render arrays.
- [ ] Attach CSS and JavaScript libraries.
- [ ] Practice real-world Drupal development scenarios.

**End of Day 7 – Views, Blocks, and Practical Drupal Development.**
