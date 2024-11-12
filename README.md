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
 ![image](https://github.com/user-attachments/assets/e9eb7041-68e4-46f8-b88a-ea33eceb8b69)

#  Phần 1: Xử lý dữ liệu (Python)
Mục tiêu của phần này là phân nhóm sản phẩm theo công năng và đặc tính sản phẩm dựa trên tên sản phẩm, xử lý dữ liệu duplicated, làm sạch dữ liệu để phân tích
## Xử lý cột dư
File đầu tháng:

![image](https://github.com/user-attachments/assets/4cbafbd8-58de-4964-8ee2-39713e3aeb56)

---
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


- B5: Dọn dẹp thông tin còn lại:
  
Sau khi loại bỏ trọng lượng và bao bì, phần còn lại của tên sản phẩm sẽ được coi là miêu tả đặc tính sản phẩm.

Trả về kết quả: Hàm trả về các thông tin phân loại sản phẩm (Danh mục, Loại, Miêu tả, Đóng gói, Khối lượng).
![image](https://github.com/user-attachments/assets/03eb91a6-7eeb-4297-8597-e43a5b48c0ce)

- B6: Tối ưu các trường hợp hàm không thể nhận diện được category do lỗi font tiếng Việt
![image](https://github.com/user-attachments/assets/aa5ccb11-8426-489c-971b-c38a4d2769ad)

# Phần 2: Phân tích chiến lược hàng hoá
![image](https://github.com/user-attachments/assets/514c6619-2223-4c29-b1de-957ebda530f4)
## 1. Tổng quan
- Bách hoá xanh có 6 Danh mục sản phẩm, với 19 loại sản phẩm và 671 dòng sản phẩm. Tổng số lượng mặt hàng BHX có là 1901 sản phẩm
- Trong khi đó, Winmart có 4 Danh mục sản phẩm, với 16 loại sản phẩm và 356 dòng sản phẩm. Tổng số lượng mặt hàng của Winmart là 667 sản phẩm
![image](https://github.com/user-attachments/assets/20806d27-bda8-4c95-852e-02610eb161f1)

### Kết luận 1
Với mục tiêu là những khách hàng từ nhiều thị trường truyền thống như nội trợ, sinh viên, người đi làm, BHX và Winmart cùng sử dụng chiến lược đa dạng hoá mặt hàng. Winmart tuy vẫn có 16 loại sản phẩm nhưng số lượng sản phẩm chỉ bằng 30% của BHX 

## 2. Về danh mục sản phẩm và loại sản phẩm
![image](https://github.com/user-attachments/assets/39c547e7-2e56-4d74-bb6b-166e6169af04)
Bách hoá xanh và Winmart đều kinh doanh: Thực phẩm ăn liền, Chăm sóc cá nhân, Cà phê và Vệ sinh nhà cửa 
Đây đều là các sản phẩm sử dụng nhiều trong sinh hoạt hàng ngày 

### Kết luận 2
Cả 2 đều đánh mạnh vào Sản phẩm tiêu dùng, đặc biệt là danh mục Chăm sóc cá nhân và Thực phẩm ăn liền.
Củng cố cho quản điểm trên, BHX có 408 sản phẩm loại dầu gội và 305. Một lần sự đa dạng hoá được thể hiện. Trong khi đó Winmart có 157 sản phẩm mì ăn liền và 119 loại dầu gội 
![image](https://github.com/user-attachments/assets/36161415-2fe0-4545-9051-cb14f60c3e1e)

## Sơ đồ sản phẩm
![image](https://github.com/user-attachments/assets/b96e946a-3cce-468f-a376-8800631a8d88)

# Phần 3: Phân tích biến động giá 
![image](https://github.com/user-attachments/assets/fe892505-654c-46bd-adab-85cbcc081693)

![image](https://github.com/user-attachments/assets/9aa881d2-2406-48a3-83a7-76af4bcf2b0f)

## 1. Về số lượng sản phẩm giảm giá và mức giảm giá
- BHX có số lượng sản phẩm giảm giá khá cao trong tất cả các nhóm hàng, đặc biệt là nhóm “Vệ sinh nhà cửa” (104 sản phẩm giảm giá, mức giảm trung bình 15%). Các mức giảm giá trung bình của BHX tương đối thấp, dao động từ 12% đến 18%.
- Winmart giảm giá ít hơn về số lượng và tập trung vào một số sản phẩm cụ thể với mức giảm mạnh. Ví dụ, nhóm “Vệ sinh nhà cửa” chỉ có 11 sản phẩm giảm giá nhưng mức giảm trung bình lên đến 46%. Các nhóm khác như “Chăm sóc cá nhân” và “Cà phê” gần như không có sản phẩm giảm giá.
### Kết luận 3
BHX có xu hướng giảm giá rộng rãi trên nhiều sản phẩm với mức giảm vừa phải, có thể nhằm giữ chân khách hàng và thúc đẩy doanh số thông qua chính sách giá dễ tiếp cận. Winmart giảm giá ít sản phẩm hơn nhưng mức giảm cao, có khả năng nhằm tạo điểm nhấn cho một số sản phẩm chủ lực.

## 2. Về số lượng sản phẩm tăng giá và mức tăng giá
- BHX tăng giá ở tất cả các nhóm sản phẩm, với tỷ lệ tăng giá trung bình từ 11% đến 21%, nhưng số lượng sản phẩm tăng giá không nhiều, trừ nhóm “Thực phẩm ăn liền” với 38 sản phẩm (mức tăng 11%). Điều này cho thấy BHX chỉ điều chỉnh giá tăng ở một số ít sản phẩm, tránh tăng mạnh để duy trì tính cạnh tranh.
- Winmart có số lượng sản phẩm tăng giá thấp, nhưng mức tăng giá trung bình cao hơn BHX, đặc biệt trong nhóm “Vệ sinh nhà cửa” (10 sản phẩm tăng giá với mức tăng trung bình 28%).

### Kết luận 4
Winmart có vẻ tập trung vào việc tăng giá trên một số mặt hàng nhất định, có thể để bù lại mức giảm giá mạnh trên các sản phẩm khác. BHX lại chọn cách tăng giá nhẹ hơn và đồng đều trên nhiều sản phẩm, duy trì sự ổn định trong chính sách giá.

# Phần 4: Tổng kết 
## Chiến lược Hàng hóa 
- BHX đang áp dụng chiến lược hàng hóa đa dạng sâu rộng, phục vụ nhu cầu chi tiết và phong phú của các phân khúc khách hàng, đồng thời tạo nên một hệ sinh thái sản phẩm phong phú để tăng tần suất mua sắm.
- Winmart chọn chiến lược tập trung, có chọn lọc hơn về số lượng sản phẩm, chỉ giữ lại những mặt hàng có nhu cầu cao. Điều này giúp Winmart tối ưu hóa việc quản lý hàng tồn kho, nhưng lại không có nhiều sự lựa chọn cho khách hàng như BHX.
- Điểm chung là cả 2 tập trung vào Sản phẩm tiêu dùng ( Chăm sóc cá nhân và Thực phẩm ăn liền)

## Chiến lược giá
- BHX sử dụng chiến lược giá cạnh tranh và ổn định, với nhiều sản phẩm giảm giá, mức giảm vừa phải, trải đều trên tất cả các danh mục. BHX cũng không tăng giá đột ngột mà chỉ điều chỉnh giá nhẹ ở một số nhóm sản phẩm. Đây là chiến lược giúp duy trì tính ổn định và cạnh tranh, làm khách hàng cảm thấy dễ tiếp cận hơn.
- Winmart tập trung vào chiến lược giảm giá sâu trên một số sản phẩm chủ lực, đồng thời tăng giá cao ở những mặt hàng khác để bù đắp chi phí. Chiến lược này tạo cảm giác rằng các sản phẩm giảm giá của Winmart có mức giảm rất hấp dẫn, nhưng lại khiến khách hàng phải cân nhắc hơn khi mua các sản phẩm không giảm giá.
----
Theo tôi BHX đang có ưu thế hơn về CLHH – CLG vì BHX có lợi thế rõ rệt trong việc đa dạng hóa sản phẩm và đưa ra mức giá ổn định, phù hợp với chiến lược giá cạnh tranh trên nhiều mặt hàng. Nhờ có danh mục sản phẩm phong phú và giá cả hợp lý, BHX có khả năng thu hút được một lượng lớn khách hàng với nhu cầu phong phú và đa dạng. Đặc biệt, chiến lược giá đều đặn và giảm giá nhiều sản phẩm cũng giúp BHX duy trì sự trung thành của khách hàng lâu dài.
