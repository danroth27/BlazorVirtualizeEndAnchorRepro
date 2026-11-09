# VirtualizeAnchorRepro

| Setting | Value |
|---------|-------|
| **Interactivity Mode** | Server |
| **Interactivity Scope** | Per-page |

## Rendering configuration
This project uses per-page Interactive Server with prerendering.
Created with `dotnet new blazor -int Server`.

Pages are static SSR by default. Only components that explicitly add `@rendermode InteractiveServer` become interactive.

## Adding new components
- Create new `.razor` files in `Components/Pages/` for routable pages or `Components/` for shared components.
- New pages are static SSR by default. Only add `@rendermode InteractiveServer` to components that need client-side behavior.

## Data access
- Components can inject services directly. No HTTP API layer is needed.

## Environment constraints
- Interactive components run on the server via SignalR. `HttpContext` is available in static components but not during the circuit lifetime.
- Browser APIs require `IJSRuntime` interop in interactive components.

## Don'ts
- Don't set `@rendermode` on `Routes` or `HeadOutlet`.
- Don't suppress experimental warnings.
