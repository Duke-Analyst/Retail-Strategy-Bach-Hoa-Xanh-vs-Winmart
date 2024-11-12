# Giới thiệu
Dự án này nhằm phân tích và so sánh chiến lược hàng hoá và chiến lược giá của hai hệ thống bán lẻ lớn tại Việt Nam: Bách Hoá Xanh và Winmart. 

## 1. Cấu trúc dự án
- Phần 1: Xử lý dữ liệu: phân nhóm sản phẩm theo công năng và đặc tính sản phẩm của mỗi nhà bán lẻ
- Phần 2: Phân tích chiến lược sản phẩm: Sử dụng Power Bi để trực quan hoá, tổng hợp số lượng từng loại sản phẩm để hiểu rõ hơn về chiến lược hàng hoá mà mỗi hệ thống áp dụng
- Phần 3: Phân tích chiến lược giá: tính toán các mức chiết khấu và số lượng sản phẩm chiết khấu để phân tích chiến lược giá của hai nhà bán lẻ.
- Phần 4: Tổng kết 

## 2. Dataset
- Bách Hoá Xanh: gồm 2 file tại ngày 01-12-2022 (đầu tháng) và 15-12-2022 (giữa tháng), chứa dữ liệu về giá, tên sản phẩm, sku,...
![image](https://github.com/user-attachments/assets/49ec25a3-d878-4c60-be48-0e0f1aeced1d)

- Winmart: gồm 2 file tại ngày 01-12-2022 (đầu tháng) và 15-12-2022 (giữa tháng), chứa dữ liệu về giá, tên sản phẩm, đơn vị sản phẩm,...
- ![image](https://github.com/user-attachments/assets/e9eb7041-68e4-46f8-b88a-ea33eceb8b69)

#  Phần 1: Xử lý dữ liệu
Mục tiêu của phần này là phân nhóm sản phẩm theo công năng và đặc tính sản phẩm dựa trên Tên sản phẩm, xử lý dữ liệu duplicated, làm sạch dữ liệu để phân tích
## xử lý cột dư
File đầu tháng
![image](https://github.com/user-attachments/assets/4cbafbd8-58de-4964-8ee2-39713e3aeb56)

File giữa tháng: Rename cho tên cột của cả 2 file giống nhau để gộp file
![image](https://github.com/user-attachments/assets/bb3c7df1-ae14-4898-ab45-0563ef90fab1)

## Gộp file và xử lý duplicated 
Chỉ xoá hàng bị duplicated ở cả 3 cột tên sản phẩm và giá, Sku (Điều này sẽ giữ lại các sản phẩm bị thay đổi về giá tại 2 thời điểm đầu tháng và cuối tháng)
![image](https://github.com/user-attachments/assets/bd479835-0ac5-4b46-a36c-181f9079f082)

## Phân loại sản phẩm 
### Extract brand
Ở đây, tôi tạo hàm extract_brand(product_name) để trích xuất tên thương hiệu từ một tên sản phẩm. 
Chia tên sản phẩm thành các từ: Hàm sẽ tách tên sản phẩm thành các từ riêng biệt bằng cách sử dụng phương thức .split().
Lọc các từ viết hoa: Từ đầu tiên trong tên sản phẩm được bỏ qua (do việc viết hoa đầu dòng). Và các từ viết hoa khác sẽ được trích xuất ra 
Ghép các từ lại thành tên thương hiệu: Các từ đã lọc được sẽ được ghép lại thành một chuỗi, được coi là tên thương hiệu. Nếu không tìm thấy từ nào hợp lệ, hàm sẽ trả về "Unknown"

![image](https://github.com/user-attachments/assets/5af5f73b-b66a-49e0-ab73-9f8433879de4)

### Extract category
Ở đây, tôi tạo hàm split_data(item) được sử dụng để phân tích và tách thông tin từ tên sản phẩm. Dưới đây là giải thích ngắn gọn các bước của hàm:

- B1: Trích xuất trọng lượng hoặc thể tích: 
![image](https://github.com/user-attachments/assets/ea1fe8be-5b6d-40a0-ae69-115af6c285c3)
Tìm kiếm các từ như 'g', 'kg', 'lít', 'ml', 'L' để lấy thông tin về trọng lượng hoặc thể tích (ví dụ: '75g', '3.4 lít')

- B2: Phân loại sản phẩm chính (category): Kiểm tra tên sản phẩm và tạo dictionary để xác định loại sản phẩm chính (ví dụ: "Cà phê", "Chăm sóc cá nhân", "Bia, nước giải khát")
 ![image](https://github.com/user-attachments/assets/6a2cf43b-c924-4ae9-82e0-6652362311d9)

- B3: Phân loại con (sub-category): Nếu sản phẩm đã được phân loại vào nhóm chính, tiếp tục tạo dictionary và kiểm tra để xác định loại sản phẩm con (ví dụ: "Cà phê hoà tan", "Cà phê lon", "Dầu gội", "Dầu xả").
![image](https://github.com/user-attachments/assets/0015733f-7606-4447-870f-a0302d8bdf90)

- B4: Xác định cách đóng gói
Sử dụng regex để tìm kiếm thông tin bao bì sản phẩm (ví dụ: 'thùng', 'gói', 'chai')
 ![image](https://github.com/user-attachments/assets/763beeb8-b983-4e06-864b-de43b3023846)


B5: Dọn dẹp thông tin còn lại: Sau khi loại bỏ trọng lượng và bao bì, phần còn lại của tên sản phẩm sẽ được coi là miêu tả đặc tính sản phẩm.
![image](https://github.com/user-attachments/assets/03eb91a6-7eeb-4297-8597-e43a5b48c0ce)
Trả về kết quả: Hàm trả về các thông tin phân loại sản phẩm (Danh mục, Loại, Miêu tả, Đóng gói, Khối lượng).

B6: Tối ưu các trường hợp hàm không thể nhận diện được category do lỗi font tiếng Việt
![image](https://github.com/user-attachments/assets/aa5ccb11-8426-489c-971b-c38a4d2769ad)

