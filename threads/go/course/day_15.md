# Golang - Day 15

## Distributed Tracing
- Tại sao cần Tracing?
- Opencensus + Jaeger

Resources:
- Jaeger: https://www.jaegertracing.io/docs/1.22/getting-started/
- https://opencensus.io/introduction/
- https://www.jaegertracing.io/docs/1.8/
- https://eng.uber.com/distributed-tracing/
- https://itnext.io/when-istio-meets-jaeger-an-example-of-end-to-end-distributed-tracing-2c136eb335eb
- https://medium.com/observability/debugging-latency-in-go-1-11-9f97a7910d68
- go.opencensus.io v0.20.2

Lưu ý
- Nhớ chỉnh tần suất tracing để ko ảnh hưởng performance -> khoảng 0.1
- Thường lần đầu truy cập thì db sẽ tốn thời gian buffer, caching
- Tại sao cần context:
    - Hỗ trợ tracing -> hierachy
- Chỉ nên đặt tracing ở IO thôi
