# vue-qa-test

### Демо

https://gtix.github.io/vue-qa-test

## Установка

```bash
npm install vue-qa-test
```

## Добавление в проект

```js
// main.js
import VueQaTest from "vue-qa-test";
Vue.component("vue-qa-test", VueQaTest);
```

## Пример

```html
<vue-qa-test
  :questions="test.questions"
  :title="test.title"
  :mode="test.mode"
  :timer="test.timer"
  :shuffle-questions="test.shuffle_questions"
  :shuffle-answers="test.shuffle_answers"
  @onFinal="saveResult"
/>
```

## Параметры

| props(Parameter)       | Type              | Default    | Description                                                      |
| ---------------------- | ----------------- | ---------- | ---------------------------------------------------------------- |
| questions (*required*) | `Array`           | -          | Список вопросов (пример ниже)                                    |
| onFinal (*required*)   | `Function`        | -          | Функция завершения теста, в параметры приходит объект с ответами |
| title                  | `String`          | -          | Заголовок теста                                                  |
| mode                   | `String`          | `multiply` | Режим отображения вопросов                                       |
| timer                  | `Number, Boolean` | `false`    | Таймер для одиночных вопросов(mode - `single`). В секундах.      |
| shuffle-questions      | `Boolean`         | `false`    | Вопросы перемешиваются при инициализации                         |
| shuffle-answers        | `Boolean`         | `false`    | Ответы к вопросам перемешиваются при инициализации               |

### Режимы отображения (mode)

#### Вопросы

- `multiply` - Вопросы отображаются списком. Внизу кнопка завершения теста.
- `single` - Вопросы отображаются по одному. После ответа на вопрос - появляется следующий, и так до тех пор, пока не закончатся вопросы.

#### Ответы

- `multiply` - Можно выбрать несколько ответов (checkbox).
- `single` - Ответ может быть только один (radio)

## Пример структуры вопроса

```json
"questions": [
      {
        "title": "Вопрос с одним ответом", // Название
        "mode": "single", // Режим отображения ответов
        "hash": "mxkpCp5Q0vtVzvAy", // Уникальное значение вопроса
        "answers": [
          // Список ответов
          {
            "id": "rdXTx0N108YU4xSq", // Уникальное значение ответа
            "text": "Ответ 1" // Текст ответа
          },
          {
            "id": "d0TVhRn3Elyg3EmT",
            "text": "Ответ 2"
          },
          {
            "id": "w1gdasDhQcRgDhs6",
            "text": "Ответ 3"
          }
        ]
      },
      ...
]
```

## Пример результата (@onFinal)

```json
{
  "wPZR1lKkGu8qUGT7":[ // HASH вопроса
    // К данному вопросу можно было выбрать несколько ответов
    { 
      // Данные ответа
      "id":"bxNrX5DehtP0fM9t", // ID - Уникальное значение ответа
      "text":"Ответ 1" // Текст ответа
    },
    {
      "id":"TgwXRC77BuL9GU0e",
      "text":"Ответ 2"
    }
  ],
  "mxkpCp5Q0vtVzvAy":{ // Hash вопроса
    // К данному вопросу можно было выбрать только один ответ
    "id":"d0TVhRn3Elyg3EmT",
    "text":"Ответ 2"
  }
}
```
