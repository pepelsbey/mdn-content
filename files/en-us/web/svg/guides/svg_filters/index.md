---
title: SVG filters
slug: Web/SVG/Guides/SVG_filters
page-type: guide
sidebar: svgref
---

SVG filters take the pixels an element would have painted and process them before they reach the screen: blurring, offsetting, recoloring, compositing, or generating new imagery altogether. They can be chained into multi-step pipelines, and they apply to both SVG and HTML content.

A filter is defined with the {{SVGElement("filter")}} element, which is never rendered itself. Give it an `id`, put it anywhere in the document — by convention inside a {{SVGElement("defs")}} element — and reference it, either from the {{SVGAttr("filter")}} attribute on an SVG element or from the {{cssxref("filter")}} CSS property on an SVG or HTML element:

```html
<rect width="100" height="100" filter="url(#blur-me)" />
```

```css
.blurred {
  filter: url("#blur-me");
}
```

> [!NOTE]
> Safari supports the CSS `filter` property on HTML elements but not on SVG elements, so use the `filter` attribute when filtering SVG content. See the [browser compatibility table](/en-US/docs/Web/CSS/Reference/Properties/filter#browser_compatibility) for the `filter` property.

## A first filter

Inside the `<filter>` element you list _filter primitives_ — the `fe*` elements, where `fe` stands for "filter effect". Each primitive performs one operation. The simplest useful filter contains a single primitive, {{SVGElement("feGaussianBlur")}}, whose {{SVGAttr("stdDeviation")}} attribute controls how much blur is applied:

```html
<svg
  viewBox="0 0 200 100"
  width="200"
  height="100"
  xmlns="http://www.w3.org/2000/svg">
  <defs>
    <filter id="blur-me">
      <feGaussianBlur stdDeviation="4" />
    </filter>
  </defs>

  <circle cx="50" cy="50" r="40" fill="steelblue" />
  <circle cx="150" cy="50" r="40" fill="steelblue" filter="url(#blur-me)" />
</svg>
```

{{EmbedLiveSample("A_first_filter", "100%", 130)}}

Both circles are identical; the second one references the filter, so it is painted, blurred, and then composited back. Raising `stdDeviation` spreads the blur further — far enough, and it gets clipped by the filter region.

## How a filter pipeline works

A filter with more than one primitive is a pipeline. Each primitive takes one or two images as input, and produces one image as output:

- {{SVGAttr("in")}} names the input. {{SVGAttr("in2")}} names the second input of the primitives that combine two images, such as {{SVGElement("feBlend")}} and {{SVGElement("feComposite")}}.
- {{SVGAttr("result")}} names the output so that a later primitive can refer to it. `result` is not an `id`: the name is only visible inside the same `<filter>` element.
- If a primitive has no `in`, it uses `SourceGraphic` when it is the first primitive in the filter, and the result of the preceding primitive otherwise. So a chain of primitives that all omit `in` and `result` runs in document order, each one processing the output of the last.

The primitives are always applied in document order, and a `result` can only be referenced by primitives that come after it.

```html
<filter id="pipeline">
  <!-- Blur the alpha channel of the source, and call it "blur" -->
  <feGaussianBlur in="SourceAlpha" stdDeviation="3" result="blur" />
  <!-- Shift that blur down and to the right -->
  <feOffset in="blur" dx="4" dy="4" result="offsetBlur" />
  <!-- Paint the source graphic back on top of it -->
  <feMerge>
    <feMergeNode in="offsetBlur" />
    <feMergeNode in="SourceGraphic" />
  </feMerge>
</filter>
```

## Filter inputs

Besides the `result` of an earlier primitive, `in` and `in2` accept these keywords:

- `SourceGraphic`
  - : The element being filtered, as it would have been painted.
- `SourceAlpha`
  - : The same thing, but only its alpha channel — a solid black silhouette of the element. This is the usual starting point for shadows.
- `FillPaint` and `StrokePaint`
  - : Infinite planes painted with the element's {{SVGAttr("fill")}} and {{SVGAttr("stroke")}} values. Only Firefox implements them, so treat them as unavailable in practice.
- `BackgroundImage` and `BackgroundAlpha`
  - : The backdrop behind the filter region, and its alpha channel. No browser implements them, so treat them as unavailable in practice.

> [!NOTE]
> `BackgroundImage` and `BackgroundAlpha` were introduced in SVG 1.1 to give a filter access to what is painted behind the element, controlled by an `enable-background` property. The Filter Effects specification still defines both keywords, but redefines them in terms of the CSS {{cssxref("isolation")}} property and [no longer supports `enable-background`](https://drafts.csswg.org/filter-effects-1/#AccessBackgroundImage). Since no browser has ever implemented either keyword, composite an element with other imagery by bringing that imagery into the filter explicitly with {{SVGElement("feImage")}}.

## The filter region

A filter is only evaluated inside its _filter region_. Anything the filter would paint outside that region is clipped, which is the most common reason for a filter that "half works": a blur or an offset shadow gets cut off at a hard edge.

The region is set by the `x`, `y`, `width`, and `height` attributes on `<filter>`. They default to `-10%`, `-10%`, `120%`, and `120%`, giving a 10% margin around the element's bounding box on every side, because so many effects spread beyond the element itself. Effects that reach further than that need a bigger region:

```html
<filter id="big-shadow" x="-30%" y="-30%" width="160%" height="160%">
  <feDropShadow dx="10" dy="10" stdDeviation="5" />
</filter>
```

Two attributes control how the numbers are interpreted:

- {{SVGAttr("filterUnits")}} — whether the region's `x`, `y`, `width`, and `height` are fractions of the element's bounding box (`objectBoundingBox`, the default, which is what makes percentages meaningful) or lengths in the current user coordinate system (`userSpaceOnUse`).
- {{SVGAttr("primitiveUnits")}} — the same choice for the values used _inside_ the primitives, such as `stdDeviation` and `dx`. The default is `userSpaceOnUse`.

Individual primitives also accept `x`, `y`, `width`, and `height`, which restrict that one operation to a _primitive subregion_ — useful for applying an effect to part of an element, and for pinning down the extent of primitives such as {{SVGElement("feFlood")}} and {{SVGElement("feTile")}} that would otherwise fill the whole region.

## Color interpolation

Filter operations are performed on premultiplied color values in a color space chosen by the {{SVGAttr("color-interpolation-filters")}} property, whose initial value is `linearRGB`. Linearized color is more physically accurate — a blur or a composite in `linearRGB` behaves the way light behaves — but it does not match what CSS filter functions, image editors, or most people's expectations produce, and the difference is very visible in blurs, gradients, and color mixes.

Set the property to `sRGB` on the `<filter>` element (or on an individual primitive) when you want results that match CSS:

```html
<filter id="grayscale" color-interpolation-filters="sRGB">
  <feColorMatrix type="saturate" values="0" />
</filter>
```

## Filter primitives

| Primitive                             | What it does                                                                                             |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| {{SVGElement("feBlend")}}             | Combines two inputs with a blend mode, like the CSS {{cssxref("mix-blend-mode")}} property.              |
| {{SVGElement("feColorMatrix")}}       | Transforms the RGBA channels with a matrix: recolor, drain saturation, adjust alpha.                     |
| {{SVGElement("feComponentTransfer")}} | Remaps each channel through a function, defined by its `feFuncR`/`feFuncG`/`feFuncB`/`feFuncA` children. |
| {{SVGElement("feComposite")}}         | Combines two inputs with Porter-Duff operators (`over`, `in`, `out`, `atop`, `xor`) or arithmetic.       |
| {{SVGElement("feConvolveMatrix")}}    | Applies a convolution kernel: sharpen, emboss, edge detection.                                           |
| {{SVGElement("feDiffuseLighting")}}   | Lights the input as a diffuse surface, using its alpha channel as a bump map.                            |
| {{SVGElement("feDisplacementMap")}}   | Displaces the pixels of one input using the channel values of another.                                   |
| {{SVGElement("feDropShadow")}}        | A blurred, offset, colored copy of the input behind it — a whole shadow in one primitive.                |
| {{SVGElement("feFlood")}}             | Fills the region with a solid color and opacity.                                                         |
| {{SVGElement("feGaussianBlur")}}      | Blurs the input.                                                                                         |
| {{SVGElement("feImage")}}             | Brings an external image, or an element of the same document, into the pipeline.                         |
| {{SVGElement("feMerge")}}             | Stacks any number of inputs, given as `feMergeNode` children, in document order.                         |
| {{SVGElement("feMorphology")}}        | Fattens (`dilate`) or thins (`erode`) the input.                                                         |
| {{SVGElement("feOffset")}}            | Shifts the input by `dx` and `dy`.                                                                       |
| {{SVGElement("feSpecularLighting")}}  | Adds a specular highlight, again using alpha as a bump map.                                              |
| {{SVGElement("feTile")}}              | Repeats a primitive subregion to fill a larger area.                                                     |
| {{SVGElement("feTurbulence")}}        | Generates Perlin noise — clouds, marble, paper, and other textures.                                      |

Firefox supports `feImage` only for external images, not for references to an element in the same document ([Firefox bug 455986](https://bugzil.la/455986)). Where you need an element as a filter input in every browser, export it as a standalone image file and reference that instead.

The lighting primitives take a light-source child: {{SVGElement("feDistantLight")}}, {{SVGElement("fePointLight")}}, or {{SVGElement("feSpotLight")}}.

## Recipes

### Drop shadows

{{SVGElement("feDropShadow")}} does the whole job in one primitive, with `dx`, `dy`, and `stdDeviation` for the geometry and the `flood-color` and `flood-opacity` properties for the color. Build a shadow by hand only when you need something the shorthand can't express, such as reusing the blurred silhouette for another effect.

The following example builds the same half-transparent shadow twice. The first filter uses `feDropShadow`; the second hand-rolls it, blurring the alpha channel, offsetting it, fading it to 50% with {{SVGElement("feComponentTransfer")}}, and merging the source graphic back on top. Both widen the filter region so the shadow isn't clipped.

```html
<svg
  viewBox="0 0 240 120"
  width="240"
  height="120"
  xmlns="http://www.w3.org/2000/svg">
  <defs>
    <filter id="shadow-shorthand" x="-30%" y="-30%" width="160%" height="160%">
      <feDropShadow
        dx="4"
        dy="4"
        stdDeviation="3"
        flood-color="black"
        flood-opacity="0.5" />
    </filter>

    <filter id="shadow-by-hand" x="-30%" y="-30%" width="160%" height="160%">
      <feGaussianBlur in="SourceAlpha" stdDeviation="3" result="blur" />
      <feOffset in="blur" dx="4" dy="4" result="offsetBlur" />
      <feComponentTransfer in="offsetBlur" result="fadedBlur">
        <feFuncA type="linear" slope="0.5" />
      </feComponentTransfer>
      <feMerge>
        <feMergeNode in="fadedBlur" />
        <feMergeNode in="SourceGraphic" />
      </feMerge>
    </filter>
  </defs>

  <rect
    x="25"
    y="25"
    width="70"
    height="70"
    fill="lightskyblue"
    filter="url(#shadow-shorthand)" />
  <rect
    x="145"
    y="25"
    width="70"
    height="70"
    fill="lightskyblue"
    filter="url(#shadow-by-hand)" />
</svg>
```

{{EmbedLiveSample("Drop_shadows", "100%", 150)}}

The two squares look the same, which is the point: `feDropShadow` is a shorthand for that four-primitive chain. Hand-rolling it only pays off when you need one of the intermediate results — `fadedBlur`, say — for something else as well.

### Recoloring with feColorMatrix

{{SVGElement("feColorMatrix")}} multiplies every pixel's RGBA values by a matrix of 20 numbers — four rows, one per output channel, of five columns: `R`, `G`, `B`, `A`, and a constant. The shorthand `type` values cover the common cases — `saturate`, `hueRotate`, `luminanceToAlpha` — while `type="matrix"` gives full control. This filter converts to grayscale using the luminance coefficients, in `sRGB` so the result matches the CSS {{cssxref("filter-function/grayscale", "grayscale()")}} function:

```html
<svg
  viewBox="0 0 240 120"
  width="240"
  height="120"
  xmlns="http://www.w3.org/2000/svg">
  <defs>
    <filter id="to-grayscale" color-interpolation-filters="sRGB">
      <feColorMatrix
        type="matrix"
        values="0.2126 0.7152 0.0722 0 0
                0.2126 0.7152 0.0722 0 0
                0.2126 0.7152 0.0722 0 0
                0      0      0      1 0" />
    </filter>
  </defs>

  <g id="circles">
    <circle cx="45" cy="45" r="30" fill="crimson" />
    <circle cx="75" cy="75" r="30" fill="seagreen" opacity="0.8" />
    <circle cx="30" cy="80" r="25" fill="orange" opacity="0.8" />
  </g>

  <g filter="url(#to-grayscale)" transform="translate(120, 0)">
    <use href="#circles" />
  </g>
</svg>
```

{{EmbedLiveSample("Recoloring_with_feColorMatrix", "100%", 150)}}

Each row of the matrix sets one output channel from a weighted sum of the input channels. The three color rows are identical, so red, green, and blue all end up at the same luminance value, while the alpha row (`0 0 0 1 0`) passes transparency through untouched.

## SVG filters and CSS filter functions

The CSS {{cssxref("filter")}} property accepts both a `url()` reference to an SVG filter and a list of [filter functions](/en-US/docs/Web/CSS/Reference/Values/filter-function) — {{cssxref("filter-function/blur", "blur()")}}, {{cssxref("filter-function/drop-shadow", "drop-shadow()")}}, {{cssxref("filter-function/grayscale", "grayscale()")}}, and the rest. The filter functions are shorthands for exactly these primitives, so:

- Reach for a filter function when one exists for what you need. It is shorter, it can be animated and interpolated by CSS, and it operates in `sRGB` with no surprises.
- Reach for an SVG filter when you need a pipeline: several operations chained together, two inputs composited, generated noise or lighting, or fine control over the color space and filter region.

Filtering is expensive: the element is rendered to an offscreen buffer, processed, and composited back, and blurs cost more the larger their `stdDeviation`. Applying a filter to large or frequently repainted areas — anything scrolling, animating, or hovering across a big surface — is a common cause of jank. Keep filter regions no bigger than necessary, and prefer animating cheap properties over animating filter parameters.

## See also

- {{SVGElement("filter")}}
- {{SVGAttr("in")}}, {{SVGAttr("result")}}, {{SVGAttr("color-interpolation-filters")}}
- {{cssxref("filter")}}
- [Filter primitive elements](/en-US/docs/Web/SVG/Reference/Element#filter_primitive_elements): reference for the `fe*` elements
- [Applying SVG effects to HTML content](/en-US/docs/Web/SVG/Guides/Applying_SVG_effects_to_HTML_content)
- [Filter effects](/en-US/docs/Web/SVG/Tutorials/SVG_from_scratch/Filter_effects): filters chapter of the SVG tutorial
- [Using filter effects](/en-US/docs/Web/CSS/Guides/Filter_effects)
