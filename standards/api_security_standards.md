### **INTRODUCTION**

This standard is developed to guide secure API implementation across our digital infrastructure, aligning with the OWASP Top 10 security risks for APIs. It outlines mandatory practices that help protect against common vulnerabilities and ensure consistent application of security controls.

### **SCOPE**

This standard applies to all IT systems and applications developed either internally or for a client by Symph Inc.

### **STANDARDS**

**1. Broken Object Level Authorization**

APIs tend to expose endpoints that handle object identifiers, creating a wide attack surface Level Access Control issue. Object level authorization checks should be considered in every function that accesses a data source using an input from the user.

1.1 Rigorous Authorization Checks: Ensure every API endpoint that receives an object ID implements robust object level authorization checks. These checks must confirm the user's rights to access or manipulate the specified object.

1.2 User Policies and Hierarchy Enforcement: Utilize user policies and hierarchical roles to govern access rights, ensuring that authorization logic is consistently applied across all API interactions.

1.3 Secure Object Identifiers: Use random and unpredictable values, such as GUIDs, for object identifiers to prevent attackers from guessing and manipulating object IDs.

1.4 Testing and Verification: Regularly test the authorization mechanisms to identify and rectify any flaws. Ensure that changes that could introduce vulnerabilities are not deployed without thorough testing.

**2. Broken Authentication**

Authentication mechanisms are often implemented incorrectly, allowing attackers to compromise authentication tokens or to exploit implementation flaws to assume other user's identities temporarily or permanently. Compromising system's ability to identify the client/user, compromises API security overall.

2.1 Robust Authentication Flows: Ensure all authentication flows are thoroughly designed and tested, including mobile, web, and any deep links that implement one-click authentication.

2.2 Endpoint Security: Treat all authentication endpoints, including credential recovery and password reset, with high-security measures such as rate limiting, captcha, and account lockout mechanisms.

2.3 Strong Authentication Practices: Utilize industry-standard methods for authentication, token generation, and password storage. Avoid reinventing these mechanisms.

2.4 Multi-Factor Authentication (MFA): Implement multi-factor authentication wherever possible to enhance security.

2.5 Anti-Brute Force Measures: Deploy strict anti brute force measures to protect against credential stuffing and dictionary attacks. These should be more stringent than regular rate limiting on other API endpoints.

**3. Excessive Data Exposure**

Looking forward to generic implementations, developers tend to expose all object properties without considering their individual sensitivity, relying on clients to perform the data filtering before displaying it to the user.

3.1 Client-Side Filtering Prohibition: Ensure that no sensitive data is ever only filtered on the client side. All data filtering and reduction to necessary elements must occur server-side before transmission.

3.2 Response Review and Restriction: Regularly review API responses to ensure they contain only data that is appropriate and necessary for the client. Avoid returning excessive or sensitive information that exceeds the requirements of the API consumer.

3.3 Property-specific Responses: Refrain from using generic serialization methods like toJSON() for objects that may contain sensitive data. Instead, explicitly define and return only the necessary properties in API responses.

3.4 Data Classification and Schema Validation: Classify all data handled by the application, particularly identifying any personally identifiable information (PII). Implement schema-based validation for all API responses to ensure compliance with data exposure policies.

**4. Lack of Resource and Rate Limiting**

Quite often, APIs do not impose any restrictions on the size or number of resources that can be requested by the client/user. Not only can this impact the API server performance, leading to Denial of Service (DoS), but also leaves the door open to authentication flaws such as brute force.

4.1 Resource Limits Implementation: Define and enforce strict limits on resource usage per API endpoint, including execution timeouts, maximum allocable memory, file descriptors, and process counts.

4.2 Rate Limiting: Implement effective rate limiting to control the number of requests a client can make within a specified timeframe, reducing the risk of denial-of-service (DoS) attacks.

4.3 Client Notification: Inform clients when they exceed rate limits, providing details on the limit and reset timing.

4.4 Request Validation: Validate all incoming request parameters, particularly those affecting resource-intensive operations or data retrieval volumes, to prevent abuse.

4.5 Payload Restrictions: Set maximum sizes for data payloads and parameters, such as string lengths and array elements, to prevent oversized requests from impacting API performance.

**5. Broken Function Level Authorization**

Complex access control policies with different hierarchies, groups, and roles, and an unclear separation between administrative and regular functions, tend to lead to authorization flaws. By exploiting these issues, attackers gain access to other users’ resources and/or administrative functions.

5.1 Strict Access Controls: Ensure all API endpoints implement strict function level authorization checks to prevent unauthorized access based on user roles and privileges.

5.2 Deny by Default: Adopt a "deny all" approach where access is blocked by default and permissions are explicitly granted based on the user’s role within the organization.

5.3 Role-Based Validation: Validate that API endpoints properly enforce restrictions based on the user's group and role, especially for sensitive actions like create, modify, or delete operations.

5.4 Endpoint Security Review: Conduct regular reviews of all API endpoints to identify and rectify any function level authorization flaws, taking into account the application's business logic and user hierarchy.

5.5 Role Hierarchy and Endpoint Mapping: Clearly define and maintain a mapping of which roles have access to specific functions and ensure that all administrative functions are securely gated behind proper authorization checks.

**6. Mass Assignment Prevention**

Binding client provided data (e.g., JSON) to data models, without proper properties filtering based on a whitelist, usually lead to Mass Assignment. Either guessing objects properties, exploring other API endpoints, reading the documentation, or providing additional object properties in request payloads, allows attackers to modify object properties they are not supposed to.

6.1 Property Whitelisting: Implement whitelisting to strictly control which properties clients are allowed to modify via API requests. This prevents clients from updating sensitive or unintended properties.

6.2 Avoid Automatic Binding: Minimize the use of frameworks and functions that automatically bind client inputs to internal object properties, to mitigate unauthorized updates.

6.3 Sensitive Property Blacklisting: Utilize built-in features or custom logic to blacklist sensitive or non-editable properties, ensuring they are not exposed or modifiable via API endpoints.

6.4 Schema Enforcement: Define and enforce strict schemas for input data payloads to ensure only allowed and intended data is processed by the API.

**7. Security Misconfiguration**

Security misconfiguration is commonly a result of unsecure default configurations, incomplete or ad-hoc configurations, open cloud storage, misconfigured HTTP headers, unnecessary HTTP methods, permissive Cross-Origin resource sharing (CORS), and verbose error messages containing sensitive information.

7.1 System Hardening and Configuration Reviews: Regularly review and harden configurations across the entire API stack, including network services, application layers, and cloud configurations, to eliminate unnecessary services and close security gaps.

7.2 Patch Management: Ensure that all components of the API stack are up-to-date with the latest security patches to protect against vulnerabilities.

7.3 Disable Unused Features: Turn off unnecessary features and HTTP verbs that are not required by the API to minimize the attack surface.

7.4 Secure Communication Protocols: Mandate the use of secure communication protocols such as TLS for all API interactions, including static asset transfers, to protect data in transit.

7.5 Security Headers and CORS Policy: Implement essential security headers and a properly configured Cross-Origin Resource Sharing (CORS) policy to enhance security in client-side interactions.

7.6 Error Handling: Configure error handling to prevent sensitive information disclosure in error messages or stack traces. Ensure that API responses do not reveal system details that could aid an attacker.

**8. Injection**

Injection flaws, such as SQL, NoSQL, Command Injection, etc., occur when untrusted data is sent to an interpreter as part of a command or query. The attacker's malicious data can trick the interpreter into executing unintended commands or accessing data without proper authorization.

8.1 Data Handling and Validation: Strictly validate, filter, and sanitize all client-supplied and external system data to prevent malicious data from reaching the interpreter.

8.2 Use of Parameterized APIs: Employ parameterized queries, prepared statements, or safe APIs to handle data inputs, particularly for SQL, NoSQL, LDAP, and OS commands to mitigate injection risks.

8.3 Special Character Escaping: Implement escaping routines specifically tailored to the syntax of the target interpreter to ensure special characters do not trigger unintended actions.

8.4 Code Review and Testing: Conduct thorough code reviews and testing to identify and rectify injection flaws. Utilize static and dynamic analysis tools to detect potentially vulnerable code.

8.5 Limiting Data Exposure: Restrict the amount of data returned by API calls to minimize the potential impact of a successful injection attack.

**9. Improper Assets Management**

APIs tend to expose more endpoints than traditional web applications, making proper and updated documentation highly important. Proper hosts and deployed API versions inventory also play an important role to mitigate issues such as deprecated API versions and exposed debug endpoints.

9.1 Comprehensive API Inventory: Maintain a detailed inventory of all API hosts, documenting each API's environment (e.g., production, staging, test, development), access controls, and version details.

9.2 Up-to-date Documentation: Ensure that all API documentation is current and accurately reflects each API's capabilities, configurations, and security measures. Include this documentation in the CI/CD pipeline for automatic updates.

9.3 Version Management and Retirement: Establish a clear retirement plan for outdated API versions and a policy for updating or patching existing APIs. Regularly review and decommission unused or unsupported API versions to prevent security vulnerabilities.

9.4 Integrated Services Management: Document and regularly review all integrated and third-party services to ensure they do not introduce vulnerabilities or misconfigurations.

9.5 Security Enhancements for All API Versions: Apply consistent security measures such as firewalls and rate-limiting across all API versions, not just the most current, to protect against unauthorized access and data breaches.

9.6 Data Handling Policies: Strictly control the use of production data in non-production environments and ensure that all API deployments, regardless of the environment, are secured as if they are in production.

**10. Insufficient Logging & Monitoring**

Insufficient logging and monitoring, coupled with missing or ineffective integration with incident response, allows attackers to further attack systems, maintain persistence, pivot to more systems to tamper with, extract, or destroy data. Most breach studies demonstrate the time to detect a breach is over 200 days, typically detected by external parties rather than internal processes or monitoring.

10.1 Comprehensive Logging: Ensure all API interactions, especially failed authentication attempts, access denials, and input validation errors are logged with sufficient detail to trace malicious activities.

10.2 Log Integrity and Security: Maintain the integrity and security of logs both at rest and in transit. Implement safeguards against log tampering and ensure logs are stored securely.

10.3 Continuous Monitoring: Deploy monitoring systems that provide real-time insights into API and infrastructure performance, with a focus on identifying and reacting to potential security threats.

10.4 Integration with SIEM Systems: Use Security Information and Event Management (SIEM) systems to aggregate and analyze logs from all API components. Configure SIEM tools to generate alerts based on anomalous activities to facilitate quick responses.

10.5 Alerting Mechanisms: Develop and configure custom dashboards and alerts that enable the IT security team to detect, investigate, and respond to incidents more rapidly.

This section is designed to enhance the detection and response capabilities of API systems, reducing the time attackers have to exploit vulnerabilities unnoticed.
