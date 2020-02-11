---
description: >-
  Маршрутизация отвечает за то, как конечные точки приложения (URI) отвечают на
  запросы клиентов.
---

# 🔌 Маршрутизация

## Routes

Маршруты \(_routes_\) в сочетании с методом запроса определяют конечные точки \(_endpoints_\), в которых могут быть сделаны запросы. Маршруты могут быть **строками**, **шаблонами строк** или **регулярными выражениями**.

#### **Специальные символы**

* Специальные символы `?` , `+` , `&` и `()` являются подмножествами их эквивалентов **регулярного выражения**.
* Дефис \( `-` \) и точка \( `.` \) интерпретируются буквально путями на **основе строк**.

#### **Примеры маршрутов на основе строк**

```go
// Это маршрут для корневого адреса, "/":
app.Get("/", func(c *fiber.Ctx) {
  c.Send("root")
})

// Это маршрут для адреса "/about":
app.Get("/about", func(c *fiber.Ctx) {
  c.Send("about")
})

// Это маршрут для адреса "/random.txt":
app.Get("/random.txt", func(c *fiber.Ctx) {
  c.Send("random.txt")
})
```

#### **Примеры маршрутов на основе строковых шаблонов**

```go
// Этот маршрут верен только для:
// "/acd" и "/abcd"
app.Get("/ab?cd", func(c *fiber.Ctx) {
  c.Send("/ab?cd")
})

// Этот маршрут верен для:
// "/abcd", "/abbcd", "/abbbcd" и т.д.
app.Get("/ab+cd", func(c *fiber.Ctx) {
  c.Send("ab+cd")
})

// Этот маршрут верен для:
// "/abcd", "/abxcd", "/abRANDOMcd", "/ab123cd" и т.д.
app.Get("/ab*cd", func(c *fiber.Ctx) {
  c.Send("ab*cd")
})

// Этот маршрут верен только для:
// "/abe" и "/abcde"
app.Get("/ab(cd)?e", func(c *fiber.Ctx) {
  c.Send("ab(cd)?e")
})
```

## Params

Параметры маршрута — это **именованные сегменты URL**, которые используются для поления значений, указанных в URL адресе. Значения могут быть получены с помощью функции [Params](https://fiber.wiki/context#params) с именем параметра маршрута, указанным в пути в качестве соответствующих ключей.

{% hint style="info" %}
Имя параметра маршрута должно состоять из **символов слова** \( `[A-Za-z0-9_]` \).
{% endhint %}

{% hint style="danger" %}
Дефис \(`-`\) и точка \(`.`\) еще **не** интерпретируются. Планируется для **Fiber** v2.
{% endhint %}

#### **Пример определения маршрутов с параметрами маршрута**

```go
app.Get("/user/:name/books/:title", func(c *fiber.Ctx) {
  c.Write(c.Params("name"))
  c.Write(c.Params("title"))
})

app.Get("/user/*", func(c *fiber.Ctx) {
  c.Send(c.Params("*"))
})

app.Get("/user/:name?", func(c *fiber.Ctx) {
  c.Send(c.Params("name"))
})
```

## Middleware

Функции, предназначенные для внесения изменений в запрос или ответ, называются промежуточными функциями \(_middleware_\). [Next](https://github.com/gofiber/docs/tree/34729974f7d6c1d8363076e7e88cd71edc34a2ac/context/README.md#next) — это функция роутера **Fiber**, которая при вызове выполняет **следующую** функцию, **соответствующую** текущему маршруту.

#### **Пример**

```go
app.Use(func(c *fiber.Ctx) {
  // Установим некоторые заголовки:
  c.Set("X-XSS-Protection", "1; mode=block")
  c.Set("X-Content-Type-Options", "nosniff")
  c.Set("X-Download-Options", "noopen")
  c.Set("Strict-Transport-Security", "max-age=5184000")
  c.Set("X-Frame-Options", "SAMEORIGIN")
  c.Set("X-DNS-Prefetch-Control", "off")

  // А теперь, перейдем в следующую функцию:
  c.Next()
})

app.Get("/", func(c *fiber.Ctx) {
  c.Send("Hello, World!")
})
```

Путь метода **Use** — это путь **монтирования** или **префикса**, который ограничивает middleware функцию только для всех запрошенных путей, начинающихся с него. Это означает, что вы не можете использовать `:params` в методе **Use**.

{% hint style="info" %}
Если вы **не** уверены, что использовать **All** или **Use**: прочитайте об [API методов здесь](https://fiber.wiki/application#methods).
{% endhint %}

