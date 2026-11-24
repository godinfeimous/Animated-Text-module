# AnimatedText 3.0.0

**Advanced text animation engine for Roblox.**

![Roblox](https://img.shields.io/badge/Platform-Roblox-222222) ![Luau](https://img.shields.io/badge/Language-Luau-00A2FF) ![Version](https://img.shields.io/badge/Version-3.0.0-blue)

Bring typography to life with 56 effects, 77 presets, independent animation layers, custom effects, advanced Rich Text and audio synchronization.

## Quick start

Place the purchased AnimatedText ModuleScript in ReplicatedStorage. Add a LocalScript to a TextLabel:

```lua
local AnimatedText = require(game.ReplicatedStorage.AnimatedText)
local Text = AnimatedText.new(script.Parent)
Text:Play("Wave")
```

Combine effects independently:

```lua
Text:Play("Wave", { Amplitude = 6 })
Text:Play("Rainbow", { Palette = "Neon" })
Text:Play("Pulse", { Strength = 0.1 })
```

## Documentation

| Guide | Description |
| --- | --- |
| [Getting started](docs/getting-started.md) | Installation, playback, selectors and sequences |
| [Effects and presets](docs/effects.md) | Animation catalog and configuration |
| [Rich Text and typography](docs/styling.md) | Colors, gradients, fonts and text parts |
| [Audio and karaoke](docs/audio.md) | Voice lines, lyrics and sound synchronization |
| [API reference](docs/api.md) | Module, text object, handles and signals |
| [Extending the engine](docs/extending.md) | Custom effects, presets and sprite renderers |
| [Performance and configuration](docs/performance.md) | Settings, optimization and limitations |

## Compatibility

TextLabel, TextButton and TextBox in ScreenGui, SurfaceGui or BillboardGui. Client-side UI animation is recommended.

This repository contains documentation and usage examples, not the commercial module itself.

Developed by Godinfeimous. UI by Ceniza_skywalker. Prototype by NotxSailorys.
