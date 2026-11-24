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

## Memory and frame-time budgets

Use the [memory management guide](memory-management.md) for lifecycle ownership, event cleanup, custom effect resources and repeatable stress tests.

| Technique | Benefit | Trade-off |
| --- | --- | --- |
| Reuse text objects | Less allocation and layout churn | Explicitly reset old animations when changing content |
| Shared scheduler | Avoid a separate update loop per label | More simultaneous targets still increase per-frame work |
| Glyph and slice pooling | Reduce repeated instance creation | Retains reusable instances in memory |
| Measurement caching | Avoid repeated text measurements | Retains cached measurements up to configured limits |
| Prewarm | Move allocation work before an intensive scene | Increases upfront allocation |
| Pause hidden text | Reduce unnecessary animation work | Does not release the text object or its resources |
| Destroy unused text | End the object's lifecycle | Recreate it when needed again |

Prefer fewer animated glyphs and modest slice counts on low-end devices. Use word or whole-text targets where suitable, but remember that the underlying renderer may still update individual glyphs. Measure on actual target devices rather than assuming an effect is inexpensive.

## Profiling workflow

1. Start with one text object and no effects.
2. Record the module's GetStats output and the client memory baseline.
3. Add effects and text objects progressively, keeping other game activity stable.
4. Compare frame time using Roblox Studio's MicroProfiler and memory using the Developer Console.
5. Repeat creation and destruction cycles to check whether retained memory stabilizes after warm-up.

GetStats describes the animation scheduler, not the entire Roblox client. A lower animation step time does not necessarily imply lower GPU cost or fewer retained instances.

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
