# BT5_Realtime-Monitor-Alert
## 1. PHẦN LÝ THUYẾT
### Docker là gì??
+ Docker là một nền tảng mã nguồn mở cho phép các nhà phát triển tự động hóa việc triển khai, đóng gói và chạy các ứng dụng bên trong các môi trường ảo hóa gọn nhẹ được gọi là Container.

  Khác với ảo hóa truyền thống (Virtual Machines - VM) vốn yêu cầu một hệ điều hành khách (Guest OS) hoàn chỉnh chạy trên một hạ tầng ảo hóa (Hypervisor), Docker sử dụng công nghệ ảo hóa ở cấp độ hệ điều hành (OS-level virtualization). Cụ thể, các Docker Container chia sẻ chung nhân (Kernel) của hệ điều hành máy chủ (Host OS) nhưng hoạt động hoàn toàn độc lập và biệt lập với nhau trong không gian người dùng (User Space).

  Nhờ cơ chế này, Docker giúp loại bỏ hoàn toàn hội chứng "chạy được trên máy tôi nhưng lỗi trên máy máy chủ", đảm bảo ứng dụng hoạt động đồng nhất trên mọi môi trường từ laptop cá nhân, môi trường thử nghiệm cho đến hệ thống triển khai thực tế.

### Các keyword được sử dụng trong docker-compose.yml
**docker-compose.yml** là file cấu hình định dạng YAML được sử dụng để định nghĩa và quản lý một ứng dụng Docker có nhiều container (Multi-container applications). Dưới đây là các keyword cốt lõi kèm ý nghĩa và ví dụ cụ thể:<br>



**a. version**  
- **Ý nghĩa**: Xác định phiên bản định dạng file Docker Compose được sử dụng (ví dụ: 3.8). Việc chỉ định phiên bản giúp Docker Engine biết cách biên dịch các cú pháp và tính năng tương thích trong file.
- **ví dụ *version : '3.8'***

**b. services**
- **Ý nghĩa**: Khởi tạo vùng định nghĩa cho các container thành phần trong hệ thống. Mỗi service tương ứng với một container được quản lý cấu hình riêng biệt.
- **Ví dụ:**
  *services:  
  web_app: # Tên service  
    image: nginx:latest*

**c.image** 
- **Ý nghĩa** : Chỉ định Docker Image (bản mẫu) được sử dụng để build và chạy container. Image này có thể được kéo về từ một Registry (như Docker Hub) hoặc từ kho lưu trữ nội bộ.
- **Ví dụ :**  
  *image: mariadb:10.6*

**d. build**
- **Ý nghĩa** :Được sử dụng thay thế hoặc kết hợp với image khi bạn muốn Docker tự động xây dựng một Image mới từ một file mã nguồn cấu hình (Dockerfile) nằm trong một thư mục cụ thể thay vì tải Image có sẵn.
- **Ví dụ:**
  *build:    
  context: ./flask_api # Đường dẫn tới thư mục chứa Dockerfile   
  dockerfile: Dockerfile*

**e.ports**
- **Ý nghĩa**: Cấu hình ánh xạ cổng (Port Forwarding) giữa Máy chủ (Host) và Container. Cú pháp chuẩn là PORT_MÁY_CHỦ:PORT_CONTAINER. Nó cho phép các thiết bị bên ngoài truy cập vào dịch vụ bên trong container thông qua cổng của máy chủ.
- **Ví dụ:**
*ports:     
  "8080:80" # Truy cập cổng 8080 trên laptop sẽ dẫn vào cổng 80 của container*

- **f. environment**
- **Ý nghĩa:** Khai báo các biến môi trường (Environment Variables) được truyền vào bên trong container khi khởi động. Thường dùng để cấu hình tham số hệ thống, mật khẩu database, token API...
- **Ví dụ** :
  *environment:    
  MYSQL_ROOT_PASSWORD: admin_secret_password   
  MYSQL_DATABASE: monitoring_db*   

- **G. volumes**
- **Ý nghĩa**:Định nghĩa cơ chế lưu trữ dữ liệu bền vững (Data Persistence). Volume giúp gắn kết (mount) một thư mục từ máy chủ vào trong container hoặc tạo một vùng lưu trữ do Docker quản lý, giúp dữ liệu không bị mất đi khi container bị xóa hoặc khởi động lại.
- **Ví dụ**:
*volumes:   
  db_data:/var/lib/mysql # Ánh xạ volume tên db_data vào thư mục data của MariaDB*   

- **H. networks**
- **Ý nghĩa**: Định nghĩa mạng ảo để kết nối các container với nhau. Các container nằm trong cùng một mạng ảo (network) có thể dễ dàng giao tiếp, phân giải tên miền nội bộ và truyền dữ liệu một cách bảo mật, biệt lập với các mạng bên ngoài.
- **Ví dụ:**
  *networks:  
     backend-net*  
- **i. depends_on**
- **Ý nghĩa:** Thiết lập thứ tự khởi động giữa các dịch vụ trong hệ thống. Nếu Service A depends_on Service B, Docker Compose sẽ tự động khởi chạy Service B trước rồi mới khởi chạy Service A.
- **Ví dụ**
*depends_on:   
    mariadb # Khởi động database trước khi chạy ứng dụng Flask API*  

### Ưu điểm khi triển khai ứng dụng sử dụng Docker
- Tính nhất quán và đồng bộ môi trường: Docker đóng gói toàn bộ mã nguồn, thư viện, biến môi trường và các tệp cấu hình phụ thuộc vào một Container duy nhất. Ứng dụng sẽ chạy giống nhau hoàn toàn trên mọi môi trường (Local, Staging, Production).

- Khởi động siêu tốc và Tiết kiệm tài nguyên: Khác với máy ảo VM phải khởi động cả một hệ điều hành dày đặc mất vài phút, Docker container khởi động chỉ trong vài giây. Container cực kỳ nhẹ vì dùng chung nhân hệ điều hành máy chủ, giúp tối ưu hiệu năng phần cứng tối đa.

- Quản lý biệt lập, an toàn (Isolation): Mỗi container hoạt động trong một môi trường cô lập tuyệt đối. Lỗi ứng dụng hay mã độc ở một container này không thể trực tiếp làm ảnh hưởng hoặc lây lan sang các container khác hay hệ điều hành gốc.

- Dễ dàng mở rộng và bảo trì (Scalability): Thiết kế theo kiến trúc Microservices giúp bạn dễ dàng nâng cấp, thay thế, sửa lỗi hay nhân bản (scale) một dịch vụ cụ thể (như tăng số lượng container Nginx) mà không cần can thiệp hay làm gián đoạn các phần khác của hệ thống.



### Quy trình triển khai ứng dụng Docker lên máy chủ thật không có Internet
 Đây là bài toán thực tế vô cùng quan trọng đối với kỹ sư hệ thống (Offline/Air-gapped Deployment). Khi máy chủ đích không thể dùng lệnh docker pull để tải ảnh từ Internet , quy trình xử lý chuẩn gồm 5 bước sau:
 
**Bước 1: Kiểm tra và Đóng gói Image tại máy cá nhân (Có Internet)**

1. Build và chạy thử ứng dụng
- Đảm bảo toàn bộ ứng dụng đã được **build** và chạy thử nghiệm ổn định trên laptop.  
- Kiểm tra các container hoạt động đúng chức năng trước khi đóng gói.

---

2. Liệt kê Image hiện có
- Sử dụng lệnh docker images để liệt kê chính xác các Image và Version đang dùng (ví dụ: nodered/node-red:latest, mariadb:latest).

---

**Bước 2: Xuất (Export) các Docker Image ra file nén (.tar)**

- Sử dụng lệnh docker save để nén các Image thành một file vật lý lưu trên ổ đĩa.
- *Lệnh thực hiện: docker save -o my_images_backup.tar nodered/node-red:latest mariadb:latest influxdb:1.8 nginx:latest*


---

**Bước 3: Sao chép file cài đặt Docker Engine Offline và file nén lên Máy chủ**

- Tải trước các file cài đặt Docker dạng ngoại tuyến (ví dụ các file .deb đối với Ubuntu Server hoặc .rpm đối với CentOS) từ máy có mạng.
- Sử dụng thiết bị lưu trữ ngoại vi (USB, ổ cứng di động) hoặc giao thức truyền file nội bộ (SFTP/SCP nếu có mạng nội bộ LAN) để chuyển file cài đặt Docker, file my_images_backup.tar và file docker-compose.yml lên máy chủ thật.


---

**Bước 4: Cài đặt Docker Engine và Giải nén (Load) Image trên Máy chủ**

- Cài đặt Docker Engine bằng các gói cài đặt offline đã chuẩn bị.  
- Sau khi Docker trên máy chủ sẵn sàng, tiến hành nạp lại các Image từ file nén vào bộ nhớ của Docker thông qua lệnh docker load.
- Lệnh thực hiện:  
     *docker load -i my_images_backup.tar*
- Kiểm tra lại bằng lệnh docker images trên máy chủ để chắc chắn các image đã xuất hiện đầy đủ.  
 


  ---


  **Bước 5: Khởi chạy hệ thống bằng Docker Compose**

  - Di chuyển vào thư mục chứa file docker-compose.yml trên máy chủ.
  - Chạy lệnh docker compose up -d để khởi động lại toàn bộ hệ thống. Docker Compose sẽ tự động nhận diện các Image đã có sẵn trong máy chủ mà không cần kết nối ra Internet để tải lại.    
  







## 2. PHẦN THỰC HÀNH : Hệ thống Monitor & Alert Realtime
- Ta tạo một dự án tên "Realtime_monitor"
- Cấu trúc thư mục như sau :
  realtime_monitor/  
├── docker-compose.yml  
├── flask_api/  
│   ├── Dockerfile  
│   ├── requirements.txt  
│   └── app.py  
└── frontend/  
    └── index.html  

##### BƯỚC 1: FILE CẤU HÌNH TỔNG THỂ docker-compose.yml    
<img width="1252" height="768" alt="image" src="https://github.com/user-attachments/assets/c9c15741-4bed-45e6-a709-8b1b81b5f3a6" />   


##### BƯỚC 2: XÂY DỰNG FLASK API (flask_api/)    
- Thành phần này đảm nhận vai trò kết nối vào MariaDB để lấy giá trị mới nhất rồi trả ra API định dạng JSON cho giao diện gọi.
  1. File flask_api/requirements.txt:
  <img width="598" height="186" alt="image" src="https://github.com/user-attachments/assets/8b548cfa-c14b-4c63-83fc-dec27f09c8a2" />  

  2. File flask_api/app.py:
  <img width="1178" height="746" alt="image" src="https://github.com/user-attachments/assets/e243bd1a-4530-4be5-b9fd-42e85a9875da" />  

  3. File flask_api/Dockerfile:
  <img width="669" height="376" alt="image" src="https://github.com/user-attachments/assets/c6213fa3-e2b6-4fb7-b4e9-d6004b23a6ad" />

##### BƯỚC 3: CẤU HÌNH NODE-RED (http://localhost:1880)     
**Cài đặt thêm các moddul trong Node-RED**  
1. node-red-node-mysql (Để kết nối và lưu dữ liệu tức thời vào MariaDB).  
2. node-red-contrib-influxdb (Để kết nối và lưu lịch sử vào InfluxDB).  
3. node-red-contrib-telegrambot (Để cấu hình gửi cảnh báo qua Bot Telegram).
- Nhấn nút Install ngay cạnh module đó để hệ thống tự động tải về. Sau khi cài xong, các node mới sẽ xuất hiện ở thanh công cụ bên trái.  
<img width="964" height="599" alt="image" src="https://github.com/user-attachments/assets/dabf8f91-da09-483f-b508-dddd3123f943" />  


---

<img width="1904" height="975" alt="image" src="https://github.com/user-attachments/assets/1e75ac9d-d3e3-4b36-8f01-a6edd01d177f" />       
1. Khởi tạo Database ban đầu trong MariaDB: Kéo 1 node **inject** nối vào node mysql để chạy câu lệnh tạo bảng:
<img width="791" height="755" alt="image" src="https://github.com/user-attachments/assets/c0d21460-83a9-4ef5-834c-f36dfc70137b" />      

3. Crawl dữ liệu động: *Dùng node inject cấu hình lặp lại (Repeat) mỗi 5 giây.*   
   - Nối vào node http request để lấy dữ liệu. Bạn có thể gọi API giá Bitcoin thực tế từ CoinGecko: *https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd*   
      <img width="718" height="730" alt="Untitled14" src="https://github.com/user-attachments/assets/741db569-fdd8-46e2-9897-53efc0021081" />    

4. Lưu trữ dữ liệu đồng thời vào 2 DB:  

   - Nhánh MariaDB (Giá trị tức thời): Sử dụng một node function để chuyển payload thành câu lệnh SQL cập nhật đè:    
        + *Cấu hình trong MARIADB*       
          <img width="709" height="807" alt="image" src="https://github.com/user-attachments/assets/646dbf87-e097-4248-a4b2-aa4bbcd5e72a" />  

   - Nhánh InfluxDB (Lịch sử): Nối payload vào thẳng node influxdb out để ghi nhận dữ liệu theo chuỗi thời gian (Time-series).
        + *Cấu hình tỏng InfluxDB:*    
          <img width="749" height="748" alt="image" src="https://github.com/user-attachments/assets/140d1c9e-0faa-4624-828d-be97134d202e" />   

5. Bắt giá trị bất thường & Gửi Alert Telegram:      
- Tạo bot với **@BotFather** trên Telegram   
  <img width="722" height="657" alt="image" src="https://github.com/user-attachments/assets/2e380d13-cc89-4676-a7c1-578719579632" />

     - Tạo 1 node function để check khoảng [A..B].          
     - Thêm Bot của bạn và ID [Của bạn] vào một nhóm Telegram chung. Nối đầu ra của node function trên vào node Telegram Sender để bắn thông báo trực tiếp vào nhóm.       + *Cấu hình bot telegram*   
   <img width="1012" height="714" alt="Untitled13" src="https://github.com/user-attachments/assets/fe0712c5-4dc5-465f-be30-867643d3ae71" />  

##### BƯỚC 4: THIẾT KẾ FRONT-END (frontend/index.html)   
Giao diện sẽ chạy trên Nginx (Cổng 8080)----> Setup cổng như nào tuỳ bạn. Nó sẽ tự động gọi API của Flask sau mỗi 2 giây để hiển thị giá realtime, đồng thời nhúng Grafana Dashboard qua thẻ iframe.
<img width="1301" height="759" alt="image" src="https://github.com/user-attachments/assets/73c9b656-6333-4039-accb-f08c5ab34a00" />


##### BƯỚC 5: KHỞI CHẠY
- Bây giờ bạn mở Terminal trên Ubuntu tại thư mục dự án và tiến hành chạy lệnh:
1. Khởi động hệ thống: **docker compose up -d --build**   
<img width="1903" height="818" alt="image" src="https://github.com/user-attachments/assets/08eaedb8-45a9-4ec6-a76c-8841d32f59e8" />

2. Cấu hình trên GaraFana  
<img width="1899" height="1026" alt="image" src="https://github.com/user-attachments/assets/bcd1790a-1184-47ea-8715-49e7119e8e28" />

#### KẾT QUẢ:
- 1. Các thông số trên grafana
     <img width="1404" height="515" alt="image" src="https://github.com/user-attachments/assets/59a16008-0624-4440-9213-8cc45852c866" />

- 2. Web cấu hình qua Ngix
<img width="1883" height="916" alt="image" src="https://github.com/user-attachments/assets/089a3829-1b03-49d1-b98d-0dc55268471b" />

- 3. Tin nhắn đẩy về Telegram mỗi 10s được update 1 lần:
  <img width="899" height="940" alt="image" src="https://github.com/user-attachments/assets/09fe8e43-ac3e-4a09-bb36-869ba8480b98" />






#####

