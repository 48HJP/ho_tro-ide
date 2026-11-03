# 🚀 Dev Runner Plugin

Plugin tự tạo cho Antigravity IDE – giúp bạn chạy dự án và giám sát gói tin nhanh chóng.

## Tính năng

### 1. 🏃 Run Project (Chạy dự án)
- **Tự động phát hiện** loại dự án (Node.js, Python, Rust, Go, Java, C#, Docker...)
- **Cài dependencies** tự động nếu thiếu
- **Chạy chương trình** với lệnh phù hợp
- **Hiển thị output** realtime trong terminal
- **Xử lý lỗi** thông minh – phân tích và đề xuất cách sửa

### 2. 📦 Package Monitor (Giám sát gói tin)
- **Liệt kê** tất cả packages đã cài
- **Kiểm tra cập nhật** – packages nào cần update
- **Quét bảo mật** – phát hiện lỗ hổng
- **Phân tích dependency tree** – xem quan hệ giữa các packages
- **Báo cáo kích thước** – biết node_modules/.venv nặng bao nhiêu

## Cách sử dụng

Sau khi cài plugin, chỉ cần nói với agent:

- *"Chạy dự án hiện tại"*
- *"Kiểm tra và hiển thị trạng thái các gói tin"*  
- *"Cập nhật packages lỗi thời"*
- *"Quét lỗ hổng bảo mật trong dependencies"*

## Hỗ trợ

| Hệ sinh thái | Package Manager | Phát hiện qua |
|--------------|-----------------|---------------|
| Node.js | npm, yarn, pnpm, bun | `package.json` |
| Python | pip, poetry, pipenv, uv | `requirements.txt`, `pyproject.toml` |
| Rust | cargo | `Cargo.toml` |
| Go | go modules | `go.mod` |
| Java | Maven, Gradle | `pom.xml`, `build.gradle` |
| C# / .NET | dotnet | `*.csproj` |
| C/C++ | CMake, Make | `CMakeLists.txt`, `Makefile` |
| Docker | docker, compose | `Dockerfile`, `docker-compose.yml` |

## Cấu trúc plugin

```
dev-runner/
├── plugin.json              ← Metadata plugin
├── README.md                ← File này
├── assets/logo.svg          ← Logo plugin
├── skills/
│   ├── run-project/
│   │   └── SKILL.md         ← Skill chạy dự án
│   └── package-monitor/
│       └── SKILL.md         ← Skill giám sát gói tin
└── rules/
    └── AGENTS.md            ← Quy tắc cho agent
```
