# NestJS Standards Guide

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
- Use `forwardRef()` judiciously to resolve circular references.

## Coding Standards

### General Guidelines

```ts
// Example: Async/Await
async function fetchData() {
  const result = await this.httpService
    .get("https://api.example.com")
    .toPromise();
  return result.data;
}
```

- Follow **TypeScript** conventions and leverage strong typing
- Prefer `async/await` over Promises directly
- Use **ESLint** and **Prettier** for consistent style enforcement
- Avoid magic strings/numbers — use enums or constants

```ts
// Example: Constants
export const DEFAULT_ROLE = "user";
```

- Name files consistently (`.controller.ts`, `.service.ts`, etc.)

### Naming Conventions

- `camelCase` for variables and functions
- `PascalCase` for classes and interfaces
- `snake_case` for database columns (if applicable)
- `UPPER_CASE` for environment variables

### Service & Controller Patterns

```ts
// users.controller.ts
@Controller("users")
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Get()
  getAllUsers() {
    return this.usersService.findAll();
  }
}

// users.service.ts
@Injectable()
export class UsersService {
  findAll() {
    return [{ id: 1, name: "Alice" }];
  }
}
```

- Keep controllers **thin**; delegate business logic to services
- Services should be **stateless** and reusable
- Do not inject repositories directly into controllers

### DTOs (Data Transfer Objects) & Validation

```ts
// create-user.dto.ts
import { IsEmail, IsNotEmpty } from "class-validator";

export class CreateUserDto {
  @IsEmail()
  email: string;

  @IsNotEmpty()
  password: string;
}

// main.ts
app.useGlobalPipes(new ValidationPipe());
```

## Guards

Guards determine whether a request should be handled by the route handler.

```ts
// roles.guard.ts
@Injectable()
export class RolesGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest();
    const user = request.user;
    return user?.roles?.includes('admin');
  }
}

// Usage
@UseGuards(RolesGuard)
@Get('admin')
getAdminData() {
  return 'Admin content';
}
```

## Pipes

Pipes are used to transform and validate data.

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

## Interceptors

Interceptors can bind extra logic before or after method execution.

```ts
@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    console.log("Before...");
    const now = Date.now();
    return next
      .handle()
      .pipe(tap(() => console.log(`After... ${Date.now() - now}ms`)));
  }
}
```

## Middleware

Middleware functions are executed before route handlers.

```ts
// logger.middleware.ts
@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log(`Request...`);
    next();
  }
}
```

## Performance Optimization

### Asynchronous Programming

```ts
async function processLargeTask() {
  await this.queueService.add("heavyJob", payload);
}
```

- Use `async/await` with all I/O operations
- Avoid blocking the event loop — offload CPU-heavy tasks

### Caching

```ts
@UseInterceptors(CacheInterceptor)
@Get('cached')
@CacheKey('cached_users')
@CacheTTL(60)
findAllCached() {
  return this.usersService.findAll();
}
```

- Use `CacheModule`: in-memory for dev; Redis for distributed

### Database Performance

```ts
// Example: Pagination
@Get()
findPaginated(@Query('page') page = 1) {
  return this.usersService.findPaginated(page);
}
```

- Use indexes appropriately in schema
- Implement pagination for large data queries
- Avoid N+1 problems with joins or eager loading

## Error Handling & Logging

### Exception Handling

```ts
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

```ts
const logger = new Logger("AppService");
logger.log("Application started");
```

- Use Nest’s built-in `Logger` or external loggers (Winston, Pino)
- Use structured logs (e.g., JSON)
- Log lifecycle events, warnings, and errors

## Testing Strategy

### Unit Testing

```ts
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

- Use **Jest** as the testing framework
- Mock dependencies with `jest.mock()` or custom classes
- Write unit tests for all services, pipes, and utilities

## Security Practices

### Input Validation & Sanitization

```ts
@UsePipes(new ValidationPipe({ transform: true }))
@Post()
create(@Body() dto: CreateUserDto) {
  return this.userService.create(dto);
}
```

### Environment

```ts
// config/configuration.ts
export default () => ({
  database: {
    host: process.env.DB_HOST,
  },
});
```

- Handle configs using `@nestjs/config`
- Validate environment variables

### Authentication & Authorization

```ts
@UseGuards(AuthGuard('jwt'))
@Get('profile')
getProfile(@Request() req) {
  return req.user;
}
```

- Use Passport strategies (e.g., JWT)
- Use `@Public()` decorator for public routes

## API Documentation

```ts
@ApiTags('users')
@ApiResponse({ status: 200, description: 'User list' })
@Get()
findAll() {
  return this.userService.findAll();
}
```

- Use `@nestjs/swagger` to auto-generate Swagger UI
- Provide examples in Swagger decorators

````

- Comment complex logic
- Maintain `README.md` and contribution guides

## Production Readiness

### Monitoring & Health Checks

```ts
@HealthCheck()
@Get('health')
check() {
  return this.health.check([
    () => this.db.pingCheck('database'),
  ]);
}
````

- Use `@nestjs/terminus` for health checks
- Integrate with Prometheus/Grafana for monitoring

## Code Reviews

- Ensure PRs pass lint, tests, and coverage thresholds
- Encourage peer reviews
- Use automated tools for validation

---

By adhering to these guidelines and applying the examples above, developers can ensure that NestJS projects are clean, maintainable, and production-ready in Symph.
