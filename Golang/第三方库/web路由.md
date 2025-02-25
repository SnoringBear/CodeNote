### 一、静态路由实现

```go
package msgo

import (
	"fmt"
	"log"
	"net/http"
)

const ANY = "ANY"

type HandleFunc func(context *Context)

// routerGroup 路由组   路由分组  每个组下面有不同的路由
type routerGroup struct {
	name string
	// 支持的路由  key:路径  value method:handle
	handlerFuncMap map[string]map[string]HandleFunc
}

func (g *routerGroup) handler(name string, method string, handler HandleFunc) {
	_, ok := g.handlerFuncMap[name]
	if !ok {
		g.handlerFuncMap[name] = make(map[string]HandleFunc)
	}
	_, ok = g.handlerFuncMap[name][method]
	if ok {
		panic("有重复的路由")
	}
	g.handlerFuncMap[name][method] = handler
}

func (g *routerGroup) Any(name string, handler HandleFunc) {
	g.handler(name, ANY, handler)
}

func (g *routerGroup) Get(name string, handler HandleFunc) {
	g.handler(name, http.MethodGet, handler)
}

func (g *routerGroup) Post(name string, handler HandleFunc) {
	g.handler(name, http.MethodPost, handler)
}

// router 路由
// 路由下面有不同的分组
type router struct {
	routerGroups []*routerGroup
}

func (r *router) Group(name string) *routerGroup {
	routerGroup := &routerGroup{
		name:           name,
		handlerFuncMap: make(map[string]map[string]HandleFunc),
	}
	r.routerGroups = append(r.routerGroups, routerGroup)
	return routerGroup
}

type Engine struct {
	router
}

func New() *Engine {
	return &Engine{
		router: router{make([]*routerGroup, 0)},
	}
}

func (e *Engine) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	method := r.Method
	fmt.Println(method)
	// 便利路由组
	for _, group := range e.routerGroups {
		// 便利路由组里面的路由
		for name, methodHandle := range group.handlerFuncMap {
			url := "/" + group.name + name
			// url一致
			if r.RequestURI == url {
				ctx := &Context{
					W: w,
					R: r,
				}
				handle, ok := methodHandle[ANY]
				if ok {
					handle(ctx)
					return
				}

				handle, ok = methodHandle[method]
				if ok {
					handle(ctx)
					return
				}
				// 不支持这种请求方式
				w.WriteHeader(http.StatusMethodNotAllowed)
				fmt.Fprint(w, "method not allowed")
				return
			}
		}
	}

	w.WriteHeader(http.StatusNotFound)
	fmt.Fprint(w, "404 page not found")
}

func (e *Engine) Run(addr string) {
	// 这种方式无法区分 Get、Post
	//for _, group := range e.routerGroups {
	//	for key, handleFunc := range group.handlerFuncMap {
	//		http.HandleFunc("/"+group.name+key, handleFunc)
	//	}
	//}

	// 所有请求由Engine本身处理
	http.Handle("/", e)
	if err := http.ListenAndServe(fmt.Sprintf("%s", addr), nil); err != nil {
		log.Fatal(err)
	}
}

```



### 二、动态路由实现

```go
```

