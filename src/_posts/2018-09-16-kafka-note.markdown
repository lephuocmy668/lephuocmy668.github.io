---
title: 'Kafka note'
layout: post
date: 2018-09-16 21:10
image: /assets/images/
image: /assets/images/2018-07-16-nhung-van-de-kafka-giai-quyet/1.png
headerImage: true
tag:
    - kafka
    - event-driven
    - microservice
category: blog
author: Le Phuoc My
description: Markdown summary with different options
---
## Tại sao phãi sài kafka?
Chúng ta bắt đầu bằng việc có một bài toán như sau: Nếu có 1 triệu người đặt hàng trên trang web của bạn cùng lúc nó có thể tạo ra một số vấn đề concurrency và việc đảm bảo việc sử lí các yêu cầu theo thứ tự ngay lập tức chúng ta nghix đến việc xếp các order vào một message queue để có thể đảm bảo thứ tự của chúng và kiểm soát được số lượng được xử lí đồng thời đó, kafka là một trong những lựa chọn tốt giúp chúng ta xử lí được bài toán trên.

### Đối với mô hình event driven:
Các ngôn ngữ lập trình truyền thống thường dựa trên thread và threadpools để xử lí các tác vụ đồng thời đơn cử như là Java theo mặc định mỗi thread thường chiếm 1MB trong stack size(trên JVM 64bit) và chúng ta có thể config stack size tuỳ ý. Việc cấp phát một thread thường khá đắt đỏ trong CPU cho nên chúng ta thường sẻ phãi config size của threadpools trước, mỗi khi có request tới, web server sẻ pick một thread trong threadpools và kêu nó xử lí reuqest,các thread trên sẻ xử lí các tác vụ một cách song song. Nếu bạn giữ các giá trị như mặc định thì nếu config 1k thread trong thread pool đồng nghĩa với việc bạn sẻ dùng hết gần 1G Ram.

Đối với hệ thống lớn chúng ta sẻ gặp phãi hai vấn đề về thread đó là stack size và threadpools size. Nếu config stack size mỗi thread nhỏ xuống thì sẻ tiết kiệm được nhiều bộ nhớ nhưng khi một thread nào đó đòi hỏi tính toán nhiều thì dễ gây ra stack overflow. Cũng như vậy đối với threadpools size, khi chúng ta giữ một lượng lớn thread trong threadpool thì memory cost và switching context cost sẻ là rất cao và hệ thống sẻ trở nên khó kiểm soát, ngược lại khi giữ một số lượng thread nhỏ hơn thì lại làm giảm tính available của hệ thống.

Nếu hệ thống chúng ta có các tác vụ tính toán mất thời gian, tốn tài nguyên, khó thể ước tính thời gian và tài nguyên cho mỗi yêu cầu nhưng lại không nhất thiết phãi response ngay lập tức thì chúng ta có thể sử dụng mô hình trên. Khi thiết kế hệ thống dựa trên mô hình event driven thì Kafka là một lựa chọn khá tốt để xử lí các event. Bạn có thể sử dụng kafka để lưu trử các request message và các worker xử lí request sau đó.

### Đối với kiến trúc microservice 
Khi làm việc với kiến trúc Microservice chúng ta thường đối mặt với các vấn đề liên quan đến internal communication và xử lí lỗi trong communication giữa các services. Những dạng giao tiếp thường thấy có thể là call trực tiếp(thông qua http rest/gRPC/thrift....), giao tiếp thông qua việc sử dụng asynchronous messaging, và có thể cao hơn đó là dùng các giao thức đặc biệt dành riêng cho hệ thống. Tuy nhiên đối với các hệ thống phãi xử lí một lượng lớn request trên kiến trúc microservice cũng như kiến trúc monolic truyền thống thì những lỗi như lỗi mạng, timeout, lỗi do bug....là điều không thể tránh khỏi. Message queue nói chung và kafka nói riêng giúp hệ thống của chúng ta xử lí các lỗi trên một cách hiệu quả bằng cách lưu lại trạng thái các request lúc nào xử lí xong mới xoá request đó đi bất cứ lỗi nào xãy ra thì chúng ta đều có thể xử lí lại request đó.
Trong kiến trúc microservice thì chúng ta thường sử dụng nhiều ngôn ngữ khác nhau tuỳ thuộc vào mục đích từng service, hơn nữa việc duy trì dependencies cứng nhắc giữa các services đôi lúc không cần thiết. Bằng cách giao tiếp thông qua kafka giữa các services với các format hợp lí và khớp lệ bạn có có thể tách biệt được dependencies giữa các service, mỗi thành phần của hệ thống đều có thể phát triển, mở rộng một cách độc lập.  

## Kiến trúc kafka:
Cơ chế hoạt động cơ bản của Kafka hình dung đơn giản giống như Logs system, các log record được lưu lên đĩa và sắp xếp theo thứ tự thời gian, Kafka tổ chức phân loại các messages theo khái niệm Topic hình dung giống như tag trong việc quản lí Log của chúng ta. Mỗi khi cài đặt kafka một trong những việc ta cần làm là setup nơi mà log sẻ được lưu xuống-url của log.dir, mỗi topic sẻ được map với thư mục con bên trong thư mục log của chúng ta và cũng có thêm nhiều thư mục con nữa đó là các thư mục topic partitions với tên thư mục format là topic-name_partition-number, bên trong mỗi thư mục sẻ là file log nơi mà message tới sẻ được append vào. Mỗi Topic có một nhiều Partitions và mỗi Partition là một list các messages, khi một message được chia sẻ lên Kafka theo Topic, message sẻ được gửi vào một Partition của Topic, việc lưu message xuống Partition nào được quyết định bởi các producers. Nếu ta muốn message xuống Partition cụ thể nào đó thì ta phãi set partition_key cho message đó ứng với Partition ta muốn message xuống, nếu không mặc định message sẻ được phân phối theo giải thuật round-robin.

{% highlight js %}
/logs
    /logs/topicA_0
    /logs/topicB_0
    /logs/topicB_1
    /logs/topicC_0
    /logs/topicC_1
    /logs/topicC_2
{% endhighlight %}

Như trên chúng ta có thể thấy rằng topic A có 1 partition, topic B có 2 partitions và C có 3 partitions. Vậy partition đó là gì, dùng để làm gì? Partitions là một phần quan trọng trong thiết kế của kafka. Các partitions chia luồng message đến một topic vào các luồng song song và đó là chìa khoá giúp Kafka achieves một lượng message khổng lồ. Tuy nhiên thứ tự message đến từ các partitions khác nhau không được đảm bảo, Kafka chỉ đảm bảo thứ tự message trong cùng một partition, do đó trong trường hợp chúng ta cần đảm bảo thứ tự message cho nhiều tác vụ nào đó, chắc chắn các message xử lí các tác vụ đó phãi được sắp xếp vào cùng 1 topic và cùng một partion_key. 

![Markdowm Image][1]

Mỗi Partition có thể được phân tán trên nhiều máy và mỗi máy như thế được gọi là một Broker, mỗi Broker có thể có 0 1 hoặc nhiều Partitions của cùng 1 Topic hoặc thậm chí các topic khác nhau. 

![Markdowm Image][2]

Kafka hổ trợ Replication để đảm bảo tính high-availability và tính fault-tolerance. Mỗi partition có thể có nhiều bản replicas tuỳ thuộc vào số replication factor mà ta config. 

![Markdowm Image][3]


Tuy nhiên chỉ có các leader partitions mới được cho phép nhận message sau đo mới đồng bộ lên các replicas partitions khác và khi một leader partition chết thì một trong những replica của partition được chọn để làm leader partition cho đến khi partition kia sống lại thì nó trở thành replica và phãi fetch lại tất cả data chưa đồng bộ được trong quá trình chết đi từ các partition khác. Kafka dùng ZOOKEEPER để handle chuyện đó, Kafka dùng nó để thực hiện việc leadership electron các kafka broker và các cặp Topic Partition, ZOOKEEPER giúp quản lí việc Service Discovery cho các Kafka Brokers từ các cluster, handle các trường hợp broker nào join vào, broker nào chết đi, topic nào được add vào topic nào bị remove đi....
Rõ ràng kafka thoã mãn hai đặc điểm đó là High-availability và Durability. 

Cách hoạt động của Kafka producers khá đơn giản, ban đầu các producers phãi fetch các metadata lên ví dụ như chúng cần biết có các topics nào đang tồn tại? có bao nhiêu partition mỗi topic đang có? leader broker hiện tại của mỗi partition là node nào? host và port của mỗi broker đó là sao? Sau đó chúng làm việc trực tiếp với các broker khác nhau mà không có một sự điều phối nào. 

![Markdowm Image][4]

Với thiết kế đó kafka producers đã loại bỏ triệt để vấn đề write-bottleneck, đồng thời giúp cho mỗi broker nhận được một số lượng messages hợp lí, dễ dàng mở rộng tuyến tính, khi muốn mở rộng chỉ cần thêm partition và broker vào.

Như vậy nhìn chung thì Kafka producers tương đối đơn giản tuy nhiên đối với Kafka consumers thì phức tạp hơn nhiều. Cũng giống như Kafka producer, Kafka consumers bắt đầu các hoạt động của chúng bằng việc fetch các metadata, mỗi consumer có thể kết nối tới nhiều brokers và đọc từ nhiều replicas. Mỗi broker xử lí một tập hợp các partition của các topics mà consumer subsribe tới cùng lúc.

![Markdowm Image][5]

Nhờ thiết kế đó mà workload giữa các broker được cân bằng đồng thời giảm thiểu vấn đề read-bottleneck. Hơn nữa mỗi consumer có nhiều replicas để đọc giúp tăng tính availability và cân bằng workload giữa các consumer.

![Markdowm Image][6]

Tuy nhiên chỉ với thiết kế đó thì vẫn chưa đảm bảo tính scale, khái niệm consumer group sinh ra giúp chúng ta dễ dàng scale các consumers. Theo đó mỗi consumer sẻ thuộc về một consumer group hay nói cách khác một consumer group sẻ chứa các consumer và message sẻ được phân phát đến tất cả consumer group, mỗi member của group sẻ xử lí message từ một hoặc nhiều partitions.

![Markdowm Image][7]

Với thiết kế này, mỗi consumer có thể xử lí message từ nhiều partitions, đảm bảo cover hết tất cả các partition cũng như thứ tự xử lí message trong mỗi partition đồng thời cân bằng tải giữa các consumers trong cùng một group. Khi chúng ta muốn scale lên chỉ cần tăng số brokers, tăng số partitions và tăng số consumers. Nhìn lại một lần nữa vào ví dụ trên, chúng ta có thể thấy rằng thiết kế trên cho phép mỗi consumer có thể xử lí nhiều partitions cùng lúc chứ không cho phép nhiều consumers cùng lúc xử lí một partition cho nên giả dụ như chúng ta có 5 partitions nhưng có đến 6 cunsumers thì hiển nhiên 1 consumer ngồi chơi làm phí phạm tài nguyên hệ thống, cho nên khi scale thì nên lưu ý rằng số consumers luôn bé hơn hoặc bằng số partitions mà tốt nhất là bằng nhau thì hơn.

Updating....

[1]: /assets/images/2018-08-26-kafka-note/1.png
[2]: /assets/images/2018-08-26-kafka-note/2.png
[3]: /assets/images/2018-08-26-kafka-note/3.png
[4]: /assets/images/2018-08-26-kafka-note/4.png
[5]: /assets/images/2018-08-26-kafka-note/5.png
[6]: /assets/images/2018-08-26-kafka-note/6.png
[7]: /assets/images/2018-08-26-kafka-note/7.png

