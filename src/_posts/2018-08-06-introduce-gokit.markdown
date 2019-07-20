---
title: 'Introduce GoKit'
layout: post
date: 2018-08-06 20:15
image: /assets/images/
headerImage: false
tag:
    - golang
    - gokit
    - microservices
category: blog
author: Le Phuoc My
description: Introduce GoKit
---

Golang là ngôn ngữ tuyệt vời cho Microservices về hiệu xuất lẫn sự thân thiện đối với developer. Sự phát triển mạnh mẽ của cộng đồng Gopher ngày càng mang đến cho chúng ta những Open Source rất có ích và làm cho việc phát triển phần mềm với Go trở nên dễ dàng hơn. Gokit là một bộ toolkit tuyệt vời cho việc xây dựng các microservices với Go, cung cấp cho chúng ta các quy tắc, ý tưởng về kiến trúc nhằm giúp developer tập trung hơn vào việc phát triển business logic và tránh khỏi các mối quan tâm chung về operational lẫn infrastructural. 

Gokit là bộ toolkit nhẹ nhàng nhưng cung cấp gần như đầy đủ các thư viện đồ chơi cho một hệ thống service mesh, từ Logging, Metrics, Tracing, Rate-Limiting, Circuit Breaking cho đến Service Discovery, Pub/Sub... 

Tuy ban đầu được giới thiệu là dùng cho việc build các microservices nhưng trong qúa trình làm việc mình thấy Gokit dường như phù hợp với monolith không kém. Với Gokit, developer cần tuân theo một số nguyên tắc sau:
- Phãi nắm bắt và tuân thủ các nguyên tắc thiết kế như SOLID, DDD, Clean Architecture và áp dụng chặt chẻ.
- Các interfaces được sử dụng như là các quy ước, không có global state, declarative composition và các Dependencies phãi rõ ràng.
- Áp dụng nguyên tắc Composition over inheritance.

### Kiến trúc của Gokit:
Gokit được chia làm ba layer chính đó là 
- Transport layer
- Endpoint layer
- Service layer

Tầng Service là tầng quan trọng nhất, nơi mà developer quan tâm nhất, là nơi chứa các domain của ứng dụng cũng như implementation của tất cả business logic cho ứng dụng, nó tách biệt hoàn toàn với các phần còn lại. Hai tầng Transport và Endpoint nằm ở tầng Interface của Clean Architect như bài trước mình đã giới thiệu về Clean Architecture https://romeo.vn/clean-architecture/. Tầng transaport được liên kết với các giao thức cụ thể như Http, gRPC, Pub/Sub nhằm lấy request từ client và encoding/decoding request. Trong Gokit, RPC là giao thức chính được ưu tiên nhất, mỗi service method trong Gokit sẻ được chuyển đổi thành một endpoint để giao tiếp giữa client và server. Mỗi endpoint sẻ expose một service method ra ngoài bằng cách sử dụng Transport liên kết với một giao thức Transport cụ thể như Http Rest/gRPC hay Pub/Sub. 

Một thành phần khác không kém phần quan trọng trong Gokit đó là các **Middlewares**. Gokit thực hiện nghiêm ngặt việc separate các concerns, các **cross-cutting concern components** của tầng services và endpoints được implement bằng việc sử dụng các Middlewares. Middleware là một thành phần mạnh mẻ nhằm bọc các services và endpoints lại để thêm các các **cross-cutting concern components** như Logging, Circuit Breakers, Rate Limiting, Load Balancing, hay Distributed tracing...

Dưới đây là một hình ảnh mô tả kiến trúc của Gokit mình copy từ trang web chính thức của Gokit 

![Atom](https://gokit.io/faq/onion.png)
