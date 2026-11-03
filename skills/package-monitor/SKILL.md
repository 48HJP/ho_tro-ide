---
name: package-monitor
description: Giám sát hoạt động các gói tin (packages/dependencies) trong dự án. Hiển thị trạng thái cài đặt, phiên bản, cập nhật có sẵn, vulnerabilities, và dependency tree. Kích hoạt khi người dùng nói "xem gói tin", "kiểm tra packages", "cập nhật dependencies", "xem hoạt động gói", hoặc bất kỳ yêu cầu nào liên quan đến quản lý packages.
---

# Package Monitor Skill

Skill này giám sát, phân tích và báo cáo hoạt động của tất cả các gói tin (packages/dependencies) trong dự án.

## Quy trình thực hiện

### Bước 1: Xác định hệ sinh thái package

Quét workspace để xác định package manager đang dùng:

```powershell
# Quét file cấu hình
$checks = @{
    "Node.js (npm)"    = "package.json"
    "Node.js (yarn)"   = "yarn.lock"
    "Node.js (pnpm)"   = "pnpm-lock.yaml"
    "Node.js (bun)"    = "bun.lockb"
    "Python (pip)"     = "requirements.txt"
    "Python (poetry)"  = "pyproject.toml"
    "Python (pipenv)"  = "Pipfile"
    "Rust (cargo)"     = "Cargo.toml"
    "Go (modules)"     = "go.mod"
    "Java (maven)"     = "pom.xml"
    "Java (gradle)"    = "build.gradle"
    "C# (.NET)"        = "*.csproj"
}
foreach ($name in $checks.Keys) {
    if (Test-Path $checks[$name]) {
        Write-Host "✅ $name detected"
    }
}
```

### Bước 2: Thu thập thông tin gói tin

Chạy các lệnh tương ứng để lấy thông tin:

#### Node.js
```powershell
# Danh sách packages đã cài
npm ls --depth=0 2>&1

# Kiểm tra packages lỗi thời
npm outdated 2>&1

# Kiểm tra lỗ hổng bảo mật
npm audit 2>&1

# Kích thước node_modules
(Get-ChildItem node_modules -Recurse -File | Measure-Object -Property Length -Sum).Sum / 1MB
```

#### Python
```powershell
# Danh sách packages
pip list 2>&1

# Packages lỗi thời
pip list --outdated 2>&1

# Kiểm tra bảo mật (nếu có pip-audit)
pip-audit 2>&1
```

#### Rust
```powershell
# Dependency tree
cargo tree 2>&1

# Packages lỗi thời
cargo outdated 2>&1

# Kiểm tra bảo mật
cargo audit 2>&1
```

#### Go
```powershell
# Danh sách modules
go list -m all 2>&1

# Dependency graph
go mod graph 2>&1
```

### Bước 3: Tạo báo cáo trạng thái

Sau khi thu thập dữ liệu, tạo báo cáo dạng bảng rõ ràng:

```
📦 BÁO CÁO GÓI TIN – [Tên dự án]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🟢 Tổng quan:
   • Hệ sinh thái: Node.js (npm)
   • Tổng packages: 142
   • Dependencies: 12 | DevDependencies: 8
   • Kích thước node_modules: 245 MB

⚠️ Cần cập nhật (3):
   ┌──────────────┬─────────┬─────────┐
   │ Package      │ Hiện tại│ Mới nhất│
   ├──────────────┼─────────┼─────────┤
   │ express      │ 4.18.2  │ 4.21.0  │
   │ typescript   │ 5.3.0   │ 5.6.2   │
   │ eslint       │ 8.55.0  │ 9.12.0  │
   └──────────────┴─────────┴─────────┘

🔴 Lỗ hổng bảo mật (1):
   • lodash@4.17.20 – Prototype Pollution (HIGH)
     → Khuyến nghị: npm update lodash

✅ Không có dependency conflicts
```

### Bước 4: Giám sát realtime (nếu cần)

Nếu người dùng muốn theo dõi liên tục:

```powershell
# Theo dõi thay đổi package.json
$watcher = New-Object System.IO.FileSystemWatcher
$watcher.Path = (Get-Location).Path
$watcher.Filter = "package.json"
$watcher.EnableRaisingEvents = $true

Register-ObjectEvent $watcher "Changed" -Action {
    Write-Host "📦 package.json đã thay đổi! Đang quét lại..."
    npm ls --depth=0
    npm outdated
}
```

### Bước 5: Hành động tự động

Dựa trên kết quả, đề xuất hành động:

| Vấn đề | Hành động đề xuất | Lệnh |
|--------|-------------------|------|
| Packages lỗi thời | Cập nhật | `npm update` / `pip install -U` |
| Lỗ hổng bảo mật | Vá lỗi | `npm audit fix` / `pip-audit --fix` |
| Dependency conflicts | Phân tích | `npm ls <package>` |
| Unused packages | Gỡ bỏ | `npm prune` / `pip-autoremove` |
| Lockfile không đồng bộ | Đồng bộ lại | `npm ci` / `pip freeze > requirements.txt` |

## Lưu ý

- **Luôn hiển thị kết quả** dạng bảng hoặc danh sách có màu sắc/biểu tượng
- Khi phát hiện lỗ hổng **HIGH** hoặc **CRITICAL**, cảnh báo rõ ràng
- Không tự động cập nhật packages mà không hỏi người dùng trước (trừ khi là security fix)
- Với monorepo, quét từng workspace/package riêng
