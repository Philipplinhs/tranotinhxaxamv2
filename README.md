# Remote config v2 (public)

Client native/web đọc **`app.json`** trên nhánh `main` (raw GitHub / jsDelivr). Đổi domain **không cần build lại APK** — user xóa cache app hoặc đợi TTL fetch.

## Phân tách domain

| Vai trò | Domain | Ví dụ |
|--------|--------|--------|
| **API / game server** | `zj3797.net` | `iportal.zj3797.net`, `wmini.zj3797.net`, `taixiu.zj3797.net`, … |
| **Web chơi (H5)** | `tha8369.net` | `https://play.tha8369.net/` (build web-mobile, không phải CDN bundle) |
| **Hot update native** (`project.manifest`, `remote-assets/`) | `rg9386.net` | `dl4.rg9386.net`, `download1` … `download3.rg9386.net` |
| **Asset bundle (Lobby + game)** | `rg9386.net` | `ab1` … `ab20.rg9386.net` — cùng nội dung `assets/`, `AssetBundleVersion.json` |

## Trường `app.json`

- **`host` / `javaHost`**: apex API → client set `NetConfig.HOST` và rebuild subdomain (portal, chat, slot WSS, …).
- **`playHost`**: ghi chú / tooling; web chơi deploy lên `play.{playHost}`.
- **`downloadHost`**: apex cho script build hot update (fallback nếu thiếu `hotupdate`).
- **`hotupdate`**: danh sách host **không scheme**, thử lần lượt khi tải `…/remote-assets/project.manifest`.
- **`assetBundleBase`**: host ưu tiên tải `AssetBundleVersion.json`.
- **`assetBundleHosts`**: danh sách mirror `ab*`; client và `gen-asset-bundle-version.js` chia URL bundle theo hash tên bundle.

## Deploy CDN

1. **Hot update** (sau build jsb-link):  
   `node ClientXu2X/buildHotUpdate/gen-hotupdate.js`  
   → upload thư mục `remote-assets/` lên **mọi** `download*.rg9386.net` (cùng path).

2. **Web + bundles** (sau build web-mobile):  
   `node ClientXu2X/buildHotUpdate/gen-asset-bundle-version.js`  
   → upload `web-mobile/` (hoặc ít nhất `assets/`) lên **mọi** `ab1`…`ab20.rg9386.net`.

3. **Play**: copy build web lên `play.tha8369.net`.

## Repo cũ

`tranotinhxaxam` trỏ hot update sang brand khác (`qjwqwjhdasjb2ju78.com`). Client mới dùng repo **`tranotinxacamv2`** — xem `RemoteGitConfig.js` trong ClientXu2X.