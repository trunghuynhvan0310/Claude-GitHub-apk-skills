# Claude GitHub APK Skills

**English** · [Tiếng Việt](#tiếng-việt)

A small set of [Claude](https://claude.ai) skills for building Android apps through the **GitHub connector** and **GitHub Actions**, from a private prototype to a public APK to Google Play, one stage at a time.

> Unofficial community project. Not affiliated with Anthropic or Google. The skill instructions themselves are written in **Vietnamese** (Claude replies to the user in Vietnamese); this README is bilingual.

## Why

When Claude writes an Android app in an empty GitHub repo and builds it with GitHub Actions, it tends to forget the boring but important parts:

- bumping the version (`1.x.x`) and naming the APK after it;
- updating the README and changelog after every change;
- making sure the APK is actually built and the workflow is green;
- walking the user through creating **their own signing key** (a fresh CI runner usually generates a different debug key each run, so updates can't be installed over older builds);
- checking the latest SDK / Gradle / library versions before coding;
- `LICENSE`, privacy policy, third-party notices;
- removing dead code when a feature is dropped;
- in-app onboarding, settings, About and «What's new» screens.

These skills put that into a checklist, split into stages so Claude does only what the current stage needs.

## The three stages

| Stage | Skill | Goal | Loaded when |
|---|---|---|---|
| 1. Personal (default) | [`android-quy-trinh`](skills/android-quy-trinh/SKILL.md) | Working app, versioned APKs, fixed signing key, short README, project notes | Always (core skill) |
| 2. Share the APK | [`android-chia-se-apk`](skills/android-chia-se-apk/SKILL.md) | LICENSE, privacy policy, changelog, GitHub Releases, onboarding/settings/About, stability checks | You agree to move to stage 2 |
| 3. Google Play | [`android-google-play`](skills/android-google-play/SKILL.md) | AAB, Play App Signing, Data safety, store listing, testing tracks | You agree to move to stage 3 |

Each later stage includes the earlier ones. Claude records the current stage on the first line of `PROJECT_NOTES.md` in your app repo (`Giai đoạn: 1`), and **asks you before moving on**, only when there is a signal (you say you want to share it, or the app has been stable for a few builds). If you decline, it doesn't ask again in that session.

## Designed to be cheap in tokens

- Only the small core skill is always loaded; stage 2 and 3 skills load on demand.
- Per turn, Claude does only the checklist items related to that turn's change.
- Claude reads `PROJECT_NOTES.md` instead of scanning the whole repo, and edits files in place.
- Latest-version lookups happen when a project starts or a library is added, not every turn.
- Full code cleanup is *proposed* after about 5 edits, and done only if you agree.
- Final reports are about 6 lines.

## Install

**Claude (web/desktop) with skills enabled:** zip each skill folder and upload it in the Skills section of your Claude settings.

```bash
cd skills
zip -r android-quy-trinh.zip android-quy-trinh
zip -r android-chia-se-apk.zip android-chia-se-apk
zip -r android-google-play.zip android-google-play
```

**Claude Code:** copy the folders into your skills directory.

```bash
cp -r skills/* ~/.claude/skills/
```

Install all three; stages 2 and 3 cost nothing until you move to them.

## Typical flow

1. Talk through the idea with Claude: features, UI, language, steps.
2. Create an empty GitHub repo and connect it to Claude.
3. Claude sets up the repo (stage 1), builds a versioned APK, and guides you through creating your signing key once.
4. Test, report bugs, repeat. Experimental features live on `feat/...` branches; failed ones are deleted, not left in `main`.
5. When you want to share the app, Claude proposes stage 2. Later, if it's worth it, stage 3.

## Limits

- Claude cannot run the app. It can say the build is green, not that the app works. You are the tester.
- Google Play rules change often, so stage 3 tells Claude to check official pages first.
- Creating a Play developer account, paying, identity verification and submitting in Play Console are yours to do.

## License

[MIT](LICENSE). Feel free to fork and adapt to your own workflow.

---

## Tiếng Việt

Bộ skill cho Claude giúp làm app Android qua **GitHub connector** và **GitHub Actions**, đi từng giai đoạn: dùng cá nhân, chia sẻ APK, rồi đưa lên Google Play.

> Dự án cộng đồng, không chính thức, không liên kết với Anthropic hay Google. Nội dung skill viết bằng tiếng Việt.

### Vì sao cần

Khi Claude viết app trong repo GitHub trống rồi build bằng Actions, Claude hay quên: tăng phiên bản `1.x.x` và đặt tên APK theo phiên bản, cập nhật README và CHANGELOG, build ra APK và kiểm tra workflow xanh, hướng dẫn tạo **khóa ký riêng**, tra SDK và thư viện mới nhất, LICENSE và chính sách riêng tư, dọn code khi bỏ chức năng, và các màn onboarding, cài đặt, giới thiệu trong app. Bộ skill biến những việc đó thành checklist, chia giai đoạn để Claude chỉ làm phần giai đoạn hiện tại cần.

### Ba giai đoạn

| Giai đoạn | Skill | Mục tiêu |
|---|---|---|
| 1. Cá nhân (mặc định) | `android-quy-trinh` | App chạy, APK có phiên bản, khóa ký cố định, README ngắn, ghi chú dự án |
| 2. Chia sẻ APK | `android-chia-se-apk` | LICENSE, chính sách riêng tư, CHANGELOG, GitHub Release, onboarding, cài đặt, giới thiệu, độ ổn định |
| 3. Google Play | `android-google-play` | AAB, Play App Signing, Data safety, hồ sơ cửa hàng, các kênh thử nghiệm |

Giai đoạn sau gồm cả giai đoạn trước. Claude ghi giai đoạn hiện tại ở dòng đầu `PROJECT_NOTES.md` trong repo app của bạn và **hỏi bạn trước khi chuyển**, chỉ khi có tín hiệu (bạn nói muốn chia sẻ, hoặc app ổn định qua vài bản). Bạn từ chối thì Claude không hỏi lại trong phiên đó.

### Tiết kiệm token

Chỉ skill lõi nạp sẵn, skill giai đoạn 2 và 3 nạp khi cần. Mỗi lượt Claude chỉ làm các mục liên quan đến thay đổi lượt đó, đọc `PROJECT_NOTES.md` thay vì quét cả repo, tra phiên bản mới chỉ khi bắt đầu hoặc thêm thư viện, chỉ đề xuất dọn code sau khoảng 5 lần sửa, và báo cáo cuối khoảng 6 dòng.

### Cài đặt

Nén từng thư mục trong `skills/` thành file zip rồi tải lên phần Skills trong cài đặt Claude, hoặc với Claude Code thì chép vào `~/.claude/skills/` (lệnh ở phần English phía trên). Nên cài cả ba skill.

### Giới hạn

Claude không chạy được app, chỉ nói được «build xanh». Quy định Google Play đổi thường xuyên nên Claude phải tra trang chính thức. Tạo tài khoản, thanh toán, xác minh và nộp trên Play Console là việc của bạn.

### Giấy phép

[MIT](LICENSE).
