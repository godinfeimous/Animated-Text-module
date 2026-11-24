# Audio and karaoke

[Home](../README.md)

## Karaoke timing

```lua
Text:SetText("Under the neon moon")
Text:Play("Karaoke", { WordsPerSecond = 2.5 })
-- Alternatively:
Text:Play("Karaoke", { Starts = { 0, 0.45, 0.7, 1.2 } })
```

Use Duration for length-weighted timing, Timings for per-word durations, or Starts for absolute word start times. Timings and Starts also accept strings of numbers.

## Modes

Wipe (default continuous fill), Word (whole word), Character (letter by letter), Fill (bottom-up), Pop (spring), Glow (halo), Bounce (bouncing ball), Underline (progress bar), Reveal (appearance) and Wave (animated sung text).

## Sync to sound

```lua
Text:PlaySynced("Karaoke", song, {
    Starts = { 0, 0.45, 0.7, 1.2 },
    Mode = "Glow",
})
```

PlaySynced starts the Sound if needed. Any effect can follow Sound.TimePosition using Sound and SoundOffset. Playback speed, pauses and seeking are reflected in the animation clock.

Use Voices for per-word voice IDs or LineVoices for per-line IDs. VoiceColors assigns colors to each voice. Other options include SungColor, UnsungColor, UnsungTransparency, Softness, ActiveScale, ActiveLift, ActiveGlow and Bounce.

```lua
Text.WordShown:Connect(function(word, index, voice)
    print(word, index, voice)
end)
Text:SetLetterSound("rbxasset://sounds/volume_slider.ogg", {
    Volume = 0.4,
    Pitch = { 0.9, 1.1 },
})
Text:Play("Typewriter")
```

Letter sound options: Volume, Pitch, Every, MinInterval and Voices. SetLetterSound(nil) disables it. LetterAppeared fires for any entrance effect. Use audio assets you have permission to use.
