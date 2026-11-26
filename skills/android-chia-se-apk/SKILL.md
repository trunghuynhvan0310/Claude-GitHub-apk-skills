---
name: android-chia-se-apk
description: Giai đoạn 2 của app Android: chuẩn bị chia sẻ APK công khai (LICENSE, chính sách riêng tư, README, CHANGELOG, Release, onboarding, cài đặt, giới thiệu). Nạp khi người dùng đồng ý chuyển sang giai đoạn 2.
---

# Giai đoạn 2 – Chia sẻ APK

Chỉ dùng sau khi người dùng đã đồng ý chuyển sang giai đoạn 2 (xem `android-quy-trinh`). Làm phần bổ sung MỘT lần theo thứ tự dưới đây, báo ngắn sau mỗi nhóm, không hỏi từng bước trừ mục A. Xong thì sửa `PROJECT_NOTES.md` thành `Giai đoạn: 2`.

## A. Hỏi gộp một lần
Loại giấy phép (gợi ý MIT, Apache-2.0, GPL-3.0; cảnh báo nếu có thư viện GPL ràng buộc), tên và năm của tác giả, kênh liên hệ báo lỗi, app có dùng mạng hoặc thu thập dữ liệu không.

## B. Thành phần repo
- `LICENSE` đúng loại, đúng tên và năm.
- `PRIVACY.md` viết thật theo quyền và dữ liệu app thực sự dùng (không dùng mẫu chung chung; app không thu thập dữ liệu thì nói rõ).
- `THIRD_PARTY_NOTICES.md`: lấy từ danh sách thư viện trong Gradle; kèm nguồn và giấy phép của font, biểu tượng, âm thanh, ảnh.
- `CHANGELOG.md` chuẩn Keep a Changelog (Added / Changed / Fixed / Removed), viết theo tác động với người dùng; dựng lại lịch sử gần đây từ `PROJECT_NOTES.md` và tag nếu có.
- README đầy đủ: giới thiệu, ảnh chụp màn hình (chừa chỗ nếu chưa có), tính năng, yêu cầu Android, cách cài APK (bật cài từ nguồn không rõ), cách build, liên kết LICENSE/PRIVACY/CHANGELOG, mục báo lỗi.
- `.github/dependabot.yml` (Gradle và Actions).

## C. Workflow phát hành
Khi push tag `v*`: tạo GitHub Release kèm APK đã ký, SHA-256 của APK, ghi chú lấy từ CHANGELOG, kích thước APK. Chạy lint. Tag và Release trùng với `VERSION_NAME`.

## D. Trong app (đề xuất theo thứ tự, người dùng được bỏ mục nào cũng được)
1. Onboarding 3–4 trang, có nút Bỏ qua, chỉ hiện lần đầu.
2. Thiết lập ban đầu: giao diện sáng/tối/theo hệ thống, ngôn ngữ, các tùy chọn chính của app. Xin quyền đúng lúc cần, kèm lý do.
3. Màn Cài đặt (lưu bằng DataStore).
4. Màn Giới thiệu: tên, phiên bản (đọc từ BuildConfig, không ghi cứng), giấy phép, liên kết mã nguồn, chính sách riêng tư, liên hệ.
5. Màn Hướng dẫn sử dụng và hộp thoại «Có gì mới» hiện một lần sau mỗi lần cập nhật.
6. Trạng thái rỗng và thông báo lỗi thân thiện; biểu tượng thích ứng; SplashScreen API; edge-to-edge; chế độ tối; mọi chữ nằm trong `strings.xml`.

## E. Độ ổn định
- Bản test cài song song bản chính: `applicationIdSuffix` là '.debug' cho biến thể debug.
- Kiểm tra cài đè từ bản cũ lên bản mới giữ được dữ liệu; đổi schema Room phải có migration; nếu bật R8 thì kiểm tra keep rules.
- Nút «Gửi nhật ký lỗi» (log nội bộ, che dữ liệu nhạy cảm) để người dùng gửi khi báo lỗi.
- Quyền tối thiểu; quyết định `allowBackup` có chủ đích; không log dữ liệu nhạy cảm.
- Unit test cho logic quan trọng, chạy cùng lint trong CI (không dùng emulator).
- Tùy chọn: nút «Kiểm tra bản mới» qua GitHub Releases API (cần quyền INTERNET, nhớ cập nhật `PRIVACY.md`).

## Từ đây, mỗi lần ra bản mới (cộng thêm vào checklist lõi)
Cập nhật CHANGELOG, nội dung «Có gì mới», hướng dẫn trong app nếu chức năng đổi, `THIRD_PARTY_NOTICES.md` và `PRIVACY.md` nếu đổi thư viện hoặc quyền; gắn tag và tạo Release.

## Gợi ý lên giai đoạn 3
Chỉ hỏi khi người dùng nói muốn đăng Google Play, hoặc app đã có nhiều người dùng và ổn định lâu. Nêu rõ giai đoạn 3 tốn nhiều công sức và có thủ tục riêng.
