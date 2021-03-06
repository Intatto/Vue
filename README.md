# Dappos

![](https://circleci.com/gh/Dappos/Dappos.svg?style=shield&circle-token=a4bb78ec3c830868731db9a3f8a8028ba024530a)

Dappos is a Ethereum based Dapp POS register.

### Table of Contents

<!-- TOC -->

- [Motivation](#motivation)
- [Contribution guide](#contribution-guide)
    - [π©π½βπ» Commiting](#-commiting)
    - [π¨πΌβπ» Developing](#-developing)
    - [π©π»βπ« Linting](#-linting)
    - [π¨π»βπ§ Frameworks](#-frameworks)
    - [πΈπΎ Import](#-import)
    - [ππ»β HTML](#-html)
    - [π¨π»βπ¨ CSS](#-css)
        - [Full Vue component + Stylus example](#full-vue-component--stylus-example)
        - [Stylus variables](#stylus-variables)
        - [Stylus helpers](#stylus-helpers)
    - [π©π»βπ¬ External dependencies](#-external-dependencies)
    - [π¨π½βπ Vuex usage](#-vuex-usage)
- [Inner workings](#inner-workings)
    - [π App initialisation](#-app-initialisation)

<!-- /TOC -->

## Motivation

There was no POS app for Ethereum to our knowledge, so we made one! βπ»

## Contribution guide

### π©π½βπ» Commiting

Always make PR towards the **DEV branch**. Always add an *emoji* after each commit βπ». Make small commits often.

### π¨πΌβπ» Developing

`npm run dev`

### π©π»βπ« Linting

ES6 StandardJS linting enabled

### π¨π»βπ§ Frameworks

Vue.js + [Quasar framework](https://quasar-framework.org/guide/)

### πΈπΎ Import

Path aliasses available at: `quasar.conf.js`.

Path | Alias
---|---
src/css/themes/common.variables.styl | import @import '~styl/variables'
src/router | import ... from '@router/...'
src/store | import ... from '@store/...'
src/helpers | import ... from '@helpers/...'
src/config | import ... from '@config/...'

### ππ»β HTML

Write functional attributes first, semantic ones and classes last:

```html
<button @click="add()" class="_add"> ... </button>
```

New line per attribute when the line feels to long:

```html
<button
  @click="dispatch('cart/openMore', item)"
  class="_more _button _extras"
>
  ...
</button>
```

### π¨π»βπ¨ CSS

Stylus with minimal syntax. We don't use BEM. π±

- Ommit traditional syntax `: ;`
- Scoped styles per component
- [Do not use element selectors](https://vue-loader.vuejs.org/guide/scoped-css.html), only classes!
- Top level div class is the component name
- Keep other class names unique to the Vue component short and start with underscore Eg. `_link` (this way it's easy to see which classes are local)
- Global classes have no underscore (eg. `reset-button` in example below)
- JS selectors start with `js-` (eg. `js-info-cart` in example below)
- Only very frequently used classes like `btn` and `link` can start with `o-` (a little bit of BEM after all π)
- No fixed width and margin on most outer div! (Setting the width on a component should be done in the parent component when importing it. This keeps our components re-usable.)
- Mobile first
- Rem/em preffered over pixels

#### Full Vue component + Stylus example

```html
<template>
  <div class="info-cart">
    <button class="_link reset-button">
      <div class="_count js-info-cart">...</div>
    </button>
  </div>
</template>
<style lang="stylus" scoped>
```
```stylus
@import '~styl/variables'

// top level div class is component name
.info-cart
  width 100%
// scoped private class
._link
  color goldenrod
```
```html
</style>
```

#### Stylus variables

Please see `src/css/themes/common.variables.styl`.

#### Stylus helpers

Several general helpers are available in `src/css/themes/helpers`:

**Breakpoints**

Screen breakpoints have variables set in `helpers/breakpoints.styl`. Media queries have a shorthand:

```stylus
._layout
  padding .5rem
  media-sm padding 1rem
```

Basically this says the padding is `.5rem` (mobile first) and `1rem` for anything bigger than 'small screens'. It will wrap `padding 1rem` in a media query for `min-width < $breakpoint-sm` which is set to 767px.

**Margin Padding**

Classes for `margin` and `padding` have shorthands like `px` for `padding-left` and `-right` (x axis) and are followed by a size like `md` for 'medium':

```stylus
._class
  px md
```

Combine margin/padding
- `py/my` or `px/mx`: padding and margin on x or y axis
- `pt/pr/pb/pl` or `mt/mr/mb/ml`: per direction
- `pa/ma`: all directions

With sizes:
- `xxs` 16px * .1
- `xs` 16px * .25
- `sm` 16px * .5
- `md` 16px
- `lg` 16px * 1.5
- `xl` 16px * 2.3
- `xxl` 16px * 3
- `xxxl` 16px * 5

### π©π»βπ¬ External dependencies

External plugins etc. to be installed with `npm` and added as a plugin. Please read about the plugin [documentation here](https://quasar-framework.org/guide/app-plugins.html).

### π¨π½βπ Vuex usage

We are using the plugins: [Vuex easy access](https://github.com/mesqueeb/VuexEasyAccess) and [Vuex easy firestore](https://github.com/mesqueeb/VuexEasyFirestore). Please read up on the documentation!

**TLDR;**

- Never set state directly! Always use the (auto-generated) setters
- Usage of getters and setters in Vue components: `get(path)  set(path, val)`
- `path` syntax is: `module/prop.subprop`
- Always set default mutations per module: `...defaultMutations(initialState(), easyAccessConf)`
- Usage of setters in vuex module: `dispatch('module/set/prop.subprop', val)`
- Usage of setters for firestore modules ('settings' and 'menulist'):
  - `dispatch('settings/set', newSettings)`
  - `dispatch('user/menulist/set' item)`
- To overwriting a setter add an action:

```js
actions: {
  'prop.subprop': ({commit}, val) => {
    // do some stuff
    commit('prop.subprop', val)
  },
}
```

## Inner workings

### π App initialisation

The inner process of how the app initialises:

1. Vuex store and router are instanciated (`store/index` and `router/index`)
2. Other plugins are instanciated (`plugins/` please see the [documentation](https://quasar-framework.org/guide/app-plugins.html))
3. Firebase gets initialised (`plugins/firebase`)
4. Authentication is checked (`plugins/boot`) after which:
  - Firestore DB channels are opened
  - Web3 checks for a wallet
  - Vue, the app & DOM get created

The order of the plugins can be checked in `quasar.conf.js`.
