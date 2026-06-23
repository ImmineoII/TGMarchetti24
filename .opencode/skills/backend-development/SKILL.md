---
name: backend-development
description: Use when implementing backend services, REST APIs, GraphQL, database access, authentication, authorization, business logic, data validation, caching, messaging, and server-side integrations. Trigger keywords: backend, API, REST, GraphQL, database, ORM, authentication, authorization, JWT, middleware, service, repository, controller, business logic, caching, queue.
---

# Backend Development

## Goal
Implement robust, secure, and performant backend services following the defined architecture, API contracts, and user stories, with a focus on correctness, security, and maintainability.

## Guiding principles

### Layered architecture
```
presentation/    # Controllers, route handlers, DTOs
application/     # Use cases, application services, command/query handlers
domain/          # Entities, value objects, domain services, repository interfaces
infrastructure/  # Repository implementations, external services, database
```

### Code quality
- Single Responsibility for every class/function
- Dependency Injection for testability
- Repository pattern for database isolation
- Input validation at all entry points
- Explicit and centralized error handling
- No hardcoded secrets (use env vars)

## Implementation patterns

### Controller / Route Handler
```typescript
// presentation/controllers/user.controller.ts
export class UserController {
  constructor(private readonly userService: UserService) {}

  async createUser(req: Request, res: Response): Promise<void> {
    try {
      const dto = await validateDto(CreateUserDto, req.body);
      const user = await this.userService.create(dto);
      res.status(201).json(toUserResponse(user));
    } catch (err) {
      if (err instanceof ValidationError) {
        res.status(400).json({ error: err.message, details: err.details });
      } else {
        throw err; // delegate to global error handler
      }
    }
  }
}
```

### Use Case / Application Service
```typescript
// application/use-cases/create-user.use-case.ts
export class CreateUserUseCase {
  constructor(
    private readonly userRepo: UserRepository,
    private readonly hashService: HashService,
    private readonly eventBus: EventBus,
  ) {}

  async execute(dto: CreateUserDto): Promise<User> {
    // 1. Validate business rules
    const existing = await this.userRepo.findByEmail(dto.email);
    if (existing) throw new DomainError('Email already registered');

    // 2. Create domain entity
    const user = User.create({
      email: dto.email,
      passwordHash: await this.hashService.hash(dto.password),
    });

    // 3. Persist
    await this.userRepo.save(user);

    // 4. Publish domain events
    await this.eventBus.publish(new UserCreatedEvent(user));

    return user;
  }
}
```

### Repository Interface + Implementation
```typescript
// domain/repositories/user.repository.ts
export interface UserRepository {
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  save(user: User): Promise<void>;
  delete(id: string): Promise<void>;
}

// infrastructure/repositories/prisma-user.repository.ts
export class PrismaUserRepository implements UserRepository {
  constructor(private readonly prisma: PrismaClient) {}

  async findById(id: string): Promise<User | null> {
    const record = await this.prisma.user.findUnique({ where: { id } });
    return record ? toDomain(record) : null;
  }
}
```

## Security

### Authentication and Authorization
```typescript
// Validate JWT on every protected request
const authMiddleware = async (req, res, next) => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  if (!token) return res.status(401).json({ error: 'Unauthorized' });

  try {
    req.user = await verifyJwt(token, process.env.JWT_SECRET);
    next();
  } catch {
    res.status(401).json({ error: 'Invalid token' });
  }
};

// RBAC - permission check
const requireRole = (role: Role) => (req, res, next) => {
  if (!req.user.roles.includes(role)) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  next();
};
```

### Input Validation
```typescript
// ALWAYS validate all input
class CreateUserDto {
  @IsEmail()
  email: string;

  @MinLength(8)
  @Matches(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/)
  password: string;
}
```

### Standard protections
- Rate limiting on all public endpoints
- SQL injection prevention (parameterized ORM, never raw queries with user input)
- XSS prevention (sanitize output)
- CORS configured correctly
- Helmet for security headers
- Secrets from environment variables only

## Database & Performance

### Optimized queries
```typescript
// Load only necessary fields
const users = await prisma.user.findMany({
  select: { id: true, email: true, name: true },
  where: { isActive: true },
  orderBy: { createdAt: 'desc' },
  skip: offset,
  take: limit,
});

// Use transactions for atomic operations
await prisma.$transaction(async (tx) => {
  await tx.order.create({ data: orderData });
  await tx.inventory.update({ where: { id }, data: { quantity: { decrement: 1 } } });
});
```

### Caching strategy
```typescript
const getUser = async (id: string): Promise<User> => {
  const cached = await cache.get(`user:${id}`);
  if (cached) return JSON.parse(cached);

  const user = await userRepo.findById(id);
  if (!user) throw new NotFoundError('User not found');

  await cache.set(`user:${id}`, JSON.stringify(user), 3600); // TTL 1h
  return user;
};
```

## Backend Testing
```typescript
describe('CreateUserUseCase', () => {
  let useCase: CreateUserUseCase;
  let userRepo: jest.Mocked<UserRepository>;

  beforeEach(() => {
    userRepo = { findByEmail: jest.fn(), save: jest.fn(), findById: jest.fn() };
    useCase = new CreateUserUseCase(userRepo, new BcryptHashService(), new InMemoryEventBus());
  });

  it('should create user when email is unique', async () => {
    userRepo.findByEmail.mockResolvedValue(null);
    const result = await useCase.execute({ email: 'test@test.com', password: 'Password1!' });
    expect(result.email).toBe('test@test.com');
    expect(userRepo.save).toHaveBeenCalledWith(expect.objectContaining({ email: 'test@test.com' }));
  });

  it('should throw when email already exists', async () => {
    userRepo.findByEmail.mockResolvedValue(existingUser);
    await expect(useCase.execute({ email: 'existing@test.com', password: 'Password1!' }))
      .rejects.toThrow('Email already registered');
  });
});
```

## Checklist for each feature
- [ ] Controller with input validation
- [ ] Use case with business logic
- [ ] Repository with database implementation
- [ ] Centralized error handling
- [ ] Authentication/authorization applied
- [ ] Structured logging (no logs with sensitive data)
- [ ] Unit tests for use cases (>85% coverage)
- [ ] Integration tests for API endpoints
- [ ] Database migration (if required)
- [ ] Environment variables documented
- [ ] Performance: optimized queries, caching where necessary

## Handoff to Code Reviewer
- PR with list of implemented endpoints
- Documentation of required environment variables
- Migrations to run
- Local setup instructions
