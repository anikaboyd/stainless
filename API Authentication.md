# API Authentication

Authentication is a fundamental aspect of API security. It verifies the identity of clients or users, ensuring that only legitimate parties can interact with your API.

In this guide, we'll explain what API authentication is, how it differs from authorization, review common authentication methods for HTTP APIs, and discuss best practices for choosing the right approach for your project.

## What is Authentication?

**Authentication** is the process of verifying the identity of a client or user. In the context of APIs, this means checking that each incoming request includes valid credentials (for example, an API key, token, or signed message) that prove the caller's identity.

Without any authentication, _anyone_ could make requests to your API. This opens the door to security risks like unauthorized data access, misuse of functionality, or abuse of your resources.

## Authentication vs. Authorization

It's common to see "authentication" and "authorization" mentioned together, but they solve different problems:

-   **Authentication**: Who are you?
    
-   **Authorization**: What are you allowed to do?
    

In summary, authentication verifies identity, while authorization determines what that identity can do. Typically, an API will authenticate first, then authorize actions based on the authenticated user's permissions.

## Common API Authentication Methods

There’s no one-size-fits-all solution to API authentication. Below are some of the most common strategies, along with their pros, cons, and examples:

### 1. HTTP Basic Authentication
![Image showing HTTP flow](/images/HTTP.png "HTTP Auth Flow")

**Overview**: The client sends a Base64-encoded string of `username:password` in the `Authorization` header of each request. This method is part of the HTTP standard and is widely supported by clients and servers.

**Pros:**

-   Simple to implement and widely supported (built into HTTP).
    
-   No need for session management or cookies on the server.
    

**Cons:**

-   Credentials are sent with every request, increasing the risk of exposure.
    
-   Insecure over plain HTTP (should **always** be used over HTTPS to encrypt credentials).
    
-   Not suitable for public or mobile clients (requires storing plaintext credentials on the client).
    

**Example:**

**Request:**

```http
GET /v1/resource HTTP/1.1
Host: api.example.com
Authorization: Basic BASE64_ENCODED_CREDENTIALS
```

**Response:**

```json
{ "data": "Sample response data" }
```

### 2. API Key Authentication
![Image showing API Key flow](/images/API%20Key.png "API Key Auth Flow")

**Overview**: The client includes a secret key provided by the API (an **API key**) with each request, typically as an HTTP header or query parameter. This approach is very common for both internal and third-party APIs because it's straightforward and doesn't require complex handshakes.

**Pros:**

-   Extremely easy to implement (just check for the key on each request).
    
-   Good enough for simple or low-risk use cases (e.g., internal services or prototypes).
    

**Cons:**

-   Lacks granularity – usually all requests made with a key have the same access level.
    
-   No built-in expiration or rotation mechanism (a key remains valid until you explicitly revoke or replace it).
    
-   Easy to leak if included in URLs or client-side code (could be logged or exposed; always keep keys secret).
    

**Example:**

**Request:**

```http
GET /v1/orders HTTP/1.1
Host: api.example.com
Authorization: Bearer sk_test_4eC39HqLyjWDarjtT1zdp7dc
```

**Response:**

```json
{ "data": "Sample response data" }
```

### 3. OAuth 2.0
![Image showing OAuth 2.0 flow](/images/OAuth.png "OAuth 2.0 Auth Flow")

**Overview**: OAuth 2.0 is an industry-standard protocol for token-based authentication and authorization. It allows users to grant third-party applications access to their data without sharing passwords. The client undergoes an OAuth "flow" (such as an authorization code grant) to obtain an access token (and sometimes a refresh token), then includes that access token in API requests (usually in the `Authorization` header).

**Pros:**

-   Provides scoped access (the token can be limited to specific permissions) and supports token expiration/renewal.
    
-   Enhances security by avoiding direct use of user credentials and allowing revocation of access without changing the user's password.
    
-   Widely adopted standard with many libraries and services available to simplify implementation.
    

**Cons:**

-   More complex to implement (requires managing an OAuth authorization server or integrating with an OAuth provider).
    
-   Requires handling of access tokens and refresh tokens (storing tokens, refreshing them when they expire, etc.).
    
-   Involves extra steps (user consent and token exchange), which can be overkill for simple use cases.
    

**Example:**

**Request:**

```http
GET /v1/resource HTTP/1.1
Host: api.example.com
Authorization: Bearer ACCESS_TOKEN
```

**Response:**

```json
{ "data": "Sample response data" }
```

### 4. JWT (JSON Web Token)
![Image showing JWT flow](/images/JWT.png "JWT Auth Flow")

**Overview**: JWT is a popular standard for stateless authentication using signed tokens. After a user logs in or authenticates, the server issues a JWT (a JSON object encoded and signed as a compact string). The client then sends this JWT on each request (often in an `Authorization` header). The server verifies the token's signature to authenticate the request, without needing to store session state.

**Pros:**

-   Enables **stateless** authentication (no server-side session or database lookup needed per request).
    
-   Tokens can include custom claims (user ID, roles, etc.) and have built-in expiration, improving security and flexibility.
    
-   Standardized format and widely supported by many libraries, making it easier to implement correctly.
    

**Cons:**

-   No easy built-in revocation – a token remains valid until it expires (you can't force it to expire early without additional infrastructure).
    
-   If a JWT is stolen, an attacker can use it until it expires, so it must be protected like any password or key.
    
-   Implementation must be done carefully (correct signing, verification, and short token lifespans) to avoid vulnerabilities.
    

**Example:**

**Request:**

```http
GET /v1/account HTTP/1.1
Host: api.example.com
Authorization: Bearer JWT_TOKEN
```

**Response:**

```json
{ "data": "Sample response data" }
```

## Choosing the Right Authentication Method

When designing your API's authentication scheme, consider the following factors:

-   **Security Requirements**: How sensitive is the data or actions your API is exposing? High-risk data or operations demand more secure methods.
    
-   **User Experience (Client Effort)**: More secure methods often require more effort (for example, an OAuth web flow). Strive for a balance between security and convenience for the client.
    
-   **Scalability**: Will the method work as your user base grows or as you integrate with other systems? (E.g., managing many API keys vs. a single token system).
    
-   **Implementation Complexity**: Evaluate the development and maintenance effort. Simple schemes (Basic, API keys) are quick to set up, whereas OAuth 2.0 or a custom JWT system requires more engineering investment.
    

**Guidance:**  
For most public or production APIs that deal with sensitive data or allow third-party integrations, a robust solution like OAuth 2.0 is often the best choice due to its security features and flexibility. If you're building a first-party API (for example, for your own mobile or single-page app) and want to avoid the overhead of OAuth, using JWTs for authentication can provide a secure, stateless alternative. On the other hand, for internal services or MVPs/prototypes where ease of implementation is key and the risk is lower, API key authentication or even Basic Auth (over HTTPS) might be sufficient in the short term.

In general, try to use the most secure method that fits your needs without unduly burdening your users or development team. It's common to start simple and then upgrade to a more advanced scheme as your API usage and security requirements grow.

## Conclusion

Choosing and implementing the appropriate authentication strategy is crucial for securing your API and maintaining user trust. Make sure to regularly review and update your authentication mechanisms to align with evolving security best practices. This includes enforcing HTTPS for all requests, rotating or revoking credentials when needed, and staying up-to-date with industry standards and libraries for authentication.

## What's Next?

Once you've chosen an authentication method, head to the [Authentication in SDK config](https://app.stainless.com/docs/guides/configure#authentication) to learn how to implement it with Stainless.
