# szn-elements

This package contains the common runtime for the custom szn-* elements.

## Example usage

Note: remove the `async` attributes and add this markup to the
`<head>` element to prevent the flash of unstyled content:

### Simple usage

This section describes the most easy-to-setup usage, which, unfortunately,
usually has the worst performance in most browsers.

The recommended use is described in the **Advanced usage** section.

```html
<script src="/bath/to/babel-polyfill.js"></script>
<script src="/path/to/szn-elements.es3.js"></script>
<script src="/path/to/szn-elements-mutation-observer.es3.js"></script>
<script src="/path/to/custom/szn/element1.js"></script>
<script src="/path/to/custom/szn/element2.js"></script>
<script src="/path/to/custom/szn/element3.js"></script>
```

This approach allows for bundling all these JS files together... a better
approach would be to use HTTP2.

### Advanced usage

```html
<script>
for (const _ of []);
var forConstSupported = true; // Firefox 52 (ESR), Palemoon and others
</script>
<script>
  (function(){
    var basePath = '/path/to/szn-elements/';
    var es6 = !!window.Proxy && forConstSupported;
    var browserRuntime;
    if (window.customElements) {
      browserRuntime = 'custom-elements';
    } else {
      browserRuntime = 'mutation-observer.es' + (es6 ? '6' : '3');
    }
    document.write(
        '<script src="' + basePath + 'szn-elements.es' + (es6 ? '6' : '3') +
        '.min.js" async><\/script>' +
        '<script src="' + basePath + browserRuntime + '.min.js" async><\/script>'
    )
  }())
</script>
<script src="/path/to/custom/szn/element1.js" async></script>
<script src="/path/to/custom/szn/element2.js" async></script>
<script src="/path/to/custom/szn/element3.js" async></script>
<!-- any other SZN elements -->
```

The script injects two other scripts:
* the core runtime (`szn-elements.es6.min.js` or `szn-elements.es3.min.js`)
* the browser-specific runtime helper (`szn-elements-custom-elements.min.js`,
  `szn-elements-mutation-observer.es6.min.js` or
  `szn-elements-mutation-observer.es3.min.js`).

The script chooses which runtime scripts to inject by detecting the features
supported in the current browser, using the more modern features when possible
in order to achieve better performance.

#### Minified version

In case you want to save every byte you can:

```html
<script>for(const _ of[]);var _=true;</script>
<script>
!function(p,v,s,e,c){
c=self.customElements?'custom-elements':'mutation-observer.es'+v;
document.write(s+p+'szn-elements.es'+v+e+s+p+c+e)
}('/path/to/szn-elements/',(self.Proxy&&self._)?6:3,'<script src="','.min.js" async><\/script>')
</script>
```

## IE and Edge support

Both IE and Edge (last checked with the 16 version) have no support for the
`template` element and incomplete implementation of the document fragment (no
support for `firstElementChild`). Use a polyfill for these features to enable
support in these browsers.

## Legacy browser support

To support legacy browsers (IE11, old Androids, etc.), you will need to include
a polyfill for the ES2017 features (e.g. the babel-polyfill) before the script
above:

```html
<!-- babel polyfill must be inject synchronously -->
<script src="/bath/to/babel-polyfill.js"></script>
```

To support IE 9-10 is theoretically possible with correct polyfills, use this
MutationObserver polyfill (https://www.npmjs.com/package/mutation-observer)
like this (IE 10 does not support conditional comments, so we have to use a
script):

```html
<script>
  if (!window.MutationObserver) {
    document.write('<script src="/path/to/mutationobserver/polyfill.js"></' + 'script>')
  }
</script>
<!-- SZN elements code goes here -->
```

If you need IE8 support as well, use the ES3-compatible polyfill
(https://www.npmjs.com/package/mutationobserver-shim) and the following
pattern:

```html
<!--[if IE 8]>
<script src="/path/to/mutationobserver/shim.js"></script>
<![endif]-->
<![if (gt IE 8)|(!IE)]>
<!-- IE 9-10 polyfill code goes here -->
<!-- SZN elements code goes here -->
<![endif]>
```

Alternatively, you may just always include the necessary polyfills, but most
of your visitors will have download javascript that they wont even use.
