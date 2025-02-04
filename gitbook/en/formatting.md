# Formatting

## HTML formatting

> :warning: NOTE: Dynamically localization arbitrary HTML on your website can be very dangerous because it can easily lead to XSS vulnerabilities. Only use HTML interpolation on trusted content and never on user-provided content. We recommended that use [component interpolation](./interpolation.md) feature.

In some cases you might want to render your translation as an HTML message and not a static string.

```javascript
const messages = {
  en: {
    message: {
      hello: 'hello <br> world'
    }
  }
}
```

Template the below:

```html
<p v-html="$t('message.hello')"></p>
```

Output the below (instead of the message pre formatted)

```html
<p>hello
<!--<br> exists but is rendered as html and not a string-->
world</p>
```

## Named formatting

Locale messages the below:

```javascript
const messages = {
  en: {
    message: {
      hello: '{msg} world'
    }
  }
}
```

Template the below:

```html
<p>{{ $t('message.hello', { msg: 'hello' }) }}</p>
```

Output the below:

```html
<p>hello world</p>
```

## List formatting

Locale messages the below:

```javascript
const messages = {
  en: {
    message: {
      hello: '{0} world'
    }
  }
}
```

Template the below:

```html
<p>{{ $t('message.hello', ['hello']) }}</p>
```

Output the below:

```html
<p>hello world</p>
```

List formatting also accepts array-like object:

```html
<p>{{ $t('message.hello', {'0': 'hello'}) }}</p>
```

Output the below:

```html
<p>hello world</p>
```

## Support ruby on rails i18n format

Locale messages the below:

```javascript
const messages = {
  en: {
    message: {
      hello: '%{msg} world'
    }
  }
}
```

Template the below:

```html
<p>{{ $t('message.hello', { msg: 'hello' }) }}</p>
```

Output the below:

```html
<p>hello world</p>
```

## Custom formatting
Sometimes, you maybe need to translate with custom formatting (e.g. [ICU message syntax](http://userguide.icu-project.org/formatparse/messages)).

You can realize with custom formatter that implement [Formatter Interface](https://github.com/kazupon/vue-i18n/blob/dev/decls/i18n.js#L41-L43).

The following custom formatter with ES2015 class syntax:

```javascript
// Custom Fromatter implementation
class CustomFormatter {
 constructor (options) {
   // ...
 }

 //
 // interpolate
 //
 // @param {string} message
 //   string of list or named format.
 //   e.g.
 //   - named formatting: 'Hi {name}'
 //   - list formatting: 'Hi {0}'
 //
 // @param {Object | Array} values
 //   values of `message` interpolation.
 //   passed values with `$t`, `$tp` and `i18n` functional component.
 //   e.g.
 //   - $t('hello', { name: 'kazupon' }) -> passed values: Object `{ name: 'kazupon' }`
 //   - $t('hello', ['kazupon']) -> passed values: Array `['kazupon']`
 //   - `i18n` functional component (component interpolation)
 //     <i18n path="hello">
 //       <p>kazupon</p>
 //       <p>how are you?</p>
 //     </i18n>
 //     -> passed values: Array (included VNode):
 //        `[VNode{ tag: 'p', text: 'kazupon', ...}, VNode{ tag: 'p', text: 'how are you?', ...}]`
 //
 // @param {string} path
 //   a path to the message, as passed into the $t/t() functions.
 //   - $t('hello.louis') -> path === 'hello.louis'
 //
 // @return {Array<any>}
 //   interpolated values. you need to return the following:
 //   - array of string, when is using `$t` or `$tp`.
 //   - array included VNode object, when is using `i18n` functional component.
 //   - null - if you want the default vue-i18n formatter to handle the case
 //
 interpolate (message, values, path) {
   // implement interpolation logic here
   // ...

   // return the interpolated array
   return ['resolved message string']
 }
}

// register with `formatter` option
const i18n = new VueI18n({
  locale: 'en-US',
  formatter: new CustomFormatter(/* here the constructor options */),
  messages: {
    'en-US': {
      // ...
    },
    // ...
  }
})

// Run!
new Vue({ i18n }).$mount('#app')
```

You can check [the custom formatter official example](https://github.com/kazupon/vue-i18n/tree/dev/examples/formatting/custom).
