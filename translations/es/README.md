---
description: Documentación de API para que puedas comenzar a crear applicaciones con Fiber.
---

# 📖 Empecemos

[![](https://img.shields.io/github/release/gofiber/fiber?style=flat-square)](https://github.com/gofiber/fiber/releases) [![](https://img.shields.io/badge/api-documentation-blue?style=flat-square)](https://fiber.wiki) ![](https://img.shields.io/badge/goreport-A%2B-brightgreen?style=flat-square) [![](https://img.shields.io/badge/coverage-91%25-brightgreen?style=flat-square)](https://gocover.io/github.com/gofiber/fiber) [![](https://img.shields.io/travis/gofiber/fiber/master.svg?label=linux&style=flat-square)](https://travis-ci.org/gofiber/fiber) [![](https://img.shields.io/travis/gofiber/fiber/master.svg?label=windows&style=flat-square)](https://travis-ci.org/gofiber/fiber)

**Fiber** es un **framework web** inspirado en [Expressjs](https://github.com/expressjs/express) construido sobre [Fasthttp](https://github.com/valyala/fasthttp) , el motor HTTP **más rápido** para [Go](https://golang.org/doc/) . Diseñado para **facilitar** las cosas y permitirte un desarrollo **rápido** con **cero asignación de memoria** y **rendimiento** en mente.

## Instalación

En primer lugar, [descargue](https://golang.org/dl/) e instale Go.

{%hint style="success"%} Se requiere Go **1.11** (con los [módulos Go](https://golang.org/doc/go1.11#modules) habilitados) o superior. {%endhint%}

Para instalar, puedes usar [`go get`](https://golang.org/cmd/go/#hdr-Add_dependencies_to_current_module_and_install_them):

```bash
go get -u github.com/gofiber/fiber
```

## ¡Hola Mundo!

A continuación, te mostramos la aplicación **Fiber** más simple que puede crear.

```text
touch server.go
```

```go
package main

import "github.com/gofiber/fiber"

func main() {
  // Creamos una nueva instancia de Fiber:
  app := fiber.New()
  
  // Creamos una ruta para la dirección base, "/":
  app.Get("/", func(c *fiber.Ctx) {
    c.Send("Hola, Mundo!")
    // => "Hola, Mundo!"
  })
  
  // Inciámos el servidor en localhost en el puerto :8080
  app.Listen(8080)
}
```

```bash
go run server.go
```

Abre `http://localhost:8080` en tu navegador y deberías ver `Hola, Mundo!` en la página.

## Enrutamiento básico

El enrutamiento es determinar cómo una aplicación responde a una solicitud del cliente a un endpoint, el cual es una URI (o ruta) y un método HTTP específico (GET, PUT, POST, etc.).

{%hint style="info"%} Cada ruta puede tener **uno o varios controladores**, que se ejecutan cuando la ruta coincide. {% endhint%} por la que el cliente solicitó.

La definición de ruta tiene la siguiente estructura:

```go
// Firma de la función
app.Method(func(*fiber.Ctx))
app.Method(path string, func(*fiber.Ctx))
```

- `app` es una instancia de **Fiber** .
- `Method` es un [método de solicitud HTTP](https://fiber.wiki/application#methods) , en mayúsculas: `Get` , `Put` , `Post` , etc.
- `path` es una ruta en el servidor.
- `func(*fiber.Ctx)` es una función de devolución de llamada que contiene el [contexto](https://fiber.wiki/context) ejecutado cuando la ruta coincide.

### Ruta simple

```go
// Respond with "Hello, World!" on root path, "/":
app.Get("/", func(c *fiber.Ctx) {
  c.Send("Hello, World!")
})
```

### Ruta con parámetro

```go
// GET http://localhost:8080/hello%20world

app.Get("/:value", func(c *fiber.Ctx) {
  c.Send("Get request with value: " + c.Params("value"))
  // => Get request with value: hello world
})
```

### Ruta con parámetro opcional

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

### Ruta con comodín

```go
// GET http://localhost:8080/api/user/john

app.Get("/api/*", func(c *fiber.Ctx) {
  c.Send("API path with wildcard: " + c.Params("*"))
  // => API path with wildcard: user/john
})
```

## Archivos estáticos

Para servir archivos estáticos como **imágenes** , archivos **CSS** y **JavaScript** , reemplace su controlador de funciones con un archivo o cadena de directorio.

Firma de la función:

```go
app.Static(root string)         // => without prefix
app.Static(prefix, root string) // => with prefix
```

Use el siguiente código para servir archivos en un directorio llamado `./public` :

```go
app := fiber.New()

app.Static("./public") // => Serve all files into ./public

app.Listen(8080)
```

Ahora, puede cargar los archivos que están en el directorio `./public` :

```bash
http://localhost:8080/hello.html
http://localhost:8080/js/jquery.js
http://localhost:8080/css/style.css
```
