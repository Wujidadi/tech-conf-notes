# Copilot Instructions

## 建置與驗證指令

此倉庫使用 HugoBlox 的 Easy Docs 主題（`github.com/HugoBlox/theme-documentation`），以 Hugo Module 匯入，而不是 `themes/` submodule。建置前先安裝 npm 相依，必要時再同步 Hugo Module。

### 全站建置

```bash
npm ci && \
hugo --minify && \
npx pagefind --site public
```

### 單頁聚焦檢查

目前沒有既有的自動化測試框架；最接近「單一測試」的做法，是直接建置一次，再檢查目標頁面是否有產出對應 HTML：

```bash
npm ci && \
hugo --minify && \
npx pagefind --site public && \
test -f public/events/cybersec_2026/beyond_prompt_attacks/index.html
```

### 測試與 lint

目前倉庫內沒有既有的測試或 lint 指令；內容與版型變更通常以 Hugo 建置成功作為主要驗證方式。

## 高階架構

- 內容來源分成四層：`content/_index.md` 是首頁、`content/events/_index.md` 是活動列表、`content/events/<活動>/_index.md` 是單一活動首頁、`content/events/<活動>/*.md` 是各議程／講題筆記。講題附檔（例如 PDF）對應放在 `static/events/<活動>/`。
- 站台設定主要放在 `config/_default/` 與 `hugoblox.yaml`。其中 `config/_default/hugo.yaml` 的 cascade 會把 `/events/**` 全部當成 docs 型頁面處理，因此目前不需要把內容搬到 `/docs/`。
- 主題與頁面框架來自 `github.com/HugoBlox/theme-documentation`，由 `config/_default/module.yaml` 匯入；`go.mod` 與 `go.sum` 管理 Hugo Module 相依。
- 前端相依由 `package.json`、`package-lock.json` 管理，主要用於 Tailwind CSS 與 `pagefind` 搜尋索引。GitHub Pages workflow 會先安裝 Node.js 24 與 npm 相依，再執行 Hugo 建置與 `pagefind`。
- `layouts/_default/_markup/render-link.html` 是關鍵：它會把 Markdown 內連到其他 `.md` 的相對連結轉成 Hugo `RelRef`，並把 `./slides.pdf` 這類相對附檔路徑轉成站內 URL。來源檔請保留相對路徑寫法，不要改成產出後的 URL。

## 關鍵慣例

- 活動與清單頁幾乎都用 `_index.md` 搭配 YAML front matter；清單內容不是依賴 Hugo 自動列子頁，而是直接在 Markdown 內手動維護議程連結順序。
- 單篇講題頁應同時保留 YAML front matter 的 `title` 與文件開頭的 `# ` 標題。前者提供 SEO / 社群 metadata，後者保留正文可讀性與站內 H1。
- 首頁 `content/_index.md` 使用 HugoBlox 的 `landing` block 結構，而不是一般 Markdown 文章頁；若要修改首頁，優先調整 front matter 內的 `sections`。
- 主題或 Hugo Module 相依若有升版，先執行 `hugo mod get ./...`；前端相依若有變動，再重新安裝 npm 套件與更新 `package-lock.json`。
- 全站語系與內容以繁體中文（台灣）為主，`config/_default/hugo.yaml` 與 `config/_default/languages.yaml` 設定 `zh-tw`；新增頁面、導覽文字與 commit message 都應維持同樣語系。
- `config/_default/hugo.yaml` 已開啟 `markup.goldmark.renderer.unsafe = true`，代表 Markdown 中可用原生 HTML。若調整這個設定，要先確認既有內容不會因此失效。
- 若需要提交 commit，請遵循 `.github/git-commit-instructions.md`：commit message 使用繁體中文（台灣）的 Conventional Commits；變更較大時，在標題下補充條列摘要。
