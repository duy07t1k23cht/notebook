---
date: 2024-01-01
authors:
    - me
categories:
    - "Vietnamese :flag_vn:"
    - ":computer: System Design"
tags:
    - Hadoop
slug: apache-hadoop-overview
---

# **Giới thiệu về Apache Hadoop**

Bài viết này giới thiệu cái nhìn tổng quan nhất về Apache Hadoop, bao gồm kiến trúc, cách thức hoạt động của nó.

<!-- more -->

Apache Hadoop là một framework dùng để lưu trữ và xử lý dữ liệu lớn. Hadoop sử dụng một cluster gồm nhiều nodes để xử lý dữ liệu song song thay vì chỉ sử dụng một máy duy nhất, cải thiện tốc độ khi xử lý dữ liệu lớn.[^1]


## Tổng quan về Hadoop

Hadoop sử dụng hàng trăm thậm chí hàng ngàn servers làm việc cùng nhau để lưu trữ và xử lý dữ liệu lớn.

Hadoop bao gồm 4 modules chính[^1]:

- **Hadoop Distributed File System (HDFS)** là một distributed file system, dùng để lưu trữ data.
- **Yet Another Resource Negotiator (YARN)** là module để quản lý và monitor các nodes. Nó có tác dụng schedules các jobs và tasks, đóng vai trò như một resource manager.
- **MapReduce** là module thực thiện xử lý data. 
- **Hadoop Common** cung cấp các thư viện java để có thể sử dụng ở các module khác.

Một Hadoop cluster bao gồm một hoặc nhiều master nodes và nhiều slave nodes, có thể được scale out bằng cách thêm nodes vào cluster.

Các phần tiếp theo của bài viết sẽ nói kỹ hơn về kiến trúc và cách hoạt động của Hadoop.

## Kiến trúc Hadoop

Kiến trúc của Hadoop có thể được chia thành 4 layers:

<figure markdown="span">
  ![Hadoop architecture](https://phoenixnap.com/kb/wp-content/uploads/2021/04/hadoop-ecosystem-layers.png)
  <figcaption>Tổng quan về kiến trúc của Hadoop</figcaption>
</figure>

### Distributed Storage Layer

Thằng này chính là thằng HDFS, bao gồm một hoặc nhiều master nodes (hay còn gọi là NameNode) và nhiều slave nodes (hay còn gọi là DataNode). Mỗi node có bộ nhớ của riêng nó. Data được đưa vào sẽ được chia thành các data blocks sau đó được lưu ở HDFS distributed storage layer. Ngoài ra, HDFS lưu thêm 3 bản copies của data ở trên khắp cluster. NameNode sẽ lưu thông tin về các data block cụ thể và các replicas của nó được lưu ở đâu trong cluster.

### Cluster Resource Management

Thằng này chính là thằng YARN. Nó sẽ chỉ định resource cho các frameworks khác được viết cho Hadoop. Một số framework như Apache Pig, Hive, Giraph, Zookeeper. Nó cũng chỉ định resource cho chính thằng MapReduce luôn.

### Processing Framework Layer

Layer này bao gồm các frameworks thực hiện các bước xử lý data. Các framework như Spark, Storm hay Tez bây giờ có thể xử lý real-time, tăng hiệu quả cho hệ thống.

### Application Programming Interface

Như tên gọi của nó, layer này gồm các API để các lập trình viên sử dụng.

Chi tiết về cách hoạt động của từng thành phần sẽ được nói kỹ hơn ở một bài viết khác.


<!-- end -->

[^1]: [AWS, _What is Hadoop_](https://aws.amazon.com/what-is/hadoop/)