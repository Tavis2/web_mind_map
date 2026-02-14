
# Behavior Driven Development
**BDD ( [Behavior Driven Development](https://ru.wikipedia.org/wiki/BDD_\(%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5\))) – это три в одном: и тесты, и документация, и примеры использования.**

Основной идеей данной методологии является совмещение в процессе разработки чисто технических интересов и интересов бизнеса, позволяя тем самым управляющему персоналу и программистам говорить на одном языке. Для общения между этими группами персонала используется [предметно-ориентированный язык](https://ru.wikipedia.org/wiki/%D0%9F%D1%80%D0%B5%D0%B4%D0%BC%D0%B5%D1%82%D0%BD%D0%BE-%D0%BE%D1%80%D0%B8%D0%B5%D0%BD%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%BD%D1%8B%D0%B9_%D1%8F%D0%B7%D1%8B%D0%BA "Предметно-ориентированный язык"), основу которого представляют конструкции из естественного языка, понятные неспециалисту, обычно выражающие поведение программного продукта и ожидаемые результаты.

BDD методология заключается в том, что перед тем как написать какой-либо тест, необходимо сначала описать желаемый результат от добавляемой функциональности на предметно-ориентированном языке. После того как это будет проделано, конструкции этого языка переводятся специалистами или специальным программным обеспечением в описание теста.

BDD фокусируется на следующих вопросах:
- С чего начинается процесс.
- Что нужно тестировать, а что нет.
- Сколько проверок должно быть совершено за один раз.
- Что можно назвать проверкой.
- Как понять, почему тест не прошёл.

## Пример: Проверка калькулятора (чистый JS)
### Требование (поведение)
В BDD всё начинается с описания поведения:
> Пользователь должен иметь возможность войти в систему,  
> если он ввёл правильный email и пароль.  
> Если данные неверные — система должна вернуть ошибку.

### Сценарий (Given / When / Then)

BDD-мышление:
```
Given пользователь с email "test@mail.com" и паролем "1234"  
When он вводит корректные данные  
Then система возвращает успешную авторизацию  
  
Given пользователь вводит неправильный пароль  
Then система возвращает ошибку
```
### Реализация функции (бизнес-логики)

```js
// auth.js
function login(email, password) {  
  const user = {  
    email: "test@mail.com",  
    password: "1234"  
  };  
  
  if (email === user.email && password === user.password) {  
    return { success: true };  
  }  
  
  return { success: false, message: "Invalid credentials" };  
}  
  
module.exports = login;
```
### BDD-тест (describe / it стиль)

```js
const assert = require("assert");
const login = require("./auth");

/*
Feature: User Login
*/

function describe(description, fn) {
  console.log(description);
  fn();
}

function it(description, fn) {
  try {
    fn();
    console.log("  ✔ " + description);
  } catch (error) {
    console.log("  ✘ " + description);
    console.error("    ", error.message);
  }
}

/*
Scenario 1: Successful login
*/
describe("User Login Feature", () => {

  describe("When user provides correct credentials", () => {

    it("should return success true", () => {
      const result = login("test@mail.com", "1234");
      assert.strictEqual(result.success, true);
    });

  });

  /*
  Scenario 2: Wrong password
  */
  describe("When user provides wrong password", () => {

    it("should return success false and error message", () => {
      const result = login("test@mail.com", "wrong");

      assert.strictEqual(result.success, false);
      assert.strictEqual(result.message, "Invalid credentials");
    });

  });

});
```

# Test Driven Development

**TDD (Test Driven Development) – это подход к разработке, при котором тесты пишутся до реализации кода.**

Чтобы понять TDD — рассмотрим практический пример разработки.

Основная идея методологии заключается в том, что разработчик сначала формулирует ожидаемое поведение системы в виде автоматического теста, затем пишет минимальный код, необходимый для прохождения этого теста, и только после этого выполняет рефакторинг. Таким образом, процесс разработки становится управляемым, предсказуемым и безопасным.

Классический цикл TDD состоит из трёх шагов:

- **Red** — написать тест, который падает.
- **Green** — написать минимальный код, чтобы тест прошёл.
- **Refactor** — улучшить код без изменения поведения.

TDD фокусируется на следующих вопросах:

- Какой именно результат мы ожидаем?
- Как минимально реализовать требуемое поведение?
- Не сломали ли мы существующую функциональность?
- Можно ли сделать код проще и чище?
## Пример: Функция сложения (чистый JS)
### Red (пишем падающий тест)
```js
// test.js:

const assert = require("assert");  
const sum = require("./sum");  
  
// Тест 1: корректное сложение  
assert.strictEqual(sum(2, 3), 5);  
  
// Тест 2: сложение с нулём  
assert.strictEqual(sum(5, 0), 5);  
  
// Тест 3: отрицательные числа  
assert.strictEqual(sum(-2, -3), -5);  
  
// Тест 4: проверка ошибки типов  
assert.throws(  
  () => sum("2", 3),  
  /Arguments must be numbers/  
);  
console.log("All tests passed ✅");
```
На этом этапе тест упадёт, потому что `sum.js` ещё не существует.
### Green (минимальная реализация)
```js
// sum.js:

function sum(a, b) {  
  return a + b;  
}  
  
module.exports = sum;
```
Теперь первые три теста проходят, но тест на типы падает.
### Шаг 3 — Red снова (добавили новый тест — он падает)

Тест с `assert.throws` показывает, что функция не обрабатывает ошибки типов.

### Шаг 4 — Green (дорабатываем код)
```js
// sum.js:

function sum(a, b) {  
  if (typeof a !== "number" || typeof b !== "number") {  
    throw new Error("Arguments must be numbers");  
  }  
  
  return a + b;  
}  
  
module.exports = sum;
```
# Главное отличие TDD от BDD

- **TDD** фокусируется на корректности кода и его внутренней логике.
- **BDD (Behavior Driven Development)** фокусируется на поведении системы с точки зрения пользователя и бизнеса.

Если кратко:
- TDD — сначала тест, потом код.
- Каждый кусок кода появляется потому, что есть тест.
- Архитектура формируется через тестирование.

TDD помогает писать более чистый, покрытый тестами и поддерживаемый код, снижая количество багов и упрощая рефакторинг.