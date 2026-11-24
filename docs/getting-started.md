# Getting started

[Home](../README.md)

## Installation

Move the AnimatedText ModuleScript into ReplicatedStorage (extract it from its Toolbox model if necessary). Create a TextLabel and configure its font, alignment, size and decorations as usual. Add a LocalScript inside it.

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Text = AT.new(script.Parent)
Text:SetRichText("You earned <color=gold>500 coins</color>!")
Text:PlayPreset("Reward")
```

The original label acts as a template. AnimatedText hides its text and creates an AnimatedTextCanvas containing the individual glyphs. Changes to text, style and layout are synchronized automatically.

## Create in one call

```lua
local Text = AT.Create(script.Parent, {
    Text = "You earned 500 coins!",
    Font = "Bangers",
    Effects = { "Rise", { "Wave", { Amplitude = 4 } } },
    Highlights = { ["500 coins"] = "Shine" },
})
```

## Playback

```lua
local wave = Text:Play("Wave")
wave:SetConfig({ Amplitude = 20 })
wave:Pause()
wave:Resume()
wave:Stop(0.5)
Text:Play("Typewriter"):Wait()
Text:StopAll(0.3)
```

Continuous effects need a duration or an explicit stop. Finite effects finish automatically. Use Skip to reveal a dialogue immediately, Reset to restore base states, and Destroy to disconnect and release resources. Destroy also restores the original label.

## Targets and selectors

Target controls the animation unit: Character (default), Word, Line, Sentence, Whole or Slice.

```lua
Text:Play("Swing", { Target = "Word" })
Text:Play("Rainbow", { Sentences = "Last" })
Text:Play("Shake", { Match = "danger" })
Text:Play("Glitch", { Range = { From = 5, To = 9 } })
```

Words, Lines and Sentences accept an index, negative index, list, First, Last, All, Odd, Even, a range such as "2-4", or a predicate. Match accepts a string or list, with optional MatchCase. Combined selectors intersect.

## Sequences

```lua
Text:Sequence({
    { Text = "Incoming!", Effect = "Typewriter", Duration = 1 },
    { Parallel = {
        { Effect = "Shake", Duration = 0.5 },
        { Effect = "Rainbow", Duration = 0.5 },
    } },
    { Wait = 1 },
    { Effect = "FadeOut" },
})
```

Steps can contain Effect, Preset, Config, Duration, Wait, Parallel, Text, Rich or Callback. Sequences wait for each step; always limit infinite effects. Pass { Loop = true, Name = "Intro" } to repeat or name a sequence.

## Studio preview

Run in the Studio command bar:

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
AT.Preview("Wave", { Text = "HELLO", Config = { Speed = 8 } })
AT.Preview.All()
AT.Preview.Credits()
AT.Preview.Stop()
```

Preview options include Text, Rich, Config, Font, TextSize, Color, Duration, Loop and Background.
