# Effects and presets

[Home](../README.md)

## Shared options

| Option | Purpose |
| --- | --- |
| Duration | Time limit for loops; per-unit time for staggered entrances; total time for Typewriter and Karaoke |
| Delay | Wait before playback |
| Loop / Hold | Repeat finite animations / retain final state |
| Target | Character, Word, Line, Sentence, Whole, Slice |
| Weight | Effect intensity multiplier |
| BlendIn / BlendOut | Smooth transition times |
| Range, Sentences, Words, Lines, Match, MatchCase | Select text |
| Sound / SoundOffset | Follow a Sound clock |
| Easing | Named curve or function |
| SliceCount / SliceAxis | Slice count and Vertical or Horizontal axis |
| Seed | Reproducible randomness |

Staggered effects support Order: Forward, Backward, Center, Edges, Random. Easing includes Linear, In/Out/InOut variants of Quad, Cubic, Quart, Quint, Sine, Expo, Circ, Back, Elastic and Bounce, plus OutSpring, OutBackStrong, Smooth and Smoother.

## Effect catalog

| Category | Effects |
| --- | --- |
| Entrance | Elastic, FadeIn, PopIn, Slam, SlideIn, Typewriter, ZoomIn, Rise, Spiral, Grow, Domino, Flash |
| Loop | Bounce, Flicker, Float, Jelly, Pulse, Shake, Spin, Swing, Wave, Rain, Ripple, Dance, Zigzag, Orbit, Hover, Sway, Twinkle |
| Color | Blink, Chrome, ColorCycle, Duotone, Marker, Rainbow, Shine, Underline |
| Glitch | Chromatic, Corrupt, Echo, Glitch, Scramble, SliceDistortion, Static, Tear |
| Special | Confetti, Extrude, Karaoke, Neon, Retro, Sparkles |
| Exit | FadeOut, Shatter, ZoomOut, Evaporate, Fall |

For the authoritative catalog and exact parameters of your installed version, query the module:

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
print(AT.GetCatalog())
print(AT.GetInfo("Wave", "en"))
print(AT.GetPresetCatalog())
```

GetInfo returns Params, Defaults, Finite, Name, Kind, Description and Category metadata.

## Common examples

```lua
Text:Play("Wave", { Speed = 4, Amplitude = 8, Spacing = 0.7 })
Text:Play("Shake", { Intensity = 5, Mode = "Impact", Decay = 0.4 })
Text:Play("Rainbow", { Smooth = true, Palette = "Gold" })
Text:Play("Typewriter", { CharactersPerSecond = 30, Entrance = "Impact" })
Text:Play("Slam", { Target = "Word", FlashColor = "gold" })
Text:Play("FadeIn", { Mode = "Wipe", Duration = 0.8 })
```

## Presets

| Category | Names |
| --- | --- |
| Credits | CreditsBurst, CreditsDistortion, CreditsIn, CreditsLoop, CreditsOut, CreditsRole |
| Game | Achievement, CoinPickup, Combo, Countdown, CriticalHit, Damage, Heal, Legendary, LevelUp, LowHealth, QuestComplete, RareDrop, Reward |
| Interface | ButtonHover, Error, Highlight, Loading, Notification, Success, Toast, Tooltip |
| Title | Arcade, BossIntro, ChapterTitle, Cinematic, Defeat, Epic, GameOver, LevelStart, Synthwave, TitleDrop, Victory |
| Dialogue | Dialogue, Robot, Shout, Subtitle, Thought, Whisper |
| Special | Karaoke |
| Entrance | Assemble, Bloom, Materialize, Swoosh, Unfold |
| Exit | Blackout, Crumble, Disintegrate, Explosion, Vanish |
| Glitch | Corrupted, Cyberpunk, Decrypt, Hacker, SignalLost |
| Ambience | Dreamy, Fire, Frozen, Love, NeonSign, Starry, Toxic, Underwater, Windy |
| Horror | Bloody, Horror, Jumpscare, Possessed, Spooky, Whispers |
| Party | Birthday, Disco |

```lua
Text:PlayPreset("Horror", { Weight = 0.5 })
Text:PlayPreset("Dialogue", { CharactersPerSecond = 60 })
Text:PlayPreset("Notification", { Hold = 2.5 })
Text:PlayPreset("CreditsDistortion", { Hold = 3, Intensity = 1.3, Intro = false })
```

Most preset options are merged into constituent effects; presets with Build functions accept their own options. Continuous presets must be stopped or bounded.
