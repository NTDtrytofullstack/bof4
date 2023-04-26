# bof4
## Ý tưởng.
- Hôm nay ta sẽ tiếp tục 1 dạng mới của buffer overflow đó là ROPchain.
![image](https://user-images.githubusercontent.com/130078745/234564690-f9afc578-ea77-4f5d-80c9-f0611b540c80.png)
- Nhìn hình thì ta có thể thấy ko có 1 hàm hay bất kì câu lệnh liên quan đến flag vì vậy ở bài dạng bài ngày hôm này ta phải tự tạo 1 cái shell **/bin/sh** và sử dụng kỹ thuật **ret2win** hoàn thành đc bài tập này.
## Tự hành.
- Đầu tiên ta cùng vào terminal để cùng check offset của **v8**.
![image](https://user-images.githubusercontent.com/130078745/234573860-e8bd0fb1-06d4-405d-ab88-aa50acbe2a21.png)
- sau khi check thì offset của **v8** là 88. 
- sau đấy ta sẽ cùng tạo 1 nơi để sau khi đến hàm return nó sẽ truy cập đc dữ liệu và tạo ra 1 shell , ở đây ta sẽ sử dụng địa chỉ của lệnh gets để xem như nó là 1 hàm tạo shell 
- để tạo đc nó ta cần phải tuân theo các điều kiện của **gets** với rax là mã ( ở trường hợp này mã của gets là 0x3b) , rdi, rsi, rdx là những điều kiện và gatget syscall.
- Thế nên ta cùng tìm địa chỉ của các điều kiện đó , nên ta sẽ cùng viết tools nào :>> .
![image](https://user-images.githubusercontent.com/130078745/234582974-a7a62795-e3ed-4e40-80d2-e84587b276a8.png)
![image](https://user-images.githubusercontent.com/130078745/234582706-caf19aef-3b3a-47a0-8430-759226b59046.png)
![image](https://user-images.githubusercontent.com/130078745/234583324-84f74087-f304-4648-8eeb-b1f9cf8a617d.png)
- Việc đầu tiên để chương trình có thể truy cập đc lệnh thực hiện shell trong hàm **gets** thì ta đầu tiền phải gán địa chi lưu trữ chuỗi **/bin/sh** vào rdi của hàm gets sau đó mới nhập chuỗi ta cần vào đó.
- với câu lệnh **pop rdi** thì sau câu lệnh đó nó sẽ gán địa chỉ đó vào chỗ rdi.
- nhưng trước đấy ta cần phải biết địa chỉ nào còn trống và nó có thể ghi vào trước đã , vì thế ta phải tìm nó.
![image](https://user-images.githubusercontent.com/130078745/234586420-f877299a-2da0-4ac7-8e35-6f1785ecb7e3.png)
- Ta xài câu lệnh **x/50xg** để có thể truy cập sau hơn tìm kiếm những địa chỉ còn trống để có thể ghi vào mà ko bị lỗi.
![image](https://user-images.githubusercontent.com/130078745/234616315-402fefec-c924-4966-9a2f-97661636b9cd.png)
- Ta lấy đại 1 địa chỉ trống nào đó cũng đc.
- tiếp đấy ta cần chuyền địa chỉ có thể nhập chuỗi vào rdi và để có thể nhập đc chuỗi ta gọi **gets** 1 lần nữa để có thể nhập đc chuỗi,  ta lại tiếp tục lặp lại vòng payload đầu để có thể nhập chuỗi **/bin/sh** vào rdi , tiếp đến là nhập vào đủ các đk còn thiếu như rsi , rdx , rax. Với giá trị rdx, rsi = 0 hay **null**
- Nhưng hãy xem lại ảnh có chứa địa chỉ của rdx ta có thể thấy 1 dòng kế bên là ** add rsp, 0x28** ở đây nó sẽ thêm dữ liệu vào là 0x28 byte vì vậy ta phải cộng thêm để tránh trường hợp bị sai dữ liệu.
![image](https://user-images.githubusercontent.com/130078745/234646009-d0267dd2-0195-458f-92e7-c50b691597ef.png)
- Chạy thử chương trình thì đã thu đc shell
![image](https://user-images.githubusercontent.com/130078745/234646566-9d1d932e-8909-4025-ba1d-6a92922d2b0d.png)


