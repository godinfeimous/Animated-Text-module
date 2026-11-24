# Performance and configuration

[Home](../README.md)

## Global configuration

Set these before constructing text objects.

| Option | Default |
| --- | --- |
| DefaultRenderer | Character |
| DefaultSliceCount | 8 |
| UpdateEvent | nil (RenderStepped on client, Heartbeat on server) |
| MaxDeltaTime | 0.1 |
| MaxTextSize | 100 |
| ContainerName | AnimatedTextCanvas |
| RichByDefault | false |
| PoolMaxSize | 1024 |
| SentenceBreakOnNewline | false |
| DefaultGlowSize | 3 |
| GlowStyle | Bloom |
| InheritDecorations | true |
| Language | en |
| MeasureCacheSize | 20000 |
| KeepLettersApart | true |
| LetterGap | 0.06 |
| ReducedMotionScale | 0.3 |
| ReducedMotion | nil (follow Roblox setting) |
| Localize | false |

## Optimization

AnimatedText uses one shared scheduler, pooled glyphs and slices, cached measurements and conditional property writes. Effects are composed from independent modifier states each frame; the layout is recalculated only when needed.

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
AT.Prewarm({ Glyphs = 200, Slices = 600, PerFrame = 40 })
print(AT.GetStats())
Text:PrepareSlices()
```

Pause hidden text, reuse text objects with SetText, avoid CanvasGroup around animated text, prewarm before intensive scenes, and disable AutoLocalize on nonlocalized interfaces. GetStats reports Lua animation cost per frame, not total game frame time.

## Accessibility

KeepLettersApart helps prevent overlapping letters. ReducedMotion follows the Roblox setting by default and scales motion. Avoid intense flashing where possible. Localize enables game translation.

## Known limitations

- Native TextLabels approximate nonuniform scaling; Sprite supports real stretching.
- Active slices cannot rotate because of Roblox clipping behavior.
- Concurrent slice effects on a glyph must use the same count and axis.
- A glyph has one UIGradient; horizontal gradients take precedence over vertical ones.
- Shadow and Echo layers are shared; the strongest competing layer wins.
- Sprite rendering does not support the same decorative layers, underline or per-character fonts as the Character renderer.

## Upgrading from 2.x

Category identifiers are English in 3.0; localized names remain available through CategoryLabel and Categories.Label. Description is English by default, with DescriptionEs available for Spanish. Registry category aliases in Spanish remain accepted.
