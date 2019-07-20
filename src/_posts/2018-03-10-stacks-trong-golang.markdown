---
title: 'Tại sao lại dùng Golang?'
layout: post
date: 2018-03-10 11:20
image: /assets/images/
headerImage: false
tag:
    - Golang
    - Event Loop
    - Event Driven
category: blog
author: Le Phuoc My
description: Markdown summary with different options
---

Khi bắt đầu làm việc với Golang mình được giới thiệu rằng Golang rất chi là ngon cho backend, nào là nó cung cấp một thứ gọi là Goruntine - Một thread nhỏ nhẹ với chỉ 2kb stack size rồi đơn giản, dễ học....
Tuy nhiên lúc đó đọc giới thiệu xong thì nãy sinh một số câu hỏi:
1. Vấn đề stack size: Mặc định Java nó cấp phát tận 1MB vẫn thường xãy ra stack overflow, đây stack size mỗi Goruntine 2kb có mà nát hết à?
2. Vấn đề switching context: Các ngôn ngữ dựa trên thread based vẫn luôn chịu một ảnh hưởng không thể tránh khỏi đó là switching context cost của CPU, thế ếu nào người ta giới thiệu Golang xử lí cái này ngon lắm, có gì đó không ổn ở đây.
3. Có chắc Golang sử dụng hiệu quả CPU đa lõi như giới thiệu?


Tuy nhiên Go nó có cách implement để điều đó không xãy ra và có thể hay hơn nũa.

## Segmented stacks:

Trong Go 1.2, nó sử dụng cái gọi là Segmented Stacks, trong cách tiếp cận này, stack không liên tục và lớn dần. Mỗi stack bắt đầu bởi một segment duy nhất. Khi stack cần lớn lên một segment sẻ được cấp phát và được liên kết với phần trước. Một stack là một danh sách được liên kết từ hai hoặc nhiều segments.
![Markdowm Image][1]

Ưu điểm của phương pháp này là stack có thể bắt đầu nhỏ và phát triển hoặc thu nhỏ khi cần thiết. Tuy nhiên khi đó sẻ có một vấn đề nãy sinh:
Hãy tưởng tượng có các lời gọi hàm xãy ra khi stack gần đầy, bắt buộc stack phãi phát triển và segment mới phãi được phân bổ. Khi hàm đó return segments sẻ được giải phóng và stack co lại. Các cuộc gọi hàm đó xãy ra thường xuyên như sau:

{% highlight js %}
func main() {
for {
big()
}
}

func big() {

var x [8180]byte
// do something with x
return

}

{% endhighlight %}

Gọi đến hàm **big ()** sẻ khiến segment mới được cấp phát, stack phình ra và segment sẻ được giải phóng và stack thu lại khi hàm return. Công đoạn đó xãy ra liên tục vì nó đang trong vòng for. Chi phí cho việc cấp phát và thu hồi các segment trở nên đáng kể. Đây gọi là “hot split” trong cộng đồng Go và người Rust gọi đó là “stack thrashing”.

### Contiguous stacks

“Hot split” đã được giải quyết trong Go 1.3 bằng cách tạo ra **contiguous stacks**.
Bây giờ khi một stack cần phát triển, thay vì cấp phát một segment mới, runtime sẽ:

1. Tạo một stack mới lớn hơn một chút.
2. Sao chép nội dung của stack sang stack mới
3. Điều chỉnh lại mọi con trỏ được sao chép để trỏ đến địa chỉ mới
4. Phá hủy stack cũ đi.

[1]: /assets/images/2017-02-10-stacks-trong-golang/1.png
