---
title: Установка
type: guide
order: 1
vue_version: 2.3.0
dev_size: "247.31"
min_size: "76.64"
gz_size: "28.03"
ro_gz_size: "19.54"
---

### Предупреждение о совместимости

Vue **не поддерживает** IE8 и ниже, потому что использует возможности ECMAScript 5, которые невозможно эмулировать в IE8. Тем не менее, поддерживаются все [браузеры, совместимые с ECMAScript 5](http://caniuse.com/#feat=es5).

### Замечания о релизах

Детальная информация об изменениях в каждой версии доступна на [GitHub](https://github.com/vuejs/vue/releases).

## Подключение через `<script>`

Просто скачайте js-файл и подключите его тегом `<script>`. Будет зарегистрирована глобальная переменная `Vue`.

<p class="tip">Не используйте минифицированную версию во время разработки, иначе вы не будете получать предупреждения о типичных ошибках!</p>

<div id="downloads">
<a class="button" href="/js/vue.js" download>Версия для разработки</a><span class="light info">С  предупреждениями для удобства разработки и отладки</span>

<a class="button" href="/js/vue.min.js" download>Версия для production</a><span class="light info">Без предупреждений, {{gz_size}} Кб min+gzip</span>
</div>

### CDN

Рекомендуем этот вариант: на [https://unpkg.com/vue](https://unpkg.com/vue) всегда доступна самая свежая версию Vue, загруженная в NPM. Исходный код самого NPM-пакета также доступен по ссылке [https://unpkg.com/vue/](https://unpkg.com/vue/).

Vue также доступен на [jsDelivr](//cdn.jsdelivr.net/vue/latest/vue.js) и [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.js), но эти сервисы синхронизируются с NPM с некоторой задержкой, из-за чего новейшая версия может быть не всегда доступна.

## NPM

Рекомендуем использовать NPM при создании крупных приложений на Vue. Этот вариант прекрасно работает в паре с инструментами сборки, такими как [Webpack](https://webpack.js.org/) и [Browserify](http://browserify.org/). Во Vue также есть совместимые с ними инструменты для использования [однофайловых компонентов](single-file-components.html).

``` bash
# последняя стабильная версия
$ npm install vue
```

## Инструменты командной строки (CLI)

Vue.js предоставляет [официальный интерпретатор командной строки (CLI)](https://github.com/vuejs/vue-cli) для быстрого создания каркаса амбициозных одностраничных приложений. Предлагаемые шаблоны содержат всё необходимое для организации современной фронтенд-разработки. Всего за несколько минут вы получите работающую конфигурацию с hot-reload, листингом кода при сохранении и настроенной конфигурацией production-сборки:

``` bash
# устанавливаем vue-cli
$ npm install --global vue-cli
# создаём новый проект из шаблона "webpack"
$ vue init webpack my-project
# устанавливаем зависимости
$ cd my-project
$ npm install
$ npm run dev
```

<p class="tip">CLI — это инструмент для тех, кто уже знаком с Node.js и соответствующими инструментами сборки. Если вы новичок во Vue или инструментах сборки фронтенда, рекомендуем сначала прочитать <a href="./">руководство</a>, не требующее использования инструментов сборки, а уже потом разбираться с CLI.</p>

## Объяснение различных сборок

В [папке `dist/` NPM пакета](https://unpkg.com/vue@latest/dist/) вы можете найти много разных сборок Vue.js. Вот краткий обзор отличий между ними:

| | UMD | CommonJS | ES Module |
| --- | --- | --- | --- |
| **Полная** | vue.js | vue.common.js | vue.esm.js |
| **Runtime-only** | vue.runtime.js | vue.runtime.common.js | vue.runtime.esm.js |
| **Полная (production)** | vue.min.js | - | - |
| **Runtime-only (production)** | vue.runtime.min.js | - | - |

### Термины

- **Полная**: сборка которая содержит и компилятор, и runtime.

- **Компилятор**: код, который отвечает за компиляцию строковых шаблонов в JavaScript render-функции.

- **Runtime**: код, который отвечает за создание экземпляров Vue, рендеринг и изменение виртуального DOM, и т.д. Обобщённо, это всё за исключением компилятора.

- **[UMD](https://github.com/umdjs/umd)**: UMD-сборки можно использовать сразу в браузере, подключая тегом `<script>`. Файлом по умолчанию в Unpkg CDN из [https://unpkg.com/vue](https://unpkg.com/vue) будет Runtime + Компилятор UMD-сборка (`vue.js`).

- **[CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)**: CommonJS-сборки предназначены для использования вместе со старыми инструментами сборки, такими как [Browserify](http://browserify.org/) или [Webpack 1](https://webpack.github.io). Файлом по умолчанию для них (`pkg.main`) будет Runtime-only CommonJS сборка (`vue.runtime.common.js`).

- **[ES Module](http://exploringjs.com/es6/ch_modules.html)**: ES module сборки предназначены для использования с современными инструментами сборки, такими как [Webpack 2](https://webpack.js.org) или [Rollup](http://rollupjs.org/). Файлом по умолчанию для них (`pkg.module`) будет Runtime-only ES Module сборка (`vue.runtime.esm.js`).

### Runtime + Компилятор vs. Runtime-only

Если вам нужно компилировать шаблоны на лету (например, если передавать строку в опцию `template`, или монтировать к элементу DOM, используя его HTML в качестве шаблона), вам потребуется компилятор и, таким образом, полная сборка:

``` js
// это требует компилятора
new Vue({
  template: `<div>{{ hi }}</div>`
})

// это нет
new Vue({
  render (h) {
    return h('div', this.hi)
  }
})
```

При использовании `vue-loader` или `vueify` шаблоны внутри файлов `*.vue` будут скомпилированы в JavaScript на этапе сборки. Вам не потребуется компилятор в финальной сборке, а значит можно использовать runtime-only сборки.

Так как runtime-only сборки примерно на 30% легче, в сравнении с полными сборками, вы должны использовать их всякий раз, когда это возможно. Если вы всё равно хотите использовать полную сборку, вам нужно настроить псевдоним в сборщике:

#### Webpack

``` js
module.exports = {
  // ...
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js' // 'vue/dist/vue.common.js' для webpack 1
    }
  }
}
```

#### Rollup

``` js
const alias = require('rollup-plugin-alias')

rollup({
  // ...
  plugins: [
    alias({
      'vue': 'vue/dist/vue.esm.js'
    })
  ]
})
```

#### Browserify

Добавьте в `package.json` вашего проекта:

``` js
{
  // ...
  "browser": {
    "vue": "vue/dist/vue.common.js"
  }
}
```

### Режим разработки vs. режим production

Режимы разработки/production жёстко установлены в UMD-сборках: неминифицированные файлы для разработки и минифицированные файлы для production.

CommonJS и ES Module сборки предназначены для инструментов сборки, поэтому мы не предоставляем минифицированных версий для них. Вы ответственны за минификацию итоговой сборки.

CommonJS и ES Module сборки также содержат проверки на `process.env.NODE_ENV` для определения режима, в котором они должны выполняться. Вы должны использовать соответствующие конфигурации систем сборки для замены этих переменных окружения для того чтобы контролировать режим, в котором Vue будет запускаться. Замена `process.env.NODE_ENV` строковым литералом также позволяет минификаторам, таким как UglifyJS полностью удалять блоки кода предназначенного только для разработки, уменьшая конечный размер файла.

#### Webpack

Используйте [DefinePlugin](https://webpack.js.org/plugins/define-plugin/):

``` js
var webpack = require('webpack')

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: JSON.stringify('production')
      }
    })
  ]
}
```

#### Rollup

Используйте [rollup-plugin-replace](https://github.com/rollup/rollup-plugin-replace):

``` js
const replace = require('rollup-plugin-replace')

rollup({
  // ...
  plugins: [
    replace({
      'process.env.NODE_ENV': JSON.stringify('production')
    })
  ]
}).then(...)
```

#### Browserify

Примените глобальную трансформацию [envify](https://github.com/hughsk/envify) для вашей сборки.

``` bash
NODE_ENV=production browserify -g envify -e main.js | uglifyjs -c -m > build.js
```

Также смотрите [советы по развёртыванию](deployment.html).

### CSP-окружения

Некоторые окружения, такие как Google Chrome Apps, требуют соблюдения Content Security Policy (CSP), запрещающей использование конструкции `new Function()` для исполнения выражений. Полная сборка использует подобные конструкции для компиляции шаблонов, и потому непригодна к использованию в таких окружениях.

С другой стороны, runtime-сборка полностью совместима с CSP. Если использовать её с [Webpack и vue-loader](https://github.com/vuejs-templates/webpack-simple) или [Browserify и vueify](https://github.com/vuejs-templates/browserify-simple) шаблоны прекомпилируются в `render`-функции, которые отлично работают в CSP-окружениях.

## Сборка dev-версии

**Важное замечание**: файлы сборки в папке `/dist` на GitHub обновляются только при релизах. Чтобы использовать версию из новейшего исходного кода с GitHub, соберите проект самостоятельно:

``` bash
git clone https://github.com/vuejs/vue.git node_modules/vue
cd node_modules/vue
npm install
npm run build
```

## Bower

Только UMD сборки доступны в Bower.

``` bash
# последняя стабильная версия
$ bower install vue
```

## Загрузчики модулей AMD

Все UMD сборки могут быть напрямую использованы как AMD-модули.
