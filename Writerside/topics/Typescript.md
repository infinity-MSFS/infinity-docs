
# Typescript

This document outlines the coding standards and best practices for TypeScript development in our projects.

## Naming Conventions

### Interfaces and Types
- Use **PascalCase** for interfaces, types, classes, and enums
- Prefix interfaces with `I`, types with `T` and enums with `E` for clarity. You may elect to add a _ after the prefix for better readability on some names.

```typescript
interface IUser {
    id: string;
    name: string;
    email: string;
}

type TUserId = string;

class UserService {
    // Implementation
}

enum E_UserRole {
    Admin = 'ADMIN',
    User = 'USER',
    Guest = 'GUEST',
}
```

### Functions and Variables
- Use **camelCase** for functions, methods, and variables
- Use **UPPER_SNAKE_CASE** for constants

```typescript
function calculateTotal(items: Item[]): number {
    let totalAmount = 0;
    // Implementation
    return totalAmount;
}

const MAX_RETRY_ATTEMPTS = 3;
const API_BASE_URL = 'https://api.example.com';
```

## Type Annotations

### Explicit Types When Needed
Add type annotations when TypeScript can't infer the type or for public APIs.

```typescript
// Good - clear return type for public API
function getUserById(id: string): Promise<User | null> {
    return fetchUser(id);
}

// Good - type can't be inferred
const numbers: number[] = [];

// Acceptable - type is obvious
const name = "John Doe";
const count = 42;

// Bad - avoid typed any
function process(data: any): void {
    // Implementation
}
```

### Use `interface` Over `type` for Objects
Prefer `interface` for object shapes as they're more extensible.

```typescript
// Preferred
interface IProduct {
    id: string;
    name: string;
    price: number;
}

// Use type for unions, intersections, or primitives
type TStatus = 'pending' | 'approved' | 'rejected';
type TCallback = (data: string) => void;
```

## Modern TypeScript Features

### Optional Chaining and Nullish Coalescing
Use optional chaining (`?.`) and nullish coalescing (`??`).

```typescript
// Optional chaining
const userName = user?.profile?.name;
const firstItem = items?.[0];
const result = callback?.();

// Nullish coalescing
const displayName = user.name ?? 'Anonymous';
const port = config.port ?? 3000;
```

### Const Assertions
Use `as const` for literal types and readonly arrays.

```typescript
const COLORS = ['red', 'green', 'blue'] as const;
type Color = typeof COLORS[number]; // 'red' | 'green' | 'blue'

const config = {
    apiUrl: 'https://api.example.com',
    timeout: 5000,
} as const;
```

### Utility Types
Leverage built-in utility types.

```typescript
interface IUser {
    id: string;
    name: string;
    email: string;
    password: string;
}

// Pick specific properties
type PublicUser = Pick<IUser, 'id' | 'name' | 'email'>;

// Omit properties
type UserWithoutPassword = Omit<IUser, 'password'>;

// Make all properties optional
type PartialUser = Partial<IUser>;

// Make all properties required
type RequiredUser = Required<IUser>;

// Make all properties readonly
type ReadonlyUser = Readonly<IUser>;
```

## Functions

### Arrow Functions vs Regular Functions
Use arrow functions for callbacks and short functions, regular functions for methods.

```typescript
// Arrow functions for callbacks
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);

// Regular functions for methods
class Calculator {
    add(a: number, b: number): number {
        return a + b;
    }
}

// Arrow functions for lexical 'this'
class Component {
    private value = 42;
    
    handleClick = () => {
        console.log(this.value); // 'this' is bound correctly
    }
}
```

### Exporting Functions
Prefer default exports to encourage single responsibility per module.

```typescript
// Good - default export
export default function calculateArea(radius: number): number {
    return Math.PI * radius * radius;
}

// Bad - named exports for single function
export function calculateCircumference(radius: number): number {
    return 2 * Math.PI * radius;
}
```

### Generic Functions
Use generics for reusable functions.

```typescript
function identity<T>(value: T): T {
    return value;
}

function firstElement<T>(arr: T[]): T | undefined {
    return arr[0];
}

// With constraints
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}
```

## Async/Await

### Prefer Async/Await
Use async/await instead of raw promises for better readability.

```typescript
// Good
async function fetchUserData(userId: string): Promise<User> {
    try {
        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Failed to fetch user:', error);
        throw error;
    }
}

// For parallel operations
async function fetchMultipleUsers(ids: string[]): Promise<User[]> {
    const promises = ids.map(id => fetchUserData(id));
    return Promise.all(promises);
}
```

## Error Handling

### Type-Safe Error Handling
Create custom error classes for different error types.

```typescript
class ApiError extends Error {
    constructor(
        public statusCode: number,
        message: string,
        public details?: unknown
    ) {
        super(message);
        this.name = 'ApiError';
    }
}

class ValidationError extends Error {
    constructor(
        public field: string,
        message: string
    ) {
        super(message);
        this.name = 'ValidationError';
    }
}

// Usage
function processRequest(data: unknown): Result {
    if (!isValid(data)) {
        throw new ValidationError('email', 'Invalid email format');
    }
    // Process...
}
```

## Code Organization

### Import Order
Organize imports in a consistent order:

```typescript
// 1. External libraries
import React, { useState, useEffect } from 'react';
import { z } from 'zod';

// 2. Internal modules
import { User } from '@/models/user';
import { ApiClient } from '@/services/api';

// 3. Relative imports
import { Button } from '../components/Button';
import { formatDate } from './utils';

// 4. Type-only imports
import type { Config } from './types';
```

### Use Barrel Exports
Create index files to simplify imports.

```typescript
// models/index.ts
export { User } from './user';
export { Product } from './product';
export { Order } from './order';

// Usage
import { User, Product, Order } from '@/models';
```

## Best Practices

### Enable Strict Mode
Always use strict mode in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

### Avoid `any`
Use `unknown` or proper types instead of `any`.

```typescript
// Bad
function process(data: any) {
    return data.value;
}

// Good
function process(data: unknown) {
    if (typeof data === 'object' && data !== null && 'value' in data) {
        return (data as { value: string }).value;
    }
    throw new Error('Invalid data');
}

// Better - use type guard
function isDataWithValue(data: unknown): data is { value: string } {
    return typeof data === 'object' && 
           data !== null && 
           'value' in data;
}

function process(data: unknown) {
    if (isDataWithValue(data)) {
        return data.value; // Type-safe
    }
    throw new Error('Invalid data');
}
```

### Use Readonly Where Appropriate
Make properties readonly when they shouldn't change.

```typescript
interface IUser {
    readonly id: string;
    name: string;
    readonly createdAt: Date;
}

const user: IUser = {
    id: '123',
    name: 'John',
    createdAt: new Date(),
};

// user.id = '456'; // Error: Cannot assign to 'id' because it is a read-only property
user.name = 'Jane'; // OK
```

## Documentation

Use JSDoc comments for public APIs:

```typescript
/**
 * Fetches user data from the API
 * 
 * @param userId - The unique identifier of the user
 * @param options - Optional fetch configuration
 * @returns A promise that resolves to the user data
 * @throws {ApiError} When the API request fails
 * 
 * @example
 * ```ts
 * const user = await fetchUser('123');
 * console.log(user.name);
 * 
*/
async function fetchUser(
userId: string,
options?: RequestOptions
): Promise<User> {
// Implementation
}
```

## Testing

Write type-safe tests:

```typescript
import { describe, it, expect } from 'vitest';

describe('Calculator', () => {
    it('should add two numbers correctly', () => {
        const calculator = new Calculator();
        const result = calculator.add(2, 3);
        expect(result).toBe(5);
    });

    it('should handle negative numbers', () => {
        const calculator = new Calculator();
        const result = calculator.add(-5, 3);
        expect(result).toBe(-2);
    });
});
```

## Formatting
Use Biome for consistent code formatting. Our Biome configuaration can be found [here.](https://raw.githubusercontent.com/infinity-MSFS/infinity-msfs.github.io/refs/heads/master/biome.json)