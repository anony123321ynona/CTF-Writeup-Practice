# ⛳ Auto Fmt

- Description: Trung bình nc 45.122.249.68 10015
- Hint:
  - Một số hàm của pwntools hỗ trợ tạo payload format string tự động (fmrstr_payload, Fmtstr, ...)
  - Đọc này đi rồi lấy điểm free nè https://docs.pwntools.com/en/stable/fmtstr.html#pwnlib.fmtstr.fmtstr_payload
  - write_size='short'

## Write-up:

Ta xem mã giả trên IDA:

![image](https://user-images.githubusercontent.com/48288606/147414211-965fb417-8844-4497-ac59-3a2c893160f4.png)

Chương trình trên thực hiện lấy 2 giá trị ngẫu nhiên từ **/dev/urandom** và đưa vào 2 biến &ptr và &v5. Sau đó printf ra giá trị của 2 biến random này và địa chỉ của biến a. Trong đó **a** và **b** là hai biến global với **addr_b = addr_a - 8**.

![image](https://user-images.githubusercontent.com/48288606/147578549-f393405c-9be1-48c8-b515-b7708970470b.png)

Nếu giá trị **a** và **b** bằng giá trị random được tạo ra thì sẽ nhảy vào được thực thi shell và hoàn thành challenge. Ở đây ta thấy hàm `printf(s)` có lỗ hổng **format string**, không truyền **format specifier**. Vì vậy ta có thể ghi đè giá trị của biến  **a** và **b**. Bên cạnh đó, cơ chế **PIE** được bật nên không thể xác định địa chỉ biến **b** bằng địa chỉ tuyệt đối mà phải thông qua **a**.

![image](https://user-images.githubusercontent.com/48288606/147579082-b5c9db3b-c882-4efb-aecd-79d9da6a4a9b.png)

Trong hint có hướng dẫn cách dùng hàm **fmtstr_payload** để tạo payload. Chỗ này phải mất một thời gian mới hiểu cách dùng, document làm mình khá sợ. Nói chung sau một hồi lĩnh hội thì mình thấy tập trung vào chỗ này là dễ hiểu nhất 

![image](https://user-images.githubusercontent.com/48288606/147579540-36b83572-a682-4fe8-af6d-f2b91d475511.png)

Cách dùng thì phải nói rất đơn giản, làm mình ban đầu cũng không nghĩ đây trở thành chall dễ nhất. Syntax: `fmtstr_payload(offset_fmt, addrA:valueA, addrB:valueB , ..., write_size='short')`
Với: 
- offset_fmt -> offset của chuỗi format string
- addrA:valueA -> Cặp địa chỉ cần được ghi - giá trị mong muốn ghi có dạng dictionary.
- write_size='short' -> Kiểu ghi byte, như thông thường mình hay tách giá trị để ghi như %n, %hn, %hhn. Để 'short' cho tối ưu.

Cú pháp đầy đủ trên document (tham khảo):

![image](https://user-images.githubusercontent.com/48288606/147580395-a4f73b93-5ca2-48d6-aca7-3493748d0349.png)

Tham khảo code exploit [tại đây](autofmt.py)

**Note**: Bài này cho biết thêm được một công cụ tạo payload cho **format string** khá hay. Bình thường mà tách byte ghi đè cho cái giá trị đại loại như này **15826374329413380831** chắc tới năm con hươu mới xong, chưa kể còn random giá trị mỗi lần chạy. Challenge rất đáng giải 🥇 !!!

Flag: **Wanna.One{do_cac_ban_tren_the_gian_nay_khoang_cach_nao_la_xa_nhat}**


