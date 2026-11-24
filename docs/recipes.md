# Practical recipes

[Home](../README.md) · [API](api.md) · [Effects](effects.md)

Each example assumes AnimatedText is in ReplicatedStorage and runs on the client.

## Dialogue with click-to-skip

Place a LocalScript under a TextButton. Clicking while the dialogue is typing reveals it immediately; clicking again advances.

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Button = script.Parent
local Text = AT.new(Button)

local Lines = {
    "Something is <color=red>wrong</color>.",
    "Do not open the door.",
    "<color=gold>Stay close.</color>",
}

local Index = 1
local Handle

local function Show()
    Text:SetRichText(Lines[Index])
    Handle = Text:Play("Typewriter", {
        CharactersPerSecond = 28,
        Entrance = "Impact",
        EntranceTime = 0.12,
        PunctuationDelay = 0.18,
    })
end

Button.Activated:Connect(function()
    if Handle and not Handle:IsFinished() then
        Handle:Skip()
        return
    end

    Index = Index % #Lines + 1
    Show()
end)

Show()
```

Use LetterAppeared for sound cues and TextFinished for dialogue progression. Avoid permanent background effects when waiting for TextFinished: it requires all animations to finish naturally.

## Reward notification

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Text = AT.new(script.Parent)

local function ShowReward(Amount: number)
    Text:SetRichText(string.format("You earned <gradient=gold>%d coins</gradient>!", Amount))
    Text:Sequence({
        { Effect = "PopIn", Config = { Stagger = 0.02, Glow = 0.5 } },
        { Parallel = {
            { Effect = "Wave", Config = { Amplitude = 3, Match = tostring(Amount) } },
            { Effect = "Shine" },
        }, Duration = 2 },
        { Effect = "FadeOut", Config = { Mode = "Wipe" } },
    })
end

ShowReward(500)
```

Keep rewards and currency validation on the server; only animate their presentation on the client.

## Interactive button

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Button = script.Parent
local Text = AT.new(Button)
local Hover

Button.MouseEnter:Connect(function()
    if Hover then Hover:Stop() end
    Hover = Text:PlayPreset("ButtonHover")
end)

Button.MouseLeave:Connect(function()
    if Hover then
        Hover:Stop(0.2)
        Hover = nil
    end
end)

Button.Activated:Connect(function()
    Text:Play("Shake", { Mode = "Impact", Intensity = 3 })
end)
```

Mouse hover is optional on touch devices; Activated supports touch and other input methods.

## Voice-synchronized subtitles

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Text = AT.new(script.Parent)
local Voice = script.Parent:WaitForChild("Voice")

Text:SetRichText('<color=gray>WE</color> <color=red>ARE</color> <color=gold>HERE</color>')
Text:PlaySynced("Typewriter", Voice, {
    Times = { 0, 0.07, 0.14, 0.24, 0.32, 0.40, 0.48, 0.59, 0.67, 0.75, 0.83 },
    Entrance = "Impact",
})
```

Times are illustrative character timestamps; author them against your own recording. This example assumes a Sound named Voice exists under the label. See [audio](audio.md) for karaoke and synchronized effects.
