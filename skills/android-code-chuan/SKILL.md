---
name: android-code-chuan
description: Dùng khi viết hoặc sửa code Android (Kotlin, Compose hoặc XML, Gradle, Manifest). Quy tắc tự rà trước khi push, đọc log build gọn, bẫy API Android, kiến trúc gọn.
---

# Code Android chuẩn (ngắn gọn, dùng chung mọi giai đoạn)

Mặc định Kotlin + Jetpack Compose + Material 3. Nếu `PROJECT_NOTES.md` ghi giao diện XML thì theo mục 6. Ưu tiên đơn giản: app cá nhân không cần Hilt, đa module hay lớp trừu tượng thừa; thêm khi thật sự cần.

## 1. Tự rà trước khi commit (Claude không build được cục bộ)
Rà diff một lượt như trình biên dịch:
- Import đủ; tên hàm và tham số khớp ĐÚNG phiên bản thư viện đã tra. Không đoán API từ trí nhớ; nghi ngờ thì tra tài liệu chính thức.
- Mọi string, drawable, id, style được tham chiếu đều đã tạo; không còn tham chiếu tới thứ đã xóa.
- Thêm Activity, Service, Receiver, Provider hoặc quyền thì khai báo trong Manifest; component có intent-filter phải ghi rõ `android:exported`.
- Null-safety: tránh `!!`; kiểu và dấu ngoặc khớp; `when` đủ nhánh.
- Gradle: thêm thư viện thì khai báo trong version catalog (`libs.versions.toml`) nếu dự án dùng; bật R8 thì thêm keep rules cho reflection và serialization.
- Không còn code chết, TODO rác, log gỡ lỗi.

## 2. Workflow in tóm tắt lỗi và đọc log gọn
Build chạy với `set -o pipefail` và `--console=plain`, ghi ra `build.log` (`... 2>&1 | tee build.log`). Thêm một bước `if: failure()` lọc các dòng lỗi (`grep -E '^e: |error:|FAILED|What went wrong' -A2 build.log | head -40`) và ghi vào `$GITHUB_STEP_SUMMARY`. Khi workflow đỏ, Claude chỉ đọc phần tóm tắt hoặc các dòng lỗi này, không đọc cả log; chỉ mở thêm log khi tóm tắt chưa đủ. Cùng một lỗi 2–3 lần chưa sửa được thì làm theo quy tắc dừng của `android-quy-trinh`.

## 3. Bẫy API Android hay gặp (tra tài liệu chính thức nếu không chắc)
- Quyền runtime: thông báo từ Android 13 cần xin `POST_NOTIFICATIONS`; xin đúng lúc cần và xử lý cả khi bị từ chối. Truy cập tệp dùng Storage Access Framework hoặc photo picker, hạn chế quyền bộ nhớ rộng.
- Chạy nền: foreground service phải khai báo loại (từ targetSdk 34); báo thức chính xác, tối ưu pin và giới hạn nền có quy tắc riêng; việc trì hoãn dùng WorkManager.
- Rào chắn `Build.VERSION.SDK_INT` khi gọi API mới hơn minSdk; không dùng API đã deprecated khi đã có thay thế (ví dụ `registerForActivityResult`, `OnBackPressedCallback`).
- Edge-to-edge bắt buộc khi targetSdk 35: xử lý window insets ở mọi màn hình.
- Chuỗi hiển thị nằm trong `strings.xml` (có `plurals` khi cần); định dạng ngày giờ và số theo locale.

## 4. Kiến trúc gọn
- Cấu trúc theo tính năng; ViewModel giữ trạng thái giao diện bằng một `UiState` (sealed hoặc data class) qua `StateFlow`; repository mỏng; không giữ Context của Activity trong ViewModel.
- Coroutines: dùng `viewModelScope`, không dùng `GlobalScope`; việc nặng và IO chạy ngoài luồng chính (`Dispatchers.IO`); thu thập flow theo vòng đời.
- Lưu trữ: cài đặt dùng DataStore; dữ liệu dùng Room với `exportSchema = true` và migration khi đổi schema (không dùng `fallbackToDestructiveMigration` khi người dùng đã có dữ liệu thật).
- Lỗi: không nuốt lỗi im lặng; hiển thị thông báo thân thiện, ghi log nội bộ; bản release không log dữ liệu nhạy cảm.
- Bảo mật: không hardcode khóa API hay mật khẩu; dùng HTTPS; dữ liệu nhạy cảm không lưu thô; WebView bật tính năng tối thiểu.
- Thư viện: ưu tiên AndroidX/Jetpack chính thức, tránh thư viện bị bỏ rơi; mỗi thư viện thêm phải có lý do và được ghi vào `THIRD_PARTY_NOTICES.md` từ giai đoạn 2.

## 5. Nếu dùng Compose
Hoist state; `remember` và `rememberSaveable` đúng chỗ; `collectAsStateWithLifecycle()`; `key` ổn định trong `LazyColumn`/`LazyRow`; `derivedStateOf` cho giá trị suy ra; không tạo đối tượng mới mỗi lần recompose; composable nhỏ, có Preview khi hữu ích; Navigation Compose cho điều hướng; theme Material 3 hỗ trợ sáng và tối.

## 6. Nếu dùng XML
ViewBinding (đặt `binding = null` trong `onDestroyView`); `RecyclerView` với `ListAdapter` và `DiffUtil`; thu thập flow bằng `repeatOnLifecycle(STARTED)` trên `viewLifecycleOwner`; Navigation component; Material Components; không rải `findViewById` hay dùng Kotlin synthetics.

## 7. Quy ước
Tên nhất quán (PascalCase cho lớp, camelCase cho hàm và biến), chú thích ngắn chỉ khi logic không hiển nhiên, KDoc cho API công khai, mỗi file một mục đích rõ.
