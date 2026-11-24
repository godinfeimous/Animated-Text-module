# API reference

[Home](../README.md)

## Module

| Member | Description |
| --- | --- |
| new(label, options?) | Construct a text object; options include Renderer, Font, Text, Rich, RendererOptions |
| Create(label, options?) | Construct and apply initial styling, effects and highlights |
| Font(name, weight?, style?) | Resolve a Roblox Font |
| Version | Version string |
| RegisterEffect / GetEffect / HasEffect / GetEffects | Effect registry |
| RegisterPreset / GetPreset / HasPreset / GetPresets | Preset registry |
| GetInfo(name, language?) | Effect or preset metadata |
| GetCatalog / GetPresetCatalog | Categorized names |
| RegisterRenderer / GetRenderers | Renderer registry |
| RegisterFont / GetFont | Sprite font registry |
| RegisterEasing(name, fn) | Register a named easing |
| Prewarm(options?) | Preallocate glyphs and slices |
| GetStats() | StepMs, AverageMs, PeakMs, Texts |
| Credits / Preview | Credits and Studio preview utilities |
| Config / Fonts / Categories | Configuration and metadata |
| EffectKit / Fx / Decor | Custom effect helpers |

Additional utilities: Color, Gradient, Selection, Easing, Math, Signal, Maid, Parser, Unicode, Scheduler and Debug.

## Text object

| Method | Description |
| --- | --- |
| SetText(text, options?) / SetRichText(text) / GetText() | Replace or read text; Rich and PlayEffects control parsing |
| SetColor / SetGradient / SetOutline / SetGlow / ClearStyle | Static styles |
| SetFont / SetBold / SetItalic / SetUnderline / SetStrike / SetSize | Typography |
| Find / Word / Sentence / Line / Range / All / Highlight | Text parts |
| SetLetterSound(sound?, options?) | Configure letter sounds |
| PrepareSlices(count?, axis?) | Preallocate slices for this text |
| GetCharacters / GetWords / GetLines / GetSentences | Layout data |
| GetStyle / SetStyle / Refresh | Label style and layout |
| Play / PlayPreset / PlaySynced / Sequence | Start animation; returns handle |
| Stop(nameOrHandle?, fade?) / StopAll(fade?) | Stop playback |
| Skip(name?) | Finish immediately |
| Pause(name?) / Resume(name?) | Pause or resume |
| Reset() | Stop and restore base state |
| IsPlaying(name?) / GetHandles() | Active animation state |
| WaitUntilFinished() | Yield until no active animations remain |
| Destroy() | Clean up resources |

Properties: Label, Container.

## Signals

Started, Completed, TextFinished, EffectStarted(name, handle), EffectCompleted(name, handle, natural), CharacterShown(character, index), WordShown(word, index, voice?), LetterAppeared(character, index), TextChanged(text), LayoutChanged().

Completed fires when no animation remains. TextFinished additionally requires natural completion and fully visible text. Connections support Disconnect; signals support Connect, Once, Wait, Fire and DisconnectAll.

## Animation handle

Name, Config, Duration, Completed(natural); Stop(fade?), Skip(), Pause(), Resume(), IsPlaying(), IsPaused(), IsFinished(), GetStatus(), GetTime(), GetProgress(), SetConfig(options), Wait().

Status values: Pending, Playing, Paused, Completed, Stopped. Wait returns true for natural completion.

## Layout

Character data includes Index, Character, IsWhitespace, IsNewline, WordIndex, LineIndex, SentenceIndex, IndexInWord, IndexInLine, Base, Final and Instance. Word, line and sentence data include text, index, start/end indices, center and size. Positions and sizes are container-relative pixels.

## Types

AnimatedText, AnimationHandle, Effect, EffectContext, EffectConfig, EffectParam, CharacterData, WordData, LineData, SentenceData, SliceData, ModifierState, BaseState, Preset, SequenceStep, SpriteFont, Renderer, TextOptions, TargetType, StyleSelector.
