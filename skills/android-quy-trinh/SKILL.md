---
name: android-quy-trinh
description: Dùng khi viết, sửa hoặc build app Android trong repo GitHub qua connector. Quản lý giai đoạn (cá nhân, chia sẻ APK, Google Play), ghi nhớ dự án, checklist gọn mỗi lượt.
---

# Quy trình app Android qua GitHub (lõi, giữ gọn)

Trả lời tiếng Việt. Claude không chạy được app: chỉ được nói «build xanh», không được nói «app chạy đúng». Người dùng là người test.

## Giai đoạn
1 Cá nhân (mặc định) → 2 Chia sẻ APK → 3 Google Play. Giai đoạn sau gồm cả giai đoạn trước.
- Giai đoạn 2 và 3 có skill riêng (`android-chia-se-apk`, `android-google-play`). CHỈ nạp skill của giai đoạn vừa được đồng ý, không nạp trước.
- Giai đoạn hiện tại nằm ở dòng đầu `PROJECT_NOTES.md`: `Giai đoạn: 1`. Phiên chat mới: đọc file này trước (chỉ file này), không quét cả repo.
- Người dùng quyết định chuyển giai đoạn. Chỉ HỎI khi có tín hiệu: người dùng nói muốn chia sẻ hoặc đăng, hoặc app ổn định qua vài bản liên tiếp không lỗi lớn. Hỏi một câu ngắn, kèm 2 dòng: giai đoạn sau thêm gì, tốn thêm gì. Nếu từ chối thì không hỏi lại trong phiên đó. Nếu đồng ý: nạp skill giai đoạn đó, làm phần bổ sung một lần, rồi sửa dòng `Giai đoạn:`.

## Giai đoạn 1 – Cá nhân (làm cùng lúc với bước tạo repo)
1. `PROJECT_NOTES.md` (dưới ~60 dòng), tạo từ `PROJECT_NOTES.template.md` cạnh file này nếu có: giai đoạn, kế hoạch đã chốt, quyết định kỹ thuật (minSdk, thư viện chính), chức năng đã thử rồi bỏ kèm lý do, việc còn dở. Cập nhật từng dòng khi cần, không viết lại cả file.
2. `.gitignore` (keystore, local.properties, build/, .gradle/).
3. README ngắn: app làm gì, cách tải và cài APK, phiên bản hiện tại.
4. Phiên bản có MỘT nguồn (`version.properties` hoặc `gradle.properties`): `VERSION_NAME` dạng 1.x.x (sửa lỗi tăng số cuối, thêm chức năng tăng số giữa), `VERSION_CODE` luôn tăng. APK tên `TenApp-v1.x.x.apk`. Không dùng «build + số» thay cho phiên bản.
5. Workflow build: chạy khi push main và `workflow_dispatch`; có `permissions: contents: write`, cache Gradle; build APK, upload artifact (và Release dạng prerelease nếu tiện).
6. Khóa ký cố định, làm MỘT lần ngay từ bản đầu: runner GitHub là máy mới mỗi lần nên khóa debug tự sinh thường khác nhau, khiến không cài đè được bản mới lên bản cũ. Hướng dẫn người dùng tự tạo keystore bằng `keytool` trên máy họ, mã hóa base64, thêm 4 secret `KEYSTORE_BASE64`, `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`, và sao lưu keystore (mất khóa là không cập nhật được app đã cài). Tuyệt đối không xin mật khẩu hay keystore trong chat, không commit chúng. Workflow dùng khóa này nếu có đủ secret; thiếu thì build debug và ghi cảnh báo rõ.
7. Tra web phiên bản mới nhất (compileSdk/targetSdk, AGP, Gradle, Kotlin, JDK, Compose BOM, các GitHub Actions, thư viện dự định dùng) và kiểm tra tương thích: khi bắt đầu dự án và khi thêm thư viện mới. Không tra lại mỗi lượt. Nếu connector không đẩy được `gradle-wrapper.jar`, dùng `gradle/actions/setup-gradle` với `gradle-version` cụ thể và gọi `gradle`, ghi rõ trong README.

## Mỗi lượt sửa hoặc ra APK (tiết kiệm token: chỉ làm mục liên quan)
- Luôn: tăng phiên bản; đọc kết quả workflow (không đoán) và xác nhận APK có thật với đúng tên phiên bản; nếu đỏ thì đọc log, sửa, chạy lại.
- Nếu đổi chức năng hoặc cách dùng: cập nhật README và dòng liên quan trong `PROJECT_NOTES.md`.
- Nếu bỏ một chức năng: gỡ sạch phần đó (code, resource, chuỗi, quyền Manifest, thư viện Gradle, mục tài liệu).
- Sửa file bằng thay đổi cục bộ, không ghi đè cả file lớn; không đọc lại file không liên quan.
- Cùng một lỗi sửa 2–3 lần chưa được: dừng, nêu nguyên nhân nghi ngờ, và hỏi bước tái hiện, ảnh hoặc log, mẫu máy và phiên bản Android trước khi sửa tiếp.
- Báo cáo cuối tối đa ~6 dòng: phiên bản, link APK, thay đổi chính, việc người dùng cần làm, điều chưa kiểm chứng được.

## Thử chức năng mới
Làm trên nhánh `feat/ten-chuc-nang`, build prerelease để test. Ổn thì gộp vào main, hỏng thì xóa nhánh để main luôn sạch.

## Dọn code
Không tự rà toàn bộ mỗi lượt. Sau khoảng 5 lần sửa, hoặc trước khi chuyển giai đoạn, ĐỀ XUẤT rà soát (code chết, import/resource/thư viện/quyền thừa, tác vụ nặng trên luồng chính, tái tổ hợp Compose thừa) và chỉ làm khi người dùng đồng ý.

## Repo đã có sẵn
Khi mở một repo đã có, chỉ liệt kê thư mục gốc một lần, đối chiếu với giai đoạn hiện tại, báo phần còn thiếu theo thứ tự ưu tiên: khóa ký, phiên bản, README, rồi các mục giai đoạn sau nếu người dùng muốn.
