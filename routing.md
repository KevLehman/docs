---
description: >-
  Маршрутизация относится к тому, как конечные точки приложения (URI) отвечают
  на запросы клиентов.
---

# 🔌 Маршрутизация

## пути

Маршруты маршрутов в сочетании с методом запроса определяют конечные точки, в которых могут быть сделаны запросы. Маршруты маршрутов могут быть **строками** , **шаблонами строк** или **регулярными выражениями** .

**Специальные символы**

* Персонажи `?` , `+` , `&` и `()` являются подмножествами их эквивалентов **регулярного выражения** .
* Дефис \( `-` \) и точка \( `.` \) Интерпретируются буквально путями на **основе строк** .

**Примеры маршрутов маршрута на основе строк**

```go
// This route path will match requests to the root route, "/":
app.Get("/", func(c *fiber.Ctx) {
  c.Send("root")
})

// This route path will match requests to "/about":
app.Get("/about", func(c *fiber.Ctx) {
  c.Send("about")
})

// This route path will match requests to "/random.txt":
app.Get("/random.txt", func(c *fiber.Ctx) {
  c.Send("random.txt")
})
```

**Примеры маршрутов маршрута на основе строковых шаблонов**

```go
// This route path will match:
// only "/acd" and "/abcd"
app.Get("/ab?cd", func(c *fiber.Ctx) {
  c.Send("/ab?cd")
})

// This route path will match:
// "/abcd", "/abbcd", "/abbbcd" and so on
app.Get("/ab+cd", func(c *fiber.Ctx) {
  c.Send("ab+cd")
})

// This route path will match:
// "/abcd", "/abxcd", "/abRANDOMcd", "/ab123cd" and so on
app.Get("/ab*cd", func(c *fiber.Ctx) {
  c.Send("ab*cd")
})

// This route path will match:
// only "/abe" and "/abcde"
app.Get("/ab(cd)?e", func(c *fiber.Ctx) {
  c.Send("ab(cd)?e")
})
```

## параметры

Параметры маршрута - это **именованные сегменты URL** , которые используются для захвата значений, указанных в их позиции в URL. Полученные значения могут быть получены с помощью функции [Params](https://fiber.wiki/context#params) с именем параметра маршрута, указанным в пути в качестве соответствующих ключей.

{% hint style="info" %}
Имя параметра маршрута должно состоять из **символов слова** \( `[A-Za-z0-9_]` \).
{% endhint %}

{% hint style="danger" %}
Дефис \( `-` \) и точка \( `.` \) буквально еще **не** интерпретируются. Планируется для **Fiber** v2.
{% endhint %}

**Пример определения маршрутов с параметрами маршрута**

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

## Промежуточное

Функции, предназначенные для внесения изменений в запрос или ответ, называются **функциями промежуточного программного обеспечения** . [Next](https://github.com/gofiber/docs/tree/34729974f7d6c1d8363076e7e88cd71edc34a2ac/context/README.md#next) - это функция **Fibre** router, при вызове она выполняет **следующую** функцию, которая **соответствует** текущему маршруту.

**Пример функции промежуточного программного обеспечения**

```go
app.Use(func(c *fiber.Ctx) {
  // Set some security headers:
  c.Set("X-XSS-Protection", "1; mode=block")
  c.Set("X-Content-Type-Options", "nosniff")
  c.Set("X-Download-Options", "noopen")
  c.Set("Strict-Transport-Security", "max-age=5184000")
  c.Set("X-Frame-Options", "SAMEORIGIN")
  c.Set("X-DNS-Prefetch-Control", "off")

  // Go to next middleware:
  c.Next()
})

app.Get("/", func(c *fiber.Ctx) {
  c.Send("Hello, World!")
})
```

Путь метода `Use` - это путь **монтирования** или **префикса, который** ограничивает промежуточное программное обеспечение только для всех запрошенных путей, начинающихся с него. Это означает, что вы не можете использовать `:params` в методе `Use` .

{% hint style="info" %}
Если вы **не уверены,** когда использовать **All** или **Use** : прочитайте об [API методов здесь](https://fiber.wiki/application#methods) .
{% endhint %}

