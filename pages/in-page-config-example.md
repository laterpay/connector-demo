---
layout: page
show_meta: false
title: "Example page for in-page configuration"
subheadline: "How to use in-Page Configuration with the LaterPay Connector"
teaser: "<p>See, how to use in-Page Configuration with the LaterPay Connector by inspecting the &lt;meta&gt; tags in this page's source code.</p><p>And/or &quot;buy&quot; article access (it's just the sandbox environment), which reveals the documentation exerpt about in-page merchant configuration.</p>"
header:
   image_fullwidth: "header_homepage_13.jpg"
permalink: "/in-page-config-example/"
head_include_file: "example/in-page-config.html"
laterpay:
    connector:
        locale: "de-de"    
        use_dev_styles: true

---

### In-page merchant configuration

The Connector is designed to be configured through the Connector UI webinterface.
However, to meet the demands of some merchants, there is a way to customize and overwrite parts of the configuration from the surrounding webpage.

The format is similar to OpenGraph and uses `<meta>` tags. These are valid examples of in-page configuration definitions:

```html
<meta property="laterpay:connector:article_id" content="my_custom_article_id">
<meta property="laterpay:connector:appearance:purchase_overlay:variant" content="raw-white">
```

#### `article_id`

Merchants can provide a custom article ID. This feature is intended to be used with multi-page articles and in other cases, where the URL is unsuitable  as an article ID.

Example:

```html
<meta property="laterpay:connector:article_id" content="my_custom_article_id">
```

This sets the `article_id` to "my_custom_article_id".

#### `callbacks`

Merchants can register named global functions that are called on certain events. Right now, only one event is supported.

- `on_user_has_access`

    If a user should have access to the paid content, this callback function is called.

    Example:

    ```html
    <meta property="laterpay:connector:callbacks:on_user_has_access" content="myGlobalCallbackFunction">

    <script>
      myGlobalCallbackFunction() {
        alert("Yay, you have access to this content!");
      }
    </script>
    ```

    This calls the `myGlobalCallbackFunction()` function, if the user has access to the paid content on the current page.

#### `appearance`

##### `advantage_overlay`

Merchants can customize the AdVantage overlay.

- `logo_url`

    The AdVantage overlay has no logo on top, by default. A custom logo can be added, which is referenced by an external URL to the image file.

    Custom logos can be free in their dimensions as they are being resized by CSS to fit into the layout. This enables high-res logos for retina displays, too.

    Example:

    ```html
    <meta property="laterpay:connector:appearance:advantage_overlay:logo_url" content="http://www.example.com/img/logo.jpg">
    ```
    This adds a custom logo at the top of the AdVantage overlay, loaded from "http://www.example.com/img/logo.jpg".

##### `purchase_overlay`

Merchants can customize different aspects of the purchase overlay, these aspects are represented as properties of the `purchase_overlay` object.

- `variant`

    There are two _variants_ of the purchase overlay, `default` and `raw-white`.

    Example:

    ```html
    <meta property="laterpay:connector:appearance:purchase_overlay:variant" content="raw-white">
    ```

    This sets the purchase overlay variant to "raw-white".

- `show_single_item_purchase_list`

     If there is only one purchase item (`Premium content` and/or `TimePass`) available for an article, the purchase list is optional. It's being displayed by default, but merchants can choose to hide it.

     _Note, that this works exclusively for purchase lists, which contain only a *single* item._

     Example:

     <meta property="laterpay:connector:appearance:purchase_overlay:show_single_item_purchase_list" content="false">

     This hides single item purchase lists.

- `logo_url`

    The purchase overlay has no logo on top, by default. A custom logo can be added, which is referenced by an external URL to the image file.

    Custom logos can be free in their dimensions as they are being resized by CSS to fit into the layout. This enables high-res logos for retina displays, too.

    Example:

    ```html
    <meta property="laterpay:connector:appearance:purchase_overlay:logo_url" content="http://www.example.com/img/logo.jpg">
    ```
    This adds a custom logo at the top of the purchase overlay, loaded from "http://www.example.com/img/logo.jpg".

- `show_payment_methods`

    The payment methods are being displayed at the bottom of the purchase overlay, by default. Merchants can choose to remove them.

    Example:

    ```html
     <meta property="laterpay:connector:appearance:purchase_overlay:show_payment_methods" content="false">
    ```

    This removes the payment methods.

- `primary_color`

    The _primary color_ defines the background color of the purchase button. Merchants can override the default green with a color of their choice.

    Example:

    ```html
    <meta property="laterpay:connector:appearance:purchase_overlay:primary_color" content="#FF0000">
    ```

    This results in a red purchase button.

- `secondary_color`

    The _secondary color_ defines the text color of the purchase button. Merchants can override the default dark gray with a color of their choice.

    Example:

    ```html
    <meta property="laterpay:connector:appearance:purchase_overlay:secondary_color" content="#00FF00">
    ```

    This results in green text on the purchase button.

##### `translations`

Merchants can provide their own translations for all translatable strings in the UI. They are identified by _translation keys_ of the format `$component:$element`.

These are the currently available elements _which have defaults_ and their en-US translations in JSON format (copied from the en-US locale in /src/app/i18n/en-us/locale.json):

```json
{
  "purchase_overlay": {
    "heading": "Read now, pay later",
    "powered_by": "Powered by",
    "current_article": "This article",
    "open_quotes": "“",
    "close_quotes": "”"
  },
  "dialog_overlay": {
    "close_btn_title": "Close dialog"
  },
  "purchase_button": {
    "label": "Buy now",
    "title": "Buy now with LaterPay",
    "label_with_price": "Buy now for %s",
  },
  "advantage": {
    "heading": "This website is financed by ads.<br>The content is not accessible for free with an active adblocker.",
    "allow_ads_heading": "Allow ads on this website",
    "allow_ads_description": "This dialog will disappear after you have disabled your Adblocker and reload the page.",
    "adblocker_deactivation_description": "Reload Page",
    "splitbar_label": "OR",
    "no_ads_heading": "Access ad-free with your adblocker",
    "powered_by": "Powered by"
  }
}
```

_Note, that the `purchase_button:label_with_price` translation is interpolated so that it can contain a price string, which is represented by a `%s` sequence in the string. This feature can be used by merchants, too._

The following elements don't have defaults. They are hidden, unless the merchants defines translations for them:

```json
{
  "purchase_overlay": {
    "body_text": undefined,
    "notes_text": undefined,
    "footer_text": undefined
  }
}
```

To protect merchants against XSS attacks, all translation strings are sanitized using the [DOMPurify](https://github.com/cure53/DOMPurify) lib. However, we allow some tags and attributes:

###### Allowed HTML tags in translation strings

```json
[ "a", "img", "strong", "em", "b", "i", "ul", "ol", "li", "br" ]
```

###### Allowed HTML attributes in translation strings

```json
[ "href", "class", "src" ]
```

Example 1:

```html
  <meta property="laterpay:connector:translations:purchase_button:label_with_price" content="Artikel sofort weiterlesen für nur %s">
```

This replaces the default translation for `purchase_button:label_with_price` with the string "Artikel sofort weiterlesen für nur %s". The `%s`sequence is replaced by the current price when the purchase overlay is rendered.

Example 2:

```html
 <meta property="laterpay:connector:translations:purchase_overlay:notes_text" content="&lt;strong&gt;Sie sind Abonnent oder haben die digitale Ausgabe gekauft?&lt;/strong&gt; &lt;br&gt; Melden Sie sich &lt;a href=&quot;#&quot;&gt;hier mit Ihrer User ID an&lt;/a&gt;.">
```

This sets the optional `purchase_overlay:notes_text`.

_Note, that [HTML entities](http://www.w3.org/html/wg/drafts/html/master/syntax.html#named-character-references) need to be encoded to be allowed in HTML attributes._

<script>
  function myGlobalCallbackFunction() {
    console.log("Yay, you have access to this content!");
  }
</script>
