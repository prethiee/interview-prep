# Day 6 – Drupal Caching and Performance Optimization

## 1. Introduction to Drupal Caching

### 1.1 What is Caching?

Caching is the process of temporarily storing data or rendered output so that it can be reused instead of being generated repeatedly.

Caching improves website performance by:
- Reducing database queries.
- Reducing PHP processing.
- Avoiding repeated rendering.
- Reducing server load.
- Improving page response time.
- Supporting better scalability.

### 1.2 Why is Caching Important in Drupal?

Drupal websites often contain:
- Complex entity structures.
- Multiple database queries.
- Views with large datasets.
- Dynamic blocks.
- Custom modules.
- API integrations.
- Personalised content.

Without caching, Drupal may repeatedly execute the same queries and rendering operations for every request.

Caching reduces unnecessary processing and improves response times.

### 1.3 Types of Caching in Drupal

Drupal provides several caching layers:

1. Render Cache
2. Dynamic Page Cache
3. Internal Page Cache
4. Entity Cache
5. Views Cache
6. Cache Backend
7. External caching through reverse proxies and CDNs.

---

## 2. Drupal Cache Systems

### 2.1 Render Cache

Render Cache stores rendered output of individual components, such as:
- Blocks.
- Entity render arrays.
- Field output.
- Other renderable components.

When Drupal needs to render the same component again, it can reuse the cached output instead of rebuilding it.

**Example:**

A custom block displays a list of popular articles.

Instead of querying and rendering the articles for every request, Drupal can reuse the cached render output.

Render caching is controlled through cache metadata such as:
- Cache tags.
- Cache contexts.
- Cache max-age.

### 2.2 Dynamic Page Cache

Dynamic Page Cache stores rendered page responses while respecting cache contexts and other cache metadata.

It can cache pages for both anonymous and authenticated users, provided the response is cacheable.

It supports pages containing dynamic or personalised components through appropriate cache contexts.

**Example:**

A website displays:
- Common page content.
- A user-specific welcome message.
- A language-specific menu.

Dynamic Page Cache can reuse the page response while varying the cached response where required by cache contexts.

### 2.3 Internal Page Cache

Internal Page Cache stores complete page responses for anonymous users.

It is particularly useful for websites with many anonymous visitors.

It can serve previously generated page responses without repeating the full Drupal rendering process.

Authenticated users are not served pages through Internal Page Cache.

### 2.4 Render Cache vs Dynamic Page Cache vs Internal Page Cache

| Cache | What it caches | Typical usage |
|---|---|---|
| Render Cache | Individual renderable components | Blocks, entities, fields |
| Dynamic Page Cache | Rendered page responses with cache metadata | Anonymous and authenticated users |
| Internal Page Cache | Complete page responses | Anonymous users |

**Interview Question: What is the difference between Internal Page Cache and Dynamic Page Cache?**

Internal Page Cache stores complete page responses for anonymous users. Dynamic Page Cache stores rendered page responses for both anonymous and authenticated users while respecting cache contexts and other cache metadata.

**Interview Question: What is the difference between Render Cache and Dynamic Page Cache?**

Render Cache stores individual renderable components, whereas Dynamic Page Cache stores rendered page responses.

**Memory Tip:**
- Render Cache = Component.
- Dynamic Page Cache = Rendered page.
- Internal Page Cache = Anonymous full page.

---

## 3. Cache Metadata

Drupal uses three important cache metadata properties:

1. Cache Tags
2. Cache Contexts
3. Cache Max-Age

Understanding these three concepts is essential for Drupal interviews.

### 3.1 Cache Tags

Cache tags identify the data that a cached item depends on.

When the underlying data changes, Drupal invalidates the corresponding cache tags.

This ensures that cached content is regenerated when necessary.

**Example:**

An article page displays:
- Article title.
- Author information.
- Related content.

The rendered output may depend on:
- `node:1`
- `user:5`

When the article or author changes, the relevant cache tags can invalidate the cached output.

Common cache tags:

| Cache Tag | Purpose |
|---|---|
| `node:1` | Specific node |
| `user:5` | Specific user |
| `taxonomy_term:2` | Specific taxonomy term |
| `config:system.site` | Site configuration |
| `node_list` | Node listing |

**Example in a render array:**

```php
$build = [
  '#markup' => 'Cached content',
  '#cache' => [
    'tags' => ['node:1'],
  ],
];
```

When node 1 changes, Drupal can invalidate cached items tagged with `node:1`.

### 3.2 Cache Contexts

Cache contexts determine the variations of cached output.

They tell Drupal when the same component or page needs different cached versions.

Examples:

| Cache Context | Purpose |
|---|---|
| `user` | Varies by current user |
| `user.roles` | Varies by user roles |
| `languages:language_interface` | Varies by interface language |
| `url.path` | Varies by URL path |
| `url.query_args` | Varies by query arguments |
| `user.permissions` | Varies by user permissions |

**Example:**

A block displays a different welcome message for each logged-in user.

```php
$build = [
  '#markup' => 'Welcome!',
  '#cache' => [
    'contexts' => ['user'],
  ],
];
```

The `user` cache context ensures that output is varied by user identity.

If the output depends only on user role, `user.roles` may be more appropriate.

### 3.3 Cache Max-Age

Cache max-age defines how long cached output remains valid based on time.

Values:

- `0`: Do not cache.
- Positive integer: Cache for the specified number of seconds.
- `Cache::PERMANENT`: No time-based expiration.

Example:

```php
use Drupal\Core\Cache\Cache;

$build = [
  '#markup' => 'Cached content',
  '#cache' => [
    'max-age' => 3600,
  ],
];
```

This specifies a cache lifetime of one hour.

Permanent cache example:

```php
use Drupal\Core\Cache\Cache;

$build = [
  '#markup' => 'Permanent cached content',
  '#cache' => [
    'max-age' => Cache::PERMANENT,
  ],
];
```

Permanent does not mean the cache can never be invalidated. Cache tags can still invalidate it.

### 3.4 Cache Tags vs Cache Contexts vs Max-Age

| Cache Metadata | Purpose | Question it answers |
|---|---|---|
| Tags | Invalidation | When should cached data be cleared? |
| Contexts | Variation | For whom or under what conditions does output differ? |
| Max-age | Expiration | How long should it remain valid? |

**Interview Question: Explain cache tags, contexts and max-age.**

Cache tags identify the data a cached item depends on and allow invalidation when that data changes. Cache contexts define variations in cached output, such as by user or language. Max-age defines how long cached output remains valid.

**Memory Tip:**
- Tags = When to clear.
- Contexts = What varies.
- Max-age = How long.

---

## 4. Cache Invalidation

### 4.1 What is Cache Invalidation?

Cache invalidation is the process of marking cached data as no longer valid so that Drupal can regenerate it when needed.

For example:

If an article is cached and its title changes, the old cached output should no longer be displayed.

Drupal uses cache tags to invalidate related cached items.

### 4.2 Cache Invalidation vs Cache Clearing

These concepts are related but not identical.

**Cache Invalidation**

Marks specific cached data as invalid using cache tags or other invalidation mechanisms.

**Cache Clearing**

Removes cached data from a cache bin or rebuilds broader Drupal caches.

### 4.3 Cache Invalidation Example

```php
\Drupal::service('cache_tags.invalidator')
  ->invalidateTags(['node:1']);
```

This invalidates cached entries tagged with `node:1`.

### 4.4 Clearing Drupal Cache

Using Drush:

```bash
drush cr
```

This rebuilds Drupal caches and clears relevant cached data, including caches used for routing, plugin discovery and other definitions.

### 4.5 Cache Invalidation vs drush cr

| Cache Invalidation | drush cr |
|---|---|
| Targets cached data using tags. | Rebuilds Drupal caches more broadly. |
| More targeted. | Broader operation. |
| Useful when content changes. | Useful when changing code, routes, services or plugin definitions. |

**Interview Question: What is the difference between cache invalidation and drush cr?**

Cache invalidation marks cached items associated with specific tags as invalid. `drush cr` rebuilds Drupal caches more broadly, including caches used for routing and plugin discovery.

### 4.6 Cache Invalidation Best Practices

- Use cache tags for data-dependent output.
- Avoid clearing all caches whenever content changes.
- Add appropriate cache contexts for personalised output.
- Use suitable max-age values.
- Ensure custom render arrays include correct cache metadata.
- Test cache behaviour for anonymous and authenticated users.

---

## 5. Cache API and Cache Backend

### 5.1 What is Cache API?

Drupal Cache API provides a standard mechanism for storing and retrieving cached data.

It allows developers to:
- Store cached data.
- Retrieve cached data.
- Delete cached data.
- Invalidate cached data.
- Manage cache expiration.

### 5.2 Cache Backend

A cache backend is responsible for storing and retrieving cached data.

Drupal provides different cache backend implementations.

Examples:
- Database cache backend.
- Memory-based cache backend.
- Redis integration through contributed modules.

### 5.3 Cache Bins

Cache bins separate cached data into logical groups.

Examples:
- `cache.default`
- `cache.render`
- `cache.dynamic_page_cache`
- `cache.data`
- `cache.discovery`
- `cache.config`

Different cache bins serve different purposes.

### 5.4 Using Cache API

Inject the cache backend service into a custom service or controller.

Example:

```php
$cache = \Drupal::cache('my_custom_cache');

$data = $cache->get('my_cache_key');

if (!$data) {
  $value = 'Expensive operation result';

  $cache->set(
    'my_cache_key',
    $value,
    time() + 3600,
    ['my_custom_tag']
  );

  $data = $cache->get('my_cache_key');
}
```

Here:
- `get()` retrieves cached data.
- `set()` stores cached data.
- The cache key identifies the item.
- Expiration is specified using a timestamp.
- Cache tags support invalidation.

### 5.5 Cache API Methods

| Method | Purpose |
|---|---|
| `get()` | Retrieves cached data. |
| `set()` | Stores cached data. |
| `delete()` | Deletes a specific cache item. |
| `deleteAll()` | Deletes all items in the bin. |
| `invalidate()` | Marks an item invalid. |
| `invalidateAll()` | Invalidates all items in the bin. |

### 5.6 Cache API vs Render Cache

| Cache API | Render Cache |
|---|---|
| General-purpose caching system. | Caches renderable output. |
| Can store arbitrary application data. | Stores rendered components and related metadata. |
| Used for expensive computations and data. | Used during Drupal rendering. |

### 5.7 Redis Cache

Redis is an in-memory data store that can be used as a Drupal cache backend through contributed integration.

Benefits:
- Fast data access.
- Reduced database cache workload.
- Better cache performance under heavy traffic.
- Shared cache storage across application servers when appropriately configured.

Redis is particularly useful for high-traffic enterprise Drupal applications.

**Interview Question: Why would you use Redis in Drupal?**

Redis provides fast in-memory cache storage and can reduce database load. It is useful for high-traffic applications where cache performance and scalability are important.

---

## 6. Views Caching

### 6.1 What is Views Caching?

Views caching stores the results or rendered output of a Drupal View to avoid repeatedly executing expensive queries and rendering operations.

It is useful for:
- Large content listings.
- Search result pages.
- News listings.
- Product listings.
- Dashboard data.

### 6.2 Views Cache Settings

Views provides caching options depending on the configured cache plugin and Drupal version.

Common approaches include:

- No caching.
- Time-based caching.
- Tag-based caching.

### 6.3 Time-Based Caching

The View output is cached for a specified duration.

Example:

A news listing can be cached for five minutes.

### 6.4 Tag-Based Caching

The View cache can be invalidated when relevant tagged data changes.

This is useful when listing content changes frequently.

### 6.5 Optimizing Views

When a View is slow:

1. Check the underlying SQL query.
2. Review filters and relationships.
3. Check database indexes.
4. Avoid unnecessary fields.
5. Use pagination.
6. Review exposed filters and sorting.
7. Configure appropriate caching.
8. Measure query execution time.

**Interview Question: How do you optimize a slow Drupal View?**

I would inspect the generated SQL query and identify expensive joins, filters and sorting operations. I would review database indexes, reduce unnecessary fields and relationships, implement pagination and configure appropriate caching. I would then measure performance improvements.

---

## 7. Database Performance Optimization

### 7.1 Why Database Optimization Matters

Drupal applications often perform multiple database queries for:
- Loading entities.
- Retrieving field values.
- Rendering Views.
- Loading configuration.
- Access checking.

Poorly optimized queries can increase response times and database load.

### 7.2 Database Indexing

An index helps the database locate records more efficiently.

Without an appropriate index, the database may need to scan many rows to find matching records.

Indexes are especially important for frequently queried columns.

Example:

A View filters thousands of records by status and creation date. Appropriate database indexes can improve query performance.

However, unnecessary indexes can increase storage requirements and slow down write operations.

### 7.3 Query Optimization

Best practices:
- Retrieve only required data.
- Avoid unnecessary database queries.
- Use appropriate indexes.
- Avoid loading entities one by one inside large loops.
- Use entity queries when appropriate.
- Review expensive joins.
- Use pagination for large result sets.

### 7.4 N+1 Query Problem

The N+1 query problem occurs when an application executes one query to retrieve a list of records and then executes an additional query for every record.

Example:

1. One query loads 100 articles.
2. A loop loads each article's author separately.
3. Another 100 queries are executed.

This can create significant database overhead.

Solutions:
- Use `loadMultiple()` where appropriate.
- Retrieve related data efficiently.
- Use entity reference relationships or optimized queries.
- Cache repeated results.
- Avoid loading the same entity multiple times.

### 7.5 Pagination

Pagination divides large datasets into smaller pages.

Instead of loading thousands of records at once, Drupal retrieves only the records required for the current page.

Pagination improves:
- Memory usage.
- Response time.
- Database workload.
- User experience.

Important: Pagination alone does not guarantee an efficient query. The underlying SQL query, filtering, sorting and indexing must also be optimized.

### Interview Questions – Database Performance

**Q1. What is database indexing?**

An index is a database structure that helps locate records faster without scanning the entire table.

**Q2. What is the N+1 query problem?**

It occurs when an application executes one query to retrieve a collection and then executes additional queries for each item in that collection.

**Q3. How do you prevent N+1 queries in Drupal?**

Use efficient entity loading, `loadMultiple()`, optimized queries and caching. Avoid loading related entities individually inside large loops.

**Q4. Why is pagination important?**

It limits the amount of data retrieved and displayed at one time, reducing memory usage and improving performance.

---

## 8. Frontend Performance Optimization

### 8.1 Asset Aggregation

Drupal supports CSS and JavaScript aggregation.

Aggregation combines multiple CSS or JavaScript files into fewer files.

Benefits:
- Fewer HTTP requests.
- Reduced request overhead.
- Improved page loading performance.

### 8.2 CSS and JavaScript Optimization

Best practices:
- Enable aggregation in production.
- Remove unused libraries.
- Avoid unnecessary JavaScript.
- Load scripts appropriately.
- Minimize large frontend assets.
- Use lazy loading where appropriate.

### 8.3 Image Optimization

Large images can significantly affect page load time.

Best practices:
- Resize images to appropriate dimensions.
- Use Drupal image styles.
- Compress images.
- Use modern formats such as WebP where supported.
- Implement responsive image techniques.
- Use lazy loading for below-the-fold images.

### 8.4 CDN

A Content Delivery Network distributes static assets across geographically distributed servers.

Benefits:
- Reduces latency.
- Improves asset delivery.
- Reduces load on the origin server.
- Helps handle high traffic.

Examples include Cloudflare and Amazon CloudFront.

### 8.5 Lazy Loading

Lazy loading delays loading resources until they are needed.

For example, images below the visible screen area can be loaded when the user scrolls towards them.

This reduces the initial page load workload.

### Interview Question: How do you optimize frontend performance in Drupal?

I would enable CSS and JavaScript aggregation, optimize images, use responsive image styles, implement lazy loading, reduce unnecessary JavaScript and CSS, and use a CDN for static assets.

---

## 9. Drupal Performance Troubleshooting

### 9.1 How to Troubleshoot a Slow Drupal Website

A structured troubleshooting approach is important.

**Step 1: Identify the Problem**

Determine whether the slowdown is related to:
- Frontend rendering.
- Backend PHP processing.
- Database queries.
- External API calls.
- Server resources.
- Network latency.

**Step 2: Inspect Browser Performance**

Use browser developer tools to inspect:
- Network requests.
- Response times.
- Large assets.
- JavaScript errors.
- Failed requests.

**Step 3: Inspect Drupal**

Review:
- Drupal logs.
- Slow Views.
- Expensive custom code.
- Cache configuration.
- Unnecessary entity loading.
- External API calls.

**Step 4: Inspect the Database**

Check:
- Slow SQL queries.
- Missing indexes.
- Expensive joins.
- N+1 queries.
- Large result sets.

**Step 5: Inspect Infrastructure**

Check:
- CPU usage.
- Memory consumption.
- PHP configuration.
- Database resource usage.
- Web server configuration.
- CDN and reverse proxy configuration.

**Step 6: Apply Optimization**

Implement the necessary fixes and verify the improvements through measurements.

### 9.2 Useful Tools

| Tool | Purpose |
|---|---|
| Browser DevTools | Inspect frontend performance and network requests. |
| Drupal Database Logging | Review Drupal log messages. |
| Devel | Developer tools and query inspection. |
| Webprofiler | Profile Drupal requests and performance. |
| Xdebug | Debug PHP execution. |
| Blackfire | Profile application performance. |
| MySQL EXPLAIN | Analyze SQL query execution plans. |
| Drush | Manage Drupal caches and site operations. |

### 9.3 Measuring Performance

Useful metrics:
- Time to First Byte (TTFB).
- Largest Contentful Paint (LCP).
- First Contentful Paint (FCP).
- Total Blocking Time (TBT).
- Database query execution time.
- PHP execution time.
- Memory usage.

Performance optimization should always be measured before and after changes.

### Interview Question: How do you troubleshoot a slow Drupal website?

I first identify whether the issue is frontend, backend, database or infrastructure-related. I inspect browser network timings, Drupal logs, expensive Views, SQL queries and external API calls. Based on the findings, I optimize queries, caching, rendering or assets and measure the improvement.

---

## 10. Important Cache Concepts – Quick Revision

### 10.1 Cache Tags

Identify dependencies and support invalidation.

### 10.2 Cache Contexts

Define variations in cached output.

### 10.3 Cache Max-Age

Defines cache lifetime.

### 10.4 Cache Invalidation

Marks cached data as no longer valid.

### 10.5 Cache Rebuild

Rebuilds Drupal caches using:

```bash
drush cr
```

### 10.6 Internal Page Cache

Caches complete responses for anonymous users.

### 10.7 Dynamic Page Cache

Caches rendered page responses for anonymous and authenticated users, respecting cache metadata.

### 10.8 Render Cache

Caches individual renderable components.

### 10.9 Redis

Provides an in-memory cache backend through contributed Drupal integration.

---

## 11. Important Differences – Interview Revision

| Concept | Difference |
|---|---|
| Render Cache vs Dynamic Page Cache | Components vs rendered page responses |
| Dynamic Page Cache vs Internal Page Cache | Anonymous and authenticated vs anonymous page caching |
| Cache Tags vs Contexts | Invalidation vs variation |
| Cache Contexts vs Max-Age | Variation vs expiration |
| Cache Invalidation vs Cache Clearing | Targeted invalidation vs broader clearing/rebuilding |
| Cache API vs Render Cache | General-purpose data caching vs rendered output caching |
| Configuration Cache vs Render Cache | Configuration data vs rendered components |
| Time-based vs Tag-based Views caching | Expiration by time vs invalidation through tags |
| Pagination vs Caching | Limits result sets vs reuses stored results |
| CDN vs Redis | Distributed asset delivery vs in-memory application caching |

---

## 12. Practical Interview Scenarios

### Scenario 1: A block displays different information for each user.

Use the `user` cache context if the output depends on user identity.

### Scenario 2: A block displays content that changes whenever an article is updated.

Add the appropriate node cache tags so the block can be invalidated when the article changes.

### Scenario 3: A custom block should not be cached.

Set:

```php
'#cache' => [
  'max-age' => 0,
],
```

### Scenario 4: A website is slow because the same expensive query runs repeatedly.

Use appropriate caching, review the query and consider whether the results can be reused.

### Scenario 5: A View displays 5,000 records and loads slowly.

Review the SQL query, filters, relationships, indexes, sorting and pagination. Configure suitable Views caching.

### Scenario 6: A personalised block displays the wrong user's information.

Review cache contexts. If the output depends on user identity, add the `user` cache context.

### Scenario 7: A page displays outdated content after an article update.

Check whether the rendered output has appropriate cache tags and whether dependent caches are being invalidated correctly.

### Scenario 8: A website has high database load.

Investigate slow queries, indexing, repeated entity loading, N+1 queries and cache backend performance. Consider Redis where appropriate.

### Scenario 9: Images are slowing down page loading.

Optimize image dimensions and compression, use image styles, responsive images, lazy loading and a CDN.

### Scenario 10: The website becomes slow during high traffic.

Investigate application and infrastructure bottlenecks. Review page and render caching, Redis, database performance, reverse proxies, CDN configuration and server scaling.

---

## 13. Final Interview Revision

Remember these important points:

1. Caching reduces repeated processing and improves performance.
2. Render Cache stores individual renderable components.
3. Dynamic Page Cache stores rendered page responses for anonymous and authenticated users.
4. Internal Page Cache stores complete page responses for anonymous users.
5. Cache tags identify dependencies and support invalidation.
6. Cache contexts define variations in cached output.
7. Cache max-age defines cache lifetime.
8. Cache invalidation marks cached data as invalid.
9. `drush cr` rebuilds Drupal caches.
10. Cache API stores and retrieves reusable data.
11. Redis can provide fast in-memory cache storage.
12. Views caching avoids repeatedly executing expensive View operations.
13. Database indexes improve data retrieval.
14. N+1 queries create unnecessary database overhead.
15. Pagination limits the number of records retrieved at a time.
16. CSS and JavaScript aggregation reduces asset requests.
17. Image optimization improves frontend performance.
18. CDNs distribute static assets closer to users.
19. Browser DevTools helps identify frontend performance problems.
20. Performance improvements should always be measured.

## 14. Day 6 Completion Checklist

- [ ] Understand Drupal's caching architecture.
- [ ] Understand Render Cache.
- [ ] Understand Dynamic Page Cache.
- [ ] Understand Internal Page Cache.
- [ ] Understand cache tags.
- [ ] Understand cache contexts.
- [ ] Understand cache max-age.
- [ ] Understand cache invalidation.
- [ ] Understand Cache API and cache bins.
- [ ] Understand Redis integration.
- [ ] Understand Views caching.
- [ ] Understand database indexing.
- [ ] Understand N+1 query problems.
- [ ] Understand pagination.
- [ ] Understand frontend optimization.
- [ ] Understand CDN and lazy loading.
- [ ] Practice troubleshooting a slow Drupal website.
- [ ] Practice explaining cache-related interview scenarios.

**End of Day 6 – Drupal Caching and Performance Optimization.**
