---
title: 'Clean Architecture'
layout: post
date: 2018-07-20 9:01
image: /assets/images/
headerImage: false
tag:
    - solid
    - ood
    - oop
    - functional programming
    - functional reactive programming
    - declarative programming
    - clean architecture
    - concurrency
category: blog
author: Le Phuoc My
description: Clean Architecture
---

Trước khi đi vào nội dung chính của bài viết này, mình xin nói qua về quá trình mình đã tiếp cận với các kiểu kiến trúc trong việc xây dựng các ứng dụng web. Điểm xuất phát của mình dường như nó không đi theo con đường “chính đạo” như các bạn các anh chị đi trước thường tiếp cận với Java, C#, Scala,…và đa số những bạn xuất phát theo con đường Java thường có cái nhìn cũng như tư duy về kiến trúc từ sớm. 

Ngược lại, xuất phát điểm của mình không quá tốt vì nhiều lí do từ khả năng bản thân cũng như lí do khách quan. Mình bắt đầu sự nghiệp coder với một dự án startup với công nghệ chính là Node.js, code không theo một paradigm cụ thể nào cả, OOP cũng không hẳn là OOP, Functional cũng không hẳn là Functional, Functional Reactive cũng không ra Functional Reactive, qua một thời gian thì mọi thứ gần như out of control và dường như không có cơ hội cứu chữa trừ khi đập đi làm lại và thực tế là dự án đó đã đập đi làm lại rất nhiều lần.

Sau một thời gian dài code quán tính thì không những không xây dựng được tư duy kiến trúc, tư duy tổ chức và làm mình khó nhồi nhét tư duy design sau này. Thật may lúc đó mình có cơ hội cho công việc mới dù kết thúc sớm vì lí do thời gian cá nhân nhưng cũng là cơ hội để mình biết được một số thứ hay ho. Yêu cầu công việc của mình thời điểm đó là tìm hiểu và áp dụng Clean Architect vào dự án với Golang với một tài liệu duy nhất là bài viết này [The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html).

Một task ngắn gọn nhưng quả là không thể tiêu hoá đối với mình tại thời điểm đó tư duy tổ chức kém, kém design partern, kém OOP thì quả thực Clean Architecture là không thể nuốt nổi. Nó là một thứ gì đó yêu cầu phãi xâu chuổi tất cả kiến thức tổ chức code, từ sự hiểu biết và kinh nghiệ về các Programming paradigms, cho đến các Design Parterns ở OOP cũng như ở các paradigms khác, tiếp đến mới là các Design Principles như SOLID Principles, Component Principles rồi mới đến các nguyên tắc, các mẫu kiến trúc cho phần mềm. Thực tế thì không chỉ là Clean Architecture mà tất cả các kiến trúc khác đều dựa trên những nguyên tắc chung đó mà xây dựng nên. 

Sau một thời gian với nhiều may mắn được làm việc với các dự án từ Golang đến Typescript mình tóm tắt lại quá trình mình tiếp cận với Clean Architecture nói riêng và các quy tắc lập trình, tổ chức code nói chung cũng và cũng là dịp để mình ôn lại kiến thức, kĩ năng lẫn thói quen nhằm ghi nhớ sâu hơn hoặc ai đó đang tìm hiểu hoặc đang implement Clean Architecture vô tình đọc bài này có thể cảm nhận và phản biện giúp mình cũng cố kiến thức hơn.

### 1. VÌ SAO PHÃI TÌM HIỂU CÁC SOFTWARE ARCHITECTURE?
Hay nói cách khác các Software Architecture sinh ra làm gì? Vì sao các Software Engineer chúng ta phãi tìm hiểu chúng?

Như chúng ta đều biết thì với yêu cầu của việc phát triển phần mềm thì việc phát triển đội ngủ về số lượng luôn là điều hiển nhiên. Tuy nhiên sự phát triển của số lượng chưa hẳn sẻ kéo theo sự phát triển chất lượng và tốc độ phát triển phần mềm. Một sự thật đớn đau là theo các con số thống kê thì đa số phần mềm khi phát đến một mức nào đó thì cho dù tăng thêm số lượng engineer đến bao nhiêu cũng không thể kéo tốc độ cũng như chất lượng lên được nữa và có thể là tốc độ và chất lượng lại đi xuống, tức nhiên là chẳng ai muốn điều đó xãy ra, ai cũng luôn luôn cố gắn phát triển cả nhưng với việc tổ chức kém code base ban đầu, việc out of control chất lượng code, độ phình to của software mà không có một chiến lược tái cấu trúc hợp lí....tất cả các nguyên nhân đó tạo ra một đống hổn độn mà không một cá nhân nào trong đó không muốn "đập đi làm lại cmn đi".

Các Software Architecture sinh ra để giải quyết bài toán đầu tiên về tổ chức cũng như độ dễ của việc duy trì một code base sạch sẻ gọn gàn, nhằm giảm tối đa resource để xây dựng phát triển cũng như maintain hệ thống phần mềm đó.

### 2. CÁC PROGRAMMING PARADIGMS:

Như chúng ta đều biết thì các Architectures đều bắt đầu từ code cho nên các Paradigms sẻ nói cho chúng ta biết cách tổ chức nào sẻ được sử dụng, đến nay thì mình chỉ mới tiếp cận được 3 Programming Paradigms nên có thể nêu lên quan điểm cá nhân của mình như sau:

#### Object Oriented Programming: 

Đây dường như là Paradigm thịnh hành nhất hiện tại với các ưu điểm đáng kể.

Việc hiểu rõ các tính chất cũng như các nguyên tắc design trong OOP giúp chúng ta dễ tổ chức và phát triển và maintain, dễ module hoá, tính reusable cao và cũng như cho phép chúng ta dễ dàng phát triển các module song song. 

Tuy nhiên về nhược điểm OOP cũng có nhiều hạn chế. Về coding style, OOP nhìn chung là quá imperative, tập trung trả lời câu hỏi "How?"(làm sao để làm điều đó) hơn là câu hỏi "What?"(chúng ta muốn gì ở đây), khi đọc code chúng ta khó nhanh chóng hiểu được người viết muốn gì. Nhược điểm thứ hai đó là về vấn đề race condition, trong concurrent programming đây là vấn đề thường gặp và rất tốn công giải quyết, việc chia sẻ trạng thái trong lập trình hướng đối tượng là một trong các nguyên nhân chủ yếu dẫn đến race condition và khiến ta khó debug cũng như fix bug.

#### Functional Programming(FP): 
Những năm gần đây FP nổi lên như một xu hướng mà các lập trình viên đang theo đuổi và dần chuyển đổi, không chỉ mang lại những trải nghiệm mới mà FP xử lí một số vấn đề hạn chế của OOP. 

Về coding style nhìn chung FP theo trường phái declarative, tập trung trả lời câu hỏi "What?"(chúng ta muốn gì), giúp chúng ta nhanh chóng hiểu code trong lúc maintain, fix bug. 

Việc sử dụng các pure functions mang lại cho chúng ta các functions đáng tin cậy hơn, hạn chế tối đa side effect có thể xãy ra, các hàm luôn luôn chỉ trả đúng kết quả mà chúng ta mong đợi. 

Về mặt Architecture, tính IMMUTABILITY là tính chất rất quan trọng mà chúng ta cần xem xét. Đứng ở vị trí là một Backend Engineer ngoài các vấn đề thuật toán, architectures/design thì các engineer thường coi trọng nhất các vấn đề về concurency như race condition, deadlock conditions và concurrent update, mà tất cả chúng sinh ra do đâu? chỉ có thể là do tính mutable của biến. 

Chúng ta sẻ không dính bất cứ race condition hoặc concurrent update nào khi không có một biến/shared memory nào được cập nhật, chúng ta sẻ không dính bất cứ deadlocks nào nếu không có mutable locks, nói cách khác tất cả vấn đề concurrency, tất cả vấn đề chúng ta đối mặt trong hệ thống multiple threads/multiple processors sẻ không bao giờ xãy ra nếu không có bất cứ mutable variables nào. 

Ở vị trí Backend Enginner chúng ta luôn muốn thiết kế một hệ thống mạnh mẻ với sự có mặt của multi threads multi processors sau đó câu hỏi mà chúng ta tự hỏi chính mình đó là tính immutability có luôn được thực hiện được hay không? Câu trả lời tức nhiên sẻ là không rồi, tuy nhiên chúng ta sẻ có những kĩ thuật nhằm phân tích các thành phần mutable và các thành phần immutable sau đó tách biệt chúng ra. Các thành phần immutable bắt buộc chúng ta phãi implement một cách purely functional và các thành phần mutable bắt buộc chúng ta phãi cân nhắc sử dụng một số loại transaction memory để tránh khỏi vấn đề concurrent updates và race conditions. 

Tuy nhiên nó vẫn chưa được bảo vệ hoàn toàn khỏi vấn đề concurrent updates và deadlocks khi có quá nhiều biến phụ thuộc xuất hiện, khi đó chúng ta lại phãi phân tách và chuyển các thành phần mutable thành các thành phần immutable nhiều nhất có thể và tập trung đẩy càng nhiều tài nguyên nhất có thể vào các thành phần immutable. Ngoài ra việc thay đổi, tận dụng sức mạnh xử lí của hệ thống máy tính hiện đại kết hợp cùng các kĩ thuật xử lí theo event, cron job,.... có thể giúp chúng ta giải quyết phần nào hiệu quả các vấn đề trên.

#### Reactive & Functional Reactive Programming(FRP):

Đối với hầu hết các lập trình viên Javascript thì đây là một Paradigm không quá xa lạ, là sự hợp thành của 2 Paradigms Functional và Reactive(Events/Data stream Driven, Push, Asynchronous). 

Đây là một Paradigm giúp chúng ta code Declarative hơn, dễ hiểu hơn theo hướng event, giúp đơn giản hoá việc xử lí bất đồng bộ đồng thời tránh đi việc gọi quá nhiều callback gây callback hell hoặc dùng promise/async-await mà quên đi tính event driven khi code. Ngoài ra đây cũng là một Paradigm giúp engineer xử lí concurent với một mô hình khác(event driven) ở mức low-level thông qua các khái niệm như observables, observers, operators, scheduler....

### 3.CÁC DESIGN PRINCIPLES:
Hệ thống phần mềm tốt phãi bắt đầu bằng việc clean code, nó cũng giống như việc xây nhà, khi bắt đầu điều tiên quyết chúng ta cần có đó là những viên gạch tốt, những viên gạch đã không tốt thì cho dù kiến trúc tổ chức tốt đến nhừng nào cũng chỉ là trên bản vẻ mà thôi, khi xây nên hoàn toàn dễ dàng sụp đổ. Một mặt khác khi bạn đã có các viên gạch đủ tốt rồi nhưng đôi lúc từ chúng bạn vẫn tạo ra một mớ hổn độn. 

Để cấu nên một toà nhà lớn chúng ta nên chia nhỏ phát triển từng components bằng cách abstract hoá lên rồi lại separate ra một cách thích hợp để tạo thành các components nhỏ dễ cho việc quản lí tái, tái sử dụng hay gỡ bỏ khi cần. 

Các Design Principles sinh ra nhằm giúp chúng ta thực hiện việc đó ở mức độ hight-level/mid-level/low-level như việc tổ chức tốt các thành phần nhỏ từ đơn vị nhỏ như ghép nối các viên gạch thành cấu trúc từng bức tường, cho chúng ta biết cách xắp xếp các hàm, các cấu trúc dữ liệu vào các class, các struct và kết nối chúng lại với nhau để xây dựng nên các module hoàn chỉnh dễ dàng tiếp nhận sự thay đổi, dễ hiểu cho người mới, dễ sử dụng lại. Các Principles phãi kể đến như Component Principles, SOLID Principles,... Các bạn có thể xem lại [SOLID Principles](https://romeo.vn/solid-principle/) .

### 4. CLEAN ARCHITECTURE:

Clean Architect là gì? Chẳng qua là tập hợp các principles, partern nhằm separate các components, các dependencies, các concerns cuối cùng để chia phần mềm thành nhiều lớp và tổ chức các lớp đó sao cho thoả mãn các yêu cầu chung của một kiến trúc có thể gọi là "SẠCH". Một số yêu cầu của một kiến trúc gọi là sạch phãi kể đến là:
-   Độc lập với các Frameworks: Kiến trúc phần mềm là không phụ thuộc vào bất cứ Framework/Tool-kit nào, các Frameworks có sẳn sinh ra nhằm giúp chúng ta một phần nào đó trong việc tổ chức hệ thống chứ không nhất nhất là chúng ta cần gì cũng kéo Framework vào để rồi phãi nhồi nhét nhiều thứ quy tắc rối rắm mà mỗi Framework yêu cầu.
-   Dễ test: Các thành phần trong phần mềm của chúng ta phãi dễ test mà không phãi phụ thuộc vào interface(UI/console UI), database hay bất cứ thành phần nào.
-   Độc lập với UI hay bất cứ interface nào: Các interface hổ trợ người dùng có thể thay đổi một cách dễ dàng mà không ảnh hưởng đến phần còn lại của hệ thống. Chẳng hạn, một giao diện web có thể được thay đổi thành giao diện command mà không làm ảnh hưởng đến các business rule.
-   Độc lập với Database, bạn có thể thay đổi từ Oracle hoặc Sql Server thành Mongo, BigTable, CouchDB, Cassandra hoặc bất kỳ hệ quản trị cơ sở dữ liệu nào mà không làm ảnh hưởng đến Business Rule ban đầu.
-   Độc lập với các thành phần third-party: Phần mềm của chúng ta sẻ không phụ thuộc vào bất cứ libraries/frameworks hay bất cứ thành phần third-party nào, chúng ta phãi có một tư duy rằng các frameworks/libraries sinh ra để chúng ta chọn cho việc phục vụ nhu cầu nghiệp vụ của chúng ta chứ không phãi chúng ta phụ thuộc vào các thứ đó để implement các logic nghiệp vụ của mình.

Để đạt được những điều trên, tác giả Uncle Bob đề ra các ý tưởng, quy tắc được mô tả trong hình dưới:
![Markdowm Image][1]

Và để dễ dàng tiếp cận chúng ta sẻ cùng phân tích các thành phần của một ứng dụng đơn giản để làm rõ những mục tiêu nguyên tắc của Clean Architect. Giả sử chúng ta có một ứng dụng đơn giản với một mô hình data-flow đơn giản như sau:
![Markdowm Image][2]

Khi đó chúng ta sẻ đặt các thành phần lên các vòng tròn cơ bản như sau:
![Markdowm Image][3]

Chia theo mức độ tổng quát chúng ta sẻ có hai tầng như sau:

#### Interface Layer: 
Hai vòng tròn ngoài cùng mình tạm đặt nó vào tầng Interface. Đây là tầng chứa các  cổng giao tiếp:
- Giao tiếp từ client đến hệ thống(Controller vs Presenter), tuỳ thuộc vào yêu cầu của hệ thống mà chúng ta sẻ implement các cổng Interfaces cụ thể để phục vụ client, có thể là giao tiếp dựa trên Rest/gRPC/TCP.... từ đó chúng ta có thêm các quyết định separate tầng này ra thành các tầng nhỏ dựa vào các lớp endpoints, transports cụ thể.
- Giao tiếp với các thành phần infrastructure như Database System, Log System, Message Queue System, Search Engine,.... . Đây là nơi chứa các implementations của các Infrastructure Interfaces(Repository Interfaces, Search Interfaces, Message Interfaces...) mà Domain Layer yêu cầu để thực hiện. Tuỳ vào yêu cầu cũng như loại infrastructure mà chúng ta implement và separate tầng này một cách phù hợp dựa vào các parterns và toolkit(DAO, Repository, Data mapper, ORM, Query builder.....).

#### Domain Layer: 
Là hai vòng tròn trong cùng, hai vòng tròn này sẻ chứa các Entities, Use cases, Infrastructure Interfaces.
- Các Entities đóng gói các business rules. Một Entity có thể là một class/struct với các phương thức hoặc có thể là một tập hợp các cấu trúc dữ liệu và hàm, miễn là nó có thể được sử dụng bởi nhiều ứng dụng khác nhau trong toàn bộ phần mềm của chúng ta. Nếu chúng ta chỉ viết một ứng dụng đơn lẻ, thì các Entities chính là các đối tượng business của ứng dụng. Chúng đóng gói các quy tắc chung và cao cấp nhất. Chúng ít có khả năng thay đổi do ảnh hưởng từ những thay đổi bên ngoài. Chẳng hạn, bạn không thể mong những đối tượng này bị ảnh hưởng bởi một thay đổi đến từ UI, security hay kể cả việc thay đổi công nghệ.  
- Các Use Case: Domain Layer sẻ chứa các business rules cụ thể của ứng dụng. Nó đóng gói và implement tất cả các use cases của hệ thống. Các use cases này sẽ điều chỉnh luồng dữ liệu đến và đi từ các Entities và chỉ đạo Entities thể đó sử dụng các business rules của doanh nghiệp để đạt được các mục tiêu của mỗi use case.

- Chúng ta không mong muốn những thay đổi trong các use cases sẽ ảnh hưởng đến các thực thể, chúng ta cũng không mong muốn lớp use cases bị ảnh hưởng bởi những thay đổi của các yếu tố bên ngoài như cơ sở dữ liệu, giao diện người dùng hoặc bất kỳ framework nào, lớp này phãi cô lập với những mối lo ngại đó.
Đây là layer vô cùng quan trọng, chứa nhiều logic do đó chúng ta phãi chú trọng tổ chức một cách hợp lí để không bị rối sau quá trình phình to. Tuỳ vào mục đích và tình huống cụ thể mà chúng ta có thể lựa chọn các partern cho phù hợp. 
- Các Infrastructure Interfaces: Ở đây là các Repository Interfaces, Search Engine Interface,... mà các Use Cases cần để phục vụ việc vận hành business logic. 

Với tất cả hệ thống thì thành phần quan trọng nhất của nó luôn là Domain Layer, đây là nơi tập trung các Domain, các Use Cases, là nơi sẻ quyết định hệ thống này làm gì cho khách hàng, quyết định giá trị gì mà chúng ta tạo ra, quyết định sống còn của hệ thống. Mọi thứ bên ngoài như Databases, Frameworks, Protocols,.... đó có thể quy chung lại là các thành phần infrastructures giúp chúng ta implement và vận hành business logic mà thôi. 

####  Quy tắc về Dependency(The Dependency Rule)
Chính sự phân cấp về độ quan trọng của các thành phần của hệ thống, chúng ta phãi có một quy tắc để tổ chức chúng quy tắc về Dependency(The Dependency Rule)

Quy tắc này yêu cầu chúng ta phãi tổ chức code làm sao mà chúng chỉ có thể hướng vào bên trong, tức là những thứ ở bên trong không cần biết về những thứ ở bên ngoài, những thứ ở bên ngoài sinh ra nhằm phục vụ việc hiện thực business logic bên trong.

Cụ thể, tên của một cái gì đó khai báo trong một vòng tròn bên ngoài không được tham chiếu đến hoặc sử dụng bởi mã nguồn trong một vòng tròn bên trong bao gồm, các hàm, các class, biến, hoặc bất kỳ thực thể phần mềm khác. Cũng giống như vậy, các định dạng dữ liệu(Class/Struct/....) được sử dụng trong vòng ngoài không nên được sử dụng bởi những thứ ở vòng tròn bên trong, đặc biệt nếu các định dạng đó được tạo ra bởi một framework trong vòng tròn bên ngoài. Chúng ta không muốn bất cứ điều gì trong một vòng tròn bên ngoài tác động vào vòng tròn bên trong.

OK! Vậy phãi làm sao khi mà các thứ nằm trong vòng tròn bên trong không hề biết việc tồn tại các thứ ở bên ngoài lại có thể sử dụng được những thứ được implement bên ngoài. Ví dụ như các Use Cases nằm ở tầng Domain cần database implementation ở tầng bên ngoài cho việc hiện thực Use Case. Lúc này thay vì chúng ta sử dụng trực tiếp các Repository implementation thì chúng ta sẻ sử dụng Repository Interface/Abstraction ở tầng domain. Đây cũng là một ví dụ về nguyên tắc Dependency Inversion trong SOLID về việc phân tách các layers, các modules. Các high-level modules sẻ không phụ thuộc vào các low-level modules mà cả hai nên phụ thuộc vào các Abstractions nằm tại các hight-level modules.

#### Interface Adapters

Một thành phần không kém phần quan trọng trong hệ thống  phần mềm của chúng ta đó là Interface Adapters. Đây là một bộ các adapters có nhiệm vụ chuyển dữ liệu từ đầng này sang  tầng khác. Ví dụ như adapter chuyển đổi dữ liệu từ các use cases và Entities/Domain Model thành dữ liệu cho Database(Database Model) và ngược lại(Data Mapper). Hoặc đối với các bạn quen thuộc với Gokit sẻ thấy đó là các Endponts, chuyển đổi các cấu trúc dữ liệu từ định dạng của request sang cấu trúc dữ liệu đầu vào cho các Use Case Logics hoặc domain model. Và các Presenters sẻ encode response với định dạng phù hợp cho client.

#### Framework và Driver

Đây là một số thành phần có thể nằm trong các vòng tròn ngoài cùng, nói chung bạn không viết nhiều code trong layer này ngoại trừ code để connect với các vòng tròn ở bên trong. Layer này là nơi tập trung của các chi tiết. Chúng ta sẻ giữ những thứ này ở bên ngoài, nơi chúng khó có thể gây ảnh hưởng đến các phần ở vòng tròn bên trong.

Tuân theo các quy tắc đơn giản trên không phải là một việc quá khó khăn nhưng nó sẻ giúp chúng ta tiết kiệm được nhiều thời gian trong tương lai. Bằng việc tách hệ thống thành các layer, đồng thời tuân theo Dependency Rule, chúng ta sẽ xây dựng được một hệ thống dễ test, cùng với những lợi ích kèm theo như đã đề cập ở trên. Khi bất kỳ bộ phận bên ngoài của hệ thống trở nên lỗi thời, chẳng hạn như database, hoặc web framework, bạn hoàn toàn có thể thay thế chúng với một effort tối thiểu.

### Chỉ chừng đó là đủ ư?
Tất nhiên đó chỉ là những thứ cơ bản, chỉ là một mẫu đơn giản, nhiều lúc bạn cần nhiều hơn 4 vòng tròn đó hoặc mỗi vòng tròn chúng ta phãi chia nhỏ thành các vòng tròn khác nữa để đảm bảo sự rõ ràng và "CLEAN" tuỳ thuộc vào sự tiến hoá của code base, hơn nữa không có quy tắc nào nói rằng bạn luôn phải có chỉ 4 vòng tròn, có thể có chừng đó layer, có thể nhiều hơn và cũng có thể ít hơn, nhiều hơn khi kiến trúc monolith của bạn ngày càng phình to và trở nên phức tạp, ít hơn khi bạn đã chia chúng được thành các microservices một cách mượt mà, chỉ tập trung là logic và loại bỏ đi những thứ rườm rà không đáng quan trọng, và có thể chia thêm layer cho phù hợp với độ tiến hoá. Cũng có thể bạn tách ra thành miroservices nhưng vẫn giữ nguyên kiến trúc với đầy đủ ban bệ của mẫu monolith củ, tuỳ vào việc bạn muốn quản lí một củ hành bự hoặc một rỗ hành hoặc một rỗ các tép hành mà thôi. Nhưng một điều mà chúng ta phãi ghi nhớ nếu không muốn bị ăn hành đó là Dependency Rule luôn phãi được áp dụng, sự phụ thuộc luôn phãi được hướng vào những thứ quan trọng nhất. Khi chia các layer theo các vòng tròn chúng ta nhìn sâu vào tâm vòng tròn thì mức độ abstract phãi tăng lên, vòng tròn trong cùng chỉ chứa những gì chung nhất, khó có thể chia nhỏ được nữa như các interface chẳng hạn. Các vòng tròn ngoài cùng phãi là các chi tiết được implement cụ thể ở mức thấp nhất và không ngại phãi thay đổi.

Clean Architecture hay các Architectures mục tiêu lớn nhất vẫn là hướng Engineer vào thứ quan trọng nhất của phần mềm là các Domain, Business Rules và là tập hợp các quy tắc, để giữ cho source code hệ thống phần mềm của chúng ta luôn được "Clean".

### Implement Clean Architecture:
Đây là hai ví dụ nhỏ mình implement Clean Architecture với Golang và Typescript các bạn có thể tham khảo:

[Golang Clean Microservice](https://github.com/lephuocmy668/daily-problems-solving/tree/master/golang/workspace/tiktok-clean-microservice)

[Typescript Clean Microservice](https://github.com/lephuocmy668/nodejs-typescript-clean-architecture)


[1]: /assets/images/2018-01-20-clean-architecture/1.jpg
[2]: /assets/images/2018-01-20-clean-architecture/2.png
[3]: /assets/images/2018-01-20-clean-architecture/3.png

