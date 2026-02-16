# Show Text Below Slideshow on Desktop

**Theme**: Dawn  
**Versions**: 15.4  
**Design time**: 15 minutes

This code snippet adds desktop text-below positioning controls for slideshow sections, mirroring the existing mobile behavior. Merchants can enable a toggle to position text below slideshow images on desktop and use a range slider (50-100%) to control text container width. The solution includes proper internationalization, and automatically adjusts text container height based on content to prevent layout shifts.

**Note:**
- **Layout behavior:** Text container height is determined by the slide with the most text to prevent cumulative layout shift when navigating between slides
- **Width recommendations:** For slides with varying text lengths, suggest narrower widths (60-70%) to maintain consistent line counts
- **Theme architecture:** Mirrors the existing mobile implementation (`.banner--mobile-bottom`) but applies to desktop breakpoints

---

## Before

![alt](https://screenshot.click/28-37-h2vhl-gcc8b.jpg)

## After

![alt](https://screenshot.click/28-39-8rzjv-f9m3y.jpg)

---

### Step 1

Open the **sections/slideshow.liquid** file

Add the **desktop-text-below** class and CSS variable to the slideshow component

- *Find* this:

```html
class="slider-mobile-gutter{% if section.settings.layout == 'grid' %} page-width{% endif %}{% if section.settings.show_text_below %} mobile-text-below{% endif %}"
```

- *Replace* it with this:

```html
class="slider-mobile-gutter{% if section.settings.layout == 'grid' %} page-width{% endif %}{% if section.settings.show_text_below %} mobile-text-below{% endif %}{% if section.settings.show_text_below_desktop %} desktop-text-below{% endif %}"
  {% if section.settings.show_text_below_desktop %}
    style="--desktop-text-width: {{ section.settings.desktop_text_width }}%;"
  {% endif %}
```

### Step 2

Continue in the **sections/slideshow.liquid** file

Add the **banner--desktop-bottom** class to the main slideshow container

- *Find* this:

```html
class="slideshow banner banner--{{ section.settings.slide_height }} grid grid--1-col slider slider--everywhere{% if section.settings.show_text_below %} banner--mobile-bottom{% endif %}{% if section.blocks.first.settings.image == blank %} slideshow--placeholder{% endif %}{% if settings.animations_reveal_on_scroll %} scroll-trigger animate--fade-in{% endif %}"
```

- *Replace* it with this:

```html
class="slideshow banner banner--{{ section.settings.slide_height }} grid grid--1-col slider slider--everywhere{% if section.settings.show_text_below %} banner--mobile-bottom{% endif %}{% if section.settings.show_text_below_desktop %} banner--desktop-bottom{% endif %}{% if section.blocks.first.settings.image == blank %} slideshow--placeholder{% endif %}{% if settings.animations_reveal_on_scroll %} scroll-trigger animate--fade-in{% endif %}"
```

### Step 3

Continue in the **sections/slideshow.liquid** file

Update the mobile setting label to use the new translation key

- *Find* this in the schema section:

```json
"label": "t:sections.slideshow.settings.show_text_below.label",
```

- *Replace* it with this:

```json
"label": "t:theme_support.settings.slideshow.show_text_below_mobile.label",
```

### Step 4

Continue in the **sections/slideshow.liquid** file

Add the Desktop header section with checkbox and width slider settings

Underneath the `show_text_below` setting that you just modified, add this new setting on a new line after the closing `},`:

```json
{
  "type": "header",
  "content": "t:theme_support.settings.slideshow.desktop_header"
},
{
  "type": "checkbox",
  "id": "show_text_below_desktop",
  "label": "t:theme_support.settings.slideshow.show_text_below_desktop.label",
  "default": false
},
{
  "type": "range",
  "id": "desktop_text_width",
  "min": 50,
  "max": 100,
  "step": 5,
  "unit": "%",
  "label": "t:theme_support.settings.slideshow.desktop_text_width.label",
  "info": "t:theme_support.settings.slideshow.desktop_text_width.info",
  "default": 80
},
```

### Step 5

Open the **assets/section-image-banner.css** file

Add CSS rules that enable desktop text-below positioning by making banner media relative, assigning explicit heights, removing absolute positioning from text wrapper, and controlling width via CSS variable

- At the bottom of the file, *add* this:

```css
@media screen and (min-width: 750px) {
  .banner--desktop-bottom .banner__media {
    position: relative;
  }

  .banner--small.banner--desktop-bottom:not(.banner--adapt) .banner__media {
    height: 42rem;
  }

  .banner--medium.banner--desktop-bottom:not(.banner--adapt) .banner__media {
    height: 56rem;
  }

  .banner--large.banner--desktop-bottom:not(.banner--adapt) .banner__media {
    height: 72rem;
  }

  .banner--desktop-bottom.banner--adapt .banner__media:not(.placeholder) {
    height: auto;
  }

  .banner--desktop-bottom .slideshow__text-wrapper.banner__content {
    position: relative;
    height: auto;
    min-height: auto;
    padding: 2rem 5rem 2rem 5rem;
  }

  .desktop-text-below .banner--desktop-bottom .slideshow__text-wrapper.banner__content {
    padding-left: 0;
    padding-right: 0;
  }

  .desktop-text-below .banner--desktop-bottom .slideshow__text.banner__box {
    padding-top: 2rem;
    padding-bottom: 2rem;
    max-width: var(--desktop-text-width, 80%);
    min-width: 0;
    width: auto;
  }

  .banner--desktop-bottom.banner--small:not(.banner--adapt),
  .banner--desktop-bottom.banner--medium:not(.banner--adapt),
  .banner--desktop-bottom.banner--large:not(.banner--adapt) {
    min-height: 0;
  }
}
```

### Step 6

Open the **locales/en.default.schema.json** file

If `"theme_support": {` already exists, only add the new keys under the existing `theme_support.settings.slideshow` object. If it doesn't exist, add the full block below. See [adding TS translations](https://github.com/Shopify/ts-code-snippets/issues/172) for more.

```json
"theme_support": {
  "settings": {
    "slideshow": {
      "show_text_below_mobile": {
        "label": "Show text below slideshow on mobile"
      },
      "desktop_header": "Desktop",
      "show_text_below_desktop": {
        "label": "Show text below slideshow on desktop"
      },
      "desktop_text_width": {
        "label": "Desktop text container width",
        "info": "Only applies when 'Show text below slideshow on desktop' is enabled"
      }
    }
  }
}
```

### Step 7

Merchant instructions for using the new settings

#### How to enable

1. Go to Online Store → Themes → Edit theme
2. Select the Slideshow section
3. Scroll to the Desktop section in settings
4. Enable "Show text below slideshow on desktop"
5. Adjust "Desktop text container width" slider (50-100%)

**Tip:** For slides with varying text lengths, use narrower widths (60-70%) to maintain consistent line counts.
