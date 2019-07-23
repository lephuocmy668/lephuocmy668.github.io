---
title: 'Xàm xí về Domain Driven Design - Part 1'
layout: post
date: 2019-07-17 20:12
headerImage: true
tag:
    - ddd
    - domain-driven-design
    - domain-driven-development
    - microservice
category: blog
author: Le Phuoc My
description: Xàm xí về Domain Driven Design - Part 1
---

Đến thời điểm hiện tại tính luôn cả thời gian làm internship là mình tròn ba năm làm việc trong lĩnh vực software development. Hai năm cho những dự án nhỏ với Node.js cũng như Golang và một năm cho một dự án thực sự lớn với một số công nghệ tương đối cool. Ba năm với những giai đoạn  phát triển khác nhau với nhiều trải nghiệm quý giá. Mấy hôm nay tận dụng khoản thời gian nghỉ bệnh rãnh rỗi mình viết một chút xàm xí về thứ mà mình nghiêm túc nghiên cứu trong một năm vừa rồi - Domain Driven Design. 

Thực sự Domain Driven Design thì đã từng nghe và tìm hiểu sơ về nó từ hai năm trước tuy nhiên về quy mô dự án cũng như khả năng bản thân tại mỗi thời điểm chưa thể lĩnh hội được cũng như áp dụng trong thực tế. Đến khoản thời gian một năm đổ lại đây thì mình mới cảm nhận về tính thiết yếu của DDD mới thực sự rõ ràng đối với software engineer đặc biệt là trong các dự án lớn có độ phức tạo cao. Trong chuỗi bài xàm xí này mình sẻ cố gắn tập trung trả lời, chốt hạ kiến thức bản thân về mộ số câu hỏi thường gặp trong quá trình làm việc cũng như bạn bè đồng nghiệp dễ mắc phãi, bạn đọc nếu có thấy bất cứ sai lầm thiếu sót nào mong rằng sẻ comment ở dưới để mình cũng cố thêm.

### Vậy Domain là gì? 
**Domain** - ngày trước mình cũng rất mơ hồ khi nghe từ này, vì bản chất từ này mang một ý nghĩa rất rộng là tất cả những gì mà một tổ chức làm và thế giới mà tổ chức đó thuộc về. Có phãi là rất mơ hồ đúng không nào?

Theo một lẽ thông thường, các doanh nghiệp/tổ chức sẻ xác định thị trường và bán sản phẩm, dịch vụ của họ. Mỗi loại tổ chức, doanh nghiệp sẻ có các bí quyết và cách thức riêng để thực hiện các thứ liên quan đến thị trường, sản phẩm dịch vụ của họ ví dụ như KFC sinh ra để cung cấp cho người tiêu dùng những bữa ăn nhanh nhằm gãi ngứa cho những người bận rộn, Tiki hướng đến việc cung cấp một giải pháp B2C mà sau đó chuyển đổi sang Marketplace để phục vụ người mua và người bán. Grab cung cấp một giải pháp kinh tế chia sẻ giữa tài xế, cửa hàng tiện ích, người đi mua hàng, người bán dịch vụ, người có nhu cầu đi lại....nhằm tối ưu hoá các hoạt động đi lại, mua sắm, vận chuyển.... 

Tổng hợp của sự hiểu biết về thị trường, người dùng hướng đến, quan hệ giữa các loại thực thể trong thế giới mà các doanh nghiệp đó muốn xây dựng nên, các phương pháp mà các doanh nghiệp như KFC, Grab, Tiki tạo ra sản phẩm dịch vụ của họ sẻ được gói gọn bằng từ Domain.

### DDD là gì? Tại sao chúng ta cần DDD?
Bạn đã từng có trải nghiệm làm việc trong một Enterprise System thực sự cool chưa? Nơi mà bộ khung của hệ thống đã được dựng vững chắc, tất cả các công việc đều trở nên dễ dàng, việc thêm tính năng mới diễn ra nhẹ nhàng hơn bao giờ hết. 

Nghe có vẻ diệu kì nhỉ! Thực tế là mình vẫn chưa được trải nghiệm cảm giác nhẹ nhàng đó nhưng trong giai đoạn này vẫn sẻ vật lộn để có thể góp một chút gì đó giúp hệ thống mà cá nhân được tham gia vào dần dần đi đến viễn cảnh tốt đẹp kia. Tuy nhiên về trải nghiệm có thể thấy rằng các vấn đề trước đây mình quan sát thấy trong hệ thống tồn tại như sau:
- Trong một hệ thống lớn, phức tạp luôn luôn tồn tại các vấn đề coupling và bottlenecks giữa các team trong development cũng như management. Nếu chúng ta có được một kiến ​​trúc loosely-coupled, encapsulate tốt với cấu trúc tổ chức phù hợp, chúng ta có thể đạt được hiệu suất tốt hơn và tăng đáng kể quy mô của dev team cũng như tăng năng suất dev.
- Trong việc phát triển phần mềm chúng ta thường có thể có ý tưởng rằng nên tạo một model duy nhất, gắn kết chặt chẻ, bao gồm toàn bộ business domain của tổ chức, giống như một mô hình hoàn chỉnh của doanh nghiệp. Tuy nhiên bất kỳ nỗ lực nào để define business của một tổ chức vừa và lớn trong một model duy nhất bao gồm tất cả mọi thứ sẽ là rất khó khăn và thường sẽ thất bại vì quá rối rắm. Mình từng tham gia một dự án nhỏ gặp trường hợp đó, mọi người cố gắn tạo ra một model duy nhất bao quát từ ban đầu, mặt dù lượng logic chưa là gì so với dự án hiện tại mình tham gia tuy nhiên việc quá ôm đồm vào một model, logic chồng chéo khiến cả chục con người mô hình hoá hơn 2 tuần vẫn chưa hoàn chĩnh và có thể để lại nhiều sai lầm về sau.
- Việc phát triển một hệ thống monolith khổng lồ là nguyên nhân dẫn đến cơn đau đầu vô cùng nặng và dai dẳng, làm trì trệ tốc độ cũng như khó có thể linh hoạt được trong quá trình phát triển cũng như scale hệ thống.
- Tuy nhiên việc tìm các service boundaries để chia nhỏ thực sự rất khó, việc thiếu domain knowledge và có quá nhiều nhiều [God object](https://en.wikipedia.org/wiki/God_object) thực sự là cản trở lớn cho việc chia nhỏ hệ thống, làm chúng ta không biết nên chia như thế nào.
- Các mục tiêu business cần được deliver một cách bền vững, nhưng phần mềm thì ngày một giảm đi tính maintainable do nhiều nguyên nhân mà cơ bản đầu tiên là về việc quản lí tổ chức hệ thống không thực sự tốt. Chúng ta cần xây dựng được một kiến trúc ngoài khả năng đáp ứng các yêu cầu business còn phãi đảm bảo tính mở rộng cũng như maintainable.

Để tạo ra một Enterprise System, chúng ta phải đảm bảo liên tục giảm độ phức tạp. Để giảm thiểu độ phức tạp, chúng ta cần hiểu sự phức tạp nào thực sự cần thiết để mô hình hóa domain của chúng ta. Và DDD về cơ bản là một tập hợp các principles, concepts, parterns, tư tưởng, quy trình hướng đến việc thảo luận, lắng nghe, thấu hiểu và khám phá các giá trị business từ đó sẻ giúp chúng ta thoả mãn các vấn đề kể trên. 

- DDD yêu cầu Domain Expert/BA team làm việc với Dev team cũng như các bên liên quan khác dựa trên một tiếng nói chung, khi đó việc chuyển đổi yêu cầu giữa Domain Expert/BA với các bên liên quan dười như bằng không, việc học hỏi củng cố domain knowledge sẻ dễ dàng và chính xác hơn.
- Các Bounded Context rõ ràng và các context map tổ chức tốt cũng như domain knowledge được cũng cố vững chắc  giúp chúng ta tránh được sự bội thực về mặt ngữ nghĩa giữa các thuật ngữ, khái niệm, đối tượng. Các concepts sẻ được chia nhỏ, đổi tên, custom sao cho hợp lí sẻ giúp chúng ta tránh được việc đưa ra các God object. Đảm bảo hệ thống sẻ được chia nhỏ thành các phần đơn giản hơn đi theo các nguyên tắc Isolation, Low coupling, Comprehensibility, High cohesion, Parallelisation/Autonomy.
- Khi chia ra được các Bounded Context rõ ràng cũng như các context map được tổ chức tốt sẻ giúp engineer team dễ dàng đáp ứng các yêu cầu logic lúc design ban đầu, logic ít chồng chéo đỡ nặng đầu hơn cũng như tổ chức code tốt hơn, có tầm nhìn tốt về hướng phát triển code base để dễ bảo trì, thay thế, bổ sung cũng như improve về sau. Các cũng manager dễ dàng quản lí phân công trách nhiệm cũng như đánh độ ưu tiên, đưa ra quyết định.
- Một khi chúng ta đã chia nhỏ hệ thống thành các modules riêng biệt tuân theo các nguyên tắc Isolation, Low coupling, Comprehensibility, High cohesion, Parallelisation/Autonomy sẻ giúp chúng ta thành công hơn trong quá trình phát triển, bảo trì cũng như quản lí đội ngủ. Vì mọi thứ được tách biệt mạnh mẻ nên một khi có thêm chức năng mới hay thay đổi chức năng, logic nào đó, chúng ta chỉ cần xem xét mức độ ảnh hưởng từ từng khu vực và đối với từng khu vực có quyết định cụ thể riêng biệt dứt khoát mà không lo ngại nhiều. Còn nếu vẫn để design một cách ôm đồm dễ dấn đến chuyện khó xác định được tầm ảnh hưởng cũng như việc đưa ra các quyết định phãi mất rất nhiều thời gian khi có thay đổi xãy ra.
- Việc các phần được chia ra đạt được tính Isolation, Parallelisation/Autonomy sẻ giúp chúng ta tăng tốc độ phát triển một cách tối đa nhất, loại bỏ tối đa các dấu hiệu quản lí tập trung, các dependencies trong dự án rõ ràng và dễ kiểm soát hơn giúp chúng ta tránh được các vấn đề coupling và bottlenecks trong development cũng như management. Microservice approach cũng chỉ là một cách tiếp cận trên con đường DDD mà thôi.
- Việc chia như thế nào, chiến lược design ra sao, các yêu cầu mục đích design là gì thì DDD sẻ cung cấp thêm các công cụ, building block partern nhằm giúp chúng ta implement chuyện đó, từ đó chúng ta có được một kiến trúc đảm bảo tính mở rộng cũng như maintainable.
- Về trãi nghiệm cá nhân, nếu có kiến thức về DDD sớm hơn và tốt hơn thì có thể các sai lầm về design trong các dự án đã phần nào tránh được, các approach như microservices, event sourcing, reactive system sẻ được nhìn nhận đúng đắn hơn đồng thời xử lí các business logic rối rắm phức tạp, các vấn đề scale, concurrency,...đã được giải quyết một cách đơn giản, hiệu quả hơn.

### Bức tranh tổng quát về DDD:
Về DDD mình chủ yếu tập trung vào ba mục chính:

- Strategic Modeling:

    Thiết kế các Enterprise Software cần được thực hiện một cách khôn ngoan và hợp lí, Strategic Modeling là tập hợp các cách tiếp cận nhằm giúp chúng ta mô hình hoá một hệ thống cho phép chúng ta có khả năng chia để trị domain lớn, lập kế hoạch cho tương lai cũng như đưa ra quyết định phù hợp với sứ mệnh và giá trị cốt lõi của domain mà chúng ta muốn làm. 

    Strategic Modeling tập trung vào việc define cũng như sử dụng các Bounded Context, SubDomain, quản lí ngữ nghĩa của các concepts giữa các context khác nhau... đồng thời rất hữu ích để trong việc cung cấp các khía cạnh phản biện, tranh luận để tìm ra một strategic architecture tốt phù hợp với domain chúng ta hướng tới. 

    Việc có một bản Context Map và các khái niệm cốt lõi chung giúp chúng ta dễ dàng improve bức tranh tổng quan của toàn hệ thống. Đặc biệt đối với Microservices approach, Strategic Modeling giúp chúng ta define và kết nối các service một cách đúng đắn tuân theo nguyên tắc low coupling, high cohesion bằng cách xác định các Bounded Context, Subdomain một cách phù hợp.

- Tactical Modeling:

    Đây sẻ là cách mà chúng ta mô hình hoá một cách có đường hướng chiến thuật trong các Bounded Context bằng cách sử dụng các building block partern của DDD như Aggregate, Repository, Factory, Services, Domain Event và các khái niệm liên quan như Entity, Value Objects,... để design các module một cách đúng đắn nhất

- Trãi nghiệm về các parterns, architectures:

    Có chiến thuật, có model rồi, chúng ta cần nhạy bén tuỳ vào các yêu cầu mục đích cụ thể mà chọn các mẫu architecture phù hợp ví dụ như Clean Architecture, CQRS/Event Sourcing,... sao cho vừa thoả mãn business đồng thời dễ dàng mở rộng thêm tính năng cũng như tối thiểu hoá chi phí maintain.

Tóm lại, bài viết ngắn này cá nhân mình đã trả lời một số băng khoăn:

- Domain Driven Design là gì?
- Domain là gì?
- Các vấn đề mà Domain Driven Design sẻ giúp chúng ta giải quyết?

Nếu có bất cứ thiếu xót nào mong các bạn góp ý bổ sung.

Bài tiếp Theo mình sẻ nói về chủ đề Strategic Modeling.