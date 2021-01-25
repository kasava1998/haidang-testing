##I, Cài đặt và định cấu hình Chế độ báo động Docker trên CentOS 7
giới thiệu tại đây : https://medium.com/@Alibaba_Cloud/how-to-install-and-configure-docker-swarm-mode-on-centos-7-c0b32f0fbc82

Docker Swarm là một công cụ phân cụm riêng cho các vùng chứa Docker có thể được sử dụng để quản lý một cụm các nút Docker như một hệ thống ảo duy nhất. Docker Swarm cho phép bạn thêm hoặc bớt các lần lặp lại vùng chứa khi nhu cầu tính toán thay đổi. Docker Swarm bao gồm hai thành phần chính là nút Manager và nút Worker. Nút trình quản lý được sử dụng để xử lý các tác vụ quản lý cụm, chẳng hạn như, duy trì trạng thái cụm, lập lịch dịch vụ và phục vụ các điểm cuối HTTP API chế độ bầy đàn. Nút công nhân là một ví dụ của công cụ Docker có thể được sử dụng để thực thi vùng chứa. Trình quản lý Swarm cho phép bạn tạo một cá thể người quản lý chính và nhiều bản sao trong trường hợp phiên bản chính bị lỗi. Bạn có thể triển khai các nút quản lý và nhân viên trong thời gian chạy ở chế độ Swarm của công cụ Docker. Trong hướng dẫn này,

###1, Yêu cầu
Ba phiên bản đám mây của Alibaba có cài đặt CentOS 7. Trong số đó một lần máy chủ sẽ hoạt động như một nút Trình quản lý và hai máy chủ sẽ hoạt động như nút Công nhân.
Địa chỉ IP tĩnh được định cấu hình trên tất cả các phiên bản. Ở đây, chúng ta sẽ sử dụng địa chỉ IP 192.168.0.102 cho nút Manager, 192.168.0.103 cho Worker node1 và 192.168.0.104 cho Worker node2.
###2, Bắt đầu
Trước khi bắt đầu, bạn sẽ cần cấu hình tệp / etc / hosts trên mỗi nút, vì vậy mỗi nút có thể giao tiếp với nhau bằng tên máy chủ. Bạn có thể cập nhật tệp / etc / hosts trên mỗi nút như hình dưới đây: nano / etc / hosts

>192.168.0.102  managernode
192.168.0.103  workernode1
192.168.0.104  workernode2

Lưu và đóng tệp khi bạn hoàn tất. Tiếp theo, bạn sẽ cần phải định cấu hình tên máy chủ trên mỗi nút theo tệp / etc / hosts. Bạn có thể thực hiện việc này bằng cách chạy lệnh sau trên từng nút một: <b> Nút trình quản lý: </b>

>hostnamectl set-hostname managernode

-Nút công nhân1:

>hostnamectl set-hostname workernode1

-Nút công nhân2

>hostnamectl set-hostname workernode2

###3, Cài đặt Docker Engine
Tiếp theo, bạn sẽ cần cài đặt Docker Community Edition trên tất cả các nút. Theo mặc định, phiên bản mới nhất của Docker CE không có sẵn trong kho lưu trữ CentOS 7. Vì vậy, bạn sẽ cần thêm kho lưu trữ Docker CE vào hệ thống của mình. Bạn có thể thực hiện việc này bằng cách chạy lệnh sau trên tất cả các nút:

> wget https://download.docker.com/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker.repo 
- Sau khi Kho lưu trữ Docker đã được cài đặt, hãy chạy lệnh sau để cài đặt Docker CE:

> yum install docker-ce –y

- Tiếp theo, khởi động dịch vụ Docker và cho phép nó bắt đầu khi khởi động bằng lệnh sau:

>systemctl start docker
systemctl enable docker

###4, Định cấu hình tường lửa

- Tiếp theo, bạn sẽ cần mở các cổng 7946, 4789, 2376, 2377 và 80 trên tường lửa để một cụm bầy hoạt động bình thường. Chạy lệnh sau trên tất cả các nút:

>firewall-cmd --permanent --add-port=2376/tcp
firewall-cmd --permanent --add-port=2377/tcp
firewall-cmd --permanent --add-port=7946/tcp
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=7946/udp
firewall-cmd --permanent --add-port=4789/udp

- Cuối cùng, tải lại tường lửa và dịch vụ Docker để áp dụng tất cả các thay đổi:

> firewall-cmd --reload
systemctl restart docker


![](https://github.com/kasava1998/haidang-testing/blob/main/33616552_358762301300219_8852602229044019200_o.jpg)