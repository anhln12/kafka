![image](https://github.com/anhln12/kafka/assets/18412583/47825b84-400c-4497-949c-e927c253c87e)# kafka

**1. Apache Kafka là gì?**

Là một event streaming platform được phân phối (distributed), có thể scalable, thông lượng cao (high-throughput), độ tễ thấp (low-batency).

Hiểu một cách đơn giản nó là một platform để xử lý việc truyền tải message trên nhiều hệ thống.

**2. Những khái niệm chính trong Kafka**

![image](https://github.com/anhln12/kafka/assets/18412583/10d34e81-9eb8-4f34-83ea-f96bdafe4f7a)

* Producer: Có thể là bất kì application nào có chức năng publish messages (events) vào 1 topic.
* Messages: Trong Kafka cũng có thể gọi là event. Nó chính là data được lữu trữ trong topic dưới dạng byte array.
* Cluster: Là 1 system bao gồm zookeeper + nhiều broker cùng làm việc với nhau. Lưu ý là trong 1 cluster chỉ có 1 broker chính luôn active, tuy nhiên các broker còn lại đều nhận được bản copy data mà broker chính nhận được
* Broker: Là server lưu trữ topic. Sở dĩ có nhiều brokerlà bởi nếu thằng này hỏng thì có thằng khác cover thay, tránh tình trạng system không thể hoạt động.
* Zookeeper: Có thể hiểu nó là 1 manager nằm trong cluster được dùng để quản lý và bố trí cacs broker.
* Topic: Là nơi lưu trữ các record.
* Partition: Mỗi topic lại được chia nhỏ vào các đoạn khác nhau, các đoạn này được gọi là partition.
* Offset: Mỗi 1 partition được chia thành nhiều đoạn nhỏ và mỗi đoạn như vậy gọi là 1 offset. Nó cũng là nơi lưu trữ data, 1 offset sẽ chứa 1 message (event).
* Consumer: Là bất kì application nào có chức năng subscribe vào 1 topic để receive messages.
  
**3. Các hoạt động của Kafka**

**Kafka Partition**
![image](https://github.com/anhln12/kafka/assets/18412583/59b1bd85-a888-44d5-9a6e-c27d26e7e93d)

* Giả sử có 1 topic phải lưu trữ quá nhiều data. Vậy nên bắt buộc phải chia nhỏ data và lưu trữ vào các servers khác nhau, mỗi server được gọi là 1 partition.
* Partition là nơi lưu trữ data trong topic & mỗi topic có thể có 1 hoặc nhiều partition. ID của các partition trong topic sẽ được tăng dần (start từ 0). Trong Kafka Cluster thì một partition có thể replicate (sao chép) ra nhiều bản.
* Việc chia nhỏ topic thành các partition thể hiện tính năng phân phối (Distributed) của Kafka, nghĩa là nó lưu trữ data trên Datacenter (Nhiều Servers khác nhau).

**Kafka Offset**

* Trên mỗi partition thì data được lưu trữ cố định và được gán cho một ID gọi là offset. (Giá trị ID của các offset sẽ tăng dần & start từ 0). Một partition có thể có 1 hoặc nhiều offset.
* Khi 1 message mới publish vào topic, Kafka sẽ quyết định message này nằm trong partition nào & offset nào.
* Để lấy được một message trong Apache Kakfa, chúng ta cần chỉ định rõ topic name, partition number & offset number.

![image](https://github.com/anhln12/kafka/assets/18412583/5e3cc6c3-4b65-4f6c-a2b1-ce6428646456)

Ví dụ mình muốn lấy message tại vị trí offset như hình trên thì: topic_name.2.0

**Kafka Consumer Group**

* Đặt vấn đề, giả sử khi topic có nhiều producer. Nếu chỉ có 1 consumer để subcribe messages thì không đủ đáp ứng, vậy nên phải có nhiều consumer cùng nhau xử lý để tránh tình trạng quá tải & độ trễ. Khi đã có nhiều consumer thì phải gom chúng lại trong 1 consumer group để dễ quản lý & phân chia công việc.
* Consumer Group là 1 group chứa các consumer subcribe messages từ các partition khác nhau trong cùng 1 topic.
![image](https://github.com/anhln12/kafka/assets/18412583/088a5444-fced-4489-8d23-695326cb3d08)

* Nhìn vào hình trên ta sẽ thấy:
    * Consumer Group 1 sẽ có độ trễ thấp hơn vì mỗi consumer chỉ subcribe messages từ 1 partition.
    * Consumer Group 2 sẽ có độ trễ cao hơn vì mỗi conusmer phải subcribe messages từ 2 partition.
    * 


/opt/kafka/bin/kafka-topics.sh --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --version
/opt/kafka/bin/kafka-topics.sh --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --list




