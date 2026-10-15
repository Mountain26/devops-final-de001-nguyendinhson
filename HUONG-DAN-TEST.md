# HƯỚNG DẪN TRIỂN KHAI & TEST — ĐỀ 001 (Quản lý phòng Lab)

> Tài liệu này viết riêng cho bạn (Nguyễn Đình Sơn). Mình viết rất kỹ từng bước, bạn chỉ cần **gõ y nguyên** các lệnh.
> Mọi lệnh (trừ khi ghi chú khác) chạy trong thư mục gốc project:
>
> ```
> C:\Users\OS\Documents\DevOps_Final\devops-final-app-de001
> ```

---

## 0. Thông tin đã điền sẵn cho bạn

### 0.1. `student.env` (đã điền xong)

| Biến | Giá trị | Ghi chú |
|------|---------|---------|
| `STUDENT_NAME` | `nguyen dinh son` | |
| `STUDENT_ID` | `ptit_hn_053` | |
| `STUDENT_CLASS` | `hn_ks24_cntt2` | |
| `STUDENT_EMAIL` | `nguyendinhson@stu.ptit.edu.vn` | |
| `GITHUB_USERNAME` | `mountain26` | |
| `EXAM_CODE` | `001` | KHÔNG sửa |
| `APP_PORT` | `8080` | cổng mở trên server |
| `COMPOSE_PROJECT_NAME` | `nguyendinhson-de001` | |
| `MYSQL_DATABASE` | `ptit_lab_db` | KHÔNG sửa |
| `MYSQL_USER` | `root` | |
| `MYSQL_PASSWORD` | `sondz1` | |
| `MYSQL_ROOT_PASSWORD` | `sondz1` | |

### 0.2. Server Azure của bạn

| Thông tin | Giá trị |
|-----------|---------|
| IP public (Primary NIC public IP) | `20.2.216.142` |
| User SSH | `sonseo157` |
| Password SSH (đăng nhập tay) | `Son_123123123` |

### 0.3. Tên repo GitHub cần tạo

```
devops-final-de001-nguyendinhson   (public, nhánh main)
```

### 0.4. URL app sau khi deploy

```
http://20.2.216.142:8080/api/labs
```

### 0.5. Hai lỗi mình đã sửa sẵn trong code (bạn KHÔNG cần làm gì)

1. `Dockerfile`: dòng cuối bị ghi `EXPOSE _` (sai) → đã sửa thành `EXPOSE 8080`.
2. `docker-compose.yml`: cổng nginx bị ghi `"${APP_PORT:_}:80"` (sai cú pháp) → đã sửa thành `"${APP_PORT}:80"`.
3. `docker-compose.yml`: service `mysql` — vì bạn muốn dùng tài khoản **root/sondz1**, mà image `mysql:8.0` **không cho** đặt `MYSQL_USER=root` (nó báo lỗi và container không chạy). Nên mình đã:
   - bỏ `MYSQL_USER`/`MYSQL_PASSWORD` khỏi service `mysql`,
   - thêm `MYSQL_ROOT_HOST: "%"` (cho phép root kết nối từ container `app`),
   - đổi healthcheck dùng `root`.
   App vẫn kết nối bằng `${MYSQL_USER}`/`${MYSQL_PASSWORD}` = `root`/`sondz1`.

> ⚠️ **Lưu ý quan trọng về Git của bạn:** thư mục project hiện đang nằm *bên trong* một git repo lớn ở `C:\Users\OS` (remote `Mountain26/BaiKiemTraDauGioSS10_JavaApplication`). Nghĩa là nếu bạn gõ `git ...` trong thư mục project, nó sẽ thao tác lên repo của cả ổ `C:\Users\OS` — **rất nguy hiểm**. Vì vậy ở Phần 1 bạn **bắt buộc** phải tạo một git repo mới ngay trong thư mục project (mình hướng dẫn cụ thể bên dưới).

---

## PHẦN 1 — Tạo repository GitHub & đẩy code (10 điểm)

### Bước 1.1 — Tạo repo trống trên GitHub

1. Mở trình duyệt → đăng nhập GitHub (`mountain26`).
2. Vào https://github.com/new
3. Điền:
   - **Repository name**: `devops-final-de001-nguyendinhson`
   - Chọn **Public**
   - **KHÔNG** tick "Add a README file", "Add .gitignore", "Choose a license" (để repo trống).
4. Bấm **Create repository**.
5. Ở trang tiếp theo, GitHub hiện link repo. Copy lại URL dạng:
   `https://github.com/mountain26/devops-final-de001-nguyendinhson.git`

### Bước 1.2 — Tạo git repo mới *bên trong* thư mục project và đẩy code lên

Mở **PowerShell** (Start → gõ "PowerShell" → Enter). Gõ từng khối lệnh:

```powershell
cd C:\Users\OS\Documents\DevOps_Final\devops-final-app-de001
```

> Kiểm tra xem thư mục này đã là repo riêng chưa:
```powershell
git rev-parse --show-toplevel
```
- Nếu kết quả là `C:/Users/OS` → đây là repo của cả ổ C, **tiếp tục làm bước dưới**.
- Nếu kết quả là chính thư mục project → bạn đã tạo rồi, bỏ qua `git init`.

Tạo repo mới cho riêng project:

```powershell
git init
git branch -M main
git add .
git commit -m "first commit"
```

> Nếu Git báo chưa cấu hình tên/email, chạy 2 dòng này rồi commit lại:
```powershell
git config --global user.name "nguyen dinh son"
git config --global user.email "nguyendinhson@stu.ptit.edu.vn"
```

Gắn remote và đẩy lên (thay URL bằng link repo bạn vừa copy):

```powershell
git remote add origin https://github.com/mountain26/devops-final-de001-nguyendinhson.git
git push -u origin main
```

Khi được hỏi đăng nhập: chọn **Sign in with your browser** (đăng nhập GitHub) hoặc dán **Personal Access Token** (xem Phần 6 nếu chưa biết tạo).

### Bước 1.3 — Kiểm tra

Mở lại https://github.com/mountain26/devops-final-de001-nguyendinhson → phải thấy đầy đủ các file: `Dockerfile`, `docker-compose.yml`, `student.env`, thư mục `src/`, `nginx/`, `database/`, `.github/`.

> 📸 **Chụp màn hình này** (trang repo trên GitHub) — dùng làm minh chứng phụ cho Phần 1.

---

## PHẦN 2 — Dockerfile multi-stage (25 điểm)

File `Dockerfile` đã hoàn chỉnh. Cách hiểu nhanh:

- **Stage 1 (`build`)**: dùng image `gradle:8.14-jdk21-alpine` (có Gradle + JDK 21) để biên dịch ra `build/libs/app.jar`.
  - Copy `build.gradle` + `settings.gradle` trước, tải dependency (để tận dụng cache), rồi mới copy `src/` và chạy `gradle bootJar -x test`.
- **Stage 2 (`runtime`)**: chỉ dùng `eclipse-temurin:21-jre-alpine` (nhẹ, chỉ có JRE), copy mỗi file `app.jar` từ stage build sang, chạy bằng user thường (`spring`, không phải root).
- `ARG APP_VERSION` được truyền vào `ENV APP_VERSION`.
- `EXPOSE 8080`, `ENTRYPOINT` dạng exec.

### Cách test Dockerfile (chạy riêng, không cần compose)

Đảm bảo **Docker Desktop** đang chạy (icon cá voi ở khay hệ thống không có dấu chấm than).

```powershell
cd C:\Users\OS\Documents\DevOps_Final\devops-final-app-de001
docker build -t devops-final-app:test .
```

Lần đầu sẽ tải image và tải thư viện Gradle → **có thể mất vài phút**, cứ để chạy.
Kết thúc phải thấy dòng cuối kiểu:

```
=> exporting to image
=> => naming to docker.io/library/devops-final-app:test
```

Kiểm tra image đã tạo và xem các layer/biến môi trường:

```powershell
docker images devops-final-app
docker run --rm --entrypoint sh devops-final-app:test -c "echo APP_VERSION=$APP_VERSION; whoami"
```

→ Kết quả mong đợi: `APP_VERSION=dev` và `whoami` = `spring` (không phải root).

> 📸 Chụp màn hình dòng `docker build` thành công + dòng `docker images` để minh chứng Phần 2 (không bắt buộc nộp, nhưng nên lưu).

---

## PHẦN 3 — Hoàn thiện `docker-compose.yml` & test local (20 điểm)

File đã điền đủ 5 TODO:

| TODO | Nội dung đã điền |
|------|------------------|
| 1 | `app.build.context: .` + `args.APP_VERSION: ${APP_VERSION:-local}` |
| 2 | `image: ${APP_IMAGE:-devops-final-app}:${APP_TAG:-latest}` |
| 3 | `DB_NAME/DB_USER/DB_PASSWORD` lấy từ `${MYSQL_DATABASE}/${MYSQL_USER}/${MYSQL_PASSWORD}` |
| 4 | nginx publish `"${APP_PORT}:80"` |
| 5 | mount read-only `./nginx/default.conf:/etc/nginx/conf.d/default.conf:ro` |

### Bước 3.1 — Build & khởi động toàn bộ hệ thống

```powershell
cd C:\Users\OS\Documents\DevOps_Final\devops-final-app-de001
docker compose --env-file student.env up -d --build
```

Lần đầu chạy sẽ tải `mysql:8.0`, `nginx:1.27-alpine`, build app → chờ vài phút.

### Bước 3.2 — Kiểm tra các container đang chạy

```powershell
docker compose --env-file student.env ps
```

Kết quả mong đợi: 3 service `mysql`, `app`, `nginx` đều **Up**; `mysql` ở trạng thái **healthy** (cột STATUS ghi `(healthy)`).

> 📸 **ẢNH MINH CHỨNG `03-compose-ps.png`**: chụp nguyên cửa sổ PowerShell đang hiện kết quả lệnh `ps` này (nhớ để lộ rõ lệnh + bảng kết quả + tên project `nguyendinhson-de001`).

### Bước 3.3 — Gọi thử API

```powershell
curl.exe http://localhost:8080/api/labs
```

> Dùng `curl.exe` (có sẵn trên Windows 10/11). Kết quả mong đợi: một mảng JSON 8 phòng lab, bắt đầu bằng:
> ```json
> [{"id":1,"labName":"DevOps Lab","building":"A1","capacity":40,...
> ```
> Thử luôn đường dẫn gốc (nginx sẽ tự chuyển hướng sang `/api/labs`):
```powershell
curl.exe -i http://localhost:8080/
```
→ phải thấy dòng `HTTP/1.1 302` và `Location: /api/labs`.

### Bước 3.4 — Xem log khi cần debug

```powershell
docker compose --env-file student.env logs -f app
```
Nhấn `Ctrl + C` để thoát khỏi chế độ xem log.

### Bước 3.5 — Dọn dẹp (khi test xong, tránh chiếm cổng)

```powershell
docker compose --env-file student.env down
```

> Nếu muốn xóa luôn dữ liệu MySQL đã tạo: thêm `-v` → `docker compose --env-file student.env down -v`.

---

## PHẦN 4 — Pipeline CI/CD với GitHub Actions (35 điểm)

### 4.1 — Cài Docker + Git trên server Azure (nếu chưa có)

Đăng nhập SSH vào server (từ PowerShell):

```powershell
ssh sonseo157@20.2.216.142
# nhập password: Son_123123123
```

> Lần đầu sẽ hỏi `Are you sure you want to continue connecting (yes/no)?` → gõ `yes` rồi Enter.

Trên server, kiểm tra đã có Docker/Git chưa:

```bash
docker --version
docker compose version
git --version
```

Nếu **thiếu**, cài (Ubuntu):

```bash
curl -fsSL https://get.docker.com | sudo sh
sudo usermod -aG docker $USER
sudo apt-get update && sudo apt-get install -y git
```

Sau đó **thoát SSH và đăng nhập lại** (`exit` rồi `ssh sonseo157@20.2.216.142`) để quyền `docker` có hiệu lực. Thử lại:

```bash
docker ps
```

→ nếu chạy được **không cần `sudo`** là OK.

### 4.2 — Tạo SSH key cho pipeline và nạp public key vào server

Quay lại **máy Windows** (thoát SSH bằng `exit`).

```powershell
cd C:\Users\OS\Documents\DevOps_Final\devops-final-app-de001
ssh-keygen -t ed25519 -f deploy_key
```

> Khi hỏi `Enter passphrase` → **nhấn Enter 2 lần** (bỏ trống passphrase). Kết quả tạo ra 2 file: `deploy_key` (private) và `deploy_key.pub` (public).

Xem nội dung public key:

```powershell
type deploy_key.pub
```

Copy **toàn bộ** dòng bắt đầu bằng `ssh-ed25519 ...` đó.

Nạp vào server: SSH lại vào server rồi dán:

```powershell
ssh sonseo157@20.2.216.142
```
```bash
mkdir -p ~/.ssh && chmod 700 ~/.ssh
echo "DAN_NGUYEN_DONG_deploy_key.pub_VAO_DAY" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
exit
```

> Thay `DAN_NGUYEN_DONG_deploy_key.pub_VAO_DAY` bằng nội dung public key bạn vừa copy.

**Kiểm tra key hoạt động** (từ Windows):

```powershell
ssh -i deploy_key sonseo157@20.2.216.142 "echo LOGIN_OK"
```
→ in ra `LOGIN_OK` là được. (Nếu báo `UNPROTECTED PRIVATE KEY FILE` → xem Phần 6.)

### 4.3 — Mở cổng trên Azure (NSG)

Vào **Azure Portal** → **Virtual machines** → chọn VM của bạn → **Networking** → **Add inbound port rule**:

- **Source**: Any
- **Destination port ranges**: `8080` (đây là `APP_PORT`)
- **Protocol**: TCP
- **Action**: Allow
- **Priority**: ví dụ `310`
- **Name**: `allow-app-port`

Bấm **Add**. (Cổng `22` cho SSH thường đã mở sẵn.)

### 4.4 — Tạo 3 GitHub Secrets

Trên trang repo GitHub → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**. Tạo lần lượt 3 secret:

| Name | Value |
|------|-------|
| `SERVER_HOST` | `20.2.216.142` |
| `SERVER_USER` | `sonseo157` |
| `SERVER_SSH_KEY` | **Toàn bộ nội dung file `deploy_key`** (private key) |

> Lấy nội dung private key trên Windows:
> ```powershell
> type deploy_key
> ```
> Copy **cả dòng** `-----BEGIN OPENSSH PRIVATE KEY-----` ... `-----END OPENSSH PRIVATE KEY-----` (kể cả 2 dòng đầu/cuối) rồi dán vào value.

> 🔒 Tuyệt đối **không** commit file `deploy_key` lên GitHub (đã được `.gitignore` chặn sẵn).

### 4.5 — Kiểm tra file `ci-cd.yml` (đã điền TODO)

File `.github/workflows/ci-cd.yml` đã điền đủ 6 TODO:

| TODO | Nội dung |
|------|----------|
| 1 | `docker compose --env-file student.env up -d --build --wait --wait-timeout 300` |
| 2 | login GHCR bằng `github.actor` + `secrets.GITHUB_TOKEN` |
| 3 | push 2 tag: `github.sha` và `latest` |
| 4 | deploy `needs: build` |
| 5 | SSH bằng `SERVER_HOST`, `SERVER_USER`, `SERVER_SSH_KEY` |
| 6 | `docker compose pull app` + `up -d --no-build --wait` |

### 4.6 — Push để kích hoạt pipeline

Commit và push lên `main`:

```powershell
cd C:\Users\OS\Documents\DevOps_Final\devops-final-app-de001
git add .
git commit -m "hoan thien de 001"
git push origin main
```

Vào repo GitHub → tab **Actions** → bấm vào workflow **CI/CD** đang chạy (dấu chấm vàng). Chờ tới khi:
- job **Build & Test** → ✅ xanh
- job **Deploy lên server** → ✅ xanh

> Nếu job đỏ (❌), bấm vào job đỏ → xem bước lỗi → tra Phần 6.

### 4.7 — Kiểm tra kết quả trên server

Từ Windows:

```powershell
ssh sonseo157@20.2.216.142
cd ~/devops-final-de001-nguyendinhson
docker compose --env-file student.env ps
curl http://localhost:8080/api/labs
```

→ thấy 3 container `Up` và JSON danh sách phòng lab.

---

## PHẦN 5 — Ảnh minh chứng (10 điểm)

Tạo đủ **4 file** ảnh trong thư mục `screenshots/` (đặt đúng tên như bảng dưới). Cách chụp trên Windows: **Windows + Shift + S** để chụp vùng, hoặc **Windows + PrtScn** để chụp cả màn hình (ảnh lưu vào `Pictures\Screenshots`).

### 5.1. `01-actions.png` — Tab Actions chạy thành công

1. Mở repo → tab **Actions**.
2. Bấm vào lần chạy mới nhất (trên nhánh `main`).
3. Phải thấy trang có 2 job: **Build & Test** và **Deploy lên server**, cả hai đều ✅ xanh, nhánh `main`.
4. Chụp vùng hiển thị: tên workflow **CI/CD**, nhánh `main`, 2 job xanh.
5. Lưu file tên `01-actions.png` vào thư mục `screenshots/`.

### 5.2. `02-ghcr.png` — Image trên GHCR có 2 tag

1. Vào trang cá nhân GitHub → tab **Packages** (link `https://github.com/mountain26?tab=packages`), hoặc vào repo → khung phải mục **Packages**.
2. Mở package tên `devops-final-de001-nguyendinhson/app`.
3. Phải thấy danh sách tag gồm: `latest` và một mã **commit SHA** (chuỗi 40 ký tự).
4. Chụp lại cả tên package + các tag.
5. Lưu file tên `02-ghcr.png`.

> (Nếu bạn push lên Docker Hub thay vì GHCR thì lưu tên `02-docker.png`.)

### 5.3. `03-compose-ps.png` — `docker compose ps` trên server

1. SSH vào server:
   ```powershell
   ssh sonseo157@20.2.216.142
   cd ~/devops-final-de001-nguyendinhson
   docker compose --env-file student.env ps
   ```
2. Kết quả hiện bảng 3 container `mysql` (healthy), `app`, `nginx` đều `Up`.
3. Chụp cửa sổ SSH sao cho thấy rõ **cả lệnh vừa gõ** và **bảng kết quả**.
4. (Khuyến khích) chụp thêm `curl http://localhost:8080/api/labs` cho sinh động.
5. Lưu file tên `03-compose-ps.png`.

### 5.4. `04-website.png` — Browser hiển thị JSON

1. Trên trình duyệt (Chrome/Edge), mở:
   ```
   http://20.2.216.142:8080/api/labs
   ```
2. Trang phải hiển thị **JSON danh sách phòng lab** (8 phòng, có `id`, `labName`, `building`, `capacity`, `equipment`, `status`).
3. Chụp **cả thanh địa chỉ** (thấy rõ `http://20.2.216.142:8080/api/labs`) và nội dung JSON.
4. Lưu file tên `04-website.png`.

> 💡 Nếu JSON hiển thị trên 1 dòng khó nhìn: cài extension Chrome **"JSON Formatter"** cho dễ đọc, rồi chụp.

### 5.5. Đóng gói nộp bài

1. Copy 4 ảnh vào **cả hai** thư mục: `screenshots/` và `submission-content/`.
2. Commit + push:
   ```powershell
   cd C:\Users\OS\Documents\DevOps_Final\devops-final-app-de001
   git add .
   git commit -m "bo sung anh minh chung"
   git push origin main
   ```
3. Theo quy trình nộp bài: dùng SFTP kéo **toàn bộ thư mục** `devops-final-de001-nguyendinhson` về máy cá nhân rồi nộp lên RAIA.

---

## PHẦN 6 — Lỗi thường gặp & cách xử lý

| Triệu chứng | Nguyên nhân | Cách sửa |
|-------------|-------------|----------|
| `docker compose up` báo `port is already allocated` | Cổng 8080 đang bị chiếm | Đổi `APP_PORT` trong `student.env` sang cổng khác (ví dụ `8090`) và chạy lại; nhớ mở cổng mới trên Azure NSG. |
| Container `mysql` cứ **Restarting** | Còn sót cấu hình `MYSQL_USER=root` | Trong file này đã sửa rồi. Nếu vẫn lỗi: `docker compose --env-file student.env down -v` rồi `up` lại (xóa volume cũ để init lại). |
| App log báo `Access denied for user 'root'` | Thiếu `MYSQL_ROOT_HOST` | Đã thêm `MYSQL_ROOT_HOST: "%"`. Nếu tự tay sửa thì kiểm tra lại dòng này. |
| App log báo `Communications link failure` / `Connection refused` | MySQL chưa healthy | Chờ thêm ~30–60s; kiểm tra `docker compose --env-file student.env ps` xem mysql đã `healthy` chưa. |
| `curl :8080` không phản hồi nhưng local OK | Azure NSG chưa mở cổng | Làm lại bước 4.3. |
| GitHub Actions báo `Chưa điền ... trong student.env` | Thiếu field trong `student.env` | Điền đủ rồi push lại. |
| `ssh -i deploy_key ...` báo `UNPROTECTED PRIVATE KEY FILE` | Quyền file key quá mở (Windows) | Chạy: `icacls deploy_key /inheritance:r /grant:r "$($env:USERNAME):R"` |
| Deploy job lỗi `Permission denied (publickey)` | Public key chưa nạp đúng vào server, hoặc SECRET sai | Kiểm tra `~/.ssh/authorized_keys` trên server và giá trị `SERVER_SSH_KEY`. |
| Deploy job lỗi `denied: denied` khi pull image | GHCR token/package quyền | Kiểm tra job build đã push xong; đảm bảo package liên kết với repo và `permissions: packages: read` có trong job deploy (đã có sẵn). |
| Push GitHub bị hỏi mật khẩu liên tục | Chưa dùng token | Tạo **Personal Access Token** (GitHub → Settings → Developer settings → Tokens (classic) → scope `repo`, `write:packages`), dùng token làm mật khẩu. |

---

## PHẦN 7 — Checklist nộp bài

- [ ] Repo `devops-final-de001-nguyendinhson` **public**, nhánh `main`, đã có code.
- [ ] `student.env` đầy đủ, `APP_PORT >= 8080`.
- [ ] `Dockerfile` multi-stage đã build thành công.
- [ ] `docker-compose.yml` chạy được: `docker compose --env-file student.env ps` → 3 container Up, mysql healthy.
- [ ] `curl http://localhost:8080/api/labs` trả JSON.
- [ ] Đã tạo 3 Secrets: `SERVER_HOST`, `SERVER_USER`, `SERVER_SSH_KEY`.
- [ ] Pipeline trên `main`: **build** ✅ và **deploy** ✅.
- [ ] Image trên GHCR có 2 tag: commit SHA + `latest`.
- [ ] `http://20.2.216.142:8080/api/labs` hiển thị JSON trên trình duyệt.
- [ ] Đủ 4 ảnh trong `screenshots/` và `submission-content/`:
  - [ ] `01-actions.png`
  - [ ] `02-ghcr.png`
  - [ ] `03-compose-ps.png`
  - [ ] `04-website.png`
- [ ] Kéo thư mục project về máy và nộp lên RAIA.
