---
title: Рендеринг списков
type: guide
order: 8
---

## `v-for`

Используйте директиву `v-for` для рендеринга списка элементов на основе массива данных. Директива `v-for` требует особого синтаксиса: записи `item in items`, где `items` — исходный массив данных, а `item` — **ссылка** на текущий элемент массива:

### Типичное использование

``` html
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```

``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

Результат:

{% raw %}
<ul id="example-1" class="demo">
  <li v-for="item in items">
    {{item.message}}
  </li>
</ul>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(document.querySelector('#example-1'))
    }
  }
})
</script>
{% endraw %}

Внутри блока `v-for` нам полностью доступны свойства из области видимости родителя. `v-for` также поддерживает необязательный второй параметр для указания индекса текущего элемента.

``` html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Родитель',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

Результат:

{% raw%}
<ul id="example-2" class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Родитель',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  },
  watch: {
    items: function () {
      smoothScroll.animateScroll(document.querySelector('#example-2'))
    }
  }
})
</script>
{% endraw %}

Вместо `in` разделителем может быть слово `of`, как в итераторах JavaScript:

``` html
<div v-for="item of items"></div>
```

### `v-for` и template

Как и в случае с `v-if`, для рендеринга нескольких элементов директивой `v-for` используйте псевдоэлемент `<template>`. Например:

``` html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider"></li>
  </template>
</ul>
```

### `v-for` для объекта

`v-for` можно также использовать для итерирования по свойствам объекта:

``` html
<ul id="repeat-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```

``` js
new Vue({
  el: '#repeat-object',
  data: {
    object: {
      firstName: 'Иван',
      lastName: 'Петров',
      age: 30
    }
  }
})
```

Результат:

{% raw %}
<ul id="repeat-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
<script>
new Vue({
  el: '#repeat-object',
  data: {
    object: {
      firstName: 'Иван',
      lastName: 'Петров',
      age: 30
    }
  }
})
</script>
{% endraw %}

Можно также указать второй аргумент для получения ключей:

``` html
<div v-for="(value, key) in object">
  {{ key }} : {{ value }}
</div>
```

И третий — для индексов:

``` html
<div v-for="(value, key, index) in object">
  {{ index }}. {{ key }} : {{ value }}
</div>
```

<p class="tip">При итерировании по объекту, порядок обхода такой же, как порядок ключей в `Object.keys()`. Его консистентность **не гарантируется** при использовании различных реализаций движков JavaScript.</p>

### `v-for` и диапазоны

В `v-for` можно также передать целое число. В этом случае шаблон будет повторён указанное число раз.

``` html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

Результат:

{% raw %}
<div id="range" class="demo">
  <span v-for="n in 10">{{ n }} </span>
</div>
<script>
new Vue({ el: '#range' })
</script>
{% endraw %}

### Компоненты и `v-for`

> Эта секция подразумевает знакомство с [компонентами](components.html). Не стесняйтесь пропустить её сейчас и вернуться потом.

Вы можете использовать `v-for` и на пользовательских компонентах — совершенно так же, как и на обычных элементах:

``` html
<my-component v-for="item in items" :key="item.id"></my-component>
```

> В версиях 2.2.0+, при использовании `v-for` на компонентах теперь обязательно требуется указывать [`key`](list.html#key).

Однако, это не передаст в компонент никаких данных автоматически, поскольку компоненты имеют изолированные области видимости. Для передачи итерируемых данных в компонент нужно явно использовать входные параметры:

``` html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
>
</my-component>
```

Причина, по которой `item` не передаётся в компонент автоматически, в том, что это сделало бы компонент жёстко связанным с логикой работы `v-for`. А если источник данных указывается явно, компонент можно будет использовать и в других ситуациях.

Ниже приведён полный пример простого списка todo:

``` html
<div id="todo-list-example">
  <input
    v-model="newTodoText"
    v-on:keyup.enter="addNewTodo"
    placeholder="Добавить todo"
  >
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo"
      v-bind:title="todo"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
```

``` js
Vue.component('todo-item', {
  template: `
    <li>
      {{ title }}
      <button v-on:click="$emit('remove')">X</button>
    </li>
  `,
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      'Вымыть посуду',
      'Вынести мусор',
      'Подстричь газон'
    ]
  },
  methods: {
    addNewTodo: function () {
      this.todos.push(this.newTodoText)
      this.newTodoText = ''
    }
  }
})
```

{% raw %}
<div id="todo-list-example" class="demo">
  <input
    v-model="newTodoText"
    v-on:keyup.enter="addNewTodo"
    placeholder="Добавить todo"
  >
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo"
      v-bind:title="todo"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
<script>
Vue.component('todo-item', {
  template: `
    <li>
      {{ title }}
      <button v-on:click="$emit('remove')">X</button>
    </li>
  `,
  props: ['title']
})
new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      'Вымыть посуду',
      'Вынести мусор',
      'Подстричь газон'
    ]
  },
  methods: {
    addNewTodo: function () {
      this.todos.push(this.newTodoText)
      this.newTodoText = ''
    }
  }
})
</script>
{% endraw %}

### `v-for` и `v-if`

Когда они присутствуют на одном элементе, `v-for` имеет больший приоритет, чем `v-if`. Другими словами, `v-if` будет выполняться отдельно для каждой итерации цикла. Это очень полезно когда нужно отобразить только _некоторые_ элементы списка, например:

``` html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

В результате будут отображены только todo, которые ещё не выполнены.

Если нужно чтобы по условию пропускалось выполнение всего цикла, поместите `v-if` на внешний элемент (или на [`<template>`](conditional.html#Условные-группы-с-использованием-v-if-и-lt-template-gt)). Например:

``` html
<ul v-if="shouldRenderTodos">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
```

## `key`

Когда Vue обновляет список элементов, управляемый директивой `v-for`, по умолчанию используется стратегия "обновления на месте". Если порядок элементов массива или объекта изменился, Vue не будет перемещать элементы DOM, а попросту обновит каждый элемент "на месте", чтобы он отображал новые данные по соответствующему индексу. Во Vue версий 1.x подобное поведение достигалось указанием `track-by="$index"`.

Режим по умолчанию эффективен, но применим только в случаях **когда результат рендеринга вашего списка не полагается на состояние дочерних компонентов или промежуточные состояния DOM (например, элементов ввода форм)**.

Чтобы подсказать Vue, как отслеживать идентичность каждого элемента, позволяющую переиспользовать и перемещать существующие элементы, укажите уникальный атрибут `key` для каждого элемента. Идеальным значением `key` будет уникальный id сущности. Этот специальный атрибут является приблизительным эквивалентом `track-by` из Vue 1.x, но работает так же, как и все остальные атрибуты, а значит вам нужно использовать `v-bind` для связывания с динамическими значениями (в примере ниже используется сокращение):

``` html
<div v-for="item in items" :key="item.id">
  <!-- содержимое -->
</div>
```

Рекомендуем указывать `key` с `v-for` по возможности всегда, за исключением случаев когда итерируемый контент DOM прост, или когда вы сознательно используете стратегию по умолчанию для улучшения производительности.

Поскольку указанный механизм является общим для идентификации элементов во Vue, `key` имеет и другие варианты применения, не связанные явно с `v-for`, как мы увидим далее в руководстве.

## Отслеживание изменений в массивах

### Методы внесения изменений

Vue оборачивает методы внесения изменений наблюдаемого массива таким образом, чтобы они вызывали обновления представления. Оборачиваются следующие методы:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

Вы можете открыть консоль и поиграть с массивом `items` из предыдущего примера, вызывая его методы внесения изменений. Например: `example1.items.push({ message: 'Baz' })`.

### Замены в массиве

Методы внесения изменений, как следует из их названия, изменяют оригинальный массив, на котором они вызываются. Существуют и неизменяющие методы, такие как `filter()`, `concat()` и `slice()`, не вносящие изменений в изначальный массив, а **всегда возвращающие новый массив**. При работе с неизменяющими методами, вы можете просто заменить старый массив новым:

``` js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

Можно было бы предположить, что это заставит Vue выбросить существующий DOM и перерендерить список целиком, но, к счастью, это не так. Во Vue есть умные эвристики для максимизации переиспользования элементов DOM, так что замена одного массива другим, в случае совпадения части элементов в этих массивах, является очень эффективной операцией.

### Предостережения

Из-за ограничений JavaScript, Vue **не способен** заметить следующие изменения в массиве:

1. Прямую установку элемента по индексу, например: `vm.items[indexOfItem] = newValue`
2. Явное изменение длины массива, например: `vm.items.length = newLength`

Обойти первую проблему можно двумя способами, оба из которых не только дадут эффект аналогичный `vm.items[indexOfItem] = newValue`, но и инициируют реактивные обновления состояния приложения:

``` js
// Vue.set
Vue.set(example1.items, indexOfItem, newValue)
```
``` js
// Array.prototype.splice
example1.items.splice(indexOfItem, 1, newValue)
```

Для обхода второй проблемы можно также использовать `splice`:

``` js
example1.items.splice(newLength)
```

## Отображение отфильтрованных/отсортированных результатов

Иногда хочется отобразить отфильтрованную или отсортированную версию массива, не изменяя оригинальных данных. В этом случае, можно создать вычисляемое свойство, возвращающее отфильтрованный или отсортированный массив.

Например:

``` html
<li v-for="n in evenNumbers">{{ n }}</li>
```

``` js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

В ситуациях когда вычисляемые свойства не пригодны к использованию (например, внутри вложенных циклов `v-for`), можно просто использовать метод:

``` html
<li v-for="n in even(numbers)">{{ n }}</li>
```

``` js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```
