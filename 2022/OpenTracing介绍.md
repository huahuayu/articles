[//title]: (OpenTracing介绍)
[//englishtitle]: (OpenTracing-introduction)
[//category]: (tracing)
[//tags]: (tracing,openTracing)
[//createtime]: (20220621)
[//updatetime]: (20220621)

Opentracing 是分布式链路追踪的一种规范标准，大概发布于 2016 年 12 月，是 CNCF（云原生计算基金会）下的项目之一。和一般的规范标准不同，Opentracing 不是传输协议，消息格式层面上的规范标准，而是一种语言层面上的 API 标准。以 Go 语言为例，只要某链路追踪系统实现了 Opentracing 规定的接口（interface），符合 Opentracing 定义的表现行为，那么就可以说该应用符合 Opentracing 标准。这意味着开发者只需修改少量的配置代码，就可以在符合 Opentracing 标准的链路追踪系统之间自由切换。

## 标准文档

https://opentracing.io/specification/

## 概念

一个 tracer 过程中，各 span 的关系：

```text
        [Span A]  ←←←(the root span)
            |
     +------+------+
     |             |
 [Span B]      [Span C] ←←←(Span C 是 Span A 的孩子节点, ChildOf)
     |             |
 [Span D]      +---+-------+
               |           |
               [Span E]    [Span F] >>> [Span G] >>> [Span H]
                                           ↑
                                           ↑
                                           ↑
                          (Span G 在 Span F 后被调用, FollowsFrom)
```

上述 tracer 与 span 的时间轴关系：

```text
––|–––––––|–––––––|–––––––|–––––––|–––––––|–––––––|–––––––|–> time

 [Span A···················································]
   [Span B··············································]
      [Span D··········································]
    [Span C········································]
         [Span E·······]        [Span F··] [Span G··] [Span H··]
```

## 规范

Opentracing json message 格式，不拘用什么客户端，只要符合这种格式的消息就可以复用 Opentracing 工具链。

```text
{
    "trace_id": undefined, // values for trace_id and span_id are not currently standardized
    "span_id": undefined,
    "references": [
        {"type":"child_of","span_id":undefined}, // currently types are "child_of" and "follows_from"
    ],
    "name": "operation type",
    "timestamp": 1234567890123456789, // UTC Epoch Unix Timestamp in Microseconds
    "duration": 300, // integer representing microseconds
    "tags": {
        "tag_key": [{"a":"b"},{"c":"d"}], // can tags have multiple values in some systems?
    },
    "logs": [
        {"timestamp":1234567890123456789, "a":"b"}, // logs optionally have a timestamp
    ],
    "baggage": {"a":"b"},
}
```

## Tracing 相关方案

Uptrace
Jaeger
Sentry
SkyWalking
SigNoz
Zipkin
Grafana Tempo
DataDog

有不少 tracing 工具底层应该是兼容 OpenTracing 协议的。

## Sample go app

https://github.com/SigNoz/sample-golang-app

## Reference

[Standard Interchange Format for OpenTracing](https://github.com/opentracing/specification/issues/64)
[distributed-tracing-tools](https://get.uptrace.dev/compare/distributed-tracing-tools.html#what-is-a-distributed-tracing-tool)
[Instrumenting a Go application with OpenTelemetry](https://signoz.io/opentelemetry/go/)
