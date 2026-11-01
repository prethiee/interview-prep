# Day 4 – Plugins, Routing, Forms, Hooks, and Events

## 1. Drupal Plugins

### 1.1 What is a Plugin?

A plugin is a reusable component that provides specific functionality within Drupal.

Plugins allow Drupal to support extensible functionality without hardcoding every implementation.

Drupal uses plugins for:
- Blocks
- Fields
- Views
- Image effects
- Queue workers
- Actions
- Conditions

Each plugin belongs to a particular plugin type and is managed by a plugin manager.

### 1.2 How Do Plugins Work?

Drupal plugins generally involve three important concepts:

1. **Plugin type:** Defines the category and purpose of the plugin.
2. **Plugin definition:** Contains metadata such as plugin ID, label and class.
3. **Plugin manager:** Discovers, creates and manages plugins of that type.

For example, Drupal's Block plugin manager discovers block plugins and makes them available in the Block Layout interface.

### 1.3 Example: Creating a Custom Block Plugin

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
      '#markup' => $this->t('Hello, Drupal!'),
    ];
  }

}
```

**Explanation:**

- `BlockBase`: Base class for custom block plugins.
- `@Block`: Identifies the class as a block plugin.
- `id`: Unique plugin identifier.
- `admin_label`: Name displayed in the administration interface.
- `build()`: Returns a render array containing the block output.
- `$this->t()`: Translates the text.

After creating the plugin:

```bash
drush cr
```

Navigate to Structure → Block layout and place the custom block.

### 1.4 Plugin Discovery

Drupal discovers plugins using different mechanisms depending on the plugin type.

Common discovery mechanisms include:
- PHP attributes.
- Annotations in plugin classes in plugin types that still support annotation discovery.
- YAML-based definitions for certain plugin types.

**Important:** Drupal 11 supports PHP attributes for many plugin types. Existing annotation-based plugins are still commonly encountered in Drupal projects. Always follow the discovery mechanism supported by the particular plugin type.

### 1.5 Plugin Manager

A plugin manager is responsible for discovering and managing plugins belonging to a particular plugin type.

Common responsibilities:
- Discover plugin definitions.
- Create plugin instances.
- Retrieve plugin definitions.
- Manage plugin instances.

Examples include:
- BlockManager
- FieldTypePluginManager
- FilterPluginManager

### 1.6 Plugin vs Service

| Plugin | Service |
|---|---|
| Represents a specific reusable component. | Provides reusable application functionality. |
| Belongs to a plugin type. | Registered in the service container. |
| Managed by a plugin manager. | Managed by the service container. |
| Example: Custom block plugin. | Example: Custom business logic service. |

**Interview Questions**

**Q1. What is a Drupal plugin?**

A Drupal plugin is a reusable component that provides specific functionality within a defined plugin type. Plugins are discovered and managed by plugin managers.

**Q2. What is a plugin manager?**

A plugin manager discovers, creates and manages plugins belonging to a particular plugin type.

**Q3. What is the difference between a plugin and a service?**

A service provides reusable functionality and is managed by the service container. A plugin represents a reusable component belonging to a specific plugin type and is managed by a plugin manager.

**Q4. What is a Block plugin?**

A Block plugin is a reusable component that provides content or functionality that can be placed into regions of a Drupal theme.

**Q5. What is plugin discovery?**

Plugin discovery is the process through which Drupal identifies available plugin definitions using the discovery mechanism configured for that plugin type.

**Memory Tip:** Plugin = Component. Plugin Manager = Discovers and manages components.

---

## 2. Drupal Routing

### 2.1 What is Routing?

Routing is the mechanism that maps an incoming URL to a controller, form or another request handler.

For example, when a user visits `/hello`, Drupal identifies the matching route and executes the associated controller.

Routes are generally defined in a module's `.routing.yml` file.

### 2.2 Creating a Custom Route

File: `my_module.routing.yml`

```yaml
my_module.hello:
  path: '/hello'
  defaults:
    _controller: '\Drupal\my_module\Controller\HelloController::hello'
    _title: 'Hello Page'
  requirements:
    _permission: 'access content'
```

**Explanation:**

- `my_module.hello`: Unique route name.
- `path`: URL accessed by the user.
- `_controller`: Controller class and method.
- `_title`: Page title.
- `_permission`: Permission required to access the route.

### 2.3 Creating a Controller

File: `src/Controller/HelloController.php`

```php
<?php

namespace Drupal\my_module\Controller;

use Drupal\Core\Controller\ControllerBase;

class HelloController extends ControllerBase {

  public function hello(): array {
    return [
      '#markup' => $this->t('Hello from Drupal!'),
    ];
  }

}
```

The controller handles the request and returns a render array.

Clear the cache:

```bash
drush cr
```

Visit:

`/hello`

### 2.4 Route Requirements and Access Control

Drupal supports different route access requirements.

**Permission-based access**

```yaml
requirements:
  _permission: 'access content'
```

**Role-based access**

```yaml
requirements:
  _role: 'authenticated'
```

**Entity access**

```yaml
requirements:
  _entity_access: 'node.view'
```

Entity access requirements allow Drupal to check whether the current user has permission to perform an operation on an entity.

**Custom access checking** can also be implemented using an access-checking service.

### 2.5 Route Parameters

Routes can accept dynamic parameters.

Example:

```yaml
my_module.user:
  path: '/user-details/{uid}'
  defaults:
    _controller: '\Drupal\my_module\Controller\UserController::details'
  requirements:
    _permission: 'access content'
    uid: '\d+'
```

Controller:

```php
public function details($uid): array {
  return [
    '#markup' => 'User ID: ' . $uid,
  ];
}
```

The `{uid}` parameter is passed to the controller method.

In production, user-provided values must be handled safely and output should be escaped appropriately.

### 2.6 Controller vs Route

| Route | Controller |
|---|---|
| Defines the URL and request configuration. | Handles the request. |
| Defined in `.routing.yml`. | Defined in a PHP class. |
| Specifies access requirements. | Executes application logic. |
| Maps URL to handler. | Returns a response. |

### 2.7 What Happens When a User Visits a Drupal URL?

1. Browser sends an HTTP request.
2. Drupal's request handling process receives the request.
3. Router matches the incoming URL to a route.
4. Access requirements are checked.
5. The associated controller or form handler is executed.
6. Drupal generates the response.
7. The response is returned to the browser.

**Interview Questions**

**Q1. What is routing in Drupal?**

Routing maps incoming URLs to controllers or other request handlers that process the request.

**Q2. Where do you define custom routes?**

Custom routes are defined in a module's `.routing.yml` file.

**Q3. What is the purpose of a controller?**

A controller handles a request, executes the required application logic and returns a response, commonly as a render array.

**Q4. How do you restrict access to a custom route?**

Access can be restricted using route requirements such as `_permission`, `_role`, `_entity_access` or a custom access checker.

**Q5. How do you pass dynamic parameters through a route?**

Define placeholders in the route path, such as `{uid}`, and receive them as arguments in the controller method.

**Q6. What is the difference between a route and a controller?**

A route defines the URL and maps it to a handler. A controller contains the logic that handles the request.

**Memory Tip:** Route → Access Check → Controller → Response.

---

## 3. Drupal Form API

### 3.1 What is Drupal Form API?

Drupal Form API is a framework used to build, validate and process forms.

It provides:
- Standard form elements.
- Server-side validation.
- Submission handling.
- Form alteration.
- CSRF protection.
- Form state management.

Forms in Drupal are generally created using classes that extend `FormBase` or `ConfigFormBase`.

### 3.2 Form API Lifecycle

The general form lifecycle is:

1. `getFormId()`
2. `buildForm()`
3. `validateForm()`
4. `submitForm()`

`validateForm()` and `submitForm()` are invoked during form submission, in that order. They are not called when the form is initially displayed.

### 3.3 Creating a Custom Form

File: `src/Form/ContactForm.php`

```php
<?php

namespace Drupal\my_module\Form;

use Drupal\Core\Form\FormBase;
use Drupal\Core\Form\FormStateInterface;

class ContactForm extends FormBase {

  public function getFormId(): string {
    return 'my_module_contact_form';
  }

  public function buildForm(array $form, FormStateInterface $form_state): array {

    $form['name'] = [
      '#type' => 'textfield',
      '#title' => $this->t('Name'),
      '#required' => TRUE,
    ];

    $form['email'] = [
      '#type' => 'email',
      '#title' => $this->t('Email'),
      '#required' => TRUE,
    ];

    $form['submit'] = [
      '#type' => 'submit',
      '#value' => $this->t('Submit'),
    ];

    return $form;
  }

  public function validateForm(array &$form, FormStateInterface $form_state): void {
    $email = $form_state->getValue('email');

    if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
      $form_state->setErrorByName('email', $this->t('Enter a valid email address.'));
    }
  }

  public function submitForm(array &$form, FormStateInterface $form_state): void {
    $name = $form_state->getValue('name');

    $this->messenger()->addStatus(
      $this->t('Hello @name!', ['@name' => $name])
    );
  }

}
```

### 3.4 Displaying the Form Through a Route

File: `my_module.routing.yml`

```yaml
my_module.contact:
  path: '/contact-form'
  defaults:
    _form: '\Drupal\my_module\Form\ContactForm'
    _title: 'Contact Form'
  requirements:
    _permission: 'access content'
```

Clear cache:

```bash
drush cr
```

Visit `/contact-form`.

### 3.5 Important Form API Properties

| Property | Purpose |
|---|---|
| `#type` | Defines the form element type. |
| `#title` | Sets the element label. |
| `#required` | Makes the element mandatory. |
| `#default_value` | Sets the initial default value. |
| `#value` | Sets a programmatically defined value. |
| `#description` | Displays help text. |
| `#access` | Controls element access and visibility. |
| `#disabled` | Disables normal user interaction. |
| `#options` | Provides options for select or checkbox elements. |

### 3.6 FormBase vs ConfigFormBase

**FormBase**
- Used for general-purpose forms.
- Can collect and process user input.
- Does not automatically manage configuration storage.

**ConfigFormBase**
- Used for configuration or settings forms.
- Integrates with Drupal's configuration system.
- Provides methods such as `getEditableConfigNames()`.

Example use cases:
- Contact form: FormBase.
- Custom module settings page: ConfigFormBase.

### 3.7 Form Alteration

Drupal allows existing forms to be modified using hooks.

Example:

```php
use Drupal\Core\Form\FormStateInterface;

/**
 * Implements hook_form_alter().
 */
function my_module_form_alter(array &$form, FormStateInterface $form_state, $form_id): void {

  if ($form_id === 'user_login_form') {
    $form['name']['#description'] = t('Enter your username.');
  }

}
```

This adds a description to the username field on the user login form.

### 3.8 Form State

`FormStateInterface` provides methods to access and manage form values and submission state.

Important methods:

| Method | Purpose |
|---|---|
| `getValue()` | Retrieves a submitted value. |
| `setValue()` | Sets a form value. |
| `setErrorByName()` | Adds a validation error. |
| `getValues()` | Retrieves all form values. |
| `setRedirect()` | Sets a redirect after submission. |

### 3.9 #access vs #disabled

| #access | #disabled |
|---|---|
| Controls whether an element is accessible/rendered. | Displays the element in a disabled state. |
| Can hide an element. | Prevents normal user interaction. |
| Should be used for access control. | Should be used to disable interaction. |

Important: Hiding a form element is not a replacement for server-side authorization or validation.

### 3.10 #default_value vs #value

- `#default_value`: Provides an initial value that the user can normally change.
- `#value`: Sets the element's value programmatically.

**Interview Questions**

**Q1. What is Drupal Form API?**

Drupal Form API is a framework for creating, validating and processing forms. It provides reusable form elements, validation handling and submission processing.

**Q2. What are the main methods in FormBase?**

`getFormId()`, `buildForm()`, `validateForm()` and `submitForm()`.

**Q3. What is the difference between buildForm() and submitForm()?**

`buildForm()` defines the form elements. `submitForm()` processes the submitted form data.

**Q4. What is validateForm() used for?**

It validates submitted values and sets errors using methods such as `setErrorByName()`.

**Q5. What is FormStateInterface?**

It provides methods to retrieve and manage form values, errors and submission state.

**Q6. What is the difference between FormBase and ConfigFormBase?**

FormBase is used for general-purpose forms. ConfigFormBase is designed for forms that manage Drupal configuration settings.

**Q7. What is the difference between #access and #disabled?**

`#access` controls whether an element is accessible and rendered. `#disabled` displays the element but prevents normal user interaction.

**Q8. How do you alter an existing Drupal form?**

Using `hook_form_alter()` to modify any form or `hook_form_FORM_ID_alter()` to modify a specific form.

**Memory Tip:** Build → Validate → Submit.

---

## 4. Drupal Hooks

### 4.1 What is a Hook?

A hook is a mechanism that allows Drupal modules to extend, alter or interact with Drupal functionality without modifying Drupal core.

Hooks provide extension points throughout Drupal.

For example:
- Altering forms.
- Modifying entity data.
- Registering theme hooks.
- Performing scheduled tasks.
- Reacting to entity operations.

### 4.2 How Do Hooks Work?

Drupal invokes hooks at specific points in its execution.

Modules that implement the relevant hook can execute their own logic when Drupal invokes it.

In Drupal 11, many hooks are implemented as ordinary functions in a module's `.module` file. Some newer hook systems also support object-oriented implementations.

### 4.3 Example: hook_form_alter()

```php
use Drupal\Core\Form\FormStateInterface;

/**
 * Implements hook_form_alter().
 */
function my_module_form_alter(array &$form, FormStateInterface $form_state, $form_id): void {

  if ($form_id === 'user_login_form') {
    $form['name']['#description'] = t('Enter your username.');
  }

}
```

This modifies the login form by adding a description to the username field.

### 4.4 hook_form_alter() vs hook_form_FORM_ID_alter()

**hook_form_alter()**

Alters any form.

```php
function my_module_form_alter(array &$form, FormStateInterface $form_state, $form_id): void {
  // Alter any form.
}
```

**hook_form_FORM_ID_alter()**

Targets a particular form.

```php
function my_module_form_user_login_form_alter(array &$form, FormStateInterface $form_state, $form_id): void {
  // Alter only the user login form.
}
```

### 4.5 Common Entity Hooks

**hook_entity_presave()**

Runs before an entity is saved, for both new and existing entities.

**hook_entity_insert()**

Runs after a new entity has been inserted.

**hook_entity_update()**

Runs after an existing entity has been updated.

**hook_entity_delete()**

Runs after an entity has been deleted.

Example:

```php
use Drupal\Core\Entity\EntityInterface;

/**
 * Implements hook_entity_presave().
 */
function my_module_entity_presave(EntityInterface $entity): void {

  if ($entity->getEntityTypeId() === 'node' && $entity->bundle() === 'article') {
    // Perform logic before saving an article.
  }

}
```

### 4.6 hook_cron()

`hook_cron()` is used to execute scheduled tasks when Drupal's cron process runs.

Example:

```php
/**
 * Implements hook_cron().
 */
function my_module_cron(): void {
  // Execute scheduled maintenance tasks.
}
```

For large or time-consuming tasks, Queue API and Queue Workers are often more appropriate than processing everything directly inside `hook_cron()`.

### 4.7 hook_theme()

`hook_theme()` registers theme hooks so Drupal knows which templates or theme functions are available.

Example:

```php
/**
 * Implements hook_theme().
 */
function my_module_theme($existing, $type, $theme, $path): array {
  return [
    'my_custom_template' => [
      'variables' => [
        'message' => NULL,
      ],
    ],
  ];
}
```

### 4.8 Hooks vs Event Subscribers

| Hooks | Event Subscribers |
|---|---|
| Drupal-specific extension mechanism. | Symfony event system integrated with Drupal. |
| Implemented using hook functions or supported hook implementations. | Implemented as services. |
| Invoked at defined Drupal extension points. | React to dispatched events. |
| Example: `hook_form_alter()`. | Example: `KernelEvents::REQUEST`. |

**Interview Questions**

**Q1. What is a hook in Drupal?**

A hook is an extension mechanism that allows modules to alter or extend Drupal functionality without modifying core code.

**Q2. What is the difference between hook_form_alter() and hook_form_FORM_ID_alter()?**

`hook_form_alter()` can alter any form, whereas `hook_form_FORM_ID_alter()` targets a specific form.

**Q3. What is the difference between hook_entity_presave() and hook_entity_insert()?**

`hook_entity_presave()` executes before an entity is saved, whereas `hook_entity_insert()` executes after a new entity has been inserted.

**Q4. What is the difference between hook_entity_insert() and hook_entity_update()?**

`hook_entity_insert()` runs after a new entity is created. `hook_entity_update()` runs after an existing entity is updated.

**Q5. What is hook_cron()?**

It is a hook that allows modules to execute scheduled tasks when Drupal's cron process runs.

**Q6. What is the difference between hook_cron() and Queue Worker?**

`hook_cron()` is a scheduled entry point. Queue Workers process individual queued jobs, allowing large tasks to be handled in smaller units.

**Q7. What is hook_theme()?**

It registers theme hooks and defines the variables that can be passed to corresponding templates.

**Q8. Can you modify Drupal core functionality using hooks?**

Yes. Hooks allow modules to extend or alter core functionality without directly modifying core files. This keeps customizations maintainable and upgrade-friendly.

**Memory Tip:** Hooks = Extend or Alter Drupal functionality.

---

## 5. Events and Event Subscribers

### 5.1 What are Events?

Events provide a mechanism for different parts of an application to communicate.

When something happens in the application, an event can be dispatched. Other components can listen for that event and execute custom logic.

Drupal uses Symfony's EventDispatcher component.

Examples include:
- Kernel request events.
- Kernel response events.
- Exception events.
- Routing events.
- Entity-related events.

### 5.2 Event Dispatcher

The event dispatcher is responsible for dispatching events to registered listeners and subscribers.

The general flow is:

1. An event occurs.
2. The event is dispatched.
3. The dispatcher identifies registered listeners.
4. Listeners or subscribers execute their logic.

### 5.3 What is an Event Subscriber?

An event subscriber is a service that listens for one or more events and reacts when those events are dispatched.

It implements:

`EventSubscriberInterface`

### 5.4 Creating a Custom Event Subscriber

File:

`src/EventSubscriber/RequestSubscriber.php`

```php
<?php

namespace Drupal\my_module\EventSubscriber;

use Symfony\Component\EventDispatcher\EventSubscriberInterface;
use Symfony\Component\HttpKernel\Event\RequestEvent;
use Symfony\Component\HttpKernel\KernelEvents;

class RequestSubscriber implements EventSubscriberInterface {

  public static function getSubscribedEvents(): array {
    return [
      KernelEvents::REQUEST => ['onRequest', 0],
    ];
  }

  public function onRequest(RequestEvent $event): void {
    // Execute custom logic during the request event.
  }

}
```

### 5.5 Registering the Subscriber

File: `my_module.services.yml`

```yaml
services:
  my_module.request_subscriber:
    class: Drupal\my_module\EventSubscriber\RequestSubscriber
    tags:
      - { name: event_subscriber }
```

Clear Drupal cache:

```bash
drush cr
```

### 5.6 Important Components

**EventSubscriberInterface**

Interface that identifies a class as an event subscriber.

**getSubscribedEvents()**

Declares which events the subscriber listens to and which methods should handle them.

**KernelEvents::REQUEST**

An event triggered during the HTTP request lifecycle.

**Priority**

Determines the execution order when multiple subscribers listen to the same event. Higher priority runs first.

### 5.7 Event Listener vs Event Subscriber

| Event Listener | Event Subscriber |
|---|---|
| Registered for an event, usually through a service tag. | Implements EventSubscriberInterface. |
| Listener is registered for a particular event and method. | Subscriber declares its event subscriptions in `getSubscribedEvents()`. |
| Suitable for a straightforward single event listener. | Convenient for managing multiple event subscriptions in one class. |

### 5.8 Hooks vs Events

Hooks are Drupal's own extension mechanism. Events use Symfony's event-dispatching architecture.

Use hooks when Drupal provides an appropriate hook for the required extension point. Use event subscribers when the required Symfony event is available or when event-based architecture is appropriate.

**Interview Questions**

**Q1. What is an event in Drupal?**

An event represents something that happens during application execution. Other components can listen for that event and execute custom logic.

**Q2. What is an event subscriber?**

An event subscriber is a service that listens for specific events and executes methods when those events are dispatched.

**Q3. How do you create an event subscriber?**

Implement `EventSubscriberInterface`, define subscribed events in `getSubscribedEvents()` and register the class as a service using the `event_subscriber` tag.

**Q4. What is the purpose of getSubscribedEvents()?**

It defines which events the subscriber listens to and identifies the callback methods that handle them.

**Q5. What is event priority?**

Event priority determines the execution order of subscribers listening to the same event. Higher priority values execute earlier.

**Q6. What is the difference between an event listener and an event subscriber?**

A listener is registered to handle an event through a service definition. A subscriber implements `EventSubscriberInterface` and declares its subscriptions inside the class.

**Q7. What is KernelEvents::REQUEST?**

It is a Symfony HTTP Kernel event triggered during request processing. Drupal modules can subscribe to it to execute logic during the request lifecycle.

**Memory Tip:** Subscribe → Listen → React.

---

## 6. Important Differences – Quick Revision

| Concept | Difference |
|---|---|
| Plugin vs Service | Component vs reusable functionality |
| Plugin Manager vs Service Container | Manages plugins vs manages services |
| Route vs Controller | Maps URL vs handles request |
| FormBase vs ConfigFormBase | General form vs configuration form |
| buildForm() vs validateForm() | Defines form vs validates input |
| validateForm() vs submitForm() | Validates input vs processes submission |
| #access vs #disabled | Controls access/rendering vs disables interaction |
| #default_value vs #value | Initial editable value vs programmatically set value |
| hook_form_alter() vs specific form alter | Any form vs one specific form |
| hook_entity_presave() vs insert | Before save vs after new entity insertion |
| hook_entity_insert() vs update | New entity vs existing entity |
| hook_cron() vs Queue Worker | Scheduled entry point vs queued job processing |
| Hook vs Event Subscriber | Drupal extension mechanism vs Symfony event listener mechanism |
| Event Listener vs Subscriber | Registered callback vs class declaring subscriptions |

---

## 7. Practical Scenarios

### Scenario 1: You need to display reusable content in a theme region.

**Answer:** Create a custom Block plugin and place it in the required theme region.

### Scenario 2: You need to create a custom URL that displays information.

**Answer:** Define a route in the module's `.routing.yml` file and map it to a controller.

### Scenario 3: You need to collect user information and validate it.

**Answer:** Use Drupal Form API with `FormBase`, `buildForm()`, `validateForm()` and `submitForm()`.

### Scenario 4: You need to modify the existing user login form.

**Answer:** Implement `hook_form_alter()` or `hook_form_user_login_form_alter()`.

### Scenario 5: You need to execute custom logic before a node is saved.

**Answer:** Implement `hook_entity_presave()` or use an appropriate entity-related event when that architecture is preferable.

### Scenario 6: You need to execute scheduled background processing.

**Answer:** Use `hook_cron()` to trigger the process and Queue API with a Queue Worker to process larger workloads in manageable units.

### Scenario 7: You need to execute custom logic when an HTTP request begins.

**Answer:** Create an event subscriber that listens to `KernelEvents::REQUEST`.

### Scenario 8: You need a configuration form for your custom module.

**Answer:** Extend `ConfigFormBase` and implement the configuration form methods.

---

## 8. Final Interview Revision

Remember these key points:

1. Plugins are reusable components managed by plugin managers.
2. Services provide reusable functionality and are managed by the service container.
3. Routing maps URLs to controllers or other handlers.
4. Controllers process requests and return responses.
5. Form API handles form building, validation and submission.
6. FormBase is for general forms; ConfigFormBase is for configuration forms.
7. Hooks allow modules to extend or alter Drupal functionality.
8. Event subscribers listen to Symfony events.
9. Queue Workers are useful for processing background tasks.
10. Drupal cache must be rebuilt after adding new plugin, routing or service definitions.

**Day 4 Completion Checklist**

- [ ] Understand Drupal plugins and plugin managers.
- [ ] Create a custom block plugin.
- [ ] Create a custom route and controller.
- [ ] Understand route access requirements.
- [ ] Build and validate a custom form.
- [ ] Understand FormBase and ConfigFormBase.
- [ ] Implement form alteration hooks.
- [ ] Understand entity hooks.
- [ ] Understand cron and Queue Workers.
- [ ] Create and register an event subscriber.
- [ ] Explain hooks vs event subscribers in an interview.

**End of Day 4 – Plugins, Routing, Forms, Hooks, and Events.**
