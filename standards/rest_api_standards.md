# Rest API Standards

This document outlines our standardized REST API guidelines for building efficient and reliable services. By following these standards, we ensure seamless integration and maintainability across our ecosystem.

## Use nouns instead of verbs in endpoint paths
- Use nouns to represent entities in endpoint paths (e.g., /articles for articles).
- Avoid verbs in endpoint paths because HTTP methods (GET, POST, PUT, DELETE) already indicate actions.
- HTTP methods correspond directly to CRUD operations:
    - GET retrieves resources.
    - POST submits new data.
    - PUT updates existing data.
    - DELETE removes data.

**Example**
- <span style="color: green;">GET /articles/</span> retrieves news articles.
- <span style="color: green;">POST /articles/</span>  adds a new article.
- <span style="color: green;">PUT /articles/:id</span>  updates an article identified by :id.
- <span style="color: green;">DELETE /articles/:id</span>  deletes an article identified by :id.

## Use logical nesting on endpoints
- Group endpoints logically to reflect associations between objects, regardless of database structure.
- This practice enhances API organization and security by avoiding direct mirroring of database schema in endpoints.
    - For example, to retrieve comments for a news article, we use <span style="color: green;">/articles/:articleId/comments</span>
    - We add comments after the <span style="color: green;">/articles/:articleId</span> path segment to indicate that it's a child resource of /articles.
- Use the same nesting structure for POST, PUT, and DELETE endpoints.
- Nesting should be limited to avoid excessive complexity; consider returning resource URLs instead for deeply nested data.
    - For instance, instead of <span style="color: green;">/articles/:articleId/comments/:commentId/author</span>, return the author's URI within the JSON response:

## Handle errors and return standard error codes
- Handle errors gracefully to provide clear feedback to API users and maintain system reliability.
- Return appropriate HTTP status codes to precisely indicate the type of error encountered:
    - **400 Bad Request**: Client-side input fails validation.
    - **401 Unauthorized**: User lacks authentication credentials.
    - **403 Forbidden**: User is authenticated but not authorized to access the resource.
    - **404 Not Found**: Resource requested is not found.
    - **500 Internal Server Error**: Generic server error (avoid throwing explicitly).
    - **502 Bad Gateway**: Invalid response from an upstream server.
    - **503 Service Unavailable**: Server unable to handle the request temporarily (e.g., overload, partial failure).
- Accompany error codes with meaningful messages for maintainers to troubleshoot issues without revealing sensitive information to attackers.
- For example, reject invalid request payload data with a 400 response code.


This approach ensures that errors are handled effectively, maintaining system reliability and security while providing actionable feedback for API consumers.

## Allow filtering, sorting, and pagination
- Implement filtering to retrieve specific subsets of data based on specified criteria, optimizing performance and preventing overload.
- Enable pagination to limit the number of results returned per request, ensuring efficient resource utilization and responsive API performance.
- Filtering and pagination are essential as database size grows, enhancing scalability and maintaining system stability.

For example, add <span style="color: green;">page</span> and <span style="color: green;">limit</span> query parameters to enable pagination:
- Use <span style="color: green;">page</span> to specify the page number of results (<span style="color: green;">GET /articles?page=1</span>).
- Use <span style="color: green;">limit</span> to determine the number of results per page (<span style="color: green;">GET /articles?limit=10</span>).
- Example: to fetch the second page of articles with 10 articles per page:

    <span style="color: green;">GET /articles?page=2&limit=10</span>

This allows efficient management of large datasets by retrieving and displaying data in manageable chunks.

## Maintain good security practices
- Ensure secure communication between client and server by using SSL/TLS encryption:
    - SSL/TLS certificates are easy to implement and often low-cost or free.
    - Example: Secure REST API communication by configuring SSL/TLS on the server.
- Enforce access controls to prevent unauthorized access to sensitive information:
    - Implement role-based access control (RBAC) to restrict access based on user roles.
    - Example: Restrict access to user-specific data to only the authenticated user.
- Follow the principle of least privilege to minimize access rights:
    - Assign roles that grant necessary permissions without excess privileges.
    - Example: Define roles with specific permissions such as <span style="color: green;">user</span>, <span style="color: green;">admin</span>, and <span style="color: green;">superadmin</span>.
- Optionally, use granular permissions to allow more detailed access control:
    - Admins can manage specific features and permissions for each user as needed.
    - Example: Implement feature-based permissions to restrict access to sensitive actions.

## Versioning our APIs
- Implement versioning to manage changes that could potentially break client applications:
    - Use semantic versioning (e.g., <span style="color: green;">/v1/</span>, <span style="color: green;">/v2/</span>) to indicate different API versions.
    - Example: Maintain <span style="color: green;">/v1/</span> endpoints for backward compatibility while introducing new features in <span style="color: green;">/v2/</span>.
- Gradually phase out older API versions to allow clients time to migrate:
    - Ensure backward compatibility for existing clients using older versions (<span style="color: green;">/v1/</span>).
    - Example: Introduce new endpoints (<span style="color: green;">/v2/</span>) for clients ready to adopt new features.
- Ensure stability and reliability for third-party developers using the API:
    - Versioning helps prevent disruptions by maintaining consistent API behavior across versions.
    - Example: Ensure API stability to support third-party applications effectively.
    
This approach emphasizes the importance of versioning for managing API changes and supporting a stable environment for both current and future client applications.

