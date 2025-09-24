#gp-auto-disconnect
1. Mô tả
Đây là một kịch bản Python được thiết kế để tự động quản lý các phiên kết nối người dùng trên Palo Alto Networks Firewall thông qua GlobalProtect.

Mục tiêu chính của kịch bản là đảm bảo mỗi người dùng chỉ có một phiên kết nối duy nhất. Khi phát hiện một người dùng đăng nhập từ nhiều thiết bị, kịch bản sẽ tự động giữ lại phiên đăng nhập cũ nhất và ngắt kết nối tất cả các phiên mới hơn. Toàn bộ hoạt động sẽ được ghi lại vào một file log riêng cho mỗi ngày để tiện cho việc theo dõi và kiểm tra.

2. Tính năng chính
Bảo mật: Sử dụng phương thức POST để lấy API key, giúp che giấu thông tin đăng nhập khỏi URL.

Thông minh: Tự động xác định các phiên đăng nhập trùng lặp của cùng một người dùng.

Công bằng: Dựa vào thời gian đăng nhập (login-time) để xác định phiên cũ nhất cần giữ lại và ngắt các phiên mới hơn.

Tự động: Có thể được lên lịch (ví dụ: qua crontab trên Linux hoặc Task Scheduler trên Windows) để chạy định kỳ mỗi phút.

Ghi log chi tiết: Mọi hành động, từ việc lấy API key, phát hiện người dùng, cho đến khi ngắt kết nối thành công hoặc thất bại, đều được ghi lại.

Quản lý log hiệu quả: Tự động tạo một file log mới cho mỗi ngày với tên chứa ngày tháng (ví dụ: gp_disconnect_log_2025-09-25.txt), giúp việc lưu trữ và tìm kiếm trở nên dễ dàng.

3. Yêu cầu
Python 3.6+

Thư viện requests của Python.

4. Cài đặt
Mở terminal hoặc command prompt và chạy lệnh sau để cài đặt thư viện cần thiết:

pip install requests

5. Cấu hình
Trước khi chạy, bạn cần mở file kịch bản và chỉnh sửa các thông số trong khối if __name__ == "__main__": cho phù hợp với môi trường của bạn:

# --- THAY ĐỔI CÁC THÔNG SỐ NÀY ---
FIREWALL_IP = "192.168.1.1"  # Thay bằng IP của firewall
USERNAME    = "admin"            # Thay bằng username có quyền API
PASSWORD    = "your_password"      # Thay bằng mật khẩu
GP_GATEWAY  = "GW-VPN-Name"    # Thay bằng tên GlobalProtect Gateway của bạn
# ------------------------------------

FIREWALL_IP: Địa chỉ IP của Firewall Palo Alto.

USERNAME: Tên tài khoản quản trị có quyền truy cập API.

PASSWORD: Mật khẩu của tài khoản trên.

GP_GATEWAY: Tên của GlobalProtect Gateway mà bạn muốn kiểm tra.

6. Sử dụng
Để chạy kịch bản, chỉ cần mở terminal hoặc command prompt, điều hướng đến thư mục chứa file và thực thi lệnh:

python your_script_name.py

(Trong đó your_script_name.py là tên bạn đã lưu cho file kịch bản).

7. Logging
Kịch bản sẽ tạo ra một file log trong cùng thư mục. Ví dụ, nếu bạn chạy kịch bản vào ngày 25 tháng 9 năm 2025, một file tên là gp_disconnect_log_2025-09-25.txt sẽ được tạo. File này chứa toàn bộ lịch sử hoạt động trong ngày hôm đó.

Ví dụ nội dung file log:

2025-09-25 15:30:01 - INFO - --- Bắt đầu kịch bản trên Firewall 192.168.1.1 ---
2025-09-25 15:30:02 - INFO - Lấy API Key thành công!
2025-09-25 15:30:03 - INFO - Lấy danh sách người dùng từ gateway 'GW-VPN-Name' thành công.
2025-09-25 15:30:03 - INFO - Bắt đầu kiểm tra và xử lý các phiên đăng nhập trùng lặp...
2025-09-25 15:30:03 - WARNING - Phát hiện user 'test.user' đăng nhập từ 2 máy. Sẽ chỉ giữ lại phiên cũ nhất.
2025-09-25 15:30:03 - INFO - Giữ lại phiên của user 'test.user': Máy 'DESKTOP-ABC' (đăng nhập lúc 2025-07-07 14:00:00)
2025-09-25 15:30:03 - INFO - Ngắt kết nối phiên của user 'test.user': Máy 'LAPTOP-XYZ' (đăng nhập lúc 2025-07-07 15:25:00)
2025-09-25 15:30:04 - INFO - Đã gửi yêu cầu ngắt kết nối cho user 'test.user' trên máy 'LAPTOP-XYZ'.
2025-09-25 15:30:04 - INFO - --- Kịch bản hoàn tất. ---

8. Lưu ý quan trọng
Quyền truy cập API: Hãy chắc chắn rằng tài khoản USERNAME đã được cấp quyền truy cập API trên Firewall.

SSL Certificate: Kịch bản này tắt cảnh báo về SSL certificate không an toàn (verify=False). Điều này là phổ biến trong môi trường nội bộ nhưng hãy đảm bảo bạn hiểu rõ về rủi ro bảo mật liên quan.
