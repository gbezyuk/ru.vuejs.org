---
title: Примеси
type: guide
order: 17
---

## Основы

Примеси (mixins)&nbsp;&mdash; это гибкий инструмент повторного использования кода в&nbsp;компонентах Vue. Объект примеси может содержать любые опции компонентов. При использовании компонентом примеси, все опции примеси &laquo;подмешиваются&raquo; к&nbsp;собственным опциям компонента.

Пример:

``` js
// определяем объект примеси
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('привет из примеси!')
    }
  }
}

// определяем компонент, использующий примесь
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // -> "привет из примеси!"
```

## Слияние опций

Если примесь и&nbsp;компонент содержат пересекающиеся опции, они будут определённым образом объединены. Например, одноимённые хуки будут объединены в&nbsp;массив, что обеспечит вызов их&nbsp;всех. Кроме того, хуки примеси будут вызваны **перед** собственными хуками компонента:

``` js
var mixin = {
  created: function () {
    console.log('вызван хук примеси')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('вызван хук компонента')
  }
})

// -> "вызван хук примеси"
// -> "вызван хук компонента"
```

Опции, ожидающие значения в&nbsp;форме объектов, такие как `methods`, `components` и&nbsp;`directives` будут объединены. В&nbsp;случае конфликта, приоритет имеют опции компонента:

``` js
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('из примеси')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('из самого компонента')
    }
  }
})

vm.foo() // -> "foo"
vm.bar() // -> "bar"
vm.conflicting() // -> "из самого компонента"
```

Обратите внимание, что те&nbsp;же самые стратегии слияния используются и&nbsp;во&nbsp;`Vue.extend()`.

## Глобальные примеси

Примесь может быть применена и&nbsp;глобально. Но&nbsp;используйте данную возможность осторожно! После применения, примесь окажет влияние на&nbsp;**все инстансы Vue**, создаваемые в&nbsp;дальнейшем. При правильном использовании это можно использовать для вставки логики обработки пользовательских опций:

``` js
// добавляем обработчик для пользовательской опции `myOption`
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'hello!'
})
// -> "hello!"
```

<p class="tip">Используйте глобальные примеси с&nbsp;осторожностью, поскольку они влияют на&nbsp;все до&nbsp;единого создаваемые инстансы Vue, включая внешние компоненты. В&nbsp;большинстве случаев их&nbsp;стоит использовать только для обработки пользовательских опций, подобно примеру выше. Неплохой идеей будет их&nbsp;оформление в&nbsp;виде [плагинов](plugins.html), что позволит избежать дублирования кода.</p>

## Пользовательские стратегии слияния опций

При слиянии пользовательских опций применяется стратегия по&nbsp;умолчанию, которая просто заменяет одни значения другими. Если вы&nbsp;хотите использовать отличную логику при слиянии пользовательских опций, добавьте функцию в&nbsp;`Vue.config.optionMergeStrategies`:

``` js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // return mergedVal
}
```

Для большей части опций-объектов можно просто использовать стратегию, применяемую по&nbsp;умолчанию для опции `methods`:

``` js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```

Более сложным примером может послужить стратегия слияния из&nbsp;[Vuex](https://github.com/vuejs/vuex)&nbsp;1.x:

``` js
const merge = Vue.config.optionMergeStrategies.computed
Vue.config.optionMergeStrategies.vuex = function (toVal, fromVal) {
  if (!toVal) return fromVal
  if (!fromVal) return toVal
  return {
    getters: merge(toVal.getters, fromVal.getters),
    state: merge(toVal.state, fromVal.state),
    actions: merge(toVal.actions, fromVal.actions)
  }
}
```
