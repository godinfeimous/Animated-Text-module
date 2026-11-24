# Changelog

## 3.0.0

- 31 new effects and 51 new presets, bringing the documented totals to 56 and 77.
- Improved existing effects with shadows, trails, chromatic separation, glow and particles.
- Ten karaoke modes, duets, synchronized sound clocks and per-letter audio.
- Text parts, per-character typography, rich tags and the Create convenience API.
- Anti-overlap, reduced motion, localization and additional glyph layers.
- Bounded measurement cache, cleanup of held animations and automatic decoration cleanup.
- Improved credit-card positioning and modular organization.
- EffectKit, Fx and Decor helpers for custom effects.

### Migration from 2.x

Categories returned by GetInfo and GetCatalog are now English identifiers. Localized names remain available through CategoryLabel and Categories.Label. Description is English by default and DescriptionEs provides Spanish. Spanish category aliases remain accepted during registration.

## 2.1

- Redesigned demonstration interface.
- Credits support per-card, per-group and per-name options and effects.
- Rich Text can apply styles without running effect tags.
- Prewarm, PrepareSlices and GetStats added.
- English and Spanish documentation bundled with the module.
