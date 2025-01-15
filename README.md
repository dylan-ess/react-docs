# Installation and Basic Configuration

## Table of Contents
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [Install Project](#install-project)
  - [Development](#development)
  - [Build](#build)
  - [Preview](#preview)
  - [Linting](#linting)
- [Scripts Overview](#scripts-overview)
- [Project Structure](#project-structure)
- [Flow Coding](#flow-coding)
  - [Router](#router)
  - [Redux](#redux)
  - [Validate](#validate)
- [Commit Flow](#commit-flow)
- [Coding Style Detail](#coding-style-detail)
  - [Component Structure](#component-structure)
  - [Hooks Usage](#hooks-usage)
  - [Type Definitions](#type-definitions)
- [Best Practice](#best-practice)
- [Eslint Config](#eslint-config)
- [Tailwind CSS Integration](#tailwind-css-integration)
- [Contributing](#contributing)
- [License](#license)

## Prerequisites
- Node.js (version 22 or higher)
- npm or yarn package manager

## Getting Started

### Install Project
To install the project dependencies:

```bash
# Using npm
npm install

# Using yarn
yarn install
```

### Development
Start the development server using Vite:

```bash
# Using npm
npm run dev

# Using yarn
yarn dev
```

The development server will be running at `http://localhost:3000`

### Build
Create a production build:

```bash
# Using npm
npm run build

# Using yarn
yarn build
```

This command will:
1. Run TypeScript compilation (`tsc -b`)
2. Build the project using Vite (`vite build`)

### Preview
To preview the production build locally:

```bash
# Using npm
npm run preview

# Using yarn
yarn preview
```

This will start a local server to preview your production build.

### Linting
Check code style and find potential issues:

```bash
# Using npm
npm run lint

# Using yarn
yarn lint
```

Fix lint issues automatically:

```bash
# Using npm
npm run lint:fix

# Using yarn
yarn lint:fix
```

## Scripts Overview

| Command | Description |
|---------|-------------|
| `npm run dev` | Start Vite development server |
| `npm run build` | Run TypeScript compilation and create production build |
| `npm run preview` | Preview production build locally |
| `npm run lint` | Check code style in src directory |
| `npm run lint:fix` | Fix lint issues automatically in src directory |

## Project Structure
```
src/
├── assets/           # Static assets, images, fonts, etc.
├── components/       # Reusable UI components
├── layouts/         # Layout components
├── pages/           # Page components
├── routes/          # Route configurations
├── schemas/         # Data schemas and validations
├── services/        # API services and external integrations
├── store/           # State management
├── styles/          # Global styles and theme
├── types/           # TypeScript type definitions
├── App.tsx          # Root application component
└── main.tsx         # Application entry point

Root files:
├── .gitignore       # Git ignore configuration
├── eslint.config.js # ESLint configuration
├── index.html       # HTML entry point
├── package.json     # Project dependencies and scripts
├── package-lock.json# Lock file for npm dependencies
├── postcss.config.cjs # PostCSS configuration
├── README.md        # Project documentation
├── tailwind.config.js # Tailwind CSS configuration
├── tsconfig.app.json # TypeScript configuration for app
├── tsconfig.json    # Base TypeScript configuration
├── tsconfig.node.json # TypeScript configuration for Node
├── vite-env.d.ts    # Vite environment declarations
├── vite.config.ts   # Vite configuration
└── yarn.lock        # Lock file for yarn dependencies
```

## Flow Coding

<h2 id="commit-flow">Commit Flow</h2>

**Remember**
- run `npm run prettier:fix` before `git add [...]`.
- Don't use `git add .` for all cases. Typing slow, Thinking more. Maybe at the current time, we just need to apply some of files changed.

**`git commit` Flow**
Typing `git commit` instead of `git commit -m "(type): title string"`

- The system will run the `lint-staged` script in the `pre-commit` setup of the `husky` file.
- The `lint-staged` script will run `npm run lint` to check the eslint rules for all of project.
  - IF The project is not valid, the commit process will fail.
  - ELSE, you will pass the eslint and just do some important steps before push your code.

1. Select the `type` of the commit

feat, style, test, fix, docs, release, update, perf, chore

2. Select the `scope` of `type`

layout, page, component, service, util, hook, store, route, type, schemas, other

3. Input the `commit title`
4. Input the `commit description`
5. Press `esc` > `:` > `wq` to write and quit
6. Run `git push`

### Redux

### Store Structure
```
store/
├── constants/
│   ├── actionTypes.ts    # Action type constants
│   └── nameSlices.ts     # Slice name constants
├── slices/
│   ├── exampleSlice.ts   # Example slice implementation
│   └── store.ts          # Root store configuration
```

### Implementation Flow

#### 1. Constants Setup (`constants/`)

##### `nameSlices.ts`
```typescript
// Define slice names to avoid string literals
export const SLICE_NAMES = {
  EXAMPLE: 'example',
  // Add other slice names
} as const;
```

#### `actionTypes.ts`
```typescript
// Define action type constants
export const EXAMPLE_ACTIONS = {
  SET_DATA: 'example/setData',
  RESET: 'example/reset',
  // Add other action types
} as const;
```

### 2. Slice Implementation (`slices/exampleSlice.ts`)
```typescript
import { createSlice, PayloadAction } from '@reduxjs/toolkit';
import { SLICE_NAMES } from '../constants/nameSlices';

// Define the state interface
interface ExampleState {
  data: string[];
  isLoading: boolean;
  error: string | null;
}

// Initial state
const initialState: ExampleState = {
  data: [],
  isLoading: false,
  error: null,
};

// Create the slice
const exampleSlice = createSlice({
  name: SLICE_NAMES.EXAMPLE,
  initialState,
  reducers: {
    setData: (state, action: PayloadAction<string[]>) => {
      state.data = action.payload;
    },
    setLoading: (state, action: PayloadAction<boolean>) => {
      state.isLoading = action.payload;
    },
    setError: (state, action: PayloadAction<string | null>) => {
      state.error = action.payload;
    },
    reset: (state) => {
      return initialState;
    },
  },
});

// Export actions and reducer
export const { setData, setLoading, setError, reset } = exampleSlice.actions;
export default exampleSlice.reducer;

// Selectors
export const selectData = (state: RootState) => state.example.data;
export const selectIsLoading = (state: RootState) => state.example.isLoading;
export const selectError = (state: RootState) => state.example.error;
```

### 3. Store Configuration (`store.ts`)
```typescript
import { configureStore } from '@reduxjs/toolkit';
import exampleReducer from './slices/exampleSlice';

export const store = configureStore({
  reducer: {
    example: exampleReducer,
    // Add other reducers
  },
});

// Export types
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### 4. Usage in Components
```typescript
import { useDispatch, useSelector } from 'react-redux';
import { setData, selectData, selectIsLoading } from '../store/slices/exampleSlice';

const ExampleComponent = () => {
  const dispatch = useDispatch();
  const data = useSelector(selectData);
  const isLoading = useSelector(selectIsLoading);

  const handleUpdateData = (newData: string[]) => {
    dispatch(setData(newData));
  };

  return (
    // Your component JSX
  );
};
```

## Best Practices

1. **Slice Organization**
   - Keep slice names in a central constants file
   - Create separate files for each slice
   - Export selectors alongside the slice

2. **Type Safety**
   - Define interfaces for all state shapes
   - Use PayloadAction type for action creators
   - Export and use RootState type for selectors

3. **State Updates**
   - Use Redux Toolkit's built-in Immer integration
   - Avoid nested spreads
   - Keep state normalized when possible

4. **Performance**
   - Use specific selectors instead of selecting entire state
   - Memoize complex selectors with createSelector
   - Split large slices into smaller ones

## Common Patterns

### Async Actions with createAsyncThunk
```typescript
import { createAsyncThunk } from '@reduxjs/toolkit';

export const fetchData = createAsyncThunk(
  'example/fetchData',
  async (params: FetchParams, { rejectWithValue }) => {
    try {
      const response = await api.getData(params);
      return response.data;
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);

// In slice:
extraReducers: (builder) => {
  builder
    .addCase(fetchData.pending, (state) => {
      state.isLoading = true;
    })
    .addCase(fetchData.fulfilled, (state, action) => {
      state.isLoading = false;
      state.data = action.payload;
    })
    .addCase(fetchData.rejected, (state, action) => {
      state.isLoading = false;
      state.error = action.payload as string;
    });
}
```

### Custom Middleware
```typescript
import { Middleware } from '@reduxjs/toolkit';

export const loggingMiddleware: Middleware = (store) => (next) => (action) => {
  console.log('Dispatching:', action);
  const result = next(action);
  console.log('Next State:', store.getState());
  return result;
};

// Add to store:
middleware: (getDefaultMiddleware) =>
  getDefaultMiddleware().concat(loggingMiddleware)
```

### Validate

## Coding Style Detail

### Component Structure
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

### Hooks Usage
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

### Type Definitions
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

## Best Practice

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

## Eslint Config

### File Naming Conventions

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

## Tailwind CSS Integration

### Setup and Configuration
The project uses Tailwind CSS for styling. The configuration is defined in `tailwind.config.js`:

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      // Your custom theme extensions
    },
  },
  plugins: [],
}
```

### Using Tailwind CSS

#### Basic Usage
Use Tailwind's utility classes directly in your JSX:

```jsx
function Button({ children }) {
  return (
    <button className="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 transition-colors">
      {children}
    </button>
  );
}
```

#### Common Patterns
1. **Responsive Design**:
```jsx
<div className="w-full md:w-1/2 lg:w-1/3">
  {/* Content adapts at different breakpoints */}
</div>
```

2. **Hover, Focus, and Other States**:
```jsx
<button className="bg-blue-500 hover:bg-blue-600 focus:ring-2 focus:ring-blue-300">
  Click me
</button>
```

3. **Flex and Grid Layouts**:
```jsx
<div className="flex items-center justify-between">
  <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
    {/* Content */}
  </div>
</div>
```

### Customizing Tailwind

#### Extending Theme
Add custom values to your theme in `tailwind.config.js`:

```javascript
module.exports = {
  theme: {
    extend: {
      colors: {
        'custom-primary': '#FF5733',
        'custom-secondary': '#33FF57',
      },
      spacing: {
        '128': '32rem',
      },
      fontFamily: {
        'custom': ['CustomFont', 'sans-serif'],
      },
    },
  },
}
```

#### Custom Classes
Create custom utility classes in your CSS file:

```css
@layer components {
  .btn-primary {
    @apply px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 transition-colors;
  }
}
```

#### Using Custom Values
After defining custom values, use them in your components:

```jsx
<div className="text-custom-primary bg-custom-secondary font-custom">
  Custom styled content
</div>
```

### Best Practices

1. **Organization**
   - Group related utilities: `className="[Layout] [Spacing] [Colors] [Effects]"`
   - Example: `className="flex items-center p-4 bg-blue-500 shadow-md"`

2. **Reusability**
   - Extract common patterns into components
   - Use composition over repetition

3. **Responsive Design**
   - Start with mobile design first
   - Use responsive prefixes systematically: `sm:`, `md:`, `lg:`, `xl:`

4. **Dark Mode**
   - Use dark mode variant: `dark:`
   - Example: `className="bg-white dark:bg-gray-800"`

### Common Issues and Solutions

1. **Purging Issues**
   - Ensure your content paths in `tailwind.config.js` include all files using Tailwind classes
   - Don't use dynamic class names without safelist

2. **Build Performance**
   - Use JIT mode (enabled by default in Tailwind CSS v3)
   - Remove unused plugin imports

3. **Class Conflicts**
   - Use `@apply` with caution
   - Maintain a consistent order of utility classes

### Tips and Tricks

1. **VS Code Extensions**
   - Install "Tailwind CSS IntelliSense" for auto-completion
   - Use "Headwind" for consistent class ordering

2. **Debugging**
   - Use browser dev tools to inspect computed styles
   - Temporarily add borders/backgrounds to debug layouts: `border border-red-500`

3. **Performance**
   - Use `@apply` for frequently reused patterns
   - Leverage CSS variables for dynamic values

## Contributing
Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
