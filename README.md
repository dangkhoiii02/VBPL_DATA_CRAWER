# 🇻🇳 VBPL Data Extractor (M1/MLX Optimized)

Công cụ trích xuất dữ liệu tự động từ các **Văn bản pháp luật Việt Nam** (`.docx`) sang định dạng `JSON` chuẩn hóa. Hệ thống được tối ưu hóa đặc biệt cho dòng chip **Apple Silicon (M1/M2/M3)**, sử dụng mô hình ngôn ngữ lớn (LLM) chạy cục bộ thông qua thư viện `mlx-lm`.

## ✨ Tính năng nổi bật (The "Bulletproof" Features)

Tool được thiết kế để xử lý các đặc thù "khó chịu" của văn bản pháp luật thực tế tại Việt Nam:

1. **Bộ lọc "Vô Trùng" (Universal Pre-processor):**
   - Quét sạch các ký tự điều khiển ẩn (`\xa0`, `\t`, `\r`) thường gặp trong file Word pháp quy.
   - Diệt tận gốc mọi dấu ngoặc kép (`"`, `“`, `”`) gây gãy cấu trúc JSON.
   - **Regex Formatting:** Tự động ép các dòng *"Căn cứ..."*, *"Chương..."*, và các Điều khoản sửa đổi xuống dòng mới để AI không bỏ sót thông tin.

2. **Kỷ Luật Thép Prompting (Strict Output Forcing):**
   - **No Content Mode:** AI được lệnh chỉ trích xuất Số Điều và Tiêu đề (VD: "Điều 4. Điều khoản thi hành"), bỏ qua toàn bộ nội dung chi tiết để tối ưu tốc độ và dung lượng.
   - **Skeleton Prompting:** Sử dụng khung JSON rỗng làm khuôn mẫu, ép AI điền thông tin thay vì để nó tự sáng tạo key lạ.

3. **Bác sĩ Phẫu thuật JSON (Auto-Fixer):**
   - Tự động sửa lỗi cú pháp JSON do AI sinh ra (thiếu dấu phẩy, đóng nhầm ngoặc nhọn cho mảng).
   - **Type Forcing:** Ép các trường *Người ký, Chức danh, Cơ quan ban hành* về dạng chuỗi (String) duy nhất ngay cả khi AI trả về mảng (Array).

---

## ⚙️ Cài đặt & Môi trường

Dự án yêu cầu **Python 3.9+** và hoạt động tốt nhất trên **macOS (Apple Silicon)**.

### 1. Cài đặt thư viện:
```bash
pip install --upgrade mlx-lm python-docx

**### 2. Tải Model:**
Lần chạy đầu tiên, công cụ sẽ tự động tải model Qwen2.5-3B-Instruct-4bit từ HuggingFace (~2.5GB).
🚀 Hướng dẫn Sử dụng
Mở file test_model_universal.py và cập nhật đường dẫn file tại khối main:
if __name__ == "__main__":
    # Thay tên file Word đầu vào và file JSON đầu ra tương ứng
    run_universal_extraction("VAN_BAN_PHAP_LUAT.docx", "output_data.json")

📄 Cấu trúc JSON Đầu Ra (Output Schema)
{
  "thuoc_tinh": {
    "so_ky_hieu": "13/2025/TTLT-BTP-BNG-TANDTC",
    "ngay_ban_hanh": "29/08/2025",
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
    { "ten_dieu": "Điều 2" },
    { "ten_dieu": "Điều 4. Điều khoản thi hành" }
  ],
  "metadata_he_thong": {
    "ngay_crawl": "2026-03-16T21:00:00.000000",
    "source_url": ""
  }
}
🐛 Gỡ lỗi (Troubleshooting)
Lỗi hệ thống (Không tìm thấy JSON): Kiểm tra file raw_debug.txt. Thường do văn bản quá dài hoặc AI phản hồi bằng văn bản thường thay vì JSON.

Lỗi cú pháp JSON: Tool sẽ in ra đoạn text gây lỗi quanh vị trí lỗi (Char X). Bạn có thể dùng thông tin này để tinh chỉnh lại Regex trong hàm read_legal_docx_universal.
