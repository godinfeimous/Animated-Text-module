# Troubleshooting

[Home](../README.md) · [API reference](api.md)

| Symptom | What to check |
| --- | --- |
| No animation | ModuleScript is in ReplicatedStorage; LocalScript is running in a client UI; the effect name is valid. |
| Wait never returns | Wave, Rainbow, Float and many presets are continuous. Give them Duration or stop them explicitly. |
| TextFinished never fires | All animations must complete naturally and the text must be fully visible. Stop does not count as natural completion. |
| Rich Text tags appear literally | Use SetRichText or SetText with Rich = true. |
| Unexpected animations after replacing text | SetText stops active effects but does not stop sequences. StopAll first if replacing a sequence. |
| Sound and subtitles drift | Use Sound or PlaySynced, and verify Starts, Times and SoundOffset against your recording. |
| Distorted letters do not rotate | Roblox cannot clip rotated descendants; slice effects disable glyph rotation. |
| Effects compete visually | Shared gradient, Shadow and Echo layers have documented priority rules. Avoid combining incompatible slice settings. |
| Large scenes stutter | Prewarm, PrepareSlices, reduce active glyph counts, pause hidden animations and avoid CanvasGroup. |
| Original text is hidden | AnimatedText replaces its display with glyphs. Destroy restores the original label. |

## Check the installed version

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
print(AT.Version)
print(AT.GetStats())
print(AT.GetInfo("Typewriter", "en"))
```

When reporting a problem, include the module version, the minimal reproduction script, the effect name and configuration, relevant UI properties, and any Studio Output errors. Never share private credentials or the purchased module publicly.

## Memory growth or accumulating animations

Use the [memory management and stress-testing guide](memory-management.md). Inspect active handles, verify that temporary text objects are destroyed, and check for external event connections and tasks that retain references. A warmed pool or cache can retain memory without being a leak.
