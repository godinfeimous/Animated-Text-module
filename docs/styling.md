# Rich Text and typography

[Home](../README.md)

```lua
Text:SetRichText('<color=gold>Legendary</color> <wave speed=6>reward!</wave>')
Text:SetRichText('<gradient from=#00F0FF to=#FF00E6>NEON</gradient>')
Text:SetRichText('<glow color=#00FF88>Glow</glow>')
```

Supported tags: color, colour, font, gradient, stroke, outline, glow, b, strong, i, em, u, s, size, br, uppercase, uc, effect names and preset names. Nested styles override outer styles. Effects run on SetText unless PlayEffects is false. Static styles remain after stopping effects.

## Text parts

```lua
Text:Find("500 coins"):Color("gold"):Play("Shine")
Text:Word(2):Font("Bangers"):Size(1.4)
Text:Sentence("Last"):Rainbow()
Text:Range(1, 5):Underline()
Text:All():Glow("#00FFAA", 0.6)
Text:Highlight("danger", { Color = "red", Bold = true, Effect = "Shake" })
```

Parts support Play, Rainbow, Color, Gradient, Glow, Outline, Font, Bold, Italic, Underline, Strike, Size, Stop, Clear, Characters and Exists.

## Styling API

SetColor, SetGradient, SetOutline, SetGlow and ClearStyle accept selectors. SetFont accepts a font name, Enum.Font, Font or asset ID. SetBold, SetItalic, SetUnderline, SetStrike and SetSize change actual typography and reflow the layout. Size can be a multiplier or a pixel string such as "30px".

Colors accept Color3, BrickColor, hex, RGB, HSV, named colors and numeric tables. Palettes include gold, fire, ice, neon, toxic, sunset, ocean, candy, vapor, pastel, blood and rainbow.

```lua
Text:Play("Rainbow", { Scope = "Sentence", Smooth = true, Palette = "Vapor" })
Text:SetGradient("gold", { Words = 2 })
```

The original label's UIStroke and UIGradient are inherited by default. Horizontal gradients remain continuous across glyphs. Sentence detection recognizes common punctuation, while grapheme-aware indexing handles composed characters and emoji.
