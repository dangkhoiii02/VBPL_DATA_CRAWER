# 🇻🇳 VBPL Data Extractor (M1/MLX Optimized)

Công cụ trích xuất dữ liệu tự động từ các Văn bản pháp luật Việt Nam (File `.docx`) sang định dạng `JSON` chuẩn hóa. Hệ thống sử dụng mô hình LLM chạy cục bộ (Local) được tối ưu hóa riêng cho chip **Apple Silicon (MacBook M1/M2/M3)** thông qua thư viện `mlx-lm`.

## ✨ Tính năng nổi bật (The "Bulletproof" Features)

Các văn bản pháp luật (VBPL) thực tế thường có định dạng cực kỳ lộn xộn (ký tự ẩn, bảng biểu phức tạp, phụ lục rác...). Tool này được trang bị 3 tầng phòng ngự để đảm bảo kết quả JSON luôn chính xác 100%:

1. **Bộ lọc "Vô Trùng" (Universal Pre-processor):**
   - Quét sạch mọi ký tự điều khiển ẩn (`\xa0`, `\t`, `\r`) do lỗi đánh máy.
   - Diệt tận gốc mọi dấu ngoặc kép (`"`, `“`, `”`) trong file Word gốc, ngăn chặn tuyệt đối lỗi gãy chuỗi JSON (JSON Decode Error).
   - Dùng Regex định dạng lại cấu trúc ẩn: Tự động ép các cụm từ quan trọng như *"Căn cứ..."*, *"Chương..."*, *"Điều..."* xuống dòng mới để AI dễ dàng nhận diện, không bị bỏ sót các Điều khoản sửa đổi (VD: Điều 10, Điều 17 bị kẹp giữa đoạn văn).
   - **Smart Crop:** Tự động tìm từ khóa `"Nơi nhận:"` để cắt bỏ hàng ngàn ký tự Phụ lục rác, giúp AI tập trung 100% (Attention) vào phần Chữ ký và Điều khoản.

2. **Kỷ Luật Thép Prompting (Strict Output Forcing):**
   - Ép AI xuất duy nhất mã JSON, cấm giải thích hay chào hỏi.
   - Sử dụng kỹ thuật *Mẫu Rỗng (Empty Skeleton)* để chống hiện tượng AI học vẹt (copy nguyên hướng dẫn vào kết quả).

3. **Bác sĩ Phẫu thuật JSON (Auto-Fixer):**
   - Tự động bắt lỗi các mô hình AI nhỏ (3B) khi chúng quên dấu phẩy `,` hoặc đóng nhầm mảng bằng dấu ngoặc nhọn `}`.
   - Ép kiểu dữ liệu (Type Forcing) bằng Python: Tự động gộp mảng `[]` thành chuỗi `" "` đối với các trường thông tin không được phép lặp lại (Cơ quan ban hành, Người ký...).

---

## ⚙️ Cài đặt Môi trường

Dự án yêu cầu Python 3.9+ và chỉ hoạt động tốt nhất trên hệ điều hành macOS (Apple Silicon).

**1. Cài đặt thư viện:**
Mở Terminal và chạy lệnh sau:
```bash
pip install --upgrade mlx-lm python-docx
2. Tải Model:
Lần chạy đầu tiên, tool sẽ tự động kéo model Qwen2.5-3B-Instruct-4bit từ HuggingFace về máy (Nặng khoảng ~2.5GB).

🚀 Hướng dẫn Sử dụng
Chỉ cần thay đổi đường dẫn file Word .docx đầu vào và tên file .json đầu ra ở cuối script test_model.py:

Python
if __name__ == "__main__":
    # Thay tên file đầu vào và đầu ra tại đây
    run_universal_extraction("THONG_TU_13_2025.docx", "ket_qua.json")
Chạy script trên Terminal:

Bash
python3 test_model.py
📄 Cấu trúc JSON Đầu Ra (Output Schema)
Hệ thống cam kết trả về đúng một định dạng JSON duy nhất cho mọi loại VBPL (Luật, Nghị định, Thông tư, Quyết định...).

Lưu ý: Mẫu dưới đây là chế độ chỉ lấy Tên Điều (Không lấy nội dung chi tiết).

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
    {
      "ten_dieu": "Điều 1. Sửa đổi, bổ sung một số điều..."
    },
    {
      "ten_dieu": "Điều 2. Điều khoản thi hành"
    }
  ],
  "metadata_he_thong": {
    "ngay_crawl": "2026-03-16T20:45:12.123456",
    "source_url": ""
  }
}
🐛 Gỡ lỗi (Troubleshooting)
Trong trường hợp file Word có cấu trúc quá "dị" khiến AI không thể sinh ra cú pháp JSON hợp lệ, script sẽ tự động ném ra cảnh báo ❌ Lỗi cú pháp JSON hoặc ❌ Không tìm thấy cấu trúc JSON.

Đừng lo lắng! Toàn bộ câu trả lời thô (nguyên văn đoạn text lỗi) của AI đã được tự động lưu vào file raw_debug.txt. Bạn chỉ cần mở file này ra, xem vị trí lỗi cú pháp ở đâu và điều chỉnh lại Regex trong hàm read_legal_docx_universal là xong.
