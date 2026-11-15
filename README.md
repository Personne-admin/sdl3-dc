# sdl3-dc

SDL3 C-ABI bindings for [DC](https://github.com/Personne-admin/dcc).

## Consumer Setup

Add `sdl3` as a dependency in your `project.dcon`;

```bash
dpm add github.com/Personne-admin/sdl3-dc
```

Ensure the native SDL3 development library (`libSDL3`) is installed on your system.

## Entry Point & `SDL_SetMainReady`

Call `sdl3::SDL_SetMainReady()` before `sdl3::SDL_Init(...)` so SDL marks its internal main-ready state initialized.

> See [docs/ownership.md](docs/ownership.md) for pointer ownership and lifetime rules.
