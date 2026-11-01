# Day 8 – REST APIs, HTTP Client, and Integrations

## 1. Introduction to REST APIs

### 1.1 What is an API?

API stands for Application Programming Interface.

An API allows two software applications to communicate and exchange data.

For example, a Drupal website may communicate with:
- Payment gateways.
- CRM systems.
- ERP platforms.
- Marketing automation tools.
- External content platforms.
- Mobile applications.

Drupal can expose its own APIs and consume APIs provided by external systems.

### 1.2 What is REST?

REST stands for Representational State Transfer.

It is an architectural style used to design network-based applications.

REST APIs allow clients and servers to communicate using HTTP.

A REST API generally:
- Uses HTTP methods.
- Exposes resources through URLs.
- Transfers representations of data.
- Uses standard HTTP status codes.
- Is stateless at the request level.

Example:

```text
GET /api/articles/10
POST /api/articles
PATCH /api/articles/10
DELETE /api/articles/10
```

### 1.3 What is a RESTful API?

A RESTful API follows REST architectural principles.

Important principles include:

1. Client-server architecture.
2. Stateless communication.
3. Cacheability.
4. Uniform interface.
5. Layered system.
6. Optional code-on-demand.

**Statelessness**

Each request contains the information required to process it. The server does not rely on stored client session context to understand the request.

Authentication tokens may still be sent with individual requests.

### 1.4 REST vs SOAP

| REST | SOAP |
|---|---|
| Architectural style. | Messaging protocol. |
| Commonly uses JSON. | Uses XML-based envelopes. |
| Typically uses HTTP methods directly. | Uses SOAP operations and message structures. |
| Generally lightweight. | More formal and structured. |
| Flexible resource-based APIs. | Often contract-based through WSDL. |

**Interview Question: What is the difference between REST and SOAP?**

REST is an architectural style that commonly uses HTTP and JSON to exchange data. SOAP is a messaging protocol that uses structured XML messages and formal service contracts.

### 1.5 REST vs JSON

REST and JSON are not alternatives.

- REST is an architectural style.
- JSON is a data-interchange format.

A REST API can return JSON or other supported formats.

**Memory Tip:** REST = Architecture. JSON = Data format.

---

## 2. HTTP Methods

HTTP methods define the operation a client wants to perform on a resource.

### 2.1 GET

Used to retrieve information.

Example:

```http
GET /api/articles/10
```

Returns article information.

GET requests should not modify server-side resource state.

### 2.2 POST

Used to create a resource or submit data for processing.

Example:

```http
POST /api/articles
```

Request body:

```json
{
  "title": "Drupal REST API",
  "status": true
}
```

### 2.3 PUT

Used to replace a resource representation.

Example:

```http
PUT /api/articles/10
```

It generally represents a complete replacement of the target resource's supplied representation.

### 2.4 PATCH

Used to partially update a resource.

Example:

```http
PATCH /api/articles/10
```

It updates selected properties without requiring a complete replacement.

### 2.5 DELETE

Used to delete a resource.

Example:

```http
DELETE /api/articles/10
```

### 2.6 HTTP Methods Comparison

| Method | Purpose | Typical operation |
|---|---|---|
| GET | Retrieve | Read data |
| POST | Create or process | Create a resource |
| PUT | Replace | Full update |
| PATCH | Partially update | Update selected fields |
| DELETE | Remove | Delete resource |

### 2.7 Idempotency

An operation is idempotent if performing it multiple times has the same intended effect on the resource state as performing it once.

Commonly:
- GET: Idempotent.
- PUT: Idempotent.
- DELETE: Idempotent in HTTP semantics.
- PATCH: Depends on the operation.
- POST: Generally not idempotent.

**Interview Question: What is the difference between PUT and PATCH?**

PUT generally replaces the complete resource representation, whereas PATCH applies partial modifications to an existing resource.

**Interview Question: What is idempotency?**

Idempotency means that repeating the same request multiple times has the same intended effect on server-side resource state as executing it once.

---

## 3. HTTP Status Codes

HTTP status codes indicate the result of an HTTP request.

### 3.1 2xx – Success

| Status | Meaning |
|---|---|
| 200 | OK |
| 201 | Created |
| 202 | Accepted |
| 204 | No Content |

### 3.2 3xx – Redirection

| Status | Meaning |
|---|---|
| 301 | Moved Permanently |
| 302 | Found |
| 304 | Not Modified |

### 3.3 4xx – Client Errors

| Status | Meaning |
|---|---|
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 405 | Method Not Allowed |
| 409 | Conflict |
| 422 | Unprocessable Content |
| 429 | Too Many Requests |

### 3.4 5xx – Server Errors

| Status | Meaning |
|---|---|
| 500 | Internal Server Error |
| 502 | Bad Gateway |
| 503 | Service Unavailable |
| 504 | Gateway Timeout |

### 3.5 401 vs 403

**401 Unauthorized**

The request lacks valid authentication credentials.

**403 Forbidden**

The server understands the request but refuses to authorize it.

**Interview Question: What is the difference between 401 and 403?**

401 indicates that valid authentication credentials are missing or invalid. 403 indicates that the request is understood, but the authenticated user does not have permission to perform the operation.

---

## 4. Drupal REST Module

### 4.1 What is Drupal REST?

Drupal provides a core REST module that allows Drupal resources to be exposed through RESTful HTTP endpoints.

It allows external applications to:
- Retrieve Drupal content.
- Create content.
- Update entities.
- Delete entities.
- Access configured resources.

The REST module works with other Drupal components such as Serialization and Authentication.

### 4.2 Enabling REST and Serialization

Enable the core modules:

```bash
drush en rest serialization -y
```

Depending on the authentication method, you may also need to enable the appropriate authentication module.

### 4.3 Configuring REST Resources

Navigate to:

Configuration → Web services → REST

You can configure:
- Resource.
- HTTP methods.
- Accepted request formats.
- Supported response formats.
- Authentication providers.

For example, you can configure the content entity resource to support GET requests and JSON responses.

### 4.4 Example REST Endpoint

A configured REST resource may expose an endpoint similar to:

```text
GET /node/10?_format=json
```

The exact endpoint and supported operations depend on the REST resource configuration and Drupal version.

### 4.5 REST Resource Configuration

REST resources define which operations are available.

For example:
- GET: Retrieve content.
- POST: Create content.
- PATCH: Update content.
- DELETE: Delete content.

Each operation can have its own authentication and permission requirements.

### 4.6 Serialization

Serialization converts PHP objects or structured data into a transferable format such as JSON.

Deserialization converts incoming data into structured objects or values that the application can process.

Example:

Drupal entity data can be serialized into JSON for an external application.

**Interview Question: What is serialization?**

Serialization converts application data or objects into a transferable format such as JSON. Deserialization converts the received representation back into structured data.

---

## 5. JSON:API in Drupal

### 5.1 What is JSON:API?

JSON:API is a standardized specification for building APIs that expose and manipulate resources using JSON.

Drupal provides a core JSON:API module.

It automatically exposes entity resources using standardized endpoints.

### 5.2 Enabling JSON:API

```bash
drush en jsonapi -y
```

Example endpoint:

```text
/jsonapi/node/article
```

This endpoint retrieves article resources.

### 5.3 JSON:API Features

- Standardized resource endpoints.
- Entity retrieval.
- Entity creation.
- Entity updates.
- Entity deletion.
- Filtering.
- Sorting.
- Pagination.
- Relationships.
- Included related resources.

JSON:API reduces the need to manually create endpoints for common entity operations.

### 5.4 Example JSON:API Request

```http
GET /jsonapi/node/article
Accept: application/vnd.api+json
```

Example response structure:

```json
{
  "data": [
    {
      "type": "node--article",
      "id": "123",
      "attributes": {
        "title": "Drupal API Integration"
      }
    }
  ]
}
```

### 5.5 Filtering

Example:

```text
/jsonapi/node/article?filter[status]=1
```

The exact filter syntax depends on the resource and supported field configuration.

### 5.6 Sorting

Example:

```text
/jsonapi/node/article?sort=-created
```

A minus sign indicates descending order.

### 5.7 Pagination

Example:

```text
/jsonapi/node/article?page[limit]=10&page[offset]=0
```

This retrieves a page of 10 records starting from offset 0.

### 5.8 REST vs JSON:API in Drupal

| Drupal REST | JSON:API |
|---|---|
| Requires resource configuration. | Automatically exposes supported entity resources. |
| Allows configurable REST resource operations. | Follows the JSON:API specification. |
| Useful for custom resource configurations and operations. | Useful for standardized entity APIs. |
| Supports configurable serialization formats. | Uses the JSON:API format. |

**Interview Question: What is the difference between Drupal REST and JSON:API?**

Drupal REST provides configurable REST resources where developers can select resources, HTTP methods, formats and authentication providers. JSON:API provides a standardized API for Drupal entities with built-in support for filtering, sorting, pagination and relationships.

**Memory Tip:** REST = Configurable resources. JSON:API = Standardized entity API.

---

## 6. Authentication and Authorization

### 6.1 What is Authentication?

Authentication verifies the identity of the client or user making an API request.

It answers:

Who are you?

Examples:
- Username and password.
- API keys.
- OAuth access tokens.
- Session cookies.

### 6.2 What is Authorization?

Authorization determines what an authenticated user or client is allowed to do.

It answers:

What are you allowed to do?

Examples:
- View content.
- Create articles.
- Update nodes.
- Delete entities.
- Access restricted information.

### 6.3 Authentication vs Authorization

| Authentication | Authorization |
|---|---|
| Verifies identity. | Checks permissions. |
| Determines who the user is. | Determines what the user can do. |
| Example: OAuth token validation. | Example: Permission to update an article. |

### 6.4 Basic Authentication

Basic authentication sends a username and password encoded using Base64.

It must be protected by HTTPS because Base64 is not encryption.

Drupal provides a core Basic Authentication module.

### 6.5 Cookie Authentication

Cookie-based authentication is commonly used for browser-based applications that authenticate through Drupal sessions.

Drupal's session cookie identifies the authenticated session.

State-changing requests must also be protected against CSRF attacks.

### 6.6 OAuth 2.0

OAuth 2.0 is an authorization framework that allows applications to obtain limited access to protected resources using access tokens.

Example:

A mobile application obtains an access token and uses it to access protected Drupal API resources.

OAuth 2.0 support in Drupal generally requires an appropriate contributed integration or external identity provider.

### 6.7 API Security Best Practices

- Always use HTTPS.
- Use appropriate authentication.
- Apply least-privilege permissions.
- Validate incoming data.
- Enforce entity access checks.
- Avoid exposing sensitive information.
- Protect against CSRF where applicable.
- Implement rate limiting where appropriate.
- Avoid storing credentials in source code.
- Keep API dependencies and authentication integrations updated.

**Interview Question: How do you secure a Drupal API?**

I would use HTTPS, configure appropriate authentication, enforce Drupal permissions and entity access checks, validate incoming data, protect sensitive information and implement rate limiting where required.

---

## 7. Drupal HTTP Client

### 7.1 What is the HTTP Client?

Drupal provides an HTTP Client service for making HTTP requests to external APIs.

It is based on the Guzzle HTTP client library.

It allows Drupal to:
- Send GET requests.
- Send POST requests.
- Send PUT and PATCH requests.
- Send DELETE requests.
- Send headers.
- Send request bodies.
- Process API responses.

### 7.2 Why Use Drupal HTTP Client?

A Drupal application may need to integrate with:
- External CRM platforms.
- Payment gateways.
- Marketing automation tools.
- External search services.
- Third-party content APIs.
- Authentication services.

The HTTP Client service provides a standard way to communicate with these systems.

### 7.3 Getting the HTTP Client Service

The service ID is:

```text
http_client
```

In production code, inject the HTTP client service using dependency injection.

### 7.4 Example: GET Request

```php
<?php

namespace Drupal\my_module;

use GuzzleHttp\ClientInterface;

class ApiService {

  protected ClientInterface $httpClient;

  public function __construct(ClientInterface $http_client) {
    $this->httpClient = $http_client;
  }

  public function getArticles(): array {
    $response = $this->httpClient->request(
      'GET',
      'https://api.example.com/articles'
    );

    $data = json_decode(
      $response->getBody()->getContents(),
      TRUE
    );

    return $data ?? [];
  }

}
```

**Explanation:**

- `ClientInterface`: Represents the HTTP client.
- `request()`: Sends the HTTP request.
- `GET`: Retrieves data.
- `getBody()`: Retrieves the response body.
- `getContents()`: Reads the response content.
- `json_decode()`: Converts JSON into a PHP array.

The example assumes a successful JSON response. Production code should also handle errors and validate the response structure.

### 7.5 Registering the Service

File: `my_module.services.yml`

```yaml
services:
  my_module.api_service:
    class: Drupal\my_module\ApiService
    arguments: ['@http_client']
```

This injects Drupal's HTTP Client into the custom service.

### 7.6 Example: POST Request

```php
$response = $this->httpClient->request(
  'POST',
  'https://api.example.com/articles',
  [
    'json' => [
      'title' => 'Drupal Integration',
      'status' => TRUE,
    ],
  ]
);
```

The `json` option automatically JSON-encodes the request data and sets the appropriate content type.

### 7.7 Example: Sending Headers

```php
$response = $this->httpClient->request(
  'GET',
  'https://api.example.com/articles',
  [
    'headers' => [
      'Authorization' => 'Bearer ' . $accessToken,
      'Accept' => 'application/json',
    ],
  ]
);
```

The authorization header sends a bearer token to the external API.

In production, obtain credentials from secure configuration or a secrets management system. Do not hardcode tokens.

### 7.8 HTTP Client Request Options

| Option | Purpose |
|---|---|
| `headers` | Sends HTTP headers. |
| `query` | Adds URL query parameters. |
| `json` | Sends a JSON request body. |
| `form_params` | Sends form-encoded data. |
| `timeout` | Sets request timeout. |
| `connect_timeout` | Sets connection timeout. |
| `auth` | Configures supported HTTP authentication. |

### 7.9 HTTP Client vs cURL

| Drupal HTTP Client | cURL |
|---|---|
| Drupal service based on Guzzle. | PHP extension/library interface. |
| Integrates with Drupal's service container. | Requires direct cURL handling. |
| Provides convenient request options and exceptions. | Provides low-level request controls. |
| Preferred for standard Drupal integrations. | Useful for specialized low-level HTTP requirements. |

**Interview Question: Why do you use Drupal's HTTP Client instead of directly using cURL?**

Drupal's HTTP Client is integrated with the service container and provides a consistent way to make external HTTP requests. It supports headers, request bodies, authentication options, timeouts and exception handling.

---

## 8. Error Handling in API Integrations

### 8.1 Why is Error Handling Important?

External APIs may fail because of:
- Network problems.
- Invalid credentials.
- Server downtime.
- Rate limits.
- Invalid request data.
- Timeouts.
- Unexpected response formats.

Drupal integrations should handle these failures gracefully.

### 8.2 Guzzle Exceptions

Common exceptions include:

- `ConnectException`: Connection-related failure.
- `RequestException`: General request-related failure.
- `ClientException`: HTTP 4xx response.
- `ServerException`: HTTP 5xx response.
- `TransferException`: Base exception for transfer-related failures.

### 8.3 Example: Handling API Errors

```php
use GuzzleHttp\Exception\GuzzleException;

try {
  $response = $this->httpClient->request(
    'GET',
    'https://api.example.com/articles',
    [
      'timeout' => 10,
    ]
  );

  $data = json_decode(
    $response->getBody()->getContents(),
    TRUE
  );

}
catch (GuzzleException $exception) {
  $this->logger->error(
    'API request failed: @message',
    [
      '@message' => $exception->getMessage(),
    ]
  );

  return [];
}
```

This catches Guzzle-related request failures and logs the error.

The logger service should also be injected into the class.

### 8.4 Timeout Handling

A timeout prevents Drupal from waiting indefinitely for an external API.

Example:

```php
$response = $this->httpClient->request(
  'GET',
  'https://api.example.com/articles',
  [
    'timeout' => 10,
    'connect_timeout' => 5,
  ]
);
```

- `timeout`: Maximum total request duration.
- `connect_timeout`: Maximum time allowed to establish the connection.

### 8.5 Retry Mechanism

Some failures are temporary.

A retry mechanism can help when an API experiences:
- Temporary network failures.
- Rate limiting.
- Temporary server errors.

Best practices:
- Use limited retries.
- Apply exponential backoff.
- Respect `Retry-After` headers where appropriate.
- Avoid retrying non-idempotent operations blindly.
- Use idempotency keys when supported by the external API.

### 8.6 Logging API Errors

Drupal's logger service can record API failures.

Example:

```php
$this->logger->error(
  'External API returned an error: @status',
  [
    '@status' => $statusCode,
  ]
);
```

Avoid logging:
- Passwords.
- Access tokens.
- API secrets.
- Sensitive personal information.

**Interview Question: How do you handle external API failures in Drupal?**

I use appropriate timeouts, exception handling and structured logging. For temporary failures, I implement limited retries with exponential backoff and respect rate-limit responses. I also ensure that external API failures do not unnecessarily break the entire Drupal application.

---

## 9. External API Integration Architecture

### 9.1 Typical Integration Flow

1. Drupal receives a request or scheduled trigger.
2. Drupal calls an external API using HTTP Client.
3. The external API processes the request.
4. The external API returns a response.
5. Drupal validates and decodes the response.
6. Drupal processes the returned data.
7. Drupal stores or displays the required information.

### 9.2 Example: CRM Integration

Consider a Drupal website integrated with an external CRM.

When a visitor submits a contact form:

1. Drupal validates the submitted data.
2. Drupal sends the contact information to the CRM API.
3. The CRM creates or updates a contact.
4. The CRM returns a response.
5. Drupal logs the result.
6. Drupal displays an appropriate success or failure message.

### 9.3 Synchronous vs Asynchronous Integration

**Synchronous Integration**

Drupal sends a request and waits for the external API response before continuing.

Example:
- Checking a payment authorization.
- Retrieving live shipping information.

**Asynchronous Integration**

Drupal queues a task and processes it separately without requiring the user request to wait for the external operation to complete.

Example:
- Sending bulk contact information to a CRM.
- Synchronizing thousands of records.
- Processing large content imports.

| Synchronous | Asynchronous |
|---|---|
| Waits for the API response. | Processes work separately. |
| Immediate result. | Result may be available later. |
| Suitable for quick operations. | Suitable for long-running operations. |
| Can increase page response time. | Reduces user-facing request time. |

### 9.4 Drupal Queue API

Drupal Queue API allows developers to process tasks in smaller units.

A queue worker processes individual queue items.

Example use cases:
- Bulk API synchronization.
- Importing external content.
- Sending notifications.
- Processing large datasets.

### 9.5 Why Use Queue Workers for Integrations?

Queue Workers help:
- Avoid long-running web requests.
- Process tasks in manageable batches.
- Retry or recover from certain failures.
- Handle large datasets.
- Improve application responsiveness.

**Interview Question: When would you use synchronous versus asynchronous API integration?**

I use synchronous integration when an immediate response is required, such as validating a payment. For long-running tasks such as bulk CRM synchronization, I use asynchronous processing with Drupal Queue API to avoid blocking the user request.

---

## 10. API Security Best Practices

When integrating external APIs:

1. Use HTTPS.
2. Store credentials securely.
3. Validate incoming and outgoing data.
4. Avoid exposing sensitive information.
5. Use appropriate authentication.
6. Apply least-privilege permissions.
7. Configure timeouts.
8. Handle rate limits.
9. Validate API response structures.
10. Avoid logging tokens and secrets.
11. Protect against SSRF when URLs are user-controlled.
12. Use allowlisted external hosts where appropriate.

### 10.1 What is SSRF?

SSRF stands for Server-Side Request Forgery.

It occurs when an attacker manipulates an application into making unintended requests from the server to internal or external resources.

For example, an insecure Drupal integration may allow a user to submit an arbitrary URL that Drupal then requests.

Prevention:
- Avoid accepting arbitrary request URLs.
- Use allowlisted hosts.
- Validate schemes and destinations.
- Block internal and metadata service addresses where appropriate.
- Revalidate destinations after DNS resolution and redirects.

**Interview Question: What is SSRF and how do you prevent it?**

SSRF occurs when an attacker causes the server to make unintended requests to internal or external resources. I prevent it by validating and allowlisting destinations, restricting protocols and blocking access to sensitive internal network addresses.

---

## 11. Important Differences – Quick Revision

| Concept | Difference |
|---|---|
| REST vs SOAP | Architectural style vs messaging protocol |
| REST vs JSON | API architecture vs data format |
| GET vs POST | Retrieve vs create/process |
| PUT vs PATCH | Replace resource vs partial update |
| 401 vs 403 | Authentication failure vs authorization denial |
| REST vs JSON:API | Configurable REST resources vs standardized entity API |
| Authentication vs Authorization | Identity verification vs permission checking |
| Serialization vs Deserialization | Convert to transferable format vs reconstruct data |
| HTTP Client vs cURL | Drupal-integrated Guzzle service vs direct cURL interface |
| Synchronous vs Asynchronous | Wait for response vs process separately |
| Queue vs Direct Processing | Deferred task processing vs immediate execution |
| Timeout vs Retry | Limits waiting time vs attempts another request |

---

## 12. Practical Interview Scenarios

### Scenario 1: Drupal needs to retrieve data from an external CRM.

Use Drupal's HTTP Client service to make a GET request, authenticate with the CRM API, decode the response and process the required data.

### Scenario 2: Drupal needs to send contact form data to an external system.

Validate the form data and use the HTTP Client service to send a POST request to the external API.

### Scenario 3: An external API is taking too long to respond.

Configure connection and request timeouts. Handle exceptions and consider asynchronous processing if an immediate response is not necessary.

### Scenario 4: An external API returns HTTP 401.

Check whether the credentials or access token are missing, invalid or expired.

### Scenario 5: An external API returns HTTP 403.

Check whether the authenticated client has sufficient permissions to access the requested resource.

### Scenario 6: An external API returns HTTP 429.

Respect the rate limit, inspect the `Retry-After` header if provided, and retry with an appropriate backoff strategy.

### Scenario 7: Drupal needs to synchronize 50,000 records with an external CRM.

Use Drupal Queue API to process records in batches instead of performing all API calls during a single page request.

### Scenario 8: A mobile application needs access to Drupal content.

Use JSON:API or configure appropriate REST resources with authentication and permissions.

### Scenario 9: Drupal needs to expose article content to another application.

Enable and configure Drupal REST resources or use JSON:API to expose the required entity data.

### Scenario 10: An API integration works locally but fails in production.

Check:
- Environment-specific credentials.
- Network restrictions.
- Firewall rules.
- DNS resolution.
- SSL certificates.
- API endpoint configuration.
- Timeout settings.
- Production permissions.
- External API availability.

---

## 13. Final Interview Revision

Remember these important points:

1. API allows applications to communicate.
2. REST is an architectural style.
3. JSON is a data-interchange format.
4. GET retrieves data.
5. POST creates or processes data.
6. PUT replaces a resource representation.
7. PATCH partially updates a resource.
8. DELETE removes a resource.
9. HTTP status codes indicate request results.
10. Authentication verifies identity.
11. Authorization checks permissions.
12. Drupal REST exposes configurable resources.
13. JSON:API provides standardized entity endpoints.
14. Serialization converts data into a transferable format.
15. Drupal HTTP Client is based on Guzzle.
16. HTTP Client can be injected using dependency injection.
17. Guzzle exceptions should be handled properly.
18. Timeouts prevent indefinite waiting.
19. Retries should use appropriate backoff strategies.
20. Queue API supports asynchronous processing.
21. API credentials must be stored securely.
22. SSRF must be prevented when external destinations are user-controlled.

## 14. Day 8 Completion Checklist

- [ ] Understand APIs and REST architecture.
- [ ] Understand REST vs SOAP.
- [ ] Understand HTTP methods.
- [ ] Understand HTTP status codes.
- [ ] Understand PUT vs PATCH.
- [ ] Understand idempotency.
- [ ] Enable and configure Drupal REST.
- [ ] Understand serialization and deserialization.
- [ ] Understand JSON:API.
- [ ] Understand REST vs JSON:API.
- [ ] Understand authentication and authorization.
- [ ] Understand Basic Authentication, cookie authentication and OAuth 2.0.
- [ ] Make GET requests using Drupal HTTP Client.
- [ ] Make POST requests with JSON data.
- [ ] Send authentication headers.
- [ ] Handle Guzzle exceptions.
- [ ] Configure timeouts and retries.
- [ ] Understand synchronous and asynchronous integrations.
- [ ] Understand Drupal Queue API.
- [ ] Understand API security and SSRF prevention.
- [ ] Practice enterprise integration scenarios.

**End of Day 8 – REST APIs, HTTP Client, and Integrations.**
