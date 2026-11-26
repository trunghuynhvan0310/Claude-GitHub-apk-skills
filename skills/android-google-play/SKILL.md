---
name: android-google-play
description: Giai đoạn 3: đưa app Android lên Google Play (AAB, khóa ký, targetSdk, Data safety, kiểm thử kín, hồ sơ cửa hàng). Nạp khi người dùng đồng ý chuyển sang giai đoạn 3.
---

# Giai đoạn 3 – Google Play

Chỉ dùng sau khi người dùng đã đồng ý chuyển sang giai đoạn 3 và app đã hoàn tất giai đoạn 2. Nhắc trước một lần: giai đoạn này tốn nhiều công sức, có thủ tục và có thể có chi phí; xin xác nhận lại rồi mới làm.

## Quy tắc đầu tiên: tra nguồn chính thức
Chính sách Google Play thay đổi thường xuyên. TRƯỚC khi liệt kê yêu cầu, tra web các trang chính thức (Play Console Help, developer.android.com) cho: phí đăng ký và xác minh nhà phát triển, yêu cầu targetSdk hiện hành, yêu cầu kiểm thử kín (số người và số ngày tối thiểu với tài khoản cá nhân mới, nếu còn áp dụng), mẫu Data safety, chính sách nội dung. Không dùng trí nhớ cho các con số này. Báo kết quả cho người dùng kèm nguồn.

## Việc Claude làm (trong repo)
1. Chốt `applicationId` cuối cùng (không đổi được sau khi đăng), tên hiển thị, minSdk và targetSdk theo yêu cầu vừa tra.
2. Khóa ký: dùng Play App Signing và AAB. Cân nhắc dùng lại keystore hiện có để người dùng đang cài APK vẫn cập nhật liền mạch; quyết định này làm một lần, kiểm tra hướng dẫn mới nhất của Play Console trước khi khuyên. Vẫn không xin mật khẩu hay keystore trong chat.
3. Workflow: thêm `bundleRelease` tạo AAB ký bằng khóa upload, đính vào Release/artifact. Ban đầu người dùng tự tải AAB lên Console; chỉ làm tự động đăng bằng Play Developer API (service account) khi người dùng yêu cầu, để tiết kiệm công sức.
4. Soạn nội dung hồ sơ cửa hàng để người dùng sao chép: tên, mô tả ngắn và dài (tiếng Việt, thêm tiếng Anh nếu muốn), danh sách ảnh chụp cần có, biểu tượng 512 px, ảnh nổi bật, phân loại nội dung, khai báo Data safety khớp với `PRIVACY.md`, URL chính sách riêng tư công khai (ví dụ GitHub Pages), thông tin liên hệ.
5. Rà kỹ: quyền thừa, thư viện thu thập dữ liệu, nội dung có bản quyền, biểu tượng và ảnh.

## Việc người dùng phải tự làm (Claude không làm được)
Tạo tài khoản nhà phát triển, thanh toán, xác minh danh tính, điền và gửi trên Play Console, tìm người thử nghiệm kín nếu yêu cầu. Đưa cho họ một checklist ngắn theo đúng thứ tự.

## Lộ trình phát hành
Thử nghiệm nội bộ, rồi thử nghiệm kín (theo yêu cầu đã tra), rồi sản phẩm chính thức với tỷ lệ triển khai tăng dần.

## Sau khi đăng
Mỗi bản mới: tăng `versionCode`, ghi chú phát hành theo ngôn ngữ, theo dõi sự cố và ANR trong Play Console, cập nhật targetSdk theo yêu cầu hằng năm, giữ `PRIVACY.md` và Data safety đồng bộ. Sửa `PROJECT_NOTES.md` thành `Giai đoạn: 3`.
