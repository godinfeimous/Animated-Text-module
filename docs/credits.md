# Cinematic credits

[Home](../README.md) · [Effects](effects.md)

AnimatedText includes a credit-roll utility inspired by the typography and distortion transitions of Resident Evil Requiem. It provides a distorted entrance, a readable hold phase and a ramping exit.

## Create a roll

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Roll = AT.Credits.new(script.Parent, {
    Hold = 2.6,
    Intensity = 1,
})

Roll:Play({
    { Role = "Director", Names = { "Your Name" } },
    {
        { Role = "Lead Developer", Names = { "Developer One" } },
        { Role = "Art Director", Names = { "Artist One", "Artist Two" } },
        Layout = "Center",
        Background = "#120000",
    },
}):Wait()
```

The parent can be a ScreenGui or GuiObject. A card is a single group or a list of up to three groups.

## Options

| Option | Default | Meaning |
| --- | --- | --- |
| Background | Black | Set false for transparency |
| NameFont | PermanentMarker | Name typography |
| RoleFont | GothamBold | Role typography |
| NameSize | 0.07 | Screen-height fraction, or pixels if >= 1 |
| RoleSize | 0.024 | Screen-height fraction, or pixels if >= 1 |
| NameColor | RGB 245,245,245 | Name color |
| RoleColor | RGB 225,225,225 | Role color |
| Hold | 2.6 | Clean phase in seconds |
| InDuration | 0.4 | Entrance duration |
| OutDuration | 1.25 | Exit duration |
| Gap | 0.5 | Black interval between cards |
| EffectBlend | 0.35 | Effect blend time |
| Intensity | 1 | Distortion multiplier |
| Layout | Scatter | Scatter or Center |
| Loop | false | Repeat the roll |
| Seed | nil | Reproducible placement and distortion |
| ZIndex | 10 | Layer order |

## Customization

Group options: Role, Names, Effect, RoleEffect, Hold, Scale and Intensity. Individual name objects accept Text and Effect. Cards accept Background, Layout and Hold.

```lua
Roll:Play({
    {
        Role = "Animation",
        Names = {
            { Text = "Neon", Effect = "NeonSign" },
            { Text = "Fire", Effect = "Fire" },
        },
    },
    {
        Role = "Special Thanks",
        Names = { "<color=gold>Everyone</color>" },
        Effect = {
            { "Wave", { Amplitude = 4 } },
            { "Shine" },
        },
    },
})
```

Effects apply during the clean phase and blend out before the exit distortion.

## Playback API

- Roll:Play(cards, startIndex?) returns a handle with Wait, IsFinished and Stop.
- Roll:PlayCard(card) plays one card and yields.
- Roll:Stop() stops playback.
- Roll:Destroy() releases the roll.
- Roll.CardIndex is the current index.
- Roll.CardStarted(index, count) and Roll.Completed(natural) are signals.

Reuse the roll rather than creating one per card. Construct it ahead of a cinematic to give the preallocation time to complete.

## Single-label credits

Use CreditsDistortion for a complete card animation, CreditsIn for the entrance, CreditsOut for the exit, CreditsBurst for a short burst, CreditsLoop for repeated distortion or CreditsRole for the role text.

```lua
Text:PlayPreset("CreditsDistortion", {
    Hold = 3,
    Intensity = 1.2,
    Intro = false,
})
```
