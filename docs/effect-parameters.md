# Effect parameter reference

[Home](../README.md) · [Effects and presets](effects.md)

This guide lists selected documented defaults. All effects also accept common playback and selection options.

| Effect | Selected defaults | Modes / notes |
| --- | --- | --- |
| Elastic | Duration 0.9, Stagger 0.04, Distance 30, Tilt 12 | Direction Down, Up, Left, Right |
| FadeIn | Duration 0.5, Stagger 0.03, Blur 0.35, Distance 12 | Fade, Wipe |
| PopIn | Duration 0.4, Stagger 0.04, From 0, Shadow 0.35 | In, Out |
| Slam | Duration 0.26, Stagger 0.24, From 3.4, Shake 7, Dust 8 | Word target by default |
| SlideIn | Duration 0.5, Stagger 0.03, Distance 40, Trail 0.4 | In, Out |
| Typewriter | CharactersPerSecond 30, PunctuationDelay 0.15, EntranceTime 0.08 | None, Fade, Pop, Drop, Slide, Glow, Rise, Ink, Impact |
| Bounce | Speed 6, Height 10, Spacing 0.5, Squash 0.15 | Loop, Drop |
| Flicker | Speed 14, Intensity 0.7, Chance 0.35, Glow 0.3 | Neon, Strobe, Candle |
| Float | Speed 0.6, Amplitude 4, Rotation 3, Spacing 0.35 | Continuous |
| Jelly | Amount 0.15, Speed 5, Decay 0.8 | Loop, Wobble |
| Pulse | Speed 2, Strength 0.15, Spacing 0.3, Echo 0.25 | Sine, Heartbeat |
| Shake | Intensity 3, Speed 25, Rotation 4, Decay 0.5 | Noise, Jitter, Impact |
| Spin | Speed 1, Spacing 0.15, Turns 1, Angle 20 | Loop, Once, Wobble |
| Swing | Angle 12, Speed 3, Spacing 0.4, Damping 0 | Top, Bottom, Center pivot |
| Wave | Speed 4, Amplitude 8, Spacing 0.7, Rotation 0, Scale 0 | Sine, Triangle, Square, Bounce, Sharp, Pulse |
| Blink | Count 3, Rate 3, Pop 0.15, Glow 0.6 | Count 0 repeats |
| Chrome | SweepTime 0.8, Interval 2.2, Glint 0.7 | Silver, Gold, Retro |
| ColorCycle | Interval 0.35, Offset 1, Pop 0.12, Blend 0.12 | Palette |
| Duotone | Speed 0.5, Split 0.55, Move 0.12, Softness 0.15 | Top and Bottom colors |
| Marker | Duration 0.5, Stagger 0.15, Height 0.55, Opacity 0.55 | Holds the marker |
| Rainbow | Speed 0.5, Spread 0.08, Strength 1, Glow 0.2 | Flow, Solid; Text, Sentence, Line, Word scope |
| SliceDistortion | SliceCount 12, Stretch 1.6, Variation 0.6, Speed 18 | Burst, In, Out, Loop |
| Static | Noise 0.45, Swap 0.06, Desaturate 0.5, Rate 24 | Continuous |
| Tear | SliceCount 8, Speed 20, Strength 18, Chance 0.4 | Burst, Loop, Constant |
| Confetti | Count 28, Speed 340, Gravity 520, Life 1.5 | Repeat = true for continuous bursts |
| Extrude | Depth 0.09, Angle 50, Swing 30, Speed 0.25 | Continuous |
| Karaoke | WordsPerSecond 2.5, Softness 0.35, ActiveScale 1.12 | See [audio guide](audio.md) |
| Neon | Glow 0.85, Ignition 0.5, Stagger 0.07, Broken 0.15 | Continuous |
| Retro | Speed 0.6, Spacing 0.3, Glow 0.45, Pulse 0.35 | Continuous |
| Sparkles | Count 5, Interval 1.2, Length 0.6, Size 0.45 | Continuous |
| FadeOut | Duration 0.5, Stagger 0.03, Distance 8, Blur 0.35 | Fade, Wipe |
| Shatter | Duration 0.9, Force 300, Lift 160, Gravity 900 | Out, In |

## Discover every parameter in your installed build

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Info = AT.GetInfo("Wave", "en")
print(Info.Defaults)
print(Info.Params)
```

Use GetCatalog and GetPresetCatalog to inspect the registered effects and presets, including your own additions.

## Tuning examples

```lua
Text:Play("Wave", {
    Amplitude = 5,
    Speed = 3,
    Spacing = 0.45,
    CrestColor = "#C8A5FF",
    CrestGlow = 0.3,
})
Text:Play("Shake", {
    Mode = "Impact",
    Intensity = 7,
    Decay = 0.35,
})
Text:Play("SliceDistortion", {
    Mode = "Burst",
    SliceCount = 12,
    Intensity = 0.8,
})
```

Start with subtle motion for readable UI and increase intensity for short, intentional cinematic moments.
