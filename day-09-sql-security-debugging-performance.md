# Day 9 – SQL, Security, Debugging, and Performance

**Filename:** `day-09-sql-security-debugging-performance.md`

## 1. SQL Fundamentals

### 1.1 What is SQL?

SQL (Structured Query Language) is used to communicate with relational databases such as MySQL.

It allows us to:
- Retrieve data using `SELECT`.
- Insert data using `INSERT`.
- Update data using `UPDATE`.
- Delete data using `DELETE`.
- Create and modify database structures using `CREATE`, `ALTER`, and `DROP`.

### 1.2 Basic SQL queries

**SELECT – Retrieve records**

```sql
SELECT * FROM users_field_data;
```

Retrieve specific columns:

```sql
SELECT uid, name, mail
FROM users_field_data;
```

**WHERE – Filter records**

```sql
SELECT uid, name
FROM users_field_data
WHERE status = 1;
```

**ORDER BY – Sort records**

```sql
SELECT uid, name
FROM users_field_data
ORDER BY name ASC;
```

**LIMIT – Restrict results**

```sql
SELECT uid, name
FROM users_field_data
LIMIT 10;
```

**INSERT – Add records**

```sql
INSERT INTO custom_table (name, status)
VALUES ('Example', 1);
```

**UPDATE – Modify records**

```sql
UPDATE custom_table
SET status = 0
WHERE id = 1;
```

**DELETE – Remove records**

```sql
DELETE FROM custom_table
WHERE id = 1;
```

Always use a suitable `WHERE` condition when updating or deleting specific records.

### 1.3 Joins

Joins combine records from multiple tables using related columns.

**INNER JOIN**

Returns records that have matching values in both tables.

```sql
SELECT u.uid, u.name, r.rid
FROM users_field_data u
INNER JOIN user__roles r ON u.uid = r.entity_id;
```

**LEFT JOIN**

Returns all records from the left table and matching records from the right table. If there is no match, right-side columns contain `NULL`.

```sql
SELECT u.uid, u.name, r.rid
FROM users_field_data u
LEFT JOIN user__roles r ON u.uid = r.entity_id;
```

**Difference:**

| INNER JOIN | LEFT JOIN |
|---|---|
| Only matching records | All records from left table |
| Unmatched records excluded | Unmatched right-side values become NULL |

### 1.4 GROUP BY and HAVING

`GROUP BY` groups records with the same value.

```sql
SELECT status, COUNT(*) AS total
FROM custom_table
GROUP BY status;
```

`HAVING` filters grouped results.

```sql
SELECT status, COUNT(*) AS total
FROM custom_table
GROUP BY status
HAVING COUNT(*) > 5;
```

Remember:
- `WHERE` filters individual rows before grouping.
- `HAVING` filters groups after aggregation.

### 1.5 Indexes

An index helps the database locate records faster without scanning the entire table.

Example:

```sql
CREATE INDEX idx_status
ON custom_table(status);
```

**Advantages:**
- Faster searches.
- Faster filtering and sorting in suitable queries.
- Can improve JOIN performance.

**Disadvantages:**
- Requires additional storage.
- Can slow down INSERT, UPDATE, and DELETE operations because indexes must also be maintained.

Indexes should be created based on actual query patterns, not randomly.

### 1.6 Primary Key and Foreign Key

**Primary key**
- Uniquely identifies each record.
- Cannot contain NULL values.
- A table has one primary key constraint, which may consist of multiple columns.

**Foreign key**
- References a key in another table.
- Helps maintain referential integrity between related tables.

Example:

```sql
CREATE TABLE departments (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  department_id INT,
  FOREIGN KEY (department_id) REFERENCES departments(id)
);
```

### 1.7 Database transactions

A transaction groups multiple database operations into one logical unit.

A transaction should satisfy ACID properties:

| Property | Meaning |
|---|---|
| Atomicity | All operations succeed or the transaction is rolled back |
| Consistency | Database remains in a valid state |
| Isolation | Concurrent transactions are isolated according to the isolation level |
| Durability | Committed changes persist |

Example:

```sql
START TRANSACTION;

UPDATE accounts
SET balance = balance - 500
WHERE id = 1;

UPDATE accounts
SET balance = balance + 500
WHERE id = 2;

COMMIT;
```

If an operation fails, the application can issue:

```sql
ROLLBACK;
```

In Drupal, use the database connection's transaction API when multiple database operations must succeed or fail together.

```php
$transaction = \Drupal::database()->startTransaction();

try {
  // Perform database operations.
}
catch (\Exception $e) {
  // Rolling back occurs when the transaction is rolled back.
  $transaction->rollBack();
  throw $e;
}
```

For production code, prefer dependency injection for the database connection rather than calling the global service locator.

---

## 2. SQL in Drupal

### 2.1 Drupal database abstraction layer

Drupal provides a database abstraction layer that allows developers to interact with the database without writing database-specific code for every operation.

It supports:
- Database connections.
- Select, insert, update, and delete queries.
- Joins and conditions.
- Transactions.
- Parameterized queries.

The database service is:

```php
$database = \Drupal::database();
```

In production code, inject the `database` service.

### 2.2 Select query in Drupal

```php
$query = $database->select('users_field_data', 'u');

$query->fields('u', ['uid', 'name']);
$query->condition('u.status', 1);
$query->orderBy('u.name', 'ASC');

$results = $query->execute()->fetchAll();
```

This retrieves active users.

### 2.3 Insert query

```php
$database->insert('custom_table')
  ->fields([
    'name' => 'Example',
    'status' => 1,
  ])
  ->execute();
```

### 2.4 Update query

```php
$database->update('custom_table')
  ->fields([
    'status' => 0,
  ])
  ->condition('id', 1)
  ->execute();
```

### 2.5 Delete query

```php
$database->delete('custom_table')
  ->condition('id', 1)
  ->execute();
```

### 2.6 Dynamic query vs static query

**Static query**

Uses SQL directly through Drupal's database connection.

```php
$result = $database->query(
  'SELECT uid, name FROM {users_field_data} WHERE status = :status',
  [':status' => 1]
);
```

**Dynamic query**

Uses Drupal's query builder.

```php
$query = $database->select('users_field_data', 'u');
$query->fields('u', ['uid', 'name']);
$query->condition('status', 1);

$result = $query->execute();
```

| Static query | Dynamic query |
|---|---|
| Written using SQL | Built using Drupal query methods |
| Useful for complex SQL | Useful for flexible query construction |
| Requires careful parameter handling | Conditions and fields are built through APIs |

### 2.7 Entity API vs direct database queries

In Drupal, prefer the Entity API when working with Drupal entities such as nodes, users, taxonomy terms, and media.

Example:

```php
$node = \Drupal::entityTypeManager()
  ->getStorage('node')
  ->load(1);
```

Use direct database queries when:
- Working with custom tables.
- Performing specialized reporting queries.
- Needing database operations that are not appropriately handled by the Entity API.

**Important:** Directly modifying entity database tables can bypass entity hooks, validation, field handling, revisions, and other Drupal functionality. Avoid doing this for normal entity updates.

---

## 3. SQL Performance Optimization

### 3.1 What is a slow query?

A slow query takes longer than expected to execute and may consume excessive database resources.

Common reasons:
- Missing indexes.
- Selecting unnecessary columns.
- Retrieving too many records.
- Inefficient JOIN operations.
- Unoptimized filtering and sorting.
- N+1 query problems.
- Large table scans.

### 3.2 Avoid SELECT *

Instead of:

```sql
SELECT * FROM users_field_data;
```

Use:

```sql
SELECT uid, name, mail
FROM users_field_data;
```

Retrieve only the columns required by the application.

### 3.3 Use indexes appropriately

If a column is frequently used in filtering:

```sql
SELECT *
FROM custom_table
WHERE status = 1;
```

An index on `status` may improve performance, depending on the data distribution and query plan.

For queries filtering by multiple columns, a composite index may be useful.

The order of columns in a composite index matters.

### 3.4 Use EXPLAIN

`EXPLAIN` helps understand how MySQL executes a query.

```sql
EXPLAIN
SELECT uid, name
FROM users_field_data
WHERE status = 1;
```

It can help identify:
- Whether an index is used.
- Which table access method is selected.
- Estimated rows examined.
- Join order and access strategy.

Do not assume that adding an index always fixes a slow query. Inspect the execution plan first.

### 3.5 Pagination

Pagination limits how many records are returned at a time.

Example:

```sql
SELECT *
FROM custom_table
ORDER BY id
LIMIT 20 OFFSET 0;
```

For the next page:

```sql
SELECT *
FROM custom_table
ORDER BY id
LIMIT 20 OFFSET 20;
```

For very large datasets, large OFFSET values can become expensive. Keyset pagination, using the last-seen indexed value, may perform better.

### 3.6 N+1 query problem

The N+1 problem occurs when an application runs one query to retrieve a list and then runs another query for every item in that list.

Example:

- One query loads 100 users.
- Another 100 queries load additional information for each user.
- Total: 101 queries.

This creates unnecessary database load.

**Solutions:**
- Fetch related data in batches.
- Use appropriate JOINs.
- Use entity queries and entity loading strategies appropriately.
- Avoid loading individual entities repeatedly inside loops.
- Use Drupal's render and entity caching where suitable.

### 3.7 Drupal query performance checklist

- Avoid unnecessary entity loads in loops.
- Use EntityQuery for entity-level queries.
- Add suitable indexes to custom tables.
- Use pagination for large result sets.
- Inspect expensive queries with EXPLAIN.
- Avoid retrieving unused columns.
- Cache expensive results when appropriate.
- Check for N+1 queries.

---

## 4. Drupal Security Fundamentals

Security is one of the most important responsibilities of a Drupal developer.

Drupal provides security APIs, but developers must use them correctly.

### 4.1 Input validation and sanitization

**Validation** checks whether the input is acceptable.

**Sanitization or escaping** makes data safe for a particular output context.

Example validation:

```php
if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
  throw new \InvalidArgumentException('Invalid email address.');
}
```

Do not confuse validation with output escaping. Both may be necessary.

### 4.2 Cross-Site Scripting (XSS)

XSS occurs when an attacker injects malicious JavaScript into a page viewed by another user.

Example of unsafe output:

```php
echo $_GET['name'];
```

If the value contains malicious HTML or JavaScript, it may execute in the browser.

In Drupal, use render arrays and appropriate escaping.

```php
$build['message'] = [
  '#plain_text' => $user_input,
];
```

For a Twig template:

```twig
{{ user_input }}
```

Twig auto-escapes ordinary output by default.

Avoid using:

```twig
{{ user_input|raw }}
```

unless the content is trusted and has been safely processed for the intended context.

**Prevention:**
- Escape output according to its context.
- Prefer Drupal render arrays.
- Use Twig's normal auto-escaping.
- Do not trust user-supplied HTML.
- Use text formats and filtering when rich text is required.

### 4.3 SQL Injection

SQL injection occurs when untrusted input is inserted directly into SQL statements.

Unsafe example:

```php
$sql = "SELECT * FROM users WHERE name = '$name'";
```

An attacker may manipulate the SQL query through the supplied value.

Safe example using parameterized queries:

```php
$result = $database->query(
  'SELECT * FROM {users_field_data} WHERE name = :name',
  [':name' => $name]
);
```

Drupal's query builder also safely handles values supplied through conditions.

**Prevention:**
- Use parameterized queries.
- Use Drupal's database abstraction layer.
- Never concatenate untrusted input into SQL.
- Validate and allowlist dynamic identifiers such as table names and sort columns. SQL parameters cannot safely substitute table or column identifiers.

### 4.4 Cross-Site Request Forgery (CSRF)

CSRF tricks an authenticated user's browser into sending an unwanted request to an application.

For example, a user is logged in to a website. An attacker tricks the user into visiting a malicious page that attempts to submit a request using the user's existing session.

Drupal protects many form submissions through Form API's form tokens.

For custom routes that perform state-changing actions, configure appropriate access and CSRF protection.

Example route requirement:

```yaml
my_module.delete_item:
  path: '/my-module/delete/{id}'
  defaults:
    _controller: '\Drupal\my_module\Controller\ItemController::delete'
  requirements:
    _permission: 'administer site configuration'
    _csrf_token: 'TRUE'
```

Use CSRF protection for relevant state-changing routes. Do not use GET requests to perform destructive actions.

### 4.5 Authentication vs authorization

**Authentication:** Determines who the user is.

**Authorization:** Determines what the user is allowed to do.

Example:
- Login verifies the user's identity.
- Permissions determine whether the user can create or delete content.

Drupal provides:
- User roles.
- Permissions.
- Route access checks.
- Entity access checks.
- Access control handlers.

### 4.6 Drupal permissions

Define custom permissions in `my_module.permissions.yml`:

```yaml
administer my module:
  title: 'Administer my module'
  description: 'Manage settings for my module.'
```

Check a permission:

```php
if ($current_user->hasPermission('administer my module')) {
  // Perform the operation.
}
```

For routes, use:

```yaml
requirements:
  _permission: 'administer my module'
```

Always apply the principle of least privilege: users should receive only the permissions they need.

### 4.7 Entity access checks

Before displaying or modifying an entity, check whether the current user has the required access.

```php
$access = $node->access('update', NULL, TRUE);

if ($access->isAllowed()) {
  // Allow the update.
}
```

The third argument requests an AccessResult object, which can carry cacheability metadata.

Do not rely only on hiding a button or link. The server-side operation must enforce access independently.

### 4.8 Route access checks

Drupal routes can use permissions:

```yaml
requirements:
  _permission: 'access content'
```

They can also use custom access check services when more complex access logic is required.

Route access and entity access are not interchangeable. A user may have permission to access a route but still lack access to a particular entity.

### 4.9 File upload security

File uploads can introduce security risks.

Risks include:
- Executable files.
- Malicious file content.
- Oversized uploads.
- Incorrect file extensions.
- Files being stored in publicly accessible locations when they should be private.

Drupal provides file validation mechanisms.

Example:

```php
$validators = [
  'file_validate_extensions' => ['pdf doc docx'],
  'file_validate_size' => [10 * 1024 * 1024],
];
```

Use Drupal's managed file system and appropriate upload validators. Do not trust a filename or browser-provided MIME type alone.

### 4.10 Password security

Never store passwords in plain text.

Drupal's user system uses secure password hashing mechanisms.

Do not implement your own password storage logic when Drupal already provides the necessary functionality.

### 4.11 Secure configuration and secrets

Never commit:
- API keys.
- Database passwords.
- Private keys.
- Access tokens.
- Production credentials.

Use environment variables or an appropriate secrets-management system.

Restrict access to sensitive configuration and logs.

---

## 5. Drupal Debugging

Debugging is the process of identifying the cause of an issue and fixing it.

A senior developer should follow a systematic debugging process instead of randomly changing code.

### 5.1 Drupal logging

Drupal provides the logger service.

```php
\Drupal::logger('my_module')->error(
  'An error occurred: @message',
  ['@message' => $exception->getMessage()]
);
```

Common logging levels include:
- emergency
- alert
- critical
- error
- warning
- notice
- info
- debug

In production code, inject the `logger.factory` service.

### 5.2 Watchdog logs

Drupal records system messages in its logging system.

When Database Logging is enabled, messages can be viewed at:

`/admin/reports/dblog`

You can use Drush to inspect logs:

```bash
drush watchdog:show
```

The availability of logs depends on the configured logging backend.

### 5.3 Enable development services

Drupal development settings can enable:
- Verbose error messages.
- Twig debugging.
- Twig template suggestions.
- Disabled Twig cache during development.

These settings should be used only in development environments.

Never expose detailed exception traces or sensitive debugging information on production websites.

### 5.4 Drush commands for debugging

```bash
drush status
```

Displays Drupal and environment information.

```bash
drush cr
```

Rebuilds Drupal caches.

```bash
drush watchdog:show
```

Displays recent watchdog messages when available.

```bash
drush config:status
```

Checks for differences between active configuration and the configuration synchronization directory.

```bash
drush sql:query "SELECT COUNT(*) FROM users_field_data;"
```

Executes an SQL query against the configured database.

### 5.5 PHP debugging with Xdebug

Xdebug is a PHP extension that helps developers debug applications.

Features include:
- Breakpoints.
- Step-by-step execution.
- Variable inspection.
- Call stack inspection.
- Watching expressions.

In VS Code, configure a PHP debugging extension and connect it to Xdebug.

This is useful when a problem cannot be understood through logs alone.

### 5.6 Common Drupal debugging issues

**White screen or 500 error**
- Check PHP and Drupal logs.
- Check recent code changes.
- Check PHP version and extension compatibility.
- Inspect the stack trace in a development environment.

**Changes not appearing**
- Rebuild caches using `drush cr`.
- Check whether the correct Twig template is being used.
- Verify configuration and theme settings.

**Module not working**
- Check module dependencies.
- Check service definitions.
- Check routing and permissions.
- Check YAML syntax.
- Rebuild caches after changing discovery-related definitions.

**Database error**
- Verify database connectivity.
- Inspect the SQL query.
- Check table and column names.
- Check schema updates.
- Inspect database logs.

---

## 6. Drupal Performance Optimization

Performance optimization means improving response time, resource utilization, and the overall experience of a website.

### 6.1 Drupal caching layers

Drupal has multiple caching mechanisms.

**Page caching**

Stores complete page responses for reuse.

**Render caching**

Stores rendered portions of the page.

**Dynamic Page Cache**

Caches pages while respecting cache contexts and other cacheability metadata. It can benefit both anonymous and authenticated users.

**Internal Page Cache**

Caches complete pages for anonymous users.

**Browser caching**

Allows browsers to reuse eligible static assets rather than downloading them repeatedly.

**CDN caching**

Stores eligible content closer to users geographically.

### 6.2 Cache metadata

Drupal cache metadata consists of three important concepts.

| Concept | Purpose | Example |
|---|---|---|
| Cache tags | Identify what invalidates cached data | `node:1` |
| Cache contexts | Define variations of cached output | `user.roles` |
| Cache max-age | Defines how long output is cacheable | `3600` seconds |

Example:

```php
$build['message'] = [
  '#markup' => 'Welcome',
  '#cache' => [
    'tags' => ['node:1'],
    'contexts' => ['user.roles'],
    'max-age' => 3600,
  ],
];
```

Remember:
- Tags = what invalidates the cache.
- Contexts = what makes the output vary.
- Max-age = how long it can remain cached.

### 6.3 Cache invalidation

Cache invalidation means marking cached data as no longer valid so Drupal can regenerate it.

For example, when a node changes, its associated cache tags can invalidate dependent cached output.

```php
\Drupal::service('cache_tags.invalidator')
  ->invalidateTags(['node:1']);
```

In production code, inject the cache tags invalidator service.

### 6.4 Database optimization

- Add appropriate indexes.
- Avoid unnecessary JOINs.
- Optimize complex queries.
- Use pagination.
- Avoid N+1 queries.
- Use EXPLAIN to inspect query plans.
- Retrieve only necessary data.

### 6.5 Drupal render optimization

- Use render arrays.
- Add appropriate cache metadata.
- Avoid unnecessary entity loading.
- Use lazy builders for expensive, independently cacheable components when appropriate.
- Avoid rendering the same expensive content repeatedly.
- Check whether custom code makes an entire page uncacheable.

### 6.6 Front-end performance

Performance is not only a backend concern.

Optimize:
- Image dimensions and file sizes.
- Image formats such as WebP or AVIF where supported.
- CSS and JavaScript aggregation.
- Unnecessary JavaScript libraries.
- Font loading.
- Third-party scripts.
- Browser caching.
- CDN delivery.

Avoid loading large images when smaller responsive versions are sufficient.

### 6.7 Drupal performance investigation tools

Useful tools include:
- Drupal database logging.
- Drupal Webprofiler (contributed module).
- Xdebug.
- MySQL EXPLAIN.
- Browser developer tools.
- Lighthouse.
- Network and performance tabs.
- Server monitoring and application performance monitoring tools.

Do not install performance tools blindly in production. Choose tools appropriate to the environment and security requirements.

---

## 7. Practical Senior Developer Scenarios

### Scenario 1: A Drupal page takes 10 seconds to load. How would you troubleshoot it?

**Interview-ready answer:**

I would first identify whether the delay is coming from the frontend, backend, or database.

My approach would be:

1. Check browser network and performance tools.
2. Inspect the page response time and individual asset loading times.
3. Check Drupal logs for errors.
4. Inspect slow database queries.
5. Look for N+1 entity loading and inefficient loops.
6. Review cache metadata and cache hit rates.
7. Check external API calls and their response times.
8. Optimize the identified bottleneck and measure the result.

I would avoid making random changes without identifying the actual cause.

### Scenario 2: A page is showing stale content even after an update. What would you check?

**Interview-ready answer:**

I would investigate the cache invalidation flow.

I would check:
- Whether the relevant cache tags are attached.
- Whether the update invalidates the required tags.
- Whether cache contexts are correct.
- Whether a CDN or reverse proxy is serving stale content.
- Whether browser caching is involved.

I would then correct the cache metadata or invalidation logic rather than simply clearing all caches permanently.

### Scenario 3: A custom module is vulnerable to SQL injection. How would you fix it?

**Interview-ready answer:**

I would identify queries that concatenate user input directly into SQL statements.

I would replace them with Drupal's database abstraction layer and parameterized queries. I would also review dynamic table names, column names, sorting, and filtering to ensure they use safe allowlisted values.

### Scenario 4: A user can access content they should not be able to see. What would you investigate?

**Interview-ready answer:**

I would check the route access requirements, entity access checks, roles, and permissions.

I would verify that the controller or service does not rely only on frontend visibility and that the server-side operation checks access before returning or modifying the entity.

### Scenario 5: A custom report is slow because it loads thousands of records. How would you improve it?

**Interview-ready answer:**

I would inspect the SQL query and execution plan, check the indexes, and identify unnecessary joins.

I would introduce pagination or batch processing, retrieve only required columns, and consider caching expensive report results if the data does not need to be refreshed on every request.

### Scenario 6: An external API makes a Drupal page slow.

**Interview-ready answer:**

I would check the API response time, network latency, and timeout configuration.

If the data does not need to be displayed immediately, I would move the integration into a queue or asynchronous process. I would also consider caching API responses, implementing sensible retries, and logging failures.

### Scenario 7: A developer has made a change, but it is not visible on the website.

**Interview-ready answer:**

I would check whether the change was deployed to the correct environment, rebuild Drupal caches, and verify the active theme, template suggestions, configuration, and browser caching.

I would also check whether another custom module or theme is overriding the expected output.

### Scenario 8: How would you secure a custom Drupal form?

**Interview-ready answer:**

I would use Drupal Form API, define appropriate validation, and rely on Form API's form-token protection for normal form submissions.

I would validate user input, use safe output rendering, enforce permissions and entity access, and avoid unsafe database queries.

### Scenario 9: The database is consuming high CPU.

**Interview-ready answer:**

I would inspect slow queries and query execution plans, identify full table scans and expensive joins, and review indexes.

I would also investigate repeated queries, N+1 patterns, and excessive traffic. After optimizing, I would monitor database load and response time to confirm the improvement.

### Scenario 10: How do you approach a production incident?

**Interview-ready answer:**

My priority is to restore service safely while minimizing impact.

I would:
1. Understand the scope and severity.
2. Check monitoring, logs, and recent deployments.
3. Identify the likely cause.
4. Apply a safe mitigation or rollback if necessary.
5. Verify that the service has recovered.
6. Investigate the root cause.
7. Document preventive actions.

I would communicate clearly with the team throughout the incident.

---

## 8. Common Interview Questions and Answers

### Q1. What is the difference between DELETE, TRUNCATE, and DROP?

- `DELETE` removes selected rows and supports a WHERE condition.
- `TRUNCATE` removes all rows from a table and resets its auto-increment counter in MySQL.
- `DROP` removes the table structure and its data.

### Q2. What is an index in SQL?

An index is a database structure that helps retrieve records faster. It improves suitable SELECT queries but adds storage overhead and can slow down writes.

### Q3. What is a composite index?

A composite index is an index created on multiple columns.

Example:

```sql
CREATE INDEX idx_status_created
ON custom_table(status, created);
```

The order of columns matters because MySQL can use the leftmost prefix of a composite index.

### Q4. What is a database deadlock?

A deadlock occurs when two or more transactions wait for resources locked by each other, so none can proceed.

The database detects the deadlock and typically rolls back one transaction.

### Q5. What is SQL injection?

SQL injection is a security vulnerability where an attacker manipulates a SQL query through untrusted input.

Prevent it using parameterized queries and Drupal's database abstraction layer.

### Q6. What is XSS?

Cross-Site Scripting is an attack in which malicious scripts are injected into content and executed in another user's browser.

Prevent it through context-appropriate output escaping, safe rendering, and input handling.

### Q7. What is CSRF?

Cross-Site Request Forgery tricks an authenticated user's browser into submitting an unwanted request.

Drupal provides form-token protection through Form API and CSRF mechanisms for applicable routes.

### Q8. What is the difference between authentication and authorization?

Authentication verifies identity. Authorization determines what that identity is permitted to access or perform.

### Q9. What is the difference between validation and sanitization?

Validation checks whether data meets expected rules. Sanitization or escaping makes data safe for a particular use or output context.

### Q10. What is the N+1 query problem?

It occurs when one query retrieves a collection and additional queries are executed individually for each item. It can be reduced through batching, suitable joins, and more efficient loading.

### Q11. How do you identify slow SQL queries?

I would use slow query logs and EXPLAIN to inspect the query plan, indexes, rows examined, and joins. I would then optimize and measure the result.

### Q12. What is the difference between cache tags and cache contexts?

Cache tags identify dependencies that invalidate cached data. Cache contexts define the conditions under which output varies.

### Q13. What is the difference between Internal Page Cache and Dynamic Page Cache?

Internal Page Cache primarily caches complete pages for anonymous users. Dynamic Page Cache caches pages while respecting cacheability metadata and can benefit both anonymous and authenticated users.

### Q14. Why should we avoid direct database updates to Drupal entities?

Direct updates can bypass entity storage, validation, hooks, field handling, revisions, and other Drupal functionality. The Entity API should be used for normal entity operations.

### Q15. What is the purpose of Drupal's database abstraction layer?

It provides a consistent API for database operations, query building, parameterized queries, and transactions while reducing database-specific application code.

### Q16. What is the purpose of EXPLAIN?

EXPLAIN shows how the database plans to execute a query. It helps identify table scans, index usage, join strategies, and other performance concerns.

### Q17. What is lazy loading?

Lazy loading delays loading a resource or rendering a component until it is actually needed. It can reduce initial page load and resource consumption.

### Q18. What is the difference between debugging and logging?

Logging records application events and errors. Debugging is the process of investigating code execution to identify and fix a problem.

### Q19. What is Xdebug?

Xdebug is a PHP debugging extension that supports breakpoints, step-by-step execution, variable inspection, and call stack analysis.

### Q20. What is the principle of least privilege?

It means giving users and services only the permissions required to perform their responsibilities.

---

## 9. Quick Revision Table

| Topic | Key point to remember |
|---|---|
| SQL index | Speeds up suitable reads; adds write overhead |
| JOIN | Combines related tables |
| GROUP BY | Groups records |
| HAVING | Filters grouped results |
| EXPLAIN | Inspects query execution plans |
| Transaction | Groups operations into one logical unit |
| ACID | Atomicity, Consistency, Isolation, Durability |
| SQL injection | Prevent with parameterized queries |
| XSS | Prevent with context-aware output escaping |
| CSRF | Protect state-changing requests |
| Authentication | Verifies identity |
| Authorization | Checks permissions |
| N+1 | Repeated queries inside loops |
| Cache tags | Invalidation dependencies |
| Cache contexts | Output variations |
| Cache max-age | Cache lifetime |
| Xdebug | PHP debugging |
| Drush cr | Rebuilds Drupal caches |

---

## 10. Day 9 Completion Checklist

- [ ] Understand SQL SELECT, INSERT, UPDATE, DELETE.
- [ ] Understand INNER JOIN and LEFT JOIN.
- [ ] Understand GROUP BY and HAVING.
- [ ] Understand primary keys and foreign keys.
- [ ] Understand indexes and composite indexes.
- [ ] Understand database transactions and ACID.
- [ ] Write queries using Drupal's database abstraction layer.
- [ ] Understand Entity API vs direct database queries.
- [ ] Understand SQL injection, XSS, and CSRF.
- [ ] Understand authentication, authorization, and access checks.
- [ ] Understand file upload and secret-management security.
- [ ] Know how to debug Drupal using logs and Drush.
- [ ] Understand Xdebug.
- [ ] Understand N+1 queries and SQL optimization.
- [ ] Revise Drupal caching and performance.
- [ ] Practise the production troubleshooting scenarios.

**Day 9 objective:** Be able to explain how you write efficient SQL, secure Drupal applications, investigate production issues, and optimize performance with practical examples.

