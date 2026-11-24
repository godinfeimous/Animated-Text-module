<div align="center">

# AnimatedText

### Make every letter move.

**The advanced text animation engine for Roblox Studio.**

[**v3.0.0**](CHANGELOG.md) · [**Roblox Studio**](#compatibility) · [**Strictly typed Luau**](docs/api.md)

**56 effects · 77 presets · 10 karaoke modes**

[Get started](docs/getting-started.md) &nbsp;•&nbsp; [Explore effects](docs/effects.md) &nbsp;•&nbsp; [Build your own effect](docs/custom-effects.md) &nbsp;•&nbsp; [API reference](docs/api.md)

[![Watch the cinematic dialogue showcase](https://img.youtube.com/vi/D4c7-PdwwSI/hqdefault.jpg)](https://youtu.be/D4c7-PdwwSI)

*Watch AnimatedText in action — a cinematic dialogue showcase.*

</div>

---

## Beyond ordinary text

AnimatedText turns Roblox UI text into independently animated characters, words, lines and sentences. Layer motion, color and distortion, synchronize them with sound, or implement entirely new behaviors with the custom effect API.

| Creative control | Developer experience |
| :--- | :--- |
| Per-character and grouped animation | Simple, strictly typed Luau API |
| Independent effect layers and smooth blending | No external dependencies |
| Rich Text, gradients, glow and typography | Interactive preview and testing tools |
| Audio synchronization and cinematic sequences | Extensible effects, presets and renderers |

## Your first animation

Place the AnimatedText ModuleScript in ReplicatedStorage and add a LocalScript to your TextLabel.

```lua
local AnimatedText = require(game.ReplicatedStorage.AnimatedText)
local Text = AnimatedText.new(script.Parent)
Text:Play("Wave")
```

### Mix effects without rewriting your UI

```lua
Text:Play("Wave", { Amplitude = 6 })
Text:Play("Rainbow", { Palette = "Neon" })
Text:Play("Pulse", { Strength = 0.1 })
```

### Animate only the words you want

```lua
Text:SetRichText("You earned <color=gold>500 coins</color>!")
Text:Find("500 coins"):Play("Shine")
```

### Create something nobody else has

```lua
AnimatedText.RegisterEffect("MyWave", {
    DefaultConfig = { Speed = 5, Height = 10 },
    Update = function(self, Context)
        local Offset = math.sin(Context.Time * Context.Config.Speed + Context.Index)
        Context.State.PositionOffset = Vector2.new(0, Offset * Context.Config.Height)
    end,
})

Text:Play("MyWave", { Height = 20 })
```

Your custom effect automatically supports selection, layering, blending, playback controls and Rich Text tags. [Learn how to build custom effects step by step →](docs/custom-effects.md)

---

## Documentation

| Start here | Reference & advanced |
| :--- | :--- |
| [Installation & quick start](docs/getting-started.md) | [Effects & presets](docs/effects.md) |
| [Practical examples](docs/recipes.md) | [Effect parameters](docs/effect-parameters.md) |
| [Rich Text & typography](docs/styling.md) | [API reference](docs/api.md) |
| [Audio & karaoke](docs/audio.md) | [Custom effect tutorial](docs/custom-effects.md) |
| [Cinematic credits](docs/credits.md) | [Custom renderers & sprite fonts](docs/extending.md) |
| [Troubleshooting](docs/troubleshooting.md) | [Performance & configuration](docs/performance.md) |
| [Profiling & stress tests](docs/memory-management.md#stress-test) | [Memory management & leak prevention](docs/memory-management.md) |

## Built for creative projects

Cinematic dialogue • Horror • Rhythm games • RPGs • Animated HUDs • Rewards • Subtitles • Intro sequences • End credits

## Compatibility

Supports TextLabel, TextButton and TextBox in ScreenGui, SurfaceGui or BillboardGui. Designed for client-side UI animation in Roblox Studio.

## Support & updates

See the [release notes](CHANGELOG.md) for changes. For help, consult [troubleshooting](docs/troubleshooting.md) or your purchase support channel.

[YouTube showcase](https://youtu.be/D4c7-PdwwSI) · [Original showcase on X](https://x.com/godinfeimous/status/2122646214344589649)

---

<div align="center">

**Your text. Your rules.**

Developed by Godinfeimous · UI by Ceniza_skywalker · Prototype by NotxSailorys

This repository contains documentation and usage examples, not the commercial module source.

</div>
