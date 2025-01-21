### 1. Description

### 2. Solution
Từ username, ta có thể tìm được Github (Edge, không rõ tại sao Chrome chưa hiện ở thời điểm mình viết bài này), có thể cả Instagram, Tiktok. 
Sử dụng Whatmynames, tuy nhiên, ở đây nó không cho ra kết quả. Sử dụng 1 công cụ khác là Sherlock, ta tìm được tài khoản Github của người này: 

![image](https://github.com/user-attachments/assets/454f6c1c-f276-44ec-af1b-cf77a0fabdd4)

Có thể kiểm tra repo Hidden, tuy nhiên đây chỉ là fake flag của mình. Hay cả status cũng chỉ là một link troll. Ta thấy được tài khoản Instagram của hắn từ tài khoản Github này. Truy cập vào, từ phần tin nổi bật của hắn,
ta biết được hắn đã tạo một tài khoản tiktok mới, các post còn lại không có ý nghĩa gì. 

![image](https://github.com/user-attachments/assets/0ef7ae2e-b943-4420-8ac7-303c38e2f08d)

Kiểm tra tài khoản Tiktok, từ video mà hắn post, ta có thể thấy được 1 link Discord : 

![Screenshot 2025-01-21 133828](https://github.com/user-attachments/assets/8ce6bc66-785b-481f-bc77-920a971b3fb1)

Đây là 1 link mời. Truy cập vào, ta được chuyển đến 1 Discord Server. Ở đây, ta nhận được phần đầu của flag.

![image](https://github.com/user-attachments/assets/b3a0dc4b-3c74-4d64-8ba5-4abf40faf315)

Vậy còn past 2 đâu nhỉ ????. Quay lại với Instagram, để ý vào bio của người này : 

![image](https://github.com/user-attachments/assets/61c4f58a-6f1b-4f6f-b5ea-cdc26c6a7a27)

Có thể nhiều bạn sẽ sử dụng Wayback Machine, nhưng cách này không ra. Ít nhất là ở thời điểm mình viết WU này, web đang bị sập. Và cũng từ bio, có vẻ WM không đúng. Ở đây, ta có 1 công cụ khác : **archive.today**. 
Copy đường dẫn Instagram và sử dụng công cụ này, ta có được 1 screenshot của web: 

![image](https://github.com/user-attachments/assets/eb9dbc73-ec67-435b-8e70-2f021a1b2f71)

Và trong đó có gắn 1 link tới 1 tài khoản Twitter : 

![image](https://github.com/user-attachments/assets/f78e2eb2-32f2-472a-8ac0-d7f386dfd6e5)

Truy cập vào tài khoản Twitter này, ta thấy 1 đoạn mã base64 ở bio, nhưng đây cũng là 1 rickroll khác. Đọc tiếp các post, 1 có đề cập đến việc hắn sẽ tới Nairobi, post còn lại là ảnh chụp trong 1 phòng khách sạn 
mà hắn nói sẽ viết review. Để ý kĩ chiếc khăn, ta có thể thấy chữ Four Points by ..... Tuy nhiên, 1 phần chữ đã bị che đi. Kết hợp cả hai thông tin, ta sử dụng GG maps. Để tìm các khách sạn bắt đầu bằng Four Points ở Nairobi. 
Sau một hồi ta có thể khoanh vùng 2 khách sạn như mình đánh dấu : 

![Screenshot 2025-01-21 135803](https://github.com/user-attachments/assets/13124d48-6cc5-4761-9cc7-7ba7768f0bd1)

Khoanh vùng các review trong thời gian gần đây, ta tìm được part 2 của flag tại khách sạn **Four Points by Sheraton Nairobi Airport** :

![image](https://github.com/user-attachments/assets/5e298f38-2cab-4732-91d1-9b0c4e3e07eb)

### 3. Lời nói cuối
Đây là lần đầu mình ra đề Osint nên sẽ có nhiều thiếu xót, thiếu logic. Mong nhận được sự góp ý và thông cảm từ mọi người. Trân trọng cảm ơn!!!!
