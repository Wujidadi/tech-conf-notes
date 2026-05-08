# Tech Events Notes

技術會議／活動筆記。

## 主題與結構

- `content/`：站台內容（首頁、活動列表、各活動與講題筆記）
- `static/`：講題附件與其他靜態檔案
- `config/_default/`、`hugoblox.yaml`：Easy Docs / HugoBlox 設定
- `go.mod`、`go.sum`：Hugo Module 相依
- `package.json`、`package-lock.json`：Tailwind 與 Pagefind 前端相依
- `layouts/_default/_markup/render-link.html`：保留相對 `.md` 與附件連結的自訂轉換

## 安裝

```bash
npm ci
```

首次安裝或更新 Hugo Module 後，可先跑一次：

```bash
hugo mod get ./...
npm ci
```

## 建置

```bash
npm ci
hugo --minify
npx pagefind --site public
```
