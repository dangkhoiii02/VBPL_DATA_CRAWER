🇻🇳 VBPL Data Extractor (M1/MLX Optimized)
Công cụ trích xuất dữ liệu tự động từ các Văn bản pháp luật Việt Nam (.docx) sang định dạng JSON chuẩn hóa. Hệ thống được tối ưu hóa đặc biệt cho dòng chip Apple Silicon (M1/M2/M3), sử dụng mô hình ngôn ngữ lớn (LLM) chạy cục bộ để đảm bảo tốc độ và quyền riêng tư dữ liệu.

🛡️ "3 Tầng Phòng Ngự" - Chống lỗi AI tuyệt đối
VBPL Việt Nam thường có định dạng lộn xộn, gây khó khăn cho AI. Tool này được trang bị 3 lớp xử lý để đảm bảo dữ liệu đầu ra không bao giờ bị "gãy":

1. Tiền xử lý văn bản (Universal Pre-processor)
Dọn rác ký tự: Loại bỏ hoàn toàn các ký tự điều khiển ẩn (\xa0, \t, \r) và diệt tận gốc mọi dấu ngoặc kép (", “, ”) gây lỗi parse JSON.

Regex Formatting: Tự động nhận diện cấu trúc ẩn, ép các dòng "Căn cứ...", "Chương...", và các Điều khoản sửa đổi (Điều 10, Điều 17...) xuống dòng mới để AI không bỏ sót.

Smart Crop: Tự động cắt bỏ phần phụ lục rác sau mục "Nơi nhận:", giúp AI tập trung tối đa vào phần Chữ ký và các Điều khoản chính.

2. Kỷ luật thép Prompting (Strict Output Forcing)
Skeleton Prompting: Sử dụng khung JSON rỗng làm mẫu, ép AI điền thông tin thay vì để nó tự sáng tạo cấu trúc.

Constraint Logic: Quy định rõ kiểu dữ liệu (String cho thuộc tính, Array cho căn cứ) để tránh việc AI nhầm lẫn giữa các trường thông tin.

3. Hậu xử lý & Auto-Fix (Python Surgery)
Syntax Repair: Tự động sửa các lỗi cú pháp JSON kinh điển của các Model nhỏ (3B) như thiếu dấu phẩy , hoặc đóng nhầm ngoặc nhọn } cho mảng.

Flattening Metadata: Nếu AI vô tình tạo mảng cho các trường như Người ký hay Chức danh, Python sẽ tự động gộp (flatten) chúng thành chuỗi văn bản duy nhất để đúng chuẩn database.

⚙️ Cài đặt & Môi trường
Yêu cầu: macOS (Chip M-Series), Python 3.9+.

Thư viện: mlx-lm, python-docx.

Cài đặt nhanh:
Bash
pip install --upgrade mlx-lm python-docx
Ghi chú: Lần chạy đầu tiên sẽ tự động tải model Qwen2.5-3B-Instruct (khoảng 2.5GB) về máy.

🚀 Hướng dẫn Sử dụng
Mở file test_model_universal.py và cập nhật đường dẫn file ở khối main:

Python
if __name__ == "__main__":
    # Thay tên file Word đầu vào và file JSON đầu ra
    run_universal_extraction("THONG_TU_LIEN_TICH_13.docx", "result_final.json")
Chạy lệnh trên Terminal:

Bash
python3 test_model_universal.py
📄 Cấu trúc JSON Chuẩn (Output Schema)
Hệ thống trả về định dạng đồng nhất cho mọi loại văn bản (Thông tư liên tịch, Nghị định, Luật...). Ở chế độ mặc định, tool chỉ trích xuất Tên Điều để tối ưu hóa tốc độ và dung lượng.

JSON
{
  "thuoc_tinh": {
    "so_ky_hieu": "13/2025/TTLT-BTP-BNG-TANDTC",
    "ngay_ban_hanh": "29 tháng 8 năm 2025",
    "loai_van_ban": "Thông tư liên tịch",
    "co_quan_ban_hanh": "Bộ Tư pháp, Bộ Ngoại giao, Tòa án nhân dân tối cao",
    "dia_diem_ban_hanh": "Hà Nội",
    "chuc_danh": "Phó Chánh án, Thứ trưởng, Thứ trưởng",
    "nguoi_ky": "Phạm Quốc Hưng, Lê Thị Thu Hằng, Nguyễn Thanh Tịnh"
  },
  "can_cu": [
    "Căn cứ Luật Ban hành văn bản quy phạm pháp luật...",
    "Căn cứ Luật Tương trợ tư pháp..."
  ],
  "chuong": [],
  "dieu": [
    { "ten_dieu": "Điều 1" },
    { "ten_dieu": "Điều 10. Thẩm quyền yêu cầu..." },
    { "ten_dieu": "Điều 17. Thẩm quyền thực hiện..." },
    { "ten_dieu": "Điều 4. Điều khoản thi hành" }
  ],
  "metadata_he_thong": {
    "ngay_crawl": "2026-03-16T20:45:12.123456",
    "source_url": ""
  }
}
🐛 Troubleshooting
Lỗi cú pháp JSON: Xảy ra khi văn bản quá dị biệt. Kiểm tra file raw_debug.txt để xem phản hồi thô của AI.

Thiếu thông tin người ký: Kiểm tra xem file Word có phần "Nơi nhận:" không. Nếu không có, hãy tăng giới hạn cắt chuỗi trong hàm read_legal_docx_universal.
