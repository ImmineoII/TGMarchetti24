---
name: frontend-development
description: Use when implementing frontend/UI features, React components, state management, CSS styling, accessibility, client-side routing, API integration from the client side, and frontend testing. Trigger keywords: frontend, UI, React, Vue, Angular, component, CSS, HTML, state management, Redux, accessibility, responsive design, SPA, PWA.
---

# Frontend Development

## Goal
Implement high-quality user interfaces following the defined architecture, API contracts, and user stories, with a focus on UX, accessibility, and performance.

## Guiding principles

### Code quality
- Small, focused, and reusable components (Single Responsibility)
- Separation of business logic from UI (custom hooks, services)
- Strong typing (TypeScript)
- No implicit `any`
- Props typed with explicit interfaces

### Recommended project structure
```
src/
  components/          # Reusable UI components (atoms, molecules)
  features/            # Feature modules (organisms + logic)
    [feature-name]/
      components/
      hooks/
      services/
      types/
      __tests__/
  pages/               # Route-level components
  hooks/               # Shared global hooks
  services/            # API clients, utilities
  store/               # Global state management
  types/               # Shared types and interfaces
  utils/               # Pure utility functions
  styles/              # Theme, tokens, global styles
```

## Implementation patterns

### React component with TypeScript
```tsx
interface Props {
  title: string;
  onAction: (id: string) => void;
  isLoading?: boolean;
}

export const MyComponent: React.FC<Props> = ({
  title,
  onAction,
  isLoading = false,
}) => {
  // Logic in custom hook, NOT in the component
  const { data, error } = useMyFeature();

  if (error) return <ErrorBoundary error={error} />;
  if (isLoading) return <Skeleton />;

  return (
    <section aria-label={title}>
      <h2>{title}</h2>
      {/* markup */}
    </section>
  );
};
```

### Custom hook for logic
```tsx
export const useMyFeature = () => {
  const [state, dispatch] = useReducer(reducer, initialState);

  const handleAction = useCallback(async (id: string) => {
    try {
      dispatch({ type: 'LOADING' });
      const result = await api.doSomething(id);
      dispatch({ type: 'SUCCESS', payload: result });
    } catch (err) {
      dispatch({ type: 'ERROR', payload: err });
    }
  }, []);

  return { ...state, handleAction };
};
```

### API Integration
```tsx
// services/api.ts
export const apiClient = {
  get: async <T>(url: string): Promise<T> => {
    const response = await fetch(url, {
      headers: { Authorization: `Bearer ${getToken()}` },
    });
    if (!response.ok) throw new ApiError(response);
    return response.json();
  },
};
```

## Accessibility (a11y)
- Use semantic HTML elements (`<button>`, `<nav>`, `<main>`, `<article>`)
- All forms must have associated `<label>` elements
- Images with descriptive `alt` text
- Focus management for modals and drawers
- ARIA attributes where necessary (`aria-label`, `aria-describedby`, `role`)
- Minimum WCAG AA color contrast (4.5:1)
- Complete keyboard navigation

## Performance
- Code splitting with `React.lazy` and `Suspense`
- Selective memoization with `useMemo` and `useCallback`
- Virtualization for long lists
- Image optimization (WebP, lazy loading)
- Periodic bundle analysis

## Frontend Testing
```tsx
// __tests__/MyComponent.test.tsx
import { render, screen, userEvent } from '@testing-library/react';

describe('MyComponent', () => {
  it('should render title correctly', () => {
    render(<MyComponent title="Test" onAction={vi.fn()} />);
    expect(screen.getByRole('heading', { name: 'Test' })).toBeInTheDocument();
  });

  it('should call onAction when button clicked', async () => {
    const onAction = vi.fn();
    render(<MyComponent title="Test" onAction={onAction} />);
    await userEvent.click(screen.getByRole('button'));
    expect(onAction).toHaveBeenCalledWith(expect.any(String));
  });
});
```

## Checklist for each feature
- [ ] Component implemented according to design system
- [ ] TypeScript without errors
- [ ] Props documented with JSDoc
- [ ] State handling: loading, error, empty state
- [ ] Accessibility verified (tabbable, screen reader)
- [ ] Responsive design (mobile first)
- [ ] Unit tests (>80% coverage)
- [ ] Integration tests for critical flows
- [ ] Performance verified (no unnecessary re-renders)
- [ ] Error boundary implemented

## Handoff to Code Reviewer
- PR with clear description of changes
- Screenshots/video for UI changes
- Link to user story acceptance criteria
- Test coverage report
