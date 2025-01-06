# Comprehensive Guide to ESLint Airbnb Style Guide

## I. Installation and Basic Configuration

### 1. Install Dependencies
```bash
# Install ESLint and required plugins
npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-react eslint-plugin-react-hooks eslint-plugin-jsx-a11y eslint-config-airbnb eslint-config-airbnb-typescript eslint-plugin-import

# Install Prettier and plugins
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier
```

### 2. ESLint Configuration (.eslintrc.js)
```javascript
module.exports = {
  root: true,
  env: {
    browser: true,
    es2021: true,
    node: true,
  },
  extends: [
    'airbnb',
    'airbnb-typescript',
    'airbnb/hooks',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:prettier/recommended',
  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    project: './tsconfig.json',
    ecmaVersion: 'latest',
    sourceType: 'module',
    ecmaFeatures: {
      jsx: true,
    },
  },
  rules: {
    // Detailed rules will be listed in later sections
  }
};
```

## II. Project Structure and Naming Conventions

### 1. Directory Structure
```
src/
├── assets/               # Static files like images, fonts
│   ├── images/
│   └── fonts/
├── components/           # Shared/Reusable components
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   ├── Button.styles.ts
│   │   └── index.ts
│   └── UserProfile/
│       └── UserProfile.tsx
├── features/            # Feature-based modules
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── services/
│   └── dashboard/
├── hooks/               # Custom hooks
│   ├── useAuth.ts
│   └── useForm.ts
├── layouts/            # Layout components
│   ├── MainLayout/
│   └── AuthLayout/
├── pages/              # Page components
│   ├── Home/
│   └── About/
├── services/           # API services
│   ├── api.ts
│   └── auth.service.ts
├── store/             # State management
│   ├── slices/
│   └── store.ts
├── styles/            # Global styles
│   └── global.css
├── types/             # TypeScript types
│   └── index.ts
├── utils/             # Utility functions
│   └── helpers.ts
└── App.tsx
```

### 2. File Naming Conventions

```typescript
// 1. Components (PascalCase)
Button.tsx
UserProfile.tsx
NavigationBar.tsx

// 2. Hooks (camelCase, prefixed with use)
useAuth.ts
useForm.ts
useLocalStorage.ts

// 3. Utils/Helpers (camelCase)
formatDate.ts
validationHelpers.ts
apiUtils.ts

// 4. Constants (SCREAMING_SNAKE_CASE)
API_ENDPOINTS.ts
ROUTES.ts
ACTION_TYPES.ts

// 5. Types/Interfaces (PascalCase)
UserTypes.ts
ApiResponse.ts
```

## III. Coding Style Details

### 1. Component Structure
```typescript
// Button.tsx
import React from 'react';
import type { ButtonProps } from './Button.types';

// Interface definition
interface Props extends ButtonProps {
  variant: 'primary' | 'secondary';
  size?: 'small' | 'medium' | 'large';
  isDisabled?: boolean;
}

// Component implementation
export const Button: React.FC<Props> = ({
  variant,
  size = 'medium',
  isDisabled = false,
  children,
  ...rest
}) => {
  // Event handlers
  const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    if (!isDisabled) {
      rest.onClick?.(event);
    }
  };

  // Render helpers
  const getButtonClasses = () => {
    return [
      'button',
      `button--${variant}`,
      `button--${size}`,
      isDisabled && 'button--disabled'
    ].filter(Boolean).join(' ');
  };

  // Component render
  return (
    <button
      className={getButtonClasses()}
      disabled={isDisabled}
      onClick={handleClick}
      type="button"
      {...rest}
    >
      {children}
    </button>
  );
};
```

### 2. Hooks Usage
```typescript
// useAuth.ts
import { useState, useEffect } from 'react';
import type { User } from '../types';

export const useAuth = () => {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    const checkAuth = async () => {
      try {
        setIsLoading(true);
        // Auth logic here
      } catch (error) {
        // Error handling
      } finally {
        setIsLoading(false);
      }
    };

    checkAuth();
  }, []);

  return { user, isLoading };
};
```

### 3. Type Definitions
```typescript
// types/user.types.ts
export interface User {
  id: string;
  firstName: string;
  lastName: string;
  email: string;
  role: UserRole;
  createdAt: Date;
}

export type UserRole = 'admin' | 'user' | 'guest';

export interface UserState {
  data: User | null;
  isLoading: boolean;
  error: Error | null;
}
```

## IV. ESLint Rules Configuration

### 1. React Rules
```javascript
{
  // Component naming and definition
  'react/function-component-definition': ['error', {
    namedComponents: 'arrow-function',
    unnamedComponents: 'arrow-function',
  }],
  'react/jsx-pascal-case': ['error', {
    allowAllCaps: true,
    ignore: [],
  }],

  // Props validation
  'react/prop-types': 'off', // Disabled for TypeScript
  'react/require-default-props': 'off',
  'react/jsx-props-no-spreading': ['error', {
    html: 'enforce',
    custom: 'enforce',
    explicitSpread: 'ignore',
    exceptions: ['Component'],
  }],

  // JSX formatting
  'react/jsx-max-props-per-line': ['error', {
    maximum: 1,
    when: 'multiline',
  }],
  'react/jsx-first-prop-new-line': ['error', 'multiline'],
  'react/jsx-closing-bracket-location': ['error', 'line-aligned'],

  // Hooks rules
  'react-hooks/rules-of-hooks': 'error',
  'react-hooks/exhaustive-deps': 'warn',
}
```

### 2. TypeScript Rules
```javascript
{
  '@typescript-eslint/explicit-function-return-type': ['error', {
    allowExpressions: true,
    allowTypedFunctionExpressions: true,
  }],
  '@typescript-eslint/no-explicit-any': 'warn',
  '@typescript-eslint/no-unused-vars': ['error', {
    vars: 'all',
    args: 'after-used',
    ignoreRestSiblings: true,
  }],
  '@typescript-eslint/naming-convention': [
    'error',
    {
      selector: 'interface',
      format: ['PascalCase'],
      prefix: ['I'],
    },
    {
      selector: 'typeAlias',
      format: ['PascalCase'],
    },
  ],
}
```

### 3. Import Rules
```javascript
{
  'import/order': ['error', {
    groups: [
      'builtin',
      'external',
      'internal',
      'parent',
      'sibling',
      'index',
    ],
    'newlines-between': 'always',
    alphabetize: {
      order: 'asc',
      caseInsensitive: true,
    },
  }],
  'import/no-cycle': 'error',
  'import/no-unresolved': 'error',
  'import/prefer-default-export': 'off',
  'import/extensions': ['error', 'never', {
    css: 'always',
    json: 'always',
  }],
}
```

## V. Best Practices

### 1. Component Best Practices
```typescript
// 1. Custom Hooks for Complex Logic
const useTableData = (initialData) => {
  // Logic for handling table data
};

// 2. Composition over Props Drilling
const Table = ({ children }) => {
  return <table>{children}</table>;
};

Table.Header = ({ children }) => {
  return <thead>{children}</thead>;
};

// 3. Render Props Pattern
const List = ({ renderItem, items }) => {
  return items.map((item) => renderItem(item));
};
```

### 2. Performance Optimization
```typescript
// 1. Memoization
const MemoizedComponent = React.memo(Component);

// 2. Callback Memoization
const handleClick = useCallback(() => {
  // Handle click
}, [dependency]);

// 3. Expensive Calculations
const expensiveValue = useMemo(() => {
  return expensiveCalculation(dependency);
}, [dependency]);
```

### 3. Error Handling
```typescript
// 1. Error Boundaries
class ErrorBoundary extends React.Component {
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // Log error
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback />;
    }
    return this.props.children;
  }
}

// 2. Async Error Handling
const fetchData = async () => {
  try {
    const response = await api.get('/data');
    return response.data;
  } catch (error) {
    if (error instanceof ApiError) {
      // Handle API errors
    }
    throw error;
  }
};
```

## VI. Testing Conventions

### 1. Test File Structure
```typescript
// Button.test.tsx
import { render, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  const defaultProps = {
    variant: 'primary',
    onClick: jest.fn(),
  };

  it('renders correctly', () => {
    const { getByRole } = render(<Button {...defaultProps}>Click me</Button>);
    expect(getByRole('button')).toBeInTheDocument();
  });

  it('handles click events', () => {
    const { getByRole } = render(<Button {...defaultProps}>Click me</Button>);
    fireEvent.click(getByRole('button'));
    expect(defaultProps.onClick).toHaveBeenCalled();
  });
});
```

### 2. Test Naming Conventions
```typescript
// Component_action_expectedResult
test('Button_Click_CallsOnClickHandler');
test('Form_Submit_ValidatesInputs');

// given_when_then
test('GivenFormData_WhenSubmitting_ThenValidatesInputs');
```

