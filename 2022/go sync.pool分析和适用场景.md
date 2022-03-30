[//title]: (go-sync.Pool-分析和适用场景)
[//englishtitle]: (go-sync-pool)
[//category]: (go,concurrent-programming,snippet)
[//tags]: (go,concurrent-programming,snippet)
[//createtime]: (20220330)
[//updatetime]: (20220330)

`sync.Pool` 可以在多个 goroutine 中通过减少结构体实例化从而减少 GC。请注意，这不是单例模式，只是尽可能通过复用减少实例创建。

`sync.Pool` 对外有 3 个接口：

- `New()` - 定义实例创建的方法 （注意，要并发安全）
- `Get()` - 从 pool 中获取实例，如果没有就创建。将实例返回同时会从 pool 中删除实例 （这时如果另一个 goroutine 再来 get 实例，会创建新实例）
- `Put()` - 将使用过后的实例归还到 pool 中

注意：

- 在请求量越大且单个请求处理时间越短的时候越有效
- `Get` 后一定要 reset 实例内容再使用，不保证每次 `Get` 回来的是同一个实例

实例：gin 框架需要为每一个 http 请求分配一个 context，为了减少 context 的创建就用了这个方法。

```go
func New() *Engine {
	engine := &Engine{

	}
	engine.pool.New = func() interface{} {
		return engine.allocateContext()
	}
	return engine
}

func (engine *Engine) allocateContext() *Context {
	return &Context{engine: engine}
}
```

```go
func (engine *Engine) ServeHTTP(w http.ResponseWriter, req *http.Request) {
	c := engine.pool.Get().(*Context)
	c.writermem.reset(w)
	c.Request = req
	c.reset()

	engine.handleHTTPRequest(c)

	engine.pool.Put(c)
}
```

```go
func (c *Context) reset() {
	c.Writer = &c.writermem
	c.Params = c.Params[:0]
	c.handlers = nil
	c.index = -1

	c.fullPath = ""
	c.Keys = nil
	c.Errors = c.Errors[:0]
	c.Accepted = nil
	c.queryCache = nil
	c.formCache = nil
	*c.params = (*c.params)[:0]
	*c.skippedNodes = (*c.skippedNodes)[:0]
}
```
