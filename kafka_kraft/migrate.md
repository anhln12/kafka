Để chuyển đổi (replace/migrate) từ 3 node cũ sang 3 node mới trong cụm Kafka Kraft, việc chỉ add thêm 3 node mới rồi off 3 node cũ ngay lập tức sẽ làm mất Quorum (vì Kafka Kraft dựa trene thuật toán đồng thuận Raft, cần đa số node sống sót: cụm 6 node yêu cầu ít nhất 4 node online, hoặc nếu off 3 node cũ cùng lúc thì chỉ còn 3 node mới => không đạt đa số  4)

Quy trình chuẩn bị thay thế hoàn hoàn 3 node cũ bằng 3 node mới an toàn mà không mất dứ liệu hoặc downtime hệ thống gồm các bước sau:

Bước 1: Scale out (mở rộng lên 6 node)
* Lấy ClusterID từ cụm cũ
* Trên 3 node cũ: Cập nhật lại Controller.quorum.voters thêm 3 node mới vào, sau đó restart cuốn chiếu từng node cũ.
* Trên 3 node mới: Cấu hình node.id, controller.quorum.voters (đủ 6 node), format storage bằng Cluster ID chung và khởi động lên
* Kiểm tra lại đảm bảo cụm 6 node hoạt động ổn định và đồng bộ metadata thành công

Bước 2: Chuyển dịch Client & Traffic (Traffic Migration)

Trước khi tắt 3 node cũ, bạn phải đảm bảo các ứng dụng (producer/consumer) đã chuyển sang kết nối tới bootstrap servers của 3 node mới.

Bước 3: Đảm bảo Leader của Partition đã rời khoản các node cũ

Kafka Kraft quản lý partition qua các broker. Trước khi gỡ node cũ, hãy kiểm tra xem các topic hiện tại có đang đặt leader/replica trên 3 node cũ không. Bạn nên thực hiện leadership rebanlance để chuyển các partitionn leader sang 3 node mới.

```
bin/kafka-leader-migration.sh --bootstrap-server <node-moi>:9092 ...
# Hoặc sử dụng công cụ reassignment partitions nếu cần di chuyển hẳn replica data sang node mới
```

Bước 4: Remove 3 node cũ khỏi Quorum (Scale in an toàn)

Khi 3 node mới đã gánh toàn bộ traffic và dữ liệu đã được replicate sang, bạn tiến hành gỡ bỏ 3 node cũ ra khỏi danh sách bầu chọn:
* cập nhật lại file config server.properties trên 3 node mới: thu hẹp danh sách controller.quorum.voters chỉ còn lại 3 node mới (node 4,5,6)
* restart cuốn chiếu 3 node mới để chúng nhận cấu hình mới chỉ gồm 3 node
* lúc này cụm đã chính thức chuyển về quy mô 3 node mới và hoạt dộng độc lập

Bước 5: Tắt hoàn toàn 3 node cũ

Bây giờ bạn có thể an toàn shutdown dịch vụ và power off 3 node cũ mà không sợ ảnh hưởng đến Quorum của cụm Kafka Kraft

