### 1. Description

Làm lại Network một tí cho nhớ nha!!!!

[Link](https://bit.ly/42i6kK6) 

### 2. Solution

Bài này cung cấp 1 file pcapng. Mở file bằng Wireshark. Như bình thường, ta kiểm tra xem có gì để export không bằng cách chọn File -> Export Objects -> HTTP, ta thấy như sau: 

![image](https://github.com/user-attachments/assets/67505a5c-037c-4879-a4ff-8c01eae6c6da)

Save toàn bộ các file ra. Ta thấy điểm chung là các file có đuôi yzo và thông tin đã bị mã hóa thành dạng hex. Ngoài ra ta thấy 1 file khác tên là lock.html. Cat nó, ta được code mã hóa file:

```python
import os
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
import binascii

def encrypt_file(input_file, output_file, key):
    iv = get_random_bytes(16)

    cipher = AES.new(key, AES.MODE_CBC, iv)

    with open(input_file, 'rb') as f:
        data = f.read()

    padding_length = 16 - len(data) % 16
    padded_data = data + bytes([padding_length]) * padding_length
    encrypted_data = cipher.encrypt(padded_data)

    iv_hex = binascii.hexlify(iv).decode('utf-8')
    encrypted_data_hex = binascii.hexlify(encrypted_data).decode('utf-8')
    with open(output_file, 'w') as f_out:
        f_out.write(iv_hex + encrypted_data_hex)

    print(f"Encrypted file written to: {output_file}")

def encrypt_folder(input_folder, output_folder, key):
    if not os.path.exists(output_folder):
        os.makedirs(output_folder)

    for root, dirs, files in os.walk(input_folder):
        for file in files:
            input_file = os.path.join(root, file)
            relative_path = os.path.relpath(input_file, input_folder)
            output_file = os.path.join(output_folder, relative_path + '.yzo')
            encrypt_file(input_file, output_file, key)

key = b'happynewyear2025bykozybeartasclb'

input_folder = './SECRET'
output_folder = './ENCRYPTED'

encrypt_folder(input_folder, output_folder, key)
```
Đoạn mã hóa này sử dụng AES với mode CBC. Key được cho sẵn trong code còn IV được tạo ngẫu nhiên 16 bytes, sau đó nó được thêm vào đầu của mỗi file đã được mã hóa. Ta chỉ cần viết code để giải mã cho nó bằng cách lấy 16 bytes đầu khỏi mỗi file đã mã hóa làm IV và key thì đã có sẵn: 

```python
import os
import binascii
from Crypto.Cipher import AES

def decrypt_file(input_file, output_file, key):
    with open(input_file, 'r') as f:
        hex_data = f.read()
    
    iv = binascii.unhexlify(hex_data[:32])
    encrypted_data = binascii.unhexlify(hex_data[32:])
    
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted_data = cipher.decrypt(encrypted_data)
    
    padding_length = decrypted_data[-1]
    decrypted_data = decrypted_data[:-padding_length]
    
    with open(output_file, 'wb') as f_out:
        f_out.write(decrypted_data)
    
    print(f"Decrypted file written to: {output_file}")

def decrypt_folder(input_folder, output_folder, key):
    if not os.path.exists(output_folder):
        os.makedirs(output_folder)
    
    for root, dirs, files in os.walk(input_folder):
        for file in files:
            if file.endswith('.yzo'):
                input_file = os.path.join(root, file)
                relative_path = os.path.relpath(input_file, input_folder)
                output_file = os.path.join(output_folder, relative_path[:-4])
                
                decrypt_file(input_file, output_file, key)

key = b'happynewyear2025bykozybeartasclb'
input_folder = './ENCRYPTED'
output_folder = './DECRYPTED'

decrypt_folder(input_folder, output_folder, key)
```

Phần đa các file không có gì liên quan lắm. Ở file secret_chat.xlsx, ta có được flag của bài này: 

![image](https://github.com/user-attachments/assets/cc76eaf3-64ea-4730-8927-fef3f4361569)

FLAG : **TASCTF{43S_w1th_k3y_4nd_1v_fr0m_c0mput3r!!!!}**
