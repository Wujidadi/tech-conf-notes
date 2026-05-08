# Tech Events Notes

技術會議／活動筆記。

## Hugo 結構

- `content/`：站台內容（首頁、活動列表、各活動與講題筆記）
- `static/`：講題附件與其他靜態檔案
- `themes/hugo-theme-bootstrap`：Bootstrap Theme for Personal Blog and Documentations 主題 submodule
- `layouts/`：針對主題保留的少量覆寫（例如 Markdown 連結、標題與列表行為）
- `packages/hugoautogen/`、`package.json`、`package-lock.json`：由 `hugo mod npm pack` 產生的前端相依描述

## 安裝

```bash
git submodule update --init --recursive
npm ci
```

## 建置

```bash
npm ci
hugo --minify
```

首次切換主題或更新主題版本後，若需要重整前端相依，可執行 `hugo mod npm pack` 後再重新執行 `npm install` 或 `npm ci`。
