# Testing Guidelines

## Unit Testing

- Write unit tests using Jest and React Native Testing Library
- Test component behavior, not implementation details
- Use descriptive test names that explain the expected behavior
- Mock external dependencies appropriately

## Integration Testing

- Implement integration tests for critical user flows using Detox
- Test complete features end-to-end
- Use realistic test data
- Test error scenarios and edge cases

## Test Structure

```typescript
describe('ComponentName', () => {
  beforeEach(() => {
    // Setup code
  });

  it('should render correctly with default props', () => {
    // Test implementation
  });

  it('should handle user interaction properly', () => {
    // Test implementation
  });
});
```

## React Native Testing

- Use React Native Testing Library for component testing
- Test user interactions (press, input, etc.)
- Use screen queries (getByText, getByRole, etc.)
- Test accessibility features

## Expo Testing

- Use Expo's testing tools for running tests in different environments
- Test on both iOS and Android platforms
- Consider snapshot testing for components to ensure UI consistency
- Use Expo's development tools for debugging tests

## Test Data

- Use factories or fixtures for test data
- Keep test data separate from test logic
- Use realistic but non-sensitive data
- Consider different data scenarios (empty, full, edge cases)

## Mocking

- Mock external services and APIs
- Mock navigation and routing
- Mock device-specific features
- Use consistent mocking patterns

## Test Coverage

- Aim for high test coverage on critical paths
- Don't just chase coverage numbers - focus on meaningful tests
- Test error handling and edge cases
- Regular review of test effectiveness