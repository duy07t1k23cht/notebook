---
draft: true
date: 2024-01-01
authors:
    - me
categories:
    - ":flag_vn:"
    - ":computer: System Design"
tags:
    - Hadoop
slug: apache-hadoop-detailed
---

# **Chi tiết hơn về Apache Hadoop**

Trước tiên hãy đọc bài viết [Giới thiệu về Apache Hadoop](hadoop-overview.md) để hiểu về tổng quan cấu trúc của một Hadoop cluster.

Bài viết này sẽ giải thích ký hơn về các thành phần trong một Hadoop cluster.

<!-- more -->

## HDFS

HDFS được thiết kế cho fault tolerance.

Data được chia thành các blocks, mỗi block có 3 bản copies ở các nodes hoặc các server racks khác nhau. Nếu một node hoặc một rack bị lỗi, ảnh hưởng đến toàn bộ hệ thống là không đáng kể vì data vẫn đang được lưu ở các block hoặc rack khác.

Khi data được chia thành các blocks, metadata của các block này, bao gồm các thông tin như tên file, permissions, số lượng replicas, locations,... được lưu ở NameNode local memory.

Như vậy nếu NameNode bị lỗi, HDFS sẽ không thể biết được vị trí của các data block, dẫn đến cả cluster bị ảnh hưởng. Điều này khiến cho NameNode trở thành single point of failure của cluster.

Để khắc phục điều này, sẽ có thêm các Standby NameNodes. Các Standby NameNodes sẽ tự động được failover trong trường hợp NameNode bị lỗi.

Các data block sẽ được lưu ở các DataNodes, sử dụng rack-aware placement policy, nghĩa là các data block replicas không thể được cùng lưu ở trong cùng một rack.

DataNode sẽ nhận các chỉ thị từ NameNode một cách định kỳ (khoảng 20 lần mỗi phút), đồng thời cũng định kỳ gửi thông tin về status và health của các data blocks. NameNode dựa vào những thông tin này để request DataNode thực hiện các thao tác như thêm hoặc xoá replica.

<figure markdown="span">
  ![HDFS](https://phoenixnap.com/kb/wp-content/uploads/2021/04/hdfs-components-namenode-datanode-datanode.png)
  <figcaption>Minh hoạ HDFS trong Hadoop</figcaption>
</figure>

## YARN

## MapReduce


<!-- end -->

[^1]: [phoenixNAP, _Apache Hadoop Architecture Explained (with Diagrams)_](https://phoenixnap.com/kb/apache-hadoop-architecture-explained)
