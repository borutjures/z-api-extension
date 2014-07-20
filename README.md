Chrome extension for using Z API.


Summary
-------

Purpose of this Chrome extension is to search for occurrences of text in a displayed page. 
Searching is done using a regex expression.

If search was successful, the found text is marked with an `<a>` tag. 
Another `<span>` tag is added after the marked text. It is hidden by default (`display:none`). 

Extension communicates with an API. This API is not part of this product. 
The product only calls the API and uses its return values.


Functional Specifications
=========================

Search and markup text
----------------------

After a page is displayed in the browser, the extension searches for text inside 
the `<body>` that matches regular expression. Regular expression must be declared as a constant.

* Regular expression for searching: `/Dart|JavaScript/gim`
* Regular expression description: match all Dart or JavaScript occurrences; use case insensitive search
* Test page: http://en.wikipedia.org/wiki/DartLang

Matched text is replaced with:

```html
<a class="flr" href="javascript:fold(this,'--ID--');" title="Show">
  --FOUND-TEXT--&nbsp;
  <img id="flr_--ID--" src="http://properius.com/projects/z-api-extension/api/v1/images/fc.png" alt="Show" title="Show" />
</a>
<span class="fld" id="fld_--ID--" style="display:none;">
  <b>--FOUND-TEXT--: </b><a class="turl" href="http://en.wikipedia.org/wiki/--FOUND-TEXT--">Wikipedia</a>
</span>
```

Where `--ID--` in the above HTML is a sequence number incremented for each matched text occurrence. 
This number must be unique for each matched text occurrence on the page. If there 
are two same texts on the page, each must get its own sequence number.

Where `--FOUND-TEXT--` is text found with the regular expression.


Show/Hide marked text
---------------------

The found text is marked with a HTML which contains a `<span>` tag with `display:none`. 
It also contains an `<a>` tag that calls a JavaScript function `fold()`. 
This function must be part of the extension.

When the `<a>` link is clicked, the `fold()` function shows the hidden `<span>`. 
When the `<a>` link is clicked again, the `fold()` function hiddes the `<span>`.

### Fold function

```javascript
function fold(flr, idx) {
    var divstyle = document.getElementById('fld_'+idx).style;
    var img = document.getElementById('flr_'+idx);
    var showhide = (divstyle.display === 'none')?'block':'none';
    divstyle.display = showhide;
    if (showhide==='none') {
        img.src = 'http://properius.com/projects/z-api-extension/api/v1/images/fc.png';
        img.alt = "Show";
        img.title = "Show";
    } else {
        img.src = 'http://properius.com/projects/z-api-extension/api/v1/images/fo.png';
        img.alt = "Close";
        img.title = "Close";
    }
}
```


Login
-----

Users must login before using the extension. After the extension is installed, 
the user is asked to enter his email. User's email is stored as a setting in local 
storage.

After the user enters his email and clicks on the `Login` button, the extensions
sends login data asynchronously to the API as a JSON object using `HTTP POST` request.

* API endpoint: http://properius.com/projects/z-api-extension/api/v1/login
* Request JSON:
  * `email`: user's email

The API responds to the `HTTP POST` request with a JSON object containing login data.

* Response JSON:
  * `token`: user's login token as a text
  * `message`: error message as a HTML

Returned login token is saved as a cookie:

* name is `z-api-token`
* value is `token` from response JSON
* expires in 10 years
* path=/
* domain=.<DOMAIN-NAME-OF-THE-CURRENT-PAGE>

This cookie must be sent to the API on each subsequent call.

If an error message is returned, cookie is not stored. Error message is displayed 
to the user. User may try to login again after reading/confirming the error message.


Settings page
-------------

The extension uses a settings page. It displays:

* User's email address.

Extension stores settings in local storage. When the settings page is displayed, 
data is read from the local storage and displayed.


Privileges
----------

The extension must use as few privileges as possible. 

Privilege to browse/read data for a current page displayed in the browser from
any domain must be included.


CSS
---

Marked text in the browser must use CSS specified by the extension.

```css
.flr{
    padding-left:2px;
    padding-right:2px;
}

.flr img{
    cursor:pointer;
}

span.fld{
    color:#333;
}
```


Technologies used
-----------------

You may choose JavaScript or Dart as the programming language for this extension. 
Dart is preferred and will be valued higher.

Code must be written as a Google Chrome Extension.


Notes
-----

* Please read [instructions](https://github.com/properius/projects) about working on this project.


Deleted sections
================

Text below this point is not part of the specifications. It is kept here for 
reference only.

## ~~Send matches to the API~~

> bj; 2014-07-20; Matches are not sent to the API. They are marked and only the ones that a user selects
are sent to the API for further processing.

The extension must make sure that the array of matched text contains only unique texts.

The unique list of matched text is sent asynchronously to the API as a JSON object using `HTTP POST` request.

API endpoint: http://properius.com/projects/z-api-extension/api/v1/matches

* Request JSON:
  * `url`: URL of the current page displayed in the browser tab
  * `matches`: array of unique regex matches

## ~~Markup text~~

> bj; 2014-07-20; Matches are not sent to the API. They are marked and only the ones that a user selects
are sent to the API for further processing.

The API responds to the `HTTP POST` request with a JSON object containing data used to markup the current page.

The extension uses returned JSON data to do one regex search&replace for each item in the JSON data. 
It uses `search-regex` value as a regex search expression and replaces all matches with the `replace-html` value.

Response JSON:
  * `replacements`: map of `search-regex` / `replace-html` pairs

Example text the API will return as `search-regex`: `dart`

Example regex used by the extension: `/dart/gim`

Example HTML the API will return as `replace-html`:

```html
<a class="flr" href="javascript:fold(this,'1');" title="Show">
  Dart&nbsp;
  <img id="flr_1" src="http://properius.com/projects/z-api-extension/api/v1/images/fc.png" alt="Show" title="Show" />
</a>
<span class="fld" id="fld_1" style="display:none;">
  <b>Dart: </b><a class="turl" href="http://en.wikipedia.org/wiki/DartLang">Wikipedia</a>
</span>
```

## ~~Show/Hide marked text~~

> bj; 2014-07-20; Matches are not sent to the API. They are marked and only the ones that a user selects
are sent to the API for further processing.

The API will return a HTML which will contain a `<span>` tag with `display:none`. 
It will also return an `<a>` tag that calls a JavaScript function `fold()`. 
This function must be part of the extension.

When the returned `<a>` link is clicked, the `fold()` function shows the hidden `<span>`. 
When the `<a>` link is clicked again, the `fold()` function hiddes the `<span>`.

Fold function:~~

```javascript
function fold(flr, idx) {
    var divstyle = document.getElementById('fld_'+idx).style;
    var img = document.getElementById('flr_'+idx);
    var showhide = (divstyle.display === 'none')?'block':'none';
    divstyle.display = showhide;
    if (showhide==='none') {
        img.src = 'http://properius.com/projects/z-api-extension/api/v1/images/fc.png';
        img.alt = "Show";
        img.title = "Show";
    } else {
        img.src = 'http://properius.com/projects/z-api-extension/api/v1/images/fo.png';
        img.alt = "Close";
        img.title = "Close";
    }
}
```
