---
name: run-project
description: Tự động phát hiện loại dự án (Node.js, Python, Rust, Go, Java, C#, C/C++...), cài đặt dependencies, chạy chương trình trong terminal, và hiển thị output realtime. Kích hoạt khi người dùng nói "chạy dự án", "run project", "start server", "build project", hoặc bất kỳ yêu cầu nào liên quan đến việc thực thi chương trình.
---

# Run Project Skill

Skill này giúp tự động phát hiện và chạy dự án trong terminal.

## Quy trình thực hiện

### Bước 1: Phát hiện loại dự án

Quét thư mục workspace hiện tại để xác định loại dự án dựa trên các file đặc trưng:

| File đặc trưng | Loại dự án | Package Manager |
|----------------|-----------|-----------------|
| `package.json` | Node.js | npm / yarn / pnpm / bun |
| `requirements.txt`, `pyproject.toml`, `setup.py`, `Pipfile` | Python | pip / pipenv / poetry / uv |
| `Cargo.toml` | Rust | cargo |
| `go.mod` | Go | go modules |
| `pom.xml` | Java (Maven) | mvn |
| `build.gradle`, `build.gradle.kts` | Java/Kotlin (Gradle) | gradle |
| `*.csproj`, `*.sln` | C# / .NET | dotnet |
| `CMakeLists.txt` | C/C++ (CMake) | cmake |
| `Makefile` | C/C++ / Generic | make |
| `Dockerfile` | Docker | docker |
| `docker-compose.yml` | Docker Compose | docker compose |

**Hành động:**
```bash
# Quét thư mục gốc workspace
ls -la
# Hoặc trên Windows:
Get-ChildItem -Force
```

Nếu tìm thấy nhiều loại (ví dụ `package.json` + `Dockerfile`), hỏi người dùng muốn chạy cái nào, hoặc gợi ý chạy theo thứ tự hợp lý.

### Bước 2: Kiểm tra dependencies

Trước khi chạy, kiểm tra dependencies đã được cài chưa:

**Node.js:**
```bash
# Kiểm tra node_modules có tồn tại không
if (!(Test-Path "node_modules")) { Write-Host "⚠️ Chưa cài dependencies. Đang cài..." ; npm install }
# Hoặc yarn/pnpm/bun tùy lockfile
if (Test-Path "yarn.lock") { yarn install }
elseif (Test-Path "pnpm-lock.yaml") { pnpm install }
elseif (Test-Path "bun.lockb") { bun install }
else { npm install }
```

**Python:**
```bash
# Kiểm tra virtual environment
if (!(Test-Path ".venv") -and !(Test-Path "venv")) {
    Write-Host "⚠️ Chưa có virtual environment. Đang tạo..."
    python -m venv .venv
}
# Activate và cài dependencies
.\.venv\Scripts\Activate.ps1
if (Test-Path "requirements.txt") { pip install -r requirements.txt }
elseif (Test-Path "pyproject.toml") { pip install -e ".[dev]" }
```

**Rust:**
```bash
cargo build
```

**Go:**
```bash
go mod download
```

### Bước 3: Chạy chương trình

Chạy lệnh phù hợp với loại dự án. **Luôn chạy bằng `run_command`** với `IsDaemon=true` cho các server/process chạy liên tục, hoặc `IsDaemon=false` cho các lệnh chạy-xong-dừng.

**Node.js:**
```bash
# Đọc package.json để tìm script phù hợp
# Ưu tiên: dev > start > main
npm run dev    # hoặc yarn dev, pnpm dev
npm start      # fallback
node index.js  # nếu không có script
```

**Python:**
```bash
python main.py          # hoặc app.py, manage.py
python -m flask run     # Flask
python -m uvicorn main:app --reload  # FastAPI
python manage.py runserver           # Django
```

**Rust:**
```bash
cargo run
```

**Go:**
```bash
go run .
# hoặc go run main.go
```

**Docker:**
```bash
docker compose up       # docker-compose
docker build -t app . && docker run -p 8080:8080 app
```

### Bước 4: Hiển thị output

Sau khi chạy, báo cáo cho người dùng:
- ✅ Lệnh đã chạy thành công hay lỗi
- 🔗 URL server nếu là web app (ví dụ `http://localhost:3000`)
- 📋 Tóm tắt output quan trọng (errors, warnings)
- 💡 Gợi ý bước tiếp theo (mở browser, chạy test, etc.)

### Bước 5: Xử lý lỗi

Nếu chạy thất bại:
1. Đọc error message
2. Phân tích nguyên nhân (thiếu dependency, port bị chiếm, syntax error...)
3. Đề xuất cách sửa cụ thể
4. Hỏi người dùng có muốn tự động sửa không

## Lưu ý quan trọng

- **Luôn hiển thị lệnh** sẽ chạy trước khi thực thi để người dùng biết
- **Server processes** (dev server, API server) dùng `IsDaemon=true` để chạy nền
- **Build/test** dùng `IsDaemon=false` để chờ kết quả
- Nếu port bị chiếm, tìm port khác hoặc hỏi người dùng có muốn kill process cũ
- Đối với monorepo, hỏi người dùng muốn chạy package/app nào
