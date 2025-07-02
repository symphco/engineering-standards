# NestJS Best Practices & Standards Guide

This comprehensive guide outlines the recommended standards and best practices for developing and working with NestJS applications. Following these guidelines will help you build maintainable, scalable, and efficient server-side applications.

## Project Structure

### Recommended Directory Layout

```text
src/
├── app/                  # Feature app
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
- Small apps: flat structure; larger apps: feature-based modules with standard NestJS layered architecture (Controller → Service → Repository with DTOs, entities, and interfaces).
- Use barrel files for clean imports and avoid hidden dependencies.
- Use forwardRef() carefully and only when needed to resolve circular references.

`Barrel` files are index.ts files that re-export multiple modules from a directory, allowing you to import everything from a single location. They simplify imports and create cleaner, more maintainable code.

Here’s an example of using barrel files in NestJS:

```ts
// users/index.ts (barrel file)
export { UsersController } from "./users.controller";
export { UsersService } from "./users.service";
export { CreateUserDto } from "./dto/create-user.dto";
export { User } from "./entities/user.entity";

// Instead of multiple imports:
import { UsersController } from "./users/users.controller";
import { UsersService } from "./users/users.service";
import { CreateUserDto } from "./users/dto/create-user.dto";

// You can import from the barrel:
import { UsersController, UsersService, CreateUserDto } from "./users";
```

## Coding Standards

### General Guidelines

- Follow **TypeScript** conventions and leverage strong typing
- Avoid using `any` type and use specific types, interfaces, or generics instead
- Prefer `async/await` over Promises directly
- Use **ESLint** and **Prettier** for consistent style enforcement
- Avoid magic strings/numbers — use enums or constants
- Name files consistently (`.controller.ts`, `.service.ts`, etc.)

### Naming Conventions

- `camelCase` for variables and functions
- `PascalCase` for classes and interfaces
- `snake_case` for database columns (if applicable)
- `UPPER_CASE` for environment variables and global constants
- `Plural` names for modules (e.g., `UsersModule`, `AppointmentsModule`) to maintain consistency with controllers and services

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
- Use the `@ApiProperty()` decorator so each field appears in the generated Swagger API docs, making it easy for others to see what data is needed or returned.

```ts
import {
  IsString,
  IsInt,
  Min,
  Max,
  IsOptional,
  IsEmail,
  IsNotEmpty,
} from "class-validator";
import { ApiProperty } from "@nestjs/swagger";

export class CreateUserDto {
  @ApiProperty()
  @IsString()
  @IsNotEmpty()
  name: string;

  @ApiProperty()
  @IsEmail()
  @IsNotEmpty()
  email: string;

  @ApiProperty()
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

### Dependency Injection

Use Dependency Injection to keep your code modular, testable, and maintainable. NestJS supports DI natively using TypeScript decorators.

Here are recommended ways and several approaches to Dependency Injection

- #### Constructor-based Injection (Most Common)

Inject services using class constructors for strong typing and clarity.

```ts
@Injectable()
export class UserService {
  constructor(private readonly userRepository: UserRepository) {}
}
```

- #### Module-based Providers

On this example below, you must declare services in the providers array of your module. Export services when you need to use them in other modules.

```ts
@Module({
  providers: [UserService],
  exports: [UserService],
})
export class UsersModule {}
```

- #### Custom Provider Tokens

Use custom provider tokens to inject abstract interfaces or alternate implementations instead of directly injecting concrete services. This improves flexibility, testability, and adheres to the Dependency Inversion Principle.

Heres an example of injecting a service via token to allow swapping implementations (e.g., real vs mock service).

```ts
// appointment.interface.ts
export interface IAppointmentService {
  findAll(): Promise<Appointment[]>;
  create(data: CreateAppointmentDto): Promise<Appointment>;
}
```

```ts
// constants.ts
export const ServiceName = {
  APPOINTMENT: "APPOINTMENT_SERVICE",
};
```

```ts
// appointments.module.ts
@Module({
  providers: [
    {
      provide: ServiceName.APPOINTMENT,
      useClass: AppointmentService,
    },
  ],
  exports: [ServiceName.APPOINTMENT],
})
export class AppointmentsModule {}
```

```ts
// appointment.controller.ts
@Controller("appointments")
export class AppointmentController {
  constructor(
    @Inject(ServiceName.APPOINTMENT)
    private readonly appointmentService: IAppointmentService
  ) {}

  @Get()
  findAll() {
    return this.appointmentService.findAll();
  }
}
```

In this example, the controller depends on an interface (IAppointmentService) instead of the concrete AppointmentService class. The @Inject() decorator uses a custom token (APPOINTMENT_SERVICE) to resolve the dependency. This enables easy swapping of implementations (e.g., using a mock service for testing) without modifying controller logic. It’s a common pattern in NestJS for achieving inversion of control and following clean architecture principles.

Also supports: `useValue`
, `useFactory`, `useExisting`

- #### useFactory for Dynamic Injection

Use useFactory when you need dynamic or async logic to create a provider.

```ts
{
  provide: 'ASYNC_TOKEN',
  useFactory: async () => await someInitFunction(),
}
```

- #### Global Modules
  Create global modules for shared utilities like logging or configuration.

```ts
@Global()
@Module({
  providers: [LoggerService],
  exports: [LoggerService],
})
export class SharedModule {}
```

### Circular Dependencies

Circular dependencies happens when two modules depend on each other. This causes undefined values at runtime.
Example `AppointmentsModule` imports `PatientsModule`. `PatientsModule` also imports `AppointmentsModule`.

While `forwardRef()` technically resolves circular references, it usually indicates poor modular design. So better to refactor your code and extract shared logic into a separate SharedModule that both modules can import.

```ts
// shared/shared.module.ts
@Global()
@Module({
  providers: [CommonService],
  exports: [CommonService],
})
export class SharedModule {}
```

1. In `AppointmentsModule`

```ts
// appointments.module.ts
@Module({
  imports: [SharedModule],
})
export class AppointmentsModule {}
```

2. In `PatientsModule`

```ts
// patients.module.ts
@Module({
  imports: [SharedModule],
})
export class PatientsModule {}
```

In this setup, both AppointmentsModule and PatientsModule import SharedModule instead of directly depending on each other. Any shared services (like CommonService) are defined once in SharedModule and reused across modules. This eliminates the circular dependency, promotes better modular design, and avoids the need for forwardRef().

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

```ts
// Approach 1: Simple version numbers (Recommended)
// Results in: /v1/users, /v2/users
app.enableVersioning({
  type: VersioningType.URI,
  prefix: "v",
});

@Controller({ path: "users", version: "1" })
export class UsersV1Controller {
  @Get()
  getUsers() {
    return this.usersService.findAll();
  }
}

// Approach 2: Semantic versioning
// Results in: /v1.0/users, /v1.1/users
app.enableVersioning({
  type: VersioningType.URI,
  prefix: "v",
});

@Controller({ path: "users", version: "1.0" })
export class UsersV1Controller {}

@Controller({ path: "users", version: "1.1" })
export class UsersV1_1Controller {}

// Approach 3: API prefix with versioning
// Results in: /api/v1/users, /api/v2/users
app.setGlobalPrefix("api");
app.enableVersioning({
  type: VersioningType.URI,
  prefix: "v",
});
```

**Example Endpoint URLs:**

- Simple: `GET /v1/users`, `POST /v2/users`
- Semantic: `GET /v1.0/users`, `GET /v1.1/users`
- With API prefix: `GET /api/v1/users`, `POST /api/v1/users`

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

At Symph, we document all projects to help teams work together better. Good documentation saves time when new developers join, makes fixing bugs easier, and keeps important decisions recorded for the future.

### API Documentation

#### Setting Up Swagger/OpenAPI Documentation

- Use @nestjs/swagger to automatically generate interactive API documentation and OpenAPI specifications.
- Apply Swagger decorators to controllers and DTOs to provide detailed API information.
- Configure Swagger UI to be accessible at a dedicated endpoint for easy API exploration.

```ts
// main.ts
import { NestFactory } from "@nestjs/core";
import { SwaggerModule, DocumentBuilder } from "@nestjs/swagger";
import { writeFileSync } from "fs";
const isProduction = process.env.NODE_ENV === "production";

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  if (!isProduction) {
    const openAPIOptions = new DocumentBuilder()
      .setTitle("API Documentation")
      .setDescription("Complete API reference")
      .setVersion("1.0")
      .addBearerAuth()
      .build();

    const openAPIDocument = SwaggerModule.createDocument(app, openAPIOptions);
    SwaggerModule.setup("api-docs", app, openAPIDocument);

    writeFileSync(
      "./openapi-spec.json",
      JSON.stringify(openAPIDocument, null, 2)
    );
  }

  await app.listen(3000);
}
bootstrap();
```

In this example, Swagger is enabled in the main entry point of the project (typically main.ts) for generating the OpenAPI specification file. It's important to note that Swagger UI and spec generation should only be enabled in non-production environments, as shown above using the `!isProduction` condition.

#### Controller and DTO Documentation Examples

- Document your API endpoints with clear descriptions, examples, and response types.
- Use Swagger decorators to provide comprehensive information about request/response structures.
- Group related endpoints using tags for better organization.

```ts
@ApiTags("users")
@Controller({ path: "users", version: "1" })
export class UsersController {
  @Get()
  @ApiOperation({ summary: "Get all users" })
  @ApiResponse({ status: 200, description: "Users retrieved successfully" })
  async getAllUsers(): Promise<User[]> {
    return this.usersService.findAll();
  }

  @Post()
  @ApiOperation({ summary: "Create a new user" })
  @ApiBody({ type: CreateUserDto })
  @ApiResponse({ status: 201, description: "User created successfully" })
  async createUser(@Body() createUserDto: CreateUserDto): Promise<User> {
    return this.usersService.create(createUserDto);
  }
}
```

In this example, UsersController handles API requests related to users. It is versioned with 'v1' and tagged as 'users' in the Swagger documentation, making it easier to organize and view in the API docs.

```ts
import { ApiProperty } from "@nestjs/swagger";

export class CreateUserDto {
  @ApiProperty()
  @IsEmail()
  email: string;

  @ApiProperty()
  @IsString()
  name: string;
}
```

The `@ApiProperty()` decorators are used to include both fields in the Swagger documentation, so they appear clearly when viewing the API docs.

**When to Generate OpenAPI Spec**

- `After Backend Changes` - Generate new clients when controllers, DTOs, or endpoints are modified
- `During Development` - Generate locally when adding new features to test integration immediately
- `In CI/CD Pipeline` - Automatically generate and update client libraries when API changes are deployed
- `Before Frontend Development` - Ensure frontend teams have the latest API interfaces before implementing new features
- `Cross-Team Integration` - Generate clients for different teams working on mobile, web, or external integrations

This approach ensures consistent API documentation and type-safe client generation across all our projects, regardless of domain or complexity.

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

By adhering to these guidelines, developers can ensure that their Nest.js projects are well-organized, maintainable, and scalable. Adapt these recommendations based on specific project needs and preferences.
