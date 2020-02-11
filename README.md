---
description: Размещенная документация, так что вы можете начать создавать веб-приложения с Fiber.
---

# 📖 Начало работы

[![](https://img.shields.io/github/release/gofiber/fiber?style=flat-square)](https://github.com/gofiber/fiber/releases) [![](https://img.shields.io/badge/api-documentation-blue?style=flat-square)](https://fiber.wiki) ![](https://img.shields.io/badge/goreport-A%2B-brightgreen?style=flat-square) [![GitHub license](https://img.shields.io/badge/coverage-91%25-brightgreen?style=flat-square)](https://gocover.io/github.com/gofiber/fiber) [![Join the chat at https://gitter.im/gofiber/community](https://img.shields.io/travis/gofiber/fiber/master.svg?label=linux&style=flat-square)](https://travis-ci.org/gofiber/fiber) [![](https://img.shields.io/travis/gofiber/fiber/master.svg?label=windows&style=flat-square)](https://travis-ci.org/gofiber/fiber)

**Fiber** - это вдохновленная [Expressjs](https://github.com/expressjs/express) **веб-инфраструктура,** [созданная](https://github.com/valyala/fasthttp) на основе [Fasthttp](https://github.com/valyala/fasthttp) , самого **быстрого** HTTP-движка для [Go](https://golang.org/doc/) . Разработанный, чтобы **упростить** процесс **быстрой** разработки с **нулевым распределением памяти** и **производительностью** .

## Установка

Прежде всего, [скачайте](https://golang.org/dl/) и установите Go.

Требуется {% hint style = "success"%} Go **1.11** (с включенными [модулями Go](https://golang.org/doc/go1.11#modules) ) или выше. {% endhint%}

Установка выполняется с помощью команды [`go get`](https://golang.org/cmd/go/#hdr-Add_dependencies_to_current_module_and_install_them) :

```bash
go get -u github.com/gofiber/fiber
```

## Привет, мир!

Ниже приведено простейшее приложение **Fiber** , которое вы можете создать.

```text
touch server.go
```

```go
package main

import "github.com/gofiber/fiber"

func main() {
  // Create new Fiber instance:
  app := fiber.New()
  
  // Create route on root path, "/":
  app.Get("/", func(c *fiber.Ctx) {
    c.Send("Hello, World!")
    // => "Hello, World!"
  })
  
  // Start server on "localhost" with port "8080":
  app.Listen(8080)
}
```

```text
go run server.go
```

Перейдите по `http://localhost:8080` и вы должны увидеть `Hello, World!` на странице.

## Базовая маршрутизация

Маршрутизация относится к определению того, как приложение отвечает на запрос клиента к конкретной конечной точке, которая является URI (или путем) и конкретным методом HTTP-запроса (GET, PUT, POST и т. Д.).

{% hint style = "info"%} Каждый маршрут может иметь **одну функцию-обработчик** , которая выполняется при сопоставлении маршрута. {% endhint%}

Определение маршрута принимает следующие структуры:

```go
// Function signature
app.Method(func(*fiber.Ctx))
app.Method(path string, func(*fiber.Ctx))
```

- `app` является экземпляром **Fiber** .
- `Method` - это [метод HTTP-запроса](https://fiber.wiki/application#methods) , с заглавной буквы: `Get` , `Put` , `Post` и т. Д.
- `path` - это путь на сервере.
- `func(*fiber.Ctx)` - это функция обратного вызова, содержащая [контекст,](https://fiber.wiki/context) выполняемый при сопоставлении маршрута.

### Простой маршрут

```go
// Respond with "Hello, World!" on root path, "/":
app.Get("/", func(c *fiber.Ctx) {
  c.Send("Hello, World!")
})
```

### Маршрут с параметром

```go
// GET http://localhost:8080/hello%20world

app.Get("/:value", func(c *fiber.Ctx) {
  c.Send("Get request with value: " + c.Params("value"))
  // => Get request with value: hello world
})
```

### Маршрут с необязательным параметром

```go
// GET http://localhost:8080/hello%20world

app.Get("/:value?", func(c *fiber.Ctx) {
  if c.Params("value") != "" {
    c.Send("Get request with value: " + c.Params("Value"))
    // => Get request with value: hello world
    return
  }
  
  c.Send("Get request without value")
})
```

### Маршрут с подстановочным знаком

```go
// GET http://localhost:8080/api/user/john

app.Get("/api/*", func(c *fiber.Ctx) {
  c.Send("API path with wildcard: " + c.Params("*"))
  // => API path with wildcard: user/john
})
```

## Статические файлы

Чтобы обслуживать статические файлы, такие как **изображения** , файлы **CSS** и **JavaScript** , замените ваш обработчик функций строкой файла или каталога.

Подпись функции:

```go
app.Static(root string)         // => without prefix
app.Static(prefix, root string) // => with prefix
```

Используйте следующий код для обслуживания файлов в каталоге с именем `./public` :

```go
app := fiber.New()

app.Static("./public") // => Serve all files into ./public

app.Listen(8080)
```

Теперь вы можете загрузить файлы, которые находятся в каталоге `./public` :

```bash
http://localhost:8080/hello.html
http://localhost:8080/js/jquery.js
http://localhost:8080/css/style.css
```
