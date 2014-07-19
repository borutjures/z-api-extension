Chrome extension for using Z API.

## Summary

Purpose of this Chrome extension is to search for occurrences of text in a displayed page. Searching is done using Regex expression.

If search was successful, the found text is marked with an `<a>` tag. Another `<span>` tag is added after the marked text. It is hidden by default (`display:none`). It contains html text returned from an API. This API is not part of this product. The product only calls the API and uses its return values to markup the text on the current page.

# Functional Specifications

## Search for text

After a page is displayed in the browser, the extension searches for text that matches regular expression inside the `<body>`. Regular expression must be declared as a constant.

* Regular expression: `/Dart|JavaScript/gim`
* Regular expression description: match all Dart or JavaScript occurrences; use case insensitive search
* Test page: http://en.wikipedia.org/wiki/DartLang

## Send matches to the API

The extension must make sure that the array of matched text contains only unique texts.

The unique list of matched text is sent asynchronously to the API as a JSON object using `HTTP POST` request.

API endpoint: http://properius.com/projects/z-api-crx/api/v1/matches

### Request JSON

* `url`: URL of the current page displayed in the browser tab
* `matches`: array of unique regex matches

## Markup text

The API responds to the `HTTP POST` request with a JSON object containing data used to markup the current page.

The extension uses returned JSON data to do one regex search&replace for each item in the JSON data. It uses `search-regex` value as a regex search expression and replaces all matches with the `replace-html` value.

### Response JSON

* `replacements`: map of `search-regex` / `replace-html` pairs

Example text the API will return as `search-regex`: `dart`

Example regex used by the extension: `/dart/gim`

Example HTML the API will return as `replace-html`:

```html
<a class="flr" href="javascript:fold(this,'1');" title="Show">Dart&nbsp;<img id="flr_1" src="http://properius.com/projects/z-api-crx/api/v1/images/fc.png" alt="Show" title="Show"></a>
<span class="fld" id="fld_1" style="display:none;"><b>Dart: </b><a class="turl" href="http://en.wikipedia.org/wiki/DartLang">Wikipedia</a></span>
```

## Show/Hide marked text

The API will return a HTML which will contain a `<span>` tag with `display:none`. It will also return an `<a>` tag that calls a JavaScript function `fold()`. This function must be part of the extension.

When the returned `<a>` link is clicked, the `fold()` function show the hidden `<span>`. When the `<a>` link is clicked again, the `fold()` function hiddes the `<span>`.

### Fold function

```javascript
function fold(flr, idx) {
    var divstyle = document.getElementById('fld_'+idx).style;
    var img = document.getElementById('flr_'+idx);
    var showhide = (divstyle.display === 'none')?'block':'none';
    divstyle.display = showhide;
    if (showhide==='none') {
        img.src = 'http://properius.com/projects/z-api-crx/api/v1/images/fc.png';
        img.alt = "Show";
        img.title = "Show";
    } else {
        img.src = 'http://properius.com/projects/z-api-crx/api/v1/images/fo.png';
        img.alt = "Close";
        img.title = "Close";
    }
}
```

## Using CSS

Marked text in the browser must use CSS specified by the extension.

## Technologies used

You may choose JavaScript or Dart as the programming language for this extension. Dart is preferred and will be valued higher.

Code must be written as a Google Chrome Extension.

## Notes

* Please read [instructions](https://github.com/properius/projects) about working on this project.

