---
name: testing-qa
description: Use when writing tests, defining test strategies, performing QA, writing unit tests, integration tests, e2e tests, test plans, bug reports, and validating acceptance criteria. Trigger keywords: testing, QA, unit test, integration test, e2e, end-to-end, test plan, test case, bug report, acceptance criteria, test coverage, regression, mocking, fixtures.
---

# Testing & Quality Assurance

## Goal
Ensure software quality through a comprehensive test strategy that validates requirements, prevents regressions, and documents the expected behavior of the system.

## Test pyramid

```
         /\
        /E2E\          <- Few, slow, cover critical flows
       /------\
      /  Integ  \      <- Medium, test collaboration between components
     /------------\
    /  Unit Tests   \  <- Many, fast, cover isolated logic
   /----------------\
```

### Recommended distribution
- Unit tests: 70% of the suite
- Integration tests: 20% of the suite
- E2E tests: 10% of the suite

## Test Plan

```markdown
# Test Plan: [Feature/Sprint Name]

## 1. Scope
- Features covered
- Features excluded
- Dependencies

## 2. Strategy
- Types of tests to run
- Test environment
- Test data

## 3. Test Cases

### TC-XXX: [Test Case Name]
- **User Story**: US-XXX
- **Preconditions**: ...
- **Steps**:
  1. ...
  2. ...
- **Expected Result**: ...
- **Type**: Unit | Integration | E2E
- **Priority**: Critical | High | Medium | Low

## 4. Completion criteria
- Minimum coverage: 80% unit, 70% integration
- Zero critical tests failing
- All acceptance criteria verified

## 5. Bug Report Template
### BUG-XXX: [Title]
- **Severity**: Critical | High | Medium | Low
- **Environment**: Dev | Staging | Prod
- **Steps to reproduce**: ...
- **Expected behavior**: ...
- **Actual behavior**: ...
- **Logs/Screenshot**: ...
```

## Unit Testing

### AAA Pattern (Arrange-Act-Assert)
```typescript
describe('UserService', () => {
  describe('create', () => {
    it('should hash password before saving', async () => {
      // Arrange
      const mockRepo = { save: jest.fn(), findByEmail: jest.fn().mockResolvedValue(null) };
      const mockHash = { hash: jest.fn().mockResolvedValue('hashed_password') };
      const service = new UserService(mockRepo, mockHash);

      // Act
      await service.create({ email: 'user@test.com', password: 'plain_password' });

      // Assert
      expect(mockHash.hash).toHaveBeenCalledWith('plain_password');
      expect(mockRepo.save).toHaveBeenCalledWith(
        expect.objectContaining({ passwordHash: 'hashed_password' })
      );
    });

    it('should throw DomainError when email already exists', async () => {
      // Arrange
      const mockRepo = { findByEmail: jest.fn().mockResolvedValue({ id: '1' }) };
      const service = new UserService(mockRepo, mockHash);

      // Act & Assert
      await expect(service.create({ email: 'exists@test.com', password: 'pass' }))
        .rejects.toThrow(DomainError);
    });
  });
});
```

### Test for each acceptance criterion
```typescript
// For each acceptance criterion of the user story
describe('US-001: User Registration', () => {
  it('AC1: should create account with valid email and password', async () => { ... });
  it('AC2: should reject duplicate emails', async () => { ... });
  it('AC3: should send welcome email after registration', async () => { ... });
  it('AC4: should require password with minimum 8 chars and uppercase', async () => { ... });
});
```

## Integration Testing

### API Integration Tests
```typescript
describe('POST /api/users', () => {
  let app: Express;
  let db: TestDatabase;

  beforeAll(async () => {
    db = await TestDatabase.create();
    app = createApp({ db });
  });

  afterAll(() => db.destroy());
  afterEach(() => db.cleanup());

  it('should return 201 with user data on success', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ email: 'new@test.com', password: 'ValidPass1!' })
      .expect(201);

    expect(response.body).toMatchObject({
      id: expect.any(String),
      email: 'new@test.com',
    });
    expect(response.body.password).toBeUndefined(); // no password leak
  });

  it('should return 409 when email already exists', async () => {
    await db.seed({ users: [{ email: 'existing@test.com' }] });

    await request(app)
      .post('/api/users')
      .send({ email: 'existing@test.com', password: 'ValidPass1!' })
      .expect(409);
  });
});
```

## E2E Testing (Playwright/Cypress)

```typescript
// e2e/registration.spec.ts
test.describe('User Registration Flow', () => {
  test('should successfully register a new user', async ({ page }) => {
    await page.goto('/register');

    // Fill form
    await page.getByLabel('Email').fill('newuser@test.com');
    await page.getByLabel('Password').fill('ValidPass1!');
    await page.getByLabel('Confirm Password').fill('ValidPass1!');

    // Submit
    await page.getByRole('button', { name: 'Register' }).click();

    // Assert redirect to dashboard
    await expect(page).toHaveURL('/dashboard');
    await expect(page.getByText('Welcome, newuser@test.com')).toBeVisible();

    // Assert welcome email sent (check email mock service)
    const emails = await getTestEmails('newuser@test.com');
    expect(emails).toHaveLength(1);
    expect(emails[0].subject).toBe('Welcome to the platform!');
  });

  test('should show error for duplicate email', async ({ page }) => {
    await page.goto('/register');
    await page.getByLabel('Email').fill('existing@test.com');
    await page.getByLabel('Password').fill('ValidPass1!');
    await page.getByRole('button', { name: 'Register' }).click();

    await expect(page.getByText('Email already in use')).toBeVisible();
  });
});
```

## Regression testing
Before every release:
1. Run the full unit test suite
2. Run integration tests on the staging environment
3. Run E2E smoke tests on critical flows
4. Verify no regression in performance metrics

## Quality metrics
- Code coverage: >80% unit, >70% integration
- Zero failing tests in CI
- Unit test execution time: <30s
- Integration test execution time: <5min
- All critical bugs resolved before deploy

## QA checklist
- [ ] Test plan written for the feature
- [ ] Test cases mapped to acceptance criteria
- [ ] Unit tests written (coverage >80%)
- [ ] Integration tests for all endpoints
- [ ] E2E for critical flows
- [ ] Negative scenarios (error cases) covered
- [ ] Tests for boundary values
- [ ] Performance tested (response time, throughput)
- [ ] Security testing (injection, auth bypass)
- [ ] Accessibility testing (screen reader, keyboard)
- [ ] Cross-browser testing (Chrome, Firefox, Safari)
- [ ] Test report produced

## Handoff to Code Reviewer
- Test coverage report
- List of failing tests (if any) with justification
- Bug reports for issues found
- Test plan updated with results
