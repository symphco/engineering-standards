
### **1.** **PURPOSE**

1.1. The purpose of this standard is to define application security requirements to help the organization develop and maintain secure applications.

1.2. To help the organization eliminate application security vulnerabilities.

1.3. To guide the development team to develop a secure code application

### **2.** **SCOPE**

2.1. This standard applies to all IT systems and applications developed either internally or for a client by Symph Inc.

###  **3.** **STANDARDS**

3.1. Architecture and Design

3.1.1. Secure coding checklist, security requirements, guidelines, or policies are available to all developers and testers. All user stories and features contain functional security constraints, such as "As a user, I should be able to view and edit my profile. I should not be able to view or edit anyone else's profile.

3.1.2. Communications between application components, including APIs, middleware, and data layers, are authenticated. Components should have the least necessary privileges needed.

3.2. Authentication

Authentication is the act of establishing or confirming that someone (or something) is authentic and that claims made by a person or about a device are correct, resistant to impersonation, and prevent recovery or interception of passwords.

3.2.1. User authentication should use Gmail authentication by default but other authentication can be used depending on the project needs.

3.2.2. Multi-factor authentication (MFA) must be implemented

3.2.3. Secure notifications are sent to users after updates to authentication details, such as credential resets, email or address changes, and logging in from unknown or risky locations. The use of push notifications - rather than SMS or email - is preferred, but in the absence of push notifications, SMS or email is acceptable as long as no sensitive information is disclosed in the notification.  

3.2.4. Password hints or knowledge-based authentication (so-called "secret questions") must not be present as this is no longer recognized as an acceptable authentication factor.

3.2.5. Password credential recovery must not reveal the old and current password in any way.

3.2.6. Shared or default accounts must not be present (e.g. "root", "admin", or "sa").

3.2.7. Using any of the authentication mechanisms (login, password reset, or password recovery), an application must respond with a generic error message regardless of whether:

- The user ID or password was incorrect.
- The account does not exist.
- The account is locked or disabled

3.2.8. Ensure that all login failures and lockouts are logged.

3.2.9. Assume that whatever password hashing method is selected will have to be upgraded in the future. Ensure that upgrading the hashing algorithm is as easy as possible.

3.3. Session Management

The application must satisfy the following high-level session management requirements:

- Sessions are unique to each individual and cannot be guessed or shared.
- Sessions are invalidated when no longer required and timed out during periods of inactivity.

3.3.1. The application must never reveal session tokens in URL parameters, error messages, or logs.

3.3.2. Logout and expiration invalidate the session token, such that the back button or a downstream relying party does not resume an authenticated session, including across relying parties.

3.3.3. The application ensures a full, valid login session or requires re-authentication or secondary verification before allowing any sensitive transactions or account modifications.

3.3.4. The session ID length must be at least 128 bits (16 bytes). However, this number should not be considered as an absolute minimum value, as other implementation factors might influence its strength.

3.3.5. The session ID must be unpredictable (random enough) to prevent guessing attacks, where an attacker can guess or predict the ID of a valid session through statistical analysis techniques.

3.3.6. Set the "secure" attribute for cookies transmitted over a TLS connection

3.3.7. Set cookies with the HttpOnly attribute, unless you specifically require client-side scripts within your application to read or set a cookie's value.

3.3.8. SameSite attribute defines a cookie attribute preventing browsers from sending a SameSite flagged cookie with cross-site requests.

3.3.9. Establish a session inactivity timeout that is as short as possible, based on balancing risk and business functional requirements.

3.4. Access Control

Authorization is the concept of allowing access to resources only to those permitted to use them. The application must satisfy the following high-level requirements:

- Persons accessing resources hold valid credentials to do so.
- Users are associated with a well-defined set of roles and privileges.
- Role and permission metadata is protected from replay or tampering.

3.4.1. The principle of least privilege exists. Users should only be able to access functions, data files, URLs, controllers, services, and other resources, for which they possess specific authorization. This implies protection against spoofing and elevation of privilege.

3.4.2. Sensitive data and APIs must be protected against Insecure Direct Object Reference (IDOR) attacks targeting the creation, reading, updating, and deletion of records, such as creating or updating someone else's record, viewing everyone's records, or deleting all records. You can check more on IDOR [here](https://owasp.org/www-chapter-ghana/assets/slides/IDOR.pdf).

3.4.3. Application or framework enforces a strong anti-CSRF mechanism to protect authenticated functionality, and effective anti-automation or anti-CSRF protects unauthenticated functionality.

3.4.4. Directory browsing must be disabled unless deliberately desired. Additionally, applications should not allow discovery or disclosure of file or directory metadata, such as Thumbs.db, .DS_Store, .git or.svn folders.

3.4.5. Design Access Control Thoroughly Up Front. Access Control is one of the main areas of application security design that must be thoroughly designed up front, especially when addressing requirements.

3.4.6. Force all requests to go through Access Control checks. Ensure that all requests go through some kind of access control verification layer.

3.4.7. Deny by Default. Deny by default is the principle that if a request is not specifically allowed, it is denied.

3.4.8. Don’t hardcode roles.

3.4.9. Enforce authorization controls on every request, including those made by server-side scripts, "includes" and requests from rich client-side technologies.

3.4.10. Restrict access to protected URLs to only authorized users.

3.4.11. Restrict access to protected functions to only authorized users.

3.5. Validation, Sanitization and Encoding

The application must satisfy the following high-level requirements:

- Input data is strongly typed, validated, range or length checked, or at worst, sanitized or filtered.
- Output data is encoded or escaped as per the context of the data as close to the interpreter as possible.

3.5.1. Input Validation

3.5.1.1. Minimum and maximum value ranges must be checked for numerical parameters and dates, and minimum and maximum lengths must be checked for strings.

3.5.1.2. Add validation against JSON Schema and XML Schema (XSD) for input in these formats.

3.5.1.3. Ensure that any input validation performed on the client is also performed on the server.

3.5.1.4. Allow list validation is the appropriate validation for all input fields provided by the user. Allow list

validation involves defining exactly what IS

authorized, and by definition, everything else is not

authorized. If it's well-structured data, like dates,

social security numbers, zip codes, email

addresses, etc. then the developer should be able

to define a very strong validation pattern, usually

based on regular expressions, for validating such

input. If the input field comes from a fixed set of

options, like a drop-down list or radio buttons, then

the input needs to match exactly one of the values

offered to the user in the first place.

3.5.1.5. All languages and most frameworks provide validation libraries or functions that must be leveraged to validate data. Validation libraries typically cover common data types, length requirements, integer ranges, “is null” checks and more.

3.5.1.6. Regular expressions offer a way to check whether data matches a specific pattern. When designing regular expressions, be aware of RegEx Denial of Service (ReDoS) attacks. These attacks cause a program using a poorly designed Regular Expression to operate very slowly and utilize CPU resources for a very long time.

3.5.1.7. Must validate all client-provided data before processing, including all parameters, URLs and HTTP header content (e.g. Cookie names, User-agent and values).

3.5.2. Sanitization and Sandboxing

3.5.2.1. All untrusted HTML input from WYSIWYG editors or similar must be properly sanitized with an HTML sanitizer library or framework feature.

3.5.2.2. The application must sanitize user input before passing to mail systems to protect against SMTP or IMAP injection.

3.5.2.3. OWASP recommends DOMPurify for HTML Sanitization.

3.5.2.4. Avoids the use of eval() or other dynamic code execution features. Where there is no alternative, any user input being included must be sanitized or sandboxed before being executed.

3.5.2.5. Application must protect against template injection attacks by ensuring that any user input being included is sanitized or sandboxed.

3.5.2.6. Application must protect against SSRF attacks, by validating or sanitizing untrusted data or HTTP file metadata, such as filenames and URL input fields, and uses allow lists of protocols, domains, paths and ports.

3.5.2.7. Application must sanitizes, disables, or sandboxes user- supplied Scalable Vector Graphics (SVG) scriptable content, especially as they relate to XSS resulting from inline scripts, and foreignObject.

3.5.2.8. Application must sanitizes, disables, or sandboxes user- supplied scriptable or expression template language content, such as Markdown, CSS or XSL stylesheets, BBCode, or similar.

3.5.3. Output Encoding and Injection Prevention

3.5.3.1. Output encoding must be relevant for the interpreter and context required. For example, use encoders specifically for HTML values, HTML attributes, JavaScript, URL parameters, HTTP headers, SMTP, and others as the context requires, especially from untrusted inputs.

3.5.3.2. Data selection or database queries must use prepared statements with parameterized queries or use properly constructed stored procedures.

3.5.3.3. Start with using your framework’s default output encoding protection when you wish to display data as the user typed it in. Automatic encoding and escaping functions are built into most frameworks.

3.5.3.4. Contextually sanitize all output of untrusted data to queries for SQL, XML, and LDAP.  

3.5.4. Deserialization Prevention

3.5.4.1. Serialized objects must use integrity checks or are encrypted to prevent hostile object creation or data tampering.

3.5.4.2. Avoid deserialization of untrusted data or is protected in both custom code and third-party libraries (such as JSON, XML and YAML parsers).

3.5.4.3. When parsing JSON in browsers or JavaScript-based backends, JSON.parse must be used to parse the JSON document. Do not use eval() to parse JSON.

3.6. Stored Cryptography

- Application must satisfies the following high level requirements:
- All cryptographic modules must fail in a secure manner and errors are handled correctly.
- Access to keys must be securely managed.

3.6.1. Regulated private data must be stored encrypted while at rest, such as Personally Identifiable Information (PII) and sensitive personal information.

3.6.2. Regulated financial data must be stored encrypted while at rest, such as financial accounts, defaults or credit history, tax records, pay history, beneficiaries, or de-anonymized market or research records.

3.6.3. Industry proven or government approved cryptographic algorithms, modes, and libraries must be used, instead of custom coded cryptography.

3.6.4. Known insecure block modes (i.e. ECB, etc.), padding modes (i.e. PKCS#1 v1.5, etc.), ciphers with small block sizes (i.e. Triple-DES, Blowfish, etc.), and weak hashing algorithms (i.e. MD5, SHA1, etc.) must not be used unless required for backwards compatibility.

3.6.5. Protect master secrets from unauthorized access.

3.6.6. All random numbers, random file names, random GUIDs, and random strings must be generated using the cryptographic module’s approved random number

generator when these random values are intended to be

unguessable.

3.7. Error Handling and Logging

The primary objective of error handling and logging is to provide useful information for the user, administrators, and incident response teams.

The objective is not to create massive amounts of logs, but high-quality logs, with more signal than discarded noise. High-quality logs will often contain sensitive data, and must be protected as per local data privacy laws or directives. This should include:

- Not collecting or logging sensitive information unless specifically required.
- Ensuring all logged information is handled securely and protected as per its data classification.
- Ensuring that logs are not stored forever, but have an absolute lifetime that is as short as possible.

3.7.1. Do not disclose sensitive information in error responses, including system details, session identifiers or account information.

3.7.2. Use error handlers that do not display debugging or stack trace information.

3.7.3. Implement generic error messages and use custom error pages.

3.7.4. Ensure log entries that include un-trusted data will not execute as code in the intended log viewing interface or software.

3.7.5. Restrict access to logs to only authorized individuals.

3.7.6. Do not store sensitive information in logs, including unnecessary system details, session identifiers or passwords.

3.7.7. Log all input validation failures.

3.7.8. Log all authentication attempts, especially failures.

<br/>

3.8. Data Protection

Application must satisfies the following high level data protection requirements:

- Confidentiality: Data must be protected from unauthorized observation or disclosure both in transit and when stored.
- Integrity: Data must be protected from being maliciously created, altered or deleted by unauthorized attackers.
- Availability: Data must be available to authorized users as required

3.8.1. Data must be protected from unauthorized observation or disclosure both in transit and when stored.

3.8.2. Implement least privilege, restrict users to only the functionality, data and system information that is required to perform their tasks.

3.8.3. Data must be protected from being maliciously created, altered or deleted by unauthorized attackers.

3.8.4. Data should be available to authorized users as required.

3.8.5. Application should minimize the number of parameters in a request, such as hidden fields, Ajax variables, cookies and header values.

3.8.6. Application must set sufficient anti-caching headers so that sensitive data is not cached in modern browsers.

3.8.7. Data stored in browser storage (such as localStorage, sessionStorage, IndexedDB, or cookies) must not contain sensitive data.

3.8.8. Authenticated data must be cleared from client storage, such as the browser DOM, after the client or session is terminated.

3.8.9. Remove unnecessary application and system documentation as this can reveal useful information to attackers.

3.8.10. Do not include sensitive information in HTTP GET request parameters.

3.8.11. Disable auto complete features on forms expected to contain sensitive information, including authentication.

3.8.12. Do not store passwords, connection strings or other sensitive information in clear text or in any non cryptographically secure manner on the client side.

3.8.13. Encrypt highly sensitive stored information, like authentication verification data, even on the server side.

3.8.14. Disable client side caching on pages containing sensitive information.

3.9. Communication

Application must meets the following high level requirements:

- Require TLS or strong encryption, independent of sensitivity of the content.
- Follow the latest guidance, including:

– Configuration advice

– Preferred algorithms and ciphers

- Avoid weak or soon to be deprecated algorithms and ciphers, except as a last resort
- Disable deprecated or known insecure algorithms and ciphers.

3.9.1. Implement encryption for the transmission of all sensitive information. This should include TLS for protecting the connection and may be supplemented by discrete encryption of sensitive files or non HTTP based connections.

3.9.2. TLS certificates must be valid and have the correct domain name, not be expired, and be installed with intermediate certificates when required.

3.9.3. Failed TLS connections must not fall back to an insecure connection.

3.9.4. Utilize TLS connections for all content requiring authenticated access and for all other sensitive information.

3.9.5. Utilize TLS for connections to external systems that involve sensitive information or functions.

3.9.6. Do Not Mix TLS and Non-TLS Content.

3.9.7. Use the "Secure" Cookie Flag.

3.9.8. Prevent Caching of Sensitive Data.

3.9.9. Use HTTP Strict Transport Security.

3.10. Malicious Code

3.10.1. Application source code and third party libraries must not contain unauthorized phone home or data collection capabilities.

3.10.2. Application must not ask for unnecessary or excessive permissions to privacy related features or sensors, such as contacts, cameras, microphones, or location.

3.10.3. Application source code and third party libraries must not contain back doors, such as hard-coded or additional undocumented accounts or keys, code obfuscation, undocumented binary blobs, rootkits, or anti-debugging, insecure debugging features, or otherwise out of date, insecure, or hidden functionality that could be used maliciously if discovered.

3.10.4. Application must employ integrity protections, such as code signing or subresource integrity. The application must not load or execute code from untrusted sources, such as loading includes, modules, plugins, code, or libraries from untrusted sources or the Internet.

3.10.5. Application must have protection from subdomain takeovers if the application relies upon DNS entries or DNS subdomains, such as expired domain names, out of date DNS pointers or CNAMEs, expired projects at public source code repos, or transient cloud APIs, serverless functions, or storage buckets (autogen-bucket- id.cloud.example.com) or similar.

3.10.6. Protections can include ensuring that DNS names used by applications are regularly checked for expiry or change.

3.11. Files and Resources

Application must satisfies the following high level requirements:

- Untrusted file data must be handled accordingly and in a secure manner.
- Untrusted file data obtained from untrusted sources must be stored outside the web root and with limited permissions.

3.11.1. Specify list of allowed extensions. Only allow safe and critical extensions for business functionality. Ensure that input validation is applied before validating the extensions.

3.11.2. Validate the file type, don't trust the Content-Type header as it can be spoofed.

3.11.3. Change the filename to something generated by the application.

3.11.4. Set a filename length limit. Restrict the allowed characters if possible.

3.11.5. Set a file size limit.

3.11.6. Only allow authorized users to upload and access uploaded files.

3.11.7. Store the files on a different server. If that's not possible, store them outside of the webroot. In the case of public access to the files, use a handler that gets mapped to filenames inside the application (someid -> file.ext).

3.11.8. Ensure that any libraries used are securely configured and kept up to date.

3.11.9. Other than defining the extension of the uploaded file, its MIME-type can be checked for a quick protection against simple file upload attacks.

3.11.10. Sanitize filename.

3.11.11. File Storage Location. The location where the files should be stored must be chosen based on security and business requirements. The following points are set by security priority, and are inclusive:

- Store the files on a different host, which allows for complete segregation of duties between the application serving the user, and the host handling file uploads and their storage.
- Store the files outside the webroot, where only administrative access is allowed.
- Store the files inside the webroot, and set them in write permissions only.
- If read access is required, setting proper controls is a must (e.g. internal IP, authorized user, etc.).

3.11.12. Set upload and download limits.

3.11.13. Do not pass user-supplied data directly to any dynamic include function.

3.11.14. Require authentication before allowing a file to be uploaded.

3.11.15. Turn off execution privileges on file upload directories.

3.11.16. Do not pass user-supplied data into a dynamic redirect. If this must be allowed, then the redirect should accept only validated, relative path URLs.

3.11.17. Ensure application files and resources are read-only.

3.12. API and Web Service

3.12.1. Should have authentication, session management and authorization of all web services, refer to sections 3.2 Authentication, 3.3 Session Management and 3.4 Access control in this document.

3.12.2. Must have input validation of all parameters that transit from a lower to higher trust level, Validation, Sanitization and Encoding in this document.

3.12.3. Effective security controls for all API types, including cloud and Serverless API.

3.12.4. API URLs must not expose sensitive information, such as the API key, session tokens etc.

3.12.5. Requests containing unexpected or missing content types must be rejected with appropriate headers (HTTP response status 406 Unacceptable or 415 Unsupported Media Type).

3.12.6. Only valid RESTful HTTP methods are allowed for the user or action, such as preventing normal users from using DELETE or PUT on protected API or resources.

3.12.7. JSON schema validation and verification must be in place before accepting input.

3.12.8. RESTful web services that utilize cookies must be protected from Cross-Site Request Forgery via the use of at least one or more of the following: double submit cookie pattern, CSRF nonces, or Origin request header checks.

3.12.9. REST services explicitly check the incoming Content-Type to be the expected one, such as application/xml or application/json.

3.13. Configuration

3.13.1. Server configuration must be hardened as per the recommendations of the application server and frameworks in use.

3.13.2. All unneeded features, documentation, sample applications and configurations must be removed.

3.13.3. Application assets, such as JavaScript libraries, CSS or web fonts, are hosted externally on a Content Delivery Network (CDN) or external provider must use Subresource Integrity (SRI) to validate the integrity of the asset.

3.13.4. Third party components come from pre-defined, trusted and continually maintained repositories.

3.13.5. Web or application server and application framework debug modes are disabled in production to eliminate debug features, developer consoles, and unintended security disclosures.

3.13.6. HTTP headers or any part of the HTTP response must not expose detailed version information of system components.

4\. **DEFINITION OF TERMS**

4.1. Access Control - is the process that limits and controls access to resources of a computer system.

4.2. OWASP - The Open Web Application Security Project (OWASP) is a worldwide free and open community focused on improving the security of application software.

4.3. Input Validation - is a programming technique that ensures only properly formatted data may enter a software system component.

4.4. Encoding - (commonly called “Output Encoding”) involves translating special characters into some different but equivalent form that is no longer dangerous in the target interpreter, for example translating the < character into the &lt; string when writing to an HTML page.

4.5. HTML sanitization - is the process of examining an HTML document and producing a new HTML document that preserves only whatever tags are designated "safe" and desired.

4.6. Authorization - is the concept of allowing access to resources only to those permitted to use them.

4.7. Malicious Code – Code introduced into an application during its development unbeknownst to the application owner, which circumvents the application's intended security policy. Not the same as malware such as a virus or worm.

4.8. Allow list – A list of permitted data or operations, for example a list of characters that are allowed to perform input validation.

4.9. Authentication – The verification of the claimed identity of an application user.

4.10. Cross-Site Scripting (XSS) – A security vulnerability typically found in web applications allowing the injection of client-side scripts into content.

4.11. Globally Unique Identifier (GUID) – a unique reference number used as an identifier in software.

4.12. Personally Identifiable Information (PII) - is information that can be used on its own or with other information to identify, contact, or locate a single person, or to identify an individual in context.

4.13. Transport Layer Security (TLS) – Cryptographic protocols that provide communication security over a network connection.

4.14. What You See Is What You Get (WYSIWYG) - A type of rich content editor which shows how the content will actually look when rendered rather than showing the coding used to govern the rendering.

4.15. Vulnerability - A vulnerability is a hole or a weakness in the application, which can be a design flaw or an implementation bug, that allows an attacker to cause harm to the stakeholders of an application.

4.16. Stakeholders - Stakeholders include the application owner, application users, and other entities that rely on the application.

4.17. Phone home - phoning home is a term often used to refer to the behavior of security systems that report network location, username, or other such data to another computer.

5\. **DOCUMENT HISTORY**

| **Editor** | **Details of Change** | **Effectivity Date** |
| --- | --- | --- |
|     |     |     |
| --- | --- | --- |
|     |     |     |
| --- | --- | --- |
```