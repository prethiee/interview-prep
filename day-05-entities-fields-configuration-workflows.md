# Day 5 – Entities, Fields, Configuration, and Workflows

## 1. Drupal Entities

### 1.1 What is an Entity in Drupal?

An entity is a structured data object used to store and manage information in Drupal.

Entities provide a standard way to work with different types of data, such as content, users, taxonomy terms and configuration.

Examples:
- Nodes
- Users
- Taxonomy terms
- Files
- Media
- Content types
- Roles
- Views

Drupal entities are managed through the Entity API.

### 1.2 Types of Entities

Drupal primarily has two categories of entities:

1. Content entities
2. Configuration entities

### 1.3 Content Entities

Content entities store the actual content or data created and managed by users.

Examples:
- Node
- User
- Taxonomy term
- Media
- File
- Comment

Characteristics:
- Usually contain fields.
- Can have revisions.
- Can have translations.
- Are generally stored in database tables.
- Can be managed through entity storage handlers.

Example: A node representing an article contains fields such as title, body, image and author.

### 1.4 Configuration Entities

Configuration entities represent structured site configuration.

Examples:
- Content types
- Taxonomy vocabularies
- Views
- Image styles
- User roles
- Text formats

Characteristics:
- Represent configuration rather than ordinary user-generated content.
- Are exportable through Drupal's Configuration Management system.
- Are commonly stored in active configuration storage and exported as YAML files.
- Can be created, updated and deleted through configuration entity APIs.

### 1.5 Content Entity vs Configuration Entity

| Content Entity | Configuration Entity |
|---|---|
| Stores content or user data. | Stores site configuration. |
| Example: Node, User, Media. | Example: Content type, View, Role. |
| Usually supports revisions where configured. | Generally does not use content revisioning. |
| Commonly stored in entity-specific database tables. | Stored through configuration storage. |
| Not normally deployed as configuration YAML. | Can be exported and imported as YAML. |

**Interview Question: What is the difference between content entities and configuration entities?**

Content entities store actual content or user data, such as nodes and users. Configuration entities represent site configuration, such as content types, Views and roles. Configuration entities can be exported and imported using Drupal's Configuration Management system.

### 1.6 Entity Type vs Bundle

An entity type defines the overall type of entity.

A bundle is a subtype or variation of an entity type.

Example:

- Entity type: Node
- Bundles: Article, Basic page, News

Each bundle can have different fields and configurations.

**Interview Question: What is the difference between an entity type and a bundle?**

An entity type defines the general entity structure, while a bundle represents a specific variation of that entity type with its own fields and configuration.

### 1.7 Node vs Entity

A node is a specific type of content entity in Drupal.

Other entities include users, taxonomy terms, media and files.

Therefore, every node is an entity, but not every entity is a node.

### 1.8 Entity API

Drupal Entity API provides methods and interfaces for creating, loading, updating, deleting and managing entities.

It allows developers to work with entities without directly manipulating database tables.

Common entity operations:

- Create
- Load
- Update
- Save
- Delete
- Query

### 1.9 Loading an Entity

Using entity type manager:

```php
$node = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->load(1);
```

This loads the node with ID 1.

### 1.10 Loading Multiple Entities

```php
$nodes = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->loadMultiple([1, 2, 3]);
```

This loads multiple nodes.

### 1.11 Creating an Entity

```php
$node = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->create([
    'type' => 'article',
    'title' => 'Drupal Interview Preparation',
  ]);

$node->save();
```

This creates and saves a new article node.

### 1.12 Updating an Entity

```php
$node = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->load(1);

if ($node) {
  $node->setTitle('Updated Article Title');
  $node->save();
}
```

### 1.13 Deleting an Entity

```php
$node = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->load(1);

if ($node) {
  $node->delete();
}
```

### 1.14 Entity Type Manager

The Entity Type Manager is a Drupal service responsible for providing access to entity definitions, storage handlers and entity-related services.

Example:

```php
$entity_type_manager = \Drupal::entityTypeManager();

$storage = $entity_type_manager->getStorage('node');

$node = $storage->load(1);
```

Important methods:

| Method | Purpose |
|---|---|
| `getStorage()` | Retrieves an entity storage handler. |
| `getDefinition()` | Retrieves an entity type definition. |
| `getDefinitions()` | Retrieves all entity type definitions. |
| `getAccessControlHandler()` | Retrieves an entity access handler. |
| `getViewBuilder()` | Retrieves an entity view builder. |

### 1.15 Entity Query

Entity Query is used to retrieve entity IDs based on conditions.

Example:

```php
$ids = \Drupal::entityQuery('node')
  ->condition('type', 'article')
  ->condition('status', 1)
  ->accessCheck(TRUE)
  ->execute();
```

This retrieves IDs of published article nodes that the current user is allowed to access.

Important: In modern Drupal, explicitly specify `accessCheck(TRUE)` or `accessCheck(FALSE)` when using entity queries.

### 1.16 Entity Type Manager vs Entity Query

| Entity Type Manager | Entity Query |
|---|---|
| Manages entity storage and related handlers. | Finds entities based on conditions. |
| Used to load, create and delete entities. | Used to query matching entity IDs. |
| Provides access to storage handlers. | Provides query conditions and sorting. |

**Interview Question: What is the difference between Entity Type Manager and Entity Query?**

Entity Type Manager provides access to entity storage and handlers for operations such as loading and creating entities. Entity Query is used to retrieve entity IDs based on specified conditions.

### 1.17 load() vs loadMultiple()

| load() | loadMultiple() |
|---|---|
| Loads one entity. | Loads multiple entities. |
| Accepts a single entity ID. | Accepts an array of IDs. |

### 1.18 Entity Access Control

Drupal provides entity access APIs to check whether a user can perform operations on an entity.

Example:

```php
$access = $node->access('update', NULL, TRUE);

if ($access->isAllowed()) {
  // User can update the node.
}
```

Common operations:
- View
- Update
- Delete

Entity access should be checked before exposing or modifying protected content.

### Interview Questions – Entities

**Q1. What is an entity in Drupal?**

An entity is a structured data object used to store and manage information in Drupal. Examples include nodes, users, taxonomy terms and media.

**Q2. What are the two main types of entities?**

Content entities and configuration entities.

**Q3. What is a bundle?**

A bundle is a subtype of an entity type. For example, Article and Basic page are bundles of the Node entity type.

**Q4. What is Entity API?**

Entity API provides a standard way to create, load, update, delete and manage Drupal entities.

**Q5. What is Entity Type Manager?**

It is a Drupal service that provides access to entity definitions, storage handlers and other entity-related handlers.

**Q6. What is Entity Query?**

Entity Query is used to retrieve entity IDs based on conditions such as bundle, status and field values.

**Q7. What is the difference between load() and loadMultiple()?**

`load()` loads one entity using an ID, whereas `loadMultiple()` loads multiple entities using an array of IDs.

**Q8. How do you check whether a user can update a node?**

Using the entity access API, for example `$node->access('update', NULL, TRUE)`, and checking whether the returned access result is allowed.

**Memory Tip:** Entity = Data object. Bundle = Variation. Entity Query = Find IDs.

---

## 2. Drupal Fields

### 2.1 What is a Field in Drupal?

A field is a data component used to store a particular type of information in an entity.

Fields allow developers and site builders to extend content structures without creating custom database tables manually.

Examples:
- Text
- Number
- Email
- Date
- Image
- File
- Entity reference
- Boolean

### 2.2 Field Types

A field type defines what kind of data the field stores.

Examples:

| Field Type | Purpose |
|---|---|
| String | Stores short text. |
| Text (formatted) | Stores longer text with formatting. |
| Integer | Stores whole numbers. |
| Decimal | Stores decimal numbers. |
| Boolean | Stores true/false values. |
| Date | Stores date and time values. |
| Image | Stores image references and metadata. |
| Entity Reference | References another entity. |

### 2.3 Field Widget

A field widget controls how a field is entered or edited in a form.

Examples:
- Textfield
- Textarea
- Select list
- Checkboxes
- Date picker
- Image upload widget

### 2.4 Field Formatter

A field formatter controls how field data is displayed when viewing an entity.

Examples:
- Plain text formatter
- Trimmed text formatter
- Image formatter
- Date formatter
- Entity reference label formatter

### 2.5 Field Type vs Widget vs Formatter

| Component | Responsibility |
|---|---|
| Field Type | Defines what data is stored. |
| Widget | Controls how users enter the data. |
| Formatter | Controls how data is displayed. |

Example:

For an image field:

- Field type: Image.
- Widget: Image upload.
- Formatter: Image displayed as a thumbnail.

**Interview Question: What is the difference between field type, widget and formatter?**

The field type defines the data being stored. The widget controls how users enter the data, while the formatter controls how the data is displayed.

### 2.6 Field Storage vs Field Instance

Drupal separates the storage definition of a field from its configuration on a particular bundle.

**Field Storage Configuration**

Defines the underlying storage characteristics, such as:
- Entity type.
- Field machine name.
- Field type.
- Cardinality.

**Field Instance Configuration**

Defines how the field is used on a specific bundle, including:
- Label.
- Required status.
- Help text.
- Default value.
- Bundle-specific settings.

Example:

A `field_summary` text field can have one storage definition and be attached to multiple bundles with different field instance settings.

### 2.7 Field Definition vs Field Value

| Field Definition | Field Value |
|---|---|
| Describes the structure and configuration of a field. | Contains the actual data stored in the field. |
| Defines field type and settings. | Contains values entered by users. |

### 2.8 Entity Reference Fields

Entity Reference fields allow one entity to reference another entity.

Example:

An Article can reference:
- An Author user.
- A Category taxonomy term.
- Related content nodes.
- Media images.

This helps build relationships between different entity types.

### 2.9 Field Cardinality

Cardinality defines how many values a field can store.

Examples:

- Cardinality 1: Only one value.
- Cardinality 3: Up to three values.
- Unlimited: Multiple values without a configured fixed limit.

Example:

An article can have:
- One title.
- Multiple images.
- Multiple taxonomy terms.

### 2.10 Creating and Accessing Field Values

Example: Reading a field value from a node.

```php
$node = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->load(1);

if ($node && $node->hasField('field_summary')) {
  $summary = $node->get('field_summary')->value;
}
```

Example: Updating a field value.

```php
$node->set('field_summary', 'Updated article summary');
$node->save();
```

For multi-value fields, values can be set as an array of item values.

### 2.11 Field API

Field API provides the system used to define, store, validate, edit and display fields attached to entities.

It integrates with:
- Entity API.
- Form API.
- Render API.
- Configuration Management.

### Interview Questions – Fields

**Q1. What is a field in Drupal?**

A field is a data component attached to an entity to store specific information, such as text, numbers, images or entity references.

**Q2. What is the difference between a field widget and a formatter?**

A widget controls how users enter field data. A formatter controls how field data is displayed.

**Q3. What is field cardinality?**

Cardinality defines the maximum number of values a field can store. It can be one, a fixed number or unlimited.

**Q4. What is an Entity Reference field?**

It allows one entity to reference another entity, such as an article referencing a taxonomy term or user.

**Q5. What is the difference between field storage and field instance configuration?**

Field storage defines the underlying field structure and storage characteristics. Field instance configuration defines how that field is configured on a specific bundle.

**Q6. What is the difference between field definition and field value?**

Field definition describes the field's structure and settings. Field value contains the actual data stored in that field.

**Q7. How do you retrieve a field value programmatically?**

Using the entity's `get()` method, for example `$node->get('field_summary')->value`.

**Memory Tip:** Type = Data. Widget = Input. Formatter = Output.

---

## 3. Drupal Configuration Management

### 3.1 What is Configuration Management?

Configuration Management is Drupal's system for managing and transferring site configuration between different environments.

It allows developers to export configuration from one environment and import it into another.

For example:

- Local development.
- Development environment.
- Testing environment.
- Staging environment.
- Production environment.

It helps maintain consistency across environments.

### 3.2 What is Configuration?

Configuration defines how Drupal behaves.

Examples:
- Content types.
- Views.
- User roles and permissions.
- Image styles.
- Text formats.
- Field definitions.
- Site settings.
- Module configuration.

Configuration is generally stored in Drupal's active configuration storage, commonly backed by the database.

### 3.3 Configuration Storage

Drupal has two important configuration storage concepts:

**Active Configuration**

The configuration currently used by the running Drupal site.

It is commonly stored in the database.

**Synchronization Configuration**

The exported configuration files, generally stored as YAML files in the configured sync directory.

These files are used to transfer configuration between environments.

### 3.4 Configuration Export

Command:

```bash
drush cex
```

This exports active configuration into the configured synchronization directory.

It can be used after making configuration changes in the development environment.

### 3.5 Configuration Import

Command:

```bash
drush cim
```

This imports configuration from the synchronization directory into active configuration storage.

It is commonly used when deploying configuration changes to another environment.

### 3.6 Configuration Import vs Export

| Export | Import |
|---|---|
| Active configuration → YAML files. | YAML files → Active configuration. |
| `drush cex` | `drush cim` |
| Used to save configuration changes. | Used to apply configuration changes. |

### 3.7 Configuration Synchronization Workflow

A typical configuration deployment workflow:

1. Developer makes configuration changes locally.
2. Developer exports configuration using `drush cex`.
3. YAML configuration files are committed to Git.
4. Changes are deployed to the next environment.
5. Configuration is imported using `drush cim`.
6. Site is tested to verify the changes.

### 3.8 Configuration Schema

Configuration schema defines the structure and data types of configuration values.

It helps Drupal understand and validate configuration.

Custom configuration schema is generally defined in files ending with:

`*.schema.yml`

Example:

```yaml
my_module.settings:
  type: config_object
  label: 'My module settings'
  mapping:
    welcome_message:
      type: string
      label: 'Welcome message'
```

### 3.9 Configuration API

Drupal provides Configuration API methods to read and update configuration.

Example: Reading configuration.

```php
$config = \Drupal::config('system.site');

$site_name = $config->get('name');
```

Example: Updating configuration.

```php
\Drupal::configFactory()
  ->getEditable('system.site')
  ->set('name', 'My Drupal Website')
  ->save();
```

Use dependency injection for services in object-oriented custom code rather than relying on global service access.

### 3.10 Configuration API vs State API

**Configuration API**

Used for settings that should be consistent across environments and can be exported.

Examples:
- Site name.
- Module settings.
- Content type settings.

**State API**

Used for temporary or environment-specific values that should not normally be deployed through configuration synchronization.

Examples:
- Last cron execution timestamp.
- Temporary processing status.
- Environment-specific runtime information.

| Configuration API | State API |
|---|---|
| Stores configuration. | Stores runtime state. |
| Exportable through configuration management. | Not exported through normal configuration synchronization. |
| Intended for deployable settings. | Intended for environment-specific or temporary values. |
| Example: Module settings. | Example: Last cron timestamp. |

**Interview Question: What is the difference between Configuration API and State API?**

Configuration API stores settings that should be consistent across environments and can be exported. State API stores temporary or environment-specific information that should not normally be deployed through configuration synchronization.

### 3.11 Configuration Management Best Practices

- Store configuration YAML files in Git.
- Export configuration after making changes.
- Review configuration differences before committing.
- Import configuration during deployment.
- Avoid making configuration changes directly in production.
- Keep configuration synchronized across environments.
- Avoid storing sensitive credentials in exported configuration files.

### Interview Questions – Configuration

**Q1. What is Configuration Management in Drupal?**

It is Drupal's mechanism for exporting, importing and synchronizing site configuration across environments.

**Q2. What is the difference between active and synchronization configuration?**

Active configuration represents the configuration currently used by Drupal. Synchronization configuration contains exported YAML files used to transfer configuration between environments.

**Q3. What is the difference between drush cex and drush cim?**

`drush cex` exports active configuration to YAML files. `drush cim` imports YAML configuration into active configuration storage.

**Q4. What is configuration schema?**

Configuration schema defines the structure, data types and metadata of configuration values.

**Q5. What is the difference between Configuration API and State API?**

Configuration API manages deployable settings. State API manages temporary or environment-specific runtime information.

**Q6. How do you deploy configuration changes between environments?**

Export configuration from the source environment, commit the YAML files to Git, deploy them to the target environment and import them using `drush cim`.

**Q7. Why should configuration be managed through Git?**

It provides version control, traceability, collaboration and consistent configuration deployment across environments.

**Memory Tip:** CEX = Export. CIM = Import. Config = Deployable settings. State = Runtime information.

---

## 4. Drupal Workflows and Content Moderation

### 4.1 What is a Workflow?

A workflow defines the sequence of steps that content follows during its lifecycle.

For example, in a publishing workflow:

Draft → Review → Published → Archived

Workflows help organizations manage content creation, review and approval.

### 4.2 What is Content Moderation?

Content Moderation is a Drupal core module that allows content to move through different moderation states.

It supports editorial processes where content must be reviewed and approved before publication.

Examples:
- Draft.
- Needs Review.
- Published.
- Archived.

Content Moderation works with the Workflows system.

### 4.3 Workflows vs Content Moderation

**Workflows**

Defines the states and transitions that make up a workflow.

**Content Moderation**

Uses workflows to manage content moderation states and revisions.

### 4.4 Moderation States

A moderation state represents the current stage of a content item.

Examples:

- Draft: Content is being created.
- Needs Review: Content is waiting for editorial approval.
- Published: Content is approved and available publicly.
- Archived: Content is no longer intended for active publication.

### 4.5 Workflow Transitions

A transition defines how content moves from one moderation state to another.

Example:

Draft → Needs Review

Needs Review → Published

Published → Archived

Transitions can be restricted based on user permissions and roles.

### 4.6 Example: Editorial Workflow

An organization may use the following workflow:

1. Author creates an article in Draft.
2. Author submits the article for review.
3. Editor reviews the article in Needs Review.
4. Editor approves and publishes the article.
5. Content manager archives outdated content.

This ensures content is reviewed before publication.

### 4.7 Revisions

Drupal revisions allow previous versions of content to be stored.

When content is edited and saved with a new revision, Drupal can retain earlier versions.

Revisions are useful for:
- Tracking content changes.
- Restoring previous versions.
- Editorial review.
- Auditability.

Content Moderation uses revisions to support the management of different content states.

### 4.8 Revision vs Moderation State

| Revision | Moderation State |
|---|---|
| Represents a version of an entity. | Represents the editorial status of a revision. |
| Stores historical changes. | Defines the current workflow stage. |
| Helps restore previous content. | Helps control the publishing process. |

### 4.9 Permissions in Content Moderation

Permissions control who can perform moderation actions.

For example:
- Authors can create and edit drafts.
- Editors can review content.
- Publishers can publish approved content.

The exact permissions depend on the configured workflow and role assignments.

### 4.10 Enabling Content Moderation

Enable the required core modules:

```bash
drush en workflows content_moderation -y
```

Then navigate to:

Configuration → Workflow → Workflows

Create or configure a workflow and assign it to the appropriate content types.

### 4.11 Real-World Use Case

Consider a healthcare publishing website.

Content may need approval from:
- Medical reviewers.
- Legal teams.
- Regulatory reviewers.
- Editors.

A workflow can ensure that content passes through the required review stages before it becomes publicly available.

This is particularly useful for regulated industries where publishing incorrect information can create compliance risks.

### Interview Questions – Workflows

**Q1. What is a workflow in Drupal?**

A workflow defines the stages and transitions through which content moves during its lifecycle.

**Q2. What is Content Moderation?**

Content Moderation is a Drupal core module that manages content through moderation states such as Draft, Needs Review and Published.

**Q3. What is the difference between Workflows and Content Moderation?**

Workflows define the states and transitions. Content Moderation uses those workflows to manage the editorial lifecycle of content and its revisions.

**Q4. What is a moderation state?**

A moderation state represents the current stage of a content item, such as Draft, Needs Review or Published.

**Q5. What is a workflow transition?**

A transition defines how content moves from one moderation state to another, such as Draft to Needs Review.

**Q6. How do revisions work with Content Moderation?**

Revisions preserve different versions of content. Content Moderation associates moderation states with revisions to manage editorial changes and publishing.

**Q7. Why are workflows useful in enterprise Drupal websites?**

They help enforce structured content approval processes, assign responsibilities, control publishing permissions and maintain editorial accountability.

**Q8. How would you implement an editorial workflow?**

Enable Workflows and Content Moderation, configure moderation states and transitions, assign the workflow to content types, and configure roles and permissions for authors, reviewers and publishers.

**Memory Tip:** Workflow = Process. State = Stage. Transition = Movement. Revision = Version.

---

## 5. Important Differences – Quick Revision

| Concept | Difference |
|---|---|
| Content Entity vs Configuration Entity | Content data vs site configuration |
| Entity Type vs Bundle | General entity type vs specific variation |
| Node vs Entity | Node is one type of entity |
| Entity Type Manager vs Entity Query | Entity storage management vs retrieving matching entity IDs |
| load() vs loadMultiple() | One entity vs multiple entities |
| Field Type vs Widget | Stored data type vs input method |
| Field Widget vs Formatter | Data input vs data display |
| Field Storage vs Field Instance | Underlying storage definition vs bundle-specific configuration |
| Field Definition vs Field Value | Field structure vs actual data |
| Configuration vs State API | Deployable settings vs runtime state |
| Active vs Sync Configuration | Current configuration vs exported YAML |
| drush cex vs drush cim | Export vs import |
| Workflow vs Content Moderation | Defines process vs manages editorial moderation |
| Revision vs Moderation State | Content version vs editorial status |

---

## 6. Practical Interview Scenarios

### Scenario 1: You need to retrieve all published article nodes.

Use Entity Query:

```php
$ids = \Drupal::entityQuery('node')
  ->condition('type', 'article')
  ->condition('status', 1)
  ->accessCheck(TRUE)
  ->execute();
```

### Scenario 2: You need to retrieve the title of a node.

Use Entity Storage:

```php
$node = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->load(1);

if ($node) {
  $title = $node->getTitle();
}
```

### Scenario 3: An article needs to reference multiple categories.

Use an Entity Reference field with multiple-value cardinality.

### Scenario 4: A field should accept multiple images but display them as thumbnails.

Configure an Image field with suitable cardinality and use an image formatter for display.

### Scenario 5: A content type created in development must be deployed to staging.

Export configuration using `drush cex`, commit the YAML files to Git and import them in staging using `drush cim`.

### Scenario 6: A setting should be consistent across environments.

Use Configuration API.

### Scenario 7: You need to store the timestamp of the last successful cron execution.

Use State API because this is runtime information rather than deployable configuration.

### Scenario 8: Authors should not publish content directly.

Use Content Moderation with appropriate states, transitions and role permissions.

### Scenario 9: An editor needs to review content before publication.

Create a workflow with Draft, Needs Review and Published states. Configure transitions and permissions accordingly.

### Scenario 10: A developer needs to restore an older version of an article.

Use Drupal's revision system to access and restore the appropriate previous revision, subject to permissions.

---

## 7. Final Interview Revision

Remember these important points:

1. Entities are structured data objects in Drupal.
2. Content entities store content and user data.
3. Configuration entities represent site configuration.
4. Bundles are variations of entity types.
5. Entity Type Manager provides access to entity storage and handlers.
6. Entity Query retrieves entity IDs based on conditions.
7. Fields store structured information on entities.
8. Widgets control data entry.
9. Formatters control data display.
10. Field cardinality defines the number of values a field can store.
11. Configuration Management synchronizes site configuration across environments.
12. `drush cex` exports configuration.
13. `drush cim` imports configuration.
14. Configuration API stores deployable settings.
15. State API stores runtime or environment-specific information.
16. Workflows define content lifecycle stages and transitions.
17. Content Moderation manages editorial states and revisions.
18. Revisions preserve previous versions of content.

## 8. Day 5 Completion Checklist

- [ ] Understand Drupal entities.
- [ ] Understand content entities and configuration entities.
- [ ] Understand entity types and bundles.
- [ ] Practice loading, creating, updating and deleting entities.
- [ ] Understand Entity Type Manager.
- [ ] Understand Entity Query and access checks.
- [ ] Understand Drupal fields and field types.
- [ ] Understand widgets and formatters.
- [ ] Understand field storage and field instance configuration.
- [ ] Understand entity reference fields and cardinality.
- [ ] Understand Configuration Management.
- [ ] Practice `drush cex` and `drush cim`.
- [ ] Understand Configuration API and State API.
- [ ] Understand Workflows and Content Moderation.
- [ ] Understand revisions, moderation states and transitions.
- [ ] Practice explaining enterprise editorial workflows.

**End of Day 5 – Entities, Fields, Configuration, and Workflows.**

