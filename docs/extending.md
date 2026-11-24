# Extending AnimatedText

[Home](../README.md)

## Custom effects

Effects write to independent modifier states instead of mutating GUI instances. This makes layering, weight and fades work consistently.

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Kit = AT.EffectKit

AT.RegisterEffect("Hop", Kit.Entrance({
    Description = "Letters hop in",
    Shape = function(context, state, p, raw)
        state.PositionOffset = Vector2.new(0, -20 * math.sin(p * math.pi))
    end,
}))
Text:Play("Hop")
```

Kit.Entrance and Kit.Exit provide staggered progress, easing and fades. Kit.Loop provides time multiplied by Speed and index multiplied by Spacing.

Effect definitions can implement Update(Context), UpdateAll(Context), Start(Context), and use DefaultConfig, Target, Duration and Category. Context.State contains modifier values; Context.Data and Context.TargetData store per-animation and per-target data.

```lua
local Split: AT.Effect = {
    Target = "Slice",
    SliceAxis = "Horizontal",
    Duration = 0.5,
    DefaultConfig = { SliceCount = 6, Distance = 20 },
}
function Split:Update(Context)
    local slice = Context.Target
    local direction = if slice.Index % 2 == 0 then 1 else -1
    local distance = Context.Config.Distance * math.sin(Context.Progress * math.pi)
    Context.State.PositionOffset = Vector2.new(direction * distance, 0)
end
AT.RegisterEffect("CustomSplit", Split)
```

Fx provides Shadow, Trail, Tilt, Chromatic, Burst and Star helpers. Context:GetDecor() exposes reusable decorative frames and labels. Effects can define KeepApart = false or Hides = true.

## Custom presets

```lua
AT.RegisterPreset("Radiant", {
    Description = "Wave with flowing colors",
    Category = "Game",
    Effects = {
        { Effect = "Wave", Config = { Amplitude = 6 } },
        { Effect = "Rainbow" },
    },
})
Text:PlayPreset("Radiant")
```

Use Sequence for ordered steps or Build(options) for dynamically generated steps. Loop repeats a preset.

## Sprite fonts

```lua
local PixelFont: AT.SpriteFont = {
    Image = "rbxassetid://123456789",
    Size = 32,
    LineHeight = 36,
    SpaceWidth = 10,
    Fallback = "?",
    CaseInsensitive = true,
    Characters = {
        A = { Offset = Vector2.new(0, 0), Size = Vector2.new(28, 32), Advance = 30 },
        ["?"] = { Offset = Vector2.new(32, 0), Size = Vector2.new(20, 32) },
    },
}
AT.RegisterFont("Pixel", PixelFont)
local Text = AT.new(label, { Renderer = "Sprite", Font = "Pixel" })
```

Replace the example asset ID with your atlas. Glyph measurements are native atlas units. Optional Bearing and Kerning adjust placement. Sprite rendering supports genuine nonuniform scaling and slice cropping, but not glyph-specific fonts or stroke/glow decorations.

## Custom renderers

Register a constructor using AT.RegisterRenderer("Mine", constructor). Pass Renderer = "Mine" and RendererOptions to new. The renderer interface includes Name, Measure, SetStyle, SetContainerSize, Attach, Detach, Refresh and Apply. Consult the included Types ModuleScript for the full renderer contract.
