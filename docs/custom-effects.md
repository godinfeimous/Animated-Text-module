# Build your own animation effect

[Home](../README.md) · [Effects](effects.md) · [API reference](api.md) · [Advanced extensions](extending.md)

AnimatedText is not limited to its built-in effects. This tutorial walks through building a reusable, configurable effect, combining it with other animations, and creating a finite entrance.

## Contents

1. [Create your first effect](#1-create-your-first-effect)
2. [Understand the animation context](#2-understand-the-animation-context)
3. [Add scale and glow](#3-add-scale-and-glow)
4. [Expose configuration](#4-expose-configuration)
5. [Target text and layer effects](#5-target-text-and-layer-effects)
6. [Build a staggered entrance](#6-build-a-staggered-entrance)
7. [Use stable randomness](#7-use-stable-randomness)
8. [Effect lifecycle and state reference](#8-effect-lifecycle-and-state-reference)
9. [Package and debug](#9-package-and-debug)

## 1. Create your first effect

Create a LocalScript under a TextLabel. Ensure the module is in ReplicatedStorage.

```lua
local AnimatedText = require(game.ReplicatedStorage.AnimatedText)
local Text = AnimatedText.new(script.Parent)

local MyWave = {
    DefaultConfig = {
        Speed = 5,
        Height = 10,
        Spacing = 0.7,
    },
}

function MyWave:Update(Context)
    local Config = Context.Config
    local Phase = Context.Time * Config.Speed + Context.Index * Config.Spacing
    local Offset = math.sin(Phase) * Config.Height

    Context.State.PositionOffset = Vector2.new(0, Offset)
end

AnimatedText.RegisterEffect("MyWave", MyWave)
Text:SetText("HELLO WORLD")
Text:Play("MyWave")
```

Each frame, the engine calls Update for each selected target. The sine function produces an oscillation between -1 and 1. Height multiplies that range into pixels. Index shifts the phase for neighboring characters, creating a traveling wave.

| Parameter | Meaning |
| --- | --- |
| Speed | Angular speed in radians per second |
| Height | Maximum vertical displacement in pixels |
| Spacing | Phase difference between successive targets |

> [!IMPORTANT]
> Never increment the actual GUI instance's Position, Rotation or Size in Update. Write to Context.State instead. It resets to neutral values before every update, and the renderer combines the result with the base layout and other effects.

## 2. Understand the animation context

| Field | Meaning |
| --- | --- |
| Context.Time | Elapsed animation time, excluding delays and pauses |
| Context.DeltaTime | Time since the previous frame |
| Context.Progress | Normalized progress for finite effects; 0 for infinite effects |
| Context.Index / Count | Current target index and total count |
| Context.Config | Default settings merged with user overrides |
| Context.Target / Character | Target data and its representative character |
| Context.TargetType | Character, Word, Line, Sentence, Whole or Slice |
| Context.State | Writable modifier state for this target and frame |
| Context.BaseState | Read-only base layout/style |
| Context.Data | Persistent data shared by this animation |
| Context.TargetData | Persistent data for an individual target |
| Context.Random | Per-animation seeded random generator |
| Context.Easing | Resolved easing function |
| Context.Skipped | Whether the animation is processing Skip |

Context also exposes TextObject, Handle, Targets, Characters, Selection and Style. Target-specific fields are available inside Update.

## 3. Add scale and glow

Replace the simple MyWave definition with the following complete effect:

```lua
local NeonWave = {
    Category = "Loop",
    Description = "A flowing wave with scale and luminous crests.",
    DefaultConfig = {
        Speed = 4,
        Height = 8,
        Spacing = 0.6,
        Strength = 0.12,
        Glow = 0.5,
        Color = "#B58CFF",
    },
}

function NeonWave:Update(Context)
    local Config = Context.Config
    local Phase = Context.Time * Config.Speed + Context.Index * Config.Spacing
    local Wave = math.sin(Phase)
    local Crest = (Wave + 1) / 2
    local Scale = 1 + Crest * Config.Strength
    local Color = AnimatedText.Color.Parse(Config.Color)

    Context.State.PositionOffset = Vector2.new(0, Wave * Config.Height)
    Context.State.Scale = Vector2.new(Scale, Scale)
    Context.State.Glow = Crest * Config.Glow
    Context.State.GlowColor = Color
    Context.State.Color = Color
    Context.State.ColorWeight = Crest * 0.5
end

AnimatedText.RegisterEffect("NeonWave", NeonWave)
Text:Play("NeonWave")
```

The crest is normalized to 0–1, making it useful for glow and color weight. Scale is expressed as a multiplier, where Vector2.new(1, 1) means no change.

## 4. Expose configuration

Add the following Params metadata to NeonWave before registering it:

```lua
NeonWave.Params = {
    { Name = "Speed", Min = 0, Max = 12 },
    { Name = "Height", Min = 0, Max = 30 },
    { Name = "Spacing", Min = 0, Max = 2 },
    { Name = "Strength", Min = 0, Max = 0.5 },
    { Name = "Glow", Min = 0, Max = 1 },
}
```

These definitions expose numeric controls in the demo laboratory and metadata through GetInfo. The DefaultConfig table specifies the actual defaults.

```lua
local Handle = Text:Play("NeonWave", {
    Speed = 2,
    Height = 12,
    Color = "#00D9FF",
})

Handle:SetConfig({ Height = 20 })
Handle:Stop(0.4)
```

## 5. Target text and layer effects

Your custom effect automatically supports standard AnimatedText playback options.

```lua
Text:SetText("Your legendary reward awaits")

Text:Play("NeonWave", {
    Match = "legendary",
    Weight = 0.8,
    BlendIn = 0.3,
})

Text:Play("Shine", { Match = "reward" })
```

Try Target = "Word" to move whole words rather than individual letters. Use Words, Lines, Sentences, Range or Match to select a subset. Target = "Whole" applies rotation and scaling around the selection's center.

You can even invoke the effect with a Rich Text tag:

```lua
Text:SetRichText("You found <neonwave height=14>treasure</neonwave>!")
```

Register the effect before parsing its tag.

## 6. Build a staggered entrance

A continuous effect needs an explicit stop or duration. An entrance normally finishes after every target has arrived. The EffectKit helper calculates stagger timing, easing and entrance fading for you.

```lua
local Kit = AnimatedText.EffectKit

AnimatedText.RegisterEffect("SoftHop", Kit.Entrance({
    Description = "Letters gently hop into place.",
    Shape = function(Context, State, Progress, RawProgress)
        local Height = 24 * math.sin(math.pi * Progress)
        State.PositionOffset = Vector2.new(0, -Height)
        State.Rotation = 8 * math.sin(math.pi * Progress)
    end,
}))

Text:Play("SoftHop", {
    Duration = 0.45,
    Stagger = 0.06,
    Easing = "OutCubic",
}):Wait()
```

Progress is eased; RawProgress is not. EffectKit also provides Exit and Loop factories. For full control, implement GetDuration using AnimatedText.Math.StaggeredDuration, then calculate each target's progress with StaggerIndex and StaggeredProgress.

## 7. Use stable randomness

Avoid generating a new phase every frame unless you intentionally want jitter. Store random values in Context.TargetData:

```lua
local StarPulse = {
    Category = "Loop",
    DefaultConfig = { Speed = 6 },
}

function StarPulse:Update(Context)
    local Data = Context.TargetData

    if Data.Phase == nil then
        Data.Phase = Context.Random:NextNumber(0, math.pi * 2)
    end

    local Glow = (math.sin(Context.Time * Context.Config.Speed + Data.Phase) + 1) / 2

    Context.State.Color = Color3.new(1, 1, 0.6)
    Context.State.ColorWeight = Glow
    Context.State.Glow = Glow * 0.6
end

AnimatedText.RegisterEffect("StarPulse", StarPulse)
Text:Play("StarPulse", { Seed = 42 })
```

Use Context.Data for shared values and Context.TargetData for individual target values. Seed makes the random sequence reproducible.

## 8. Effect lifecycle and state reference

Only Update or UpdateAll is required.

| Callback | When called |
| --- | --- |
| Start(Context) | Once before playback |
| GetDuration(Context) | Resolves finite playback duration |
| UpdateAll(Context) | Once per frame before target updates |
| Update(Context) | Once per selected target per frame |
| Relayout(Context) | After layout recalculation |
| Finish(Context, natural) | On completion or explicit stop |
| Cleanup(Context) | Release resources created by the effect |

Duration priority: GetDuration, Config.Duration, Effect.Duration, then infinite. Hold preserves the final state; Loop repeats finite effects.

| State field | Composition |
| --- | --- |
| PositionOffset / Rotation | Additive pixels / degrees |
| Scale | Multiplies Vector2 scale |
| Transparency / StrokeTransparency | Multiplied visibility |
| Color / ColorWeight | Weighted blend |
| Gradient / GradientWeight | Weighted group or glyph gradient |
| GradientAlpha / TransparencyGradient | Position-dependent gradient weight or transparency |
| Glow / GlowColor / GlowSize | Strongest glow wins |
| StrokeColor / StrokeColorWeight | Weighted stroke color |
| Visible | All effects must allow visibility |
| Text | Temporary replacement; last effect wins |
| Shadow / ShadowOffset / ShadowColor | Shared shadow layer |
| Echo / EchoOffset / EchoColor / EchoScale | Shared echo layer |
| GradientAxis | Vertical or default horizontal |
| Ghost / SliceWiden | Slice-specific modifiers |
| Loose | Exclude a glyph from overlap prevention |

Advanced effects can use AnimatedText.Gradient, AnimatedText.Fx, and Context:GetDecor(). See [advanced extensions](extending.md).

## 9. Package and debug

For automatic registration, create a ModuleScript named after the effect inside AnimatedText > Effects and return its effect definition. Alternatively, register it manually before using Play or parsing Rich Text.

- Keep each effect independent of the GUI instances.
- Cache persistent data instead of allocating objects on every frame.
- Test with character and word targets, selectors and concurrent animations.
- Test Pause, Resume, Stop with a fade, and Skip for finite effects.
- Check Studio Output if an effect fails; the engine stops the failing animation without stopping the others.
- Inspect effect metadata with AnimatedText.GetInfo("NeonWave", "en").

For resource ownership, event connections and cleanup callbacks, see [Memory management for custom effects](memory-management.md#custom-effects-and-external-resources).

You now have the building blocks to design your own animation library.

[← Back to documentation](../README.md) · [Next: custom renderers and sprite fonts →](extending.md)
