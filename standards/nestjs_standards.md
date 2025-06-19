# NestJS Best Practices & Standards Guide

This comprehensive guide outlines the recommended standards and best practices for developing and working with NestJS applications. Following these guidelines will help you build maintainable, scalable, and efficient server-side applications.

## Project Structure

### Recommended Directory Layout

```text
src/
├── modules/              # Feature modules
│   ├── users/            # Example feature module
│   │   ├── dto/          # Data Transfer Objects
│   │   ├── entities/     # Database entities
│   │   ├── interfaces/   # Type definitions
│   │   ├── users.controller.ts
│   │   ├── users.module.ts
│   │   ├── users.service.ts
│   │   └── users.spec.ts
├── common/              # Shared utilities
│   ├── filters/         # Exception filters
│   ├── interceptors/    # Request/response interceptors
│   ├── guards/          # Auth guards
│   ├── pipes/           # Validation pipes
│   └── decorators/      # Custom decorators
├── config/              # Configuration files
├── main.ts              # Application entry point
└── app.module.ts        # Root module
```

### Module Organization Principles

- Each feature in its own module; cross-cutting concerns (auth, email, metrics) go in common or shared modules.
- Small apps: flat structure; larger apps: domain or hexagonal architecture per module.
- Use barrel files for clean imports—avoid hidden dependencies.
- Use forwardRef() judiciously to resolve circular references.

## Coding Standards

### General Guidelines

- Follow **TypeScript** conventions and leverage strong typing
- Prefer `async/await` over Promises directly
- Use **ESLint** and **Prettier** for consistent style enforcement
- Avoid magic strings/numbers — use enums or constants
- Name files consistently (`.controller.ts`, `.service.ts`, etc.)

### Naming Conventions

- `camelCase` for variables and functions
- `PascalCase` for classes and interfaces
- `snake_case` for database columns (if applicable)
- `UPPER_CASE` for environment variables

### Service & Controller Patterns

- Keep controllers **thin**; delegate business logic to services
- Services should be **stateless** and reusable
- Do not inject repositories directly into controllers

```ts
// users.controller.ts
@UseGuards(UserGuard)
@ApiTags("users")
@Controller("users")
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @CheckPolicies((ability) => ability.can(Action.Read, User))
  @Get()
  getAllUsers() {
    return this.usersService.findAll();
  }
}
```

### DTOs (Data Transfer Objects)

- Use DTOs for all incoming requests
- Apply validation decorators from `class-validator`

```ts
import {
  IsString,
  IsInt,
  Min,
  Max,
  IsOptional,
  IsEmail,
} from "class-validator";

export class CreateUserDto {
  @IsString()
  @IsNotEmpty()
  name: string;

  @IsEmail()
  @IsNotEmpty()
  email: string;

  @IsInt()
  @Min(18)
  @Max(100)
  age: number;

  @IsOptional()
  @IsString()
  @IsNotEmpty()
  address?: string;
}
```

### Guards

- Apply authentication guards globally or per-route to ensure secure access to all APIs by default.
- Explicitly mark public endpoints (e.g like login or registration) using the @Public() decorator to allow unauthenticated access.

```ts
// auth.controller.ts - Public endpoint
@Controller("auth")
export class AuthController {
  @Public()
  @Post("login")
  login(@Body() loginDto: LoginDto) {
    return this.authService.login(loginDto);
  }
}

// users.controller.ts - Protected endpoints
@Controller("users")
export class UsersController {
  @Get()
  @UseGuards(AuthGuard)
  getUsers() {
    return this.usersService.findAll();
  }
}
```

### Pipes

Pipes are used to transform and validate data.

- Use NestJS Pipes to validate, transform, and sanitize incoming data before it reaches controllers.

- Apply built-in pipes (e.g., ValidationPipe, ParseIntPipe, DefaultValuePipe) for common use cases.

- Define custom pipes for domain-specific transformations or validations.

```ts
// parse-int.pipe.ts
@Injectable()
export class ParseIntPipe implements PipeTransform<string, number> {
  transform(value: string): number {
    const val = parseInt(value, 10);
    if (isNaN(val)) {
      throw new BadRequestException('Validation failed');
    }
    return val;
  }
}

// Usage
@Get(':id')
findOne(@Param('id', ParseIntPipe) id: number) {
  return this.userService.findOne(id);
}
```

### Middleware

Middleware functions are executed before route handlers.

- Use NestJS middleware to perform logic before the route handler is invoked (e.g., logging, request mutation, auth token parsing).
- Implement middleware as classes that implement the NestMiddleware interface.

```ts
// Example
@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log(`Request...`);
    next();
  }
}
```

### API Versioning

- **Enable API versioning by default** - Use `app.enableVersioning({ type: VersioningType.URI })` in `main.ts`
- Version all controllers with `@Controller({ path: 'users', version: '1' })`
- Maintain backward compatibility when introducing new versions

## Performance Optimization

### Asynchronous Programming

- Use `async/await` with all I/O operations
- Avoid blocking the event loop — offload CPU-heavy tasks

### Caching

- Use NestJS's built-in caching module (CacheModule) to improve performance on frequently accessed data.
- Explicitly apply caching to specific GET endpoints using @UseInterceptors(CacheInterceptor) where performance benefits are clear.
- Use Redis for distributed or shared caching

```ts
@UseInterceptors(CacheInterceptor)
@Get('cached')
@CacheKey('cached_users')
@CacheTTL(60)
findAllCached() {
  return this.usersService.findAll();
}
```

### Database Performance

- Use indexes appropriately in schema
- Implement pagination for large data queries
- Avoid N+1 problems with joins or eager loading

```ts
// Example: Pagination
@Get()
findPaginated(@Query('page') page = 1) {
  return this.usersService.findPaginated(page);
}
```

## Error Handling & Logging

### Exception Handling

- Use `HttpException` and custom filters (`@Catch()`)
- Apply a **global exception filter** for consistency
- Avoid exposing stack traces in production

```ts
// Example
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const status = exception.getStatus();

    response.status(status).json({
      statusCode: status,
      message: exception.message,
    });
  }
}
```

### Logging

- Use Symph's @symphco/logger library for structured, centralized logging, including outgoing HTTP logging via Axios interceptors.

### Install with npm install @symphco/logger.

1. Import LoggerService and centralizedLoggerMiddleware in your app.module.ts file.

```ts
import { LoggerService, centralizedLoggerMiddleware } from "@symphco/logger";
```

2. Inside AppModule class, add the following:

```ts
export class AppModule {
  async configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(
        await LoggerService.generateMiddleware(
          process.env.NODE_ENV !== "development"
        ),
        centralizedLoggerMiddleware
      )
      .forRoutes("*");
  }
}
```

3. In any service file where Logger will be used, import LoggerService and create an instance of it. For example:

```ts
export class RequestService {
  private loggerService: LoggerService;
  constructor() {
    this.loggerService = new LoggerService("Request Service");
  }
}
```

4. You can now use the Logger service’s methods when applicable. For example:

#### For errors:

- this.loggerService.error('findAll', error.message);

#### For info:

- this.loggerService.info('getFindRequestsQuery', params, user);

## Testing Strategy

### Unit Testing

- Use **Jest** as the testing framework
- Mock dependencies with `jest.mock()` or custom classes
- Write unit tests for all services, pipes, and utilities

```ts
// Example
describe("UsersService", () => {
  let service: UsersService;

  beforeEach(() => {
    service = new UsersService();
  });

  it("should return all users", () => {
    expect(service.findAll()).toEqual([{ id: 1, name: "Alice" }]);
  });
});
```

## Security Practices

### Input Validation & Sanitization

- Use `class-validator` decorators for validation
- Enable global `ValidationPipe`
- Sanitize inputs to prevent injection attacks
- Ensure secrets and sensitive data (e.g., api credentials) are managed securely using environment variables

### Authentication & Authorization

```ts
@UseGuards(AuthGuard('jwt'))
@Get('profile')
getProfile(@Request() req) {
  return req.user;
}
```

- Use Passport strategies (e.g., JWT)
- Use @Public() decorator for public routes
- Apply @UseGuards(AuthGuard) globally or per controller as needed.

### Role-Based Access Control (RBAC)

- Define roles as an enum for type safety and consistency.
- Use a centralized RBAC rule map to associate roles with permissions.
- Create custom guards (e.g., RolesGuard) to restrict access based on roles.
- Use roles to inform PBAC systems (e.g., CASL ability definitions).

### Permission-Based Access Control (PBAC) with CASL

- Use CASL (@casl/ability) for fine-grained, context-aware access control.

- Define dynamic rules (e.g., ownership, status) in a centralized ability factory.

- You can use roles inside CASL to build abilities—combining RBAC and PBAC.

```ts
// Example
@CheckPolicies((ability) => ability.can(Action.Read, User))
@Get('users')
getAllUsers() {
  return this.usersService.findAll();
}
```

## Documentation

### API Documentation

- Use `@nestjs/swagger` to auto-generate Swagger UI
- Group routes using tags
- Provide examples in Swagger decorators

### Internal Documentation

- Comment complex logic
- Maintain `README.md` and contribution guides
- Document architecture decisions (optional: ADRs)

## Production Readiness

### Environment

- Use `@nestjs/config` for configuration management
- Validate required environment variables
- Use `.env` files for environment-specific settings
- Ensure .env files are not included in the commits

```ts
// config/configuration.ts
export default () => ({
  database: {
    host: process.env.DB_HOST,
  },
});
```

### Monitoring & Health Checks

- Use `@nestjs/terminus` for health checks
- Monitor metrics (CPU, memory, latency)
- Integrate with monitoring tools (e.g., Prometheus, Grafana)

### Code Reviews

- Ensure PRs pass lint, tests, and coverage thresholds
- Encourage peer reviews
- Use automated tools for validation

## Documentation

- Maintain a README file with project setup instructions and other essential information.

By adhering to these guidelines, developers can ensure that their Nest.js projects are well-organized, maintainable, and scalable. Adapt these recommendations based on specific project needs and preferences.
