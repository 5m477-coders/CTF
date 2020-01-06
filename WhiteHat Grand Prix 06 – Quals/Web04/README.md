# WEB 04 - VIETNAMESE FOOD Challenge \
Challenge: http://15.165.89.5/ \
Backup: http://15.165.89.5:4567, http://15.165.89.5:4568, http://15.165.89.5:4569 \
*Docker images available here: https://hub.docker.com/repository/docker/haonh/wh/* 
 
   
# ================================== 
  
  
* Go to website of challenge we will see: 
<img website>
Chúng ta có thể kiểm tra nhanh website được viết bằng nodejs với framework expreess
Website web have menu home, service, Contact and login. We check all menu and see login or contact may be vuln.
* We view source code website and find interesting is like user of website is test/test Use user we got to login website and get two information *You don’t have permission* and token of users test like base 64
* Từ đây ta có thể đoán được user test mà ta tìm thấy không phải là user có quyền admin. Do đó chúng ta cần tìm user có quyền admin để giải quyết challenge này. Có rất nhiều đội chơi đã cố gắng chiếm quyền Admin qua việc khai thác lỗi XSS ở form contact. Tất nhiên sẽ không khai thác được vì website không có lỗi xss. Quay lại đầu vào ban đầu ở chức năng login dựa vào căn cứ website được viết bằng nodejs do đó sẽ có rất nhiều khả năng dùng csdl mongodb. Do đó ta sẽ test lỗi nosql injection ở chức năng đăng nhập. Ta dùng payload : username=test&password[$ne]= check bypass login. Các bạn có thể tìm hiểu thêm tại đây https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection#tools
<img burpsuite bypass>
Payload đã hoạt động và ta đã bypass login được qua lỗi sqlinjection. Từ đó có thể khắng định chắc chắn website bị dính lỗi sql injection ở chức năng login. Mở ra hướng giải quyết challenge là sử dụng lỗi nosql injection để dump dữ liệu tất cả các user và tìm user nào có quyền admin.
Chúng ta có thể dùng các payload với từ khóa regex để dump dữ liệu các user. 
* Sau khi dump toàn bộ user và login kiểm tra quyền admin ta thấy user có quyền admin có tên là *itachi*. Dùng user này login vào website qua việc bypass đăng nhập ta thấy mở thêm chức năng checktoken.
Quay lại với chuỗi token mỗi user được cung cấp. Đó là các chuỗi base64 sử dụng tools decode ra token 1 user ta có thông tin:

Là chuỗi json chứa thông tin phiên làm việc của người dùng. Chuỗi này có vẻ giống được dump ra từ session của người dùng. 
* Chức năng check token lấy đầu vào là chuỗi base64 và in ra thông tin người dùng. Thử sửa lại thông tin người dùng và upload lên thì ta thấy website đang nhận thông tin từ người dùng để hiện thị ra.
Ta có thể hình dung luồng xử lý ở phía server: Base64 token => Decodebase64 => Convert object to json => Tra ve cho nguoi dung

Chuỗi token được dump từ session của người dùng và sau đó được convert ngược lại. Trong nodejs có hàm serialize và unserialize hỗ trợ làm việc này. Do đó ta check khai thac lỗi qua lỗi serialize. Tham khảo bài viết 
Ta tiến hành khai thác revershell về máy chủ và lấy flag
File exploit.


  
