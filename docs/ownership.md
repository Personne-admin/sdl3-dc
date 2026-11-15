# Pointer Ownership and Lifetime Rules

Callers must follow SDL3's ownership rules for strings, arrays, and handles.

## 1. Entry-Point Initialization

Always call:

```dc
sdl3::SDL_SetMainReady();
```

before `sdl3::SDL_Init(...)`.

## 2. Borrowed Strings

The following functions return pointers to SDL-managed internal buffers.
They are valid until the next SDL call on that thread or subsystem shutdown, and **must not** be passed to `SDL_free`:

- `SDL_GetError()`
- `SDL_GetRevision()`
- `SDL_GetHint(name)`
- `SDL_GetAppMetadataProperty(name)`
- `SDL_GetWindowTitle(window)`
- `SDL_GetDisplayName(displayID)`
- `SDL_GetPixelFormatName(format)`
- `SDL_GetKeyName(key)` / `SDL_GetScancodeName(scancode)`
- `SDL_GetBasePath()`

## 3. Caller-Owned Allocations

The following functions allocate memory on the SDL heap and transfer ownership to the caller.
Free them with `sdl3::SDL_free(ptr)` when done:

- `SDL_GetPrefPath(org, app)` -> `char*`
- `SDL_GetDisplays(&count)` -> `SDL_DisplayID*`
- `SDL_LoadFile(file, &datasize)` / `SDL_LoadFile_IO(src, &datasize, closeio)` -> `void*`
- `SDL_LoadWAV(path, &spec, &audio_buf, &audio_len)` -> `audio_buf` (`u8*`)

## 4. Opaque Handles and Destroy Functions

Every `SDL_Create*` / `SDL_Open*` / `SDL_Load*` handle must be paired with its matching destroy/close function:

| Resource | Acquire | Release |
|---|---|---|
| Window | `SDL_CreateWindow` / `SDL_CreateWindowWithProperties` | `SDL_DestroyWindow` |
| Renderer | `SDL_CreateRenderer` / `SDL_CreateWindowAndRenderer` | `SDL_DestroyRenderer` |
| Texture | `SDL_CreateTexture` / `SDL_CreateTextureFromSurface` | `SDL_DestroyTexture` |
| Surface | `SDL_CreateSurface` / `SDL_LoadBMP` / `SDL_ConvertSurface` | `SDL_DestroySurface` |
| GL Context | `SDL_GL_CreateContext` | `SDL_GL_DestroyContext` |
| Cursor | `SDL_CreateSystemCursor` | `SDL_DestroyCursor` |
| IOStream | `SDL_IOFromFile` / `SDL_IOFromMem` | `SDL_CloseIO` |
| AudioStream | `SDL_OpenAudioDeviceStream` | `SDL_DestroyAudioStream` |
| Timer | `SDL_AddTimer` / `SDL_AddTimerNS` | `SDL_RemoveTimer` |

Note: `SDL_GetCurrentDisplayMode` and `SDL_GetDesktopDisplayMode` return a borrowed `const SDL_DisplayMode*` managed by SDL.
