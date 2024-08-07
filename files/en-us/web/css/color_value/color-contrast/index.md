---
title: color-contrast()
slug: Web/CSS/color_value/color-contrast
page-type: css-function
status:
  - experimental
browser-compat: css.types.color.color-contrast
---

{{CSSRef}}{{SeeCompatTable}}

The **`color-contrast()`** functional notation takes a {{cssxref("color_value","color")}} value and compares it to a list of other {{cssxref("color_value","color")}} values, selecting the one with the highest contrast from the list.

## Syntax

```css
color-contrast(wheat vs tan, sienna, #d2691e)
color-contrast(#008080 vs olive, var(--myColor), #d2691e)
```

### Values

Functional notation: `color-contrast(color vs color-list)`

- `color`

  - : Any valid {{CSSXref("&lt;color&gt;")}}.

- `vs`

  - : A literal token as a component of the syntax.

- `color-list`

  - : A comma-separated list of at least two color values to compare with the first value.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- {{cssxref("color_value", "&lt;color>")}} data type
- [CSS colors](/en-US/docs/Web/CSS/CSS_colors) module
- [`prefers-contrast`](/en-US/docs/Web/CSS/@media/prefers-contrast) and [`prefers-color-scheme`](/en-US/docs/Web/CSS/@media/prefers-color-scheme) {{cssxref("@media")}} features
- [`contrast()`](/en-US/docs/Web/CSS/filter-function/contrast)
- [WCAG: color contrast](/en-US/docs/Web/Accessibility/Understanding_WCAG/Perceivable/Color_contrast)
- [CSS custom properties](/en-US/docs/Web/CSS/--*) and {{cssxref("var")}}
