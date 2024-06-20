# API, Backend, and Database Naming Standards

This document outlines the naming conventions used in our API routes, entities, and database tables.

## API

- **API Routes** should be named in plural form and in kebab-case (lowercase with hyphens separating words). For example, if you have a route for users, it should be named 'users'. If the route is for user roles, it should be named 'user-roles'.

- **API Controllers** should be named in PascalCase with the word 'Controller' at the end. For example, if you have a controller for users, it should be named 'UsersController'. If the controller is for user roles, it should be named 'UserRolesController'.


- **API Functions** should be prefixed with verb and named in camelCase. For example, if you have a function that gets user data, it should be named 'getUserData'. If the function is for updating user roles, it should be named 'updateUserRoles'.


In code, this would look like:

```
@Controller('users')
class UsersController {
    @Get()
    async getUsers() {
        // code to get users
    }

    @Get(':id')
    async getUser(@Param('id') id: string) {
        // code to get a single user
    }
}
```

## Entities

Entities should be named in PascalCase and in singular form. For example, if you have an entity for a user, it should be named 'User'. If the entity is for a user role, it should be named 'UserRole'. 

In code, this would look like:

```
@Entity()
class User {
// ...
}
```

## Database

- **Database Tables** should be named in plural form and in snake_case (lowercase with underscores separating words). For example, a table for users would be 'users', and a table for user roles would be 'user_roles'.

- **Database Columns** should be named in snake_case, which is all lowercase with underscores between words. For example, if you have a column for user's email address, it should be named 'email_address'. 


In SQL, this would look like:

```
CREATE TABLE users (
id INT AUTO_INCREMENT,
name VARCHAR(100),
user_email VARCHAR(100),
PRIMARY KEY(id)
);
```

```
CREATE TABLE user_roles (
id INT AUTO_INCREMENT,
user_id INT,
role_id INT,
PRIMARY KEY(id)
);
```







