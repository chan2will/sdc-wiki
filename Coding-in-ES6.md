## 🚨 LET'S WRITE SOME 🚨
![ES6](https://image.slidesharecdn.com/4-es6metbabel-150513100342-lva1-app6891/95/es6-with-babeljs-1-638.jpg)

# Overview
We can use the `django-compressor` template tag to wrap ES6 script tags, marking them for ES6 -> ES5 transpilation.

Locally, this will happen per-request, whereas the transpiled ES5 is built all at once in every other environment and then served statically.

ES6 is way awesome and you will like it! No more passing around a global object as a module
```
var sdc = sdc || {};
sdc.doImportantStuff = function () { ... };
```
but instead

**file1**
```
const doImportantStuff = () => { ... };
export default doImportantStuff
```
**file2**
```
import doImportantStuff from 'website/js/stuffToDo';
```

`django-compressor` knows how to navigate any static assets that we serve, meaning that you're free to import from any other ES6 file from a main ES6 file, and only use the `django-compressor` template tag on the main one!

# How To
1. Load the `compress` Django template tag into the template you're adding the script tag to.
2. Wrap the script tag in the `compress js` tag, making sure that the script tag has `type='module'`. It is preferable to use script tags linking to external script files rather than inline scripts; the latter works but there's a caveat, which will be detailed in the `gotchas` section.

# Example
**website/pages/patient-portal/settings.pug**
```
| {% load compress %}
...
| {% compress js %}
script(type='module', src='{% static "website/js/patient_portal_settings_vue_init.js" %}')
| {% endcompress %}
```
**website/js/patient_portal_settings_vue_init.js**
```
import Vue from 'vue/dist/vue.min';
import App from 'website/components/patient-portal-settings/App.vue';

new Vue(App).$mount('.vue-root');
```
Note that, above, we're importing `App.vue`. Normally when importing a `.js` file you'll omit the file extension, but we also have `Vueify` as part of this pre-processing pipeline, which means that we can use single-file Vue components like the following real example from our codebase!

**website/components/patient-portal-settings/App.vue**
```
<template lang="pug">
  ...
</template>

<script>
  import _isObject from 'lodash/isObject';
  import SettingsSection from './SettingsSection.vue';
  ...
</script>
```

*Please note* that you can also import styles into these single-page Vue components; Vueify knows where to find our Sass files. So you can do this:

**website/components/patient-portal-settings/Payment.vue**
```
<style lang="sass" scoped>
  @import '_app.mixins_v3.scss';
  ...
  @include field-container-narrow();
  @include credit-card-badge();
  @include form-control-inline();
</style>
```
In general it would make sense to me to follow a similar pattern: create an ES6 "entry point" into which other ES6 files/Vue components are imported. When creating a Vue app, have a container component like `App.vue` above that contains the whole thing.

# Gotchas

**1. Inline Script Tags**

As alluded to above, inline script tags will work so long as you wrap them in `compress js` like you would for a normal script tag.

However, as currently implemented, source maps (tags that tell the browser where bugs happened so that it can point you to the errant line of code) will not work for inline tags.

The reason for this is because JS files are usually kept within directories marked to Django as `static` (which I think might be as simple as naming a folder `"static"`). These are ultimately bundled and served from a single `/static` folder.

When you make an inline script tag, the per-request pre-processor will find it and transpile it, but it ultimately puts it in a separate file that lives within whatever directory structure that the _template_ happens to be in.

Templates aren't kept within a `/static` folder, hence they aren't ultimately put into the same place as the rest of the static assets, and the browser doesn't know how to get to the source file anymore (the template) since it doesn't exist within the same directory structure.

**2. Running Your Local Server in PyCharm**

Tony has run into problems with this, whereas Jon H. has gotten it to run.

The issue relates to environment variables. `Django-compressor` and `Django-compressor-toolkit` concatenate together all of the static directories in which ES6 files (or styles, so that you can import styles too) might live, and then uses them to set Node.js environment variables.

I don't currently know exactly what's getting messed up, but running the server through PyCharm seems to throw an additional wrinkle into this setup. Hopefully it can be alleviated by setting some specific environment variables in PyCharm.

# Reference

* Django-Compressor
  * http://django-compressor.readthedocs.io/en/latest/
  * https://github.com/django-compressor/django-compressor
* Django-Compressor-Toolkit
  * https://github.com/kottenator/django-compressor-toolkit
* Browserify
  * http://browserify.org/
* Babelify
  * https://github.com/babel/babelify
* Vueify
  * https://github.com/vuejs/vueify