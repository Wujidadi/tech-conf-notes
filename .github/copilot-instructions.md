# Copilot Instructions

## 建置與驗證指令

此倉庫已整理成標準 Hugo 結構，並使用 `themes/hugo-theme-bootstrap` 主題 submodule。站台內容放在 `content/`，靜態附件放在 `static/`，少量主題覆寫放在 `layouts/`。建置前先同步 submodule 並安裝 npm 相依。

### 全站建置

```bash
git submodule update --init --recursive && \
npm ci && \
hugo --minify
```

### 單頁聚焦檢查

目前沒有既有的自動化測試框架；最接近「單一測試」的做法，是直接建置一次，再檢查目標頁面是否有產出對應 HTML：

```bash
npm ci && \
hugo --minify && \
test -f public/events/cybersec_2026/beyond_prompt_attacks/index.html
```

### 測試與 lint

目前倉庫內沒有既有的測試或 lint 指令；內容與版型變更通常以 Hugo 建置成功作為主要驗證方式。

## 高階架構

- 內容來源分成四層：`content/_index.md` 是首頁、`content/events/_index.md` 是活動列表、`content/events/<活動>/_index.md` 是單一活動首頁、`content/events/<活動>/*.md` 是各議程／講題筆記。講題附檔（例如 PDF）對應放在 `static/events/<活動>/`。
- 站台樣式與頁面框架主要來自 `themes/hugo-theme-bootstrap`；GitHub Pages workflow 會先抓取 submodule、安裝 npm 相依，再於倉庫根目錄建置 Hugo 站台。
- 主題前端相依由 `hugo mod npm pack` 產生的 `package.json`、`package-lock.json` 與 `packages/hugoautogen/` 管理。
- `layouts/_default/_markup/render-link.html` 是關鍵：它會把 Markdown 內連到其他 `.md` 的相對連結轉成 Hugo `RelRef`，並把 `./slides.pdf` 這類相對附檔路徑轉成站內 URL。來源檔請保留相對路徑寫法，不要改成產出後的 URL。
- 目前站內保留的主題覆寫集中在 `layouts/partials/`，用來保留 H1 作為標題來源、避免單篇頁雙標題、讓 `showList: false` 繼續生效，並停用主題內對舊 Hugo API 不相容的作者 sidebar。

## 關鍵慣例

- 活動與清單頁幾乎都用 `_index.md` 搭配 YAML front matter，並設定 `title` 與 `showList: false`；清單內容不是依賴 Hugo 自動列子頁，而是直接在 Markdown 內手動維護議程連結順序。
- 單篇講題頁應同時保留 YAML front matter 的 `title` 與文件開頭的 `# ` 標題。前者提供主題的 SEO / 社群 metadata，後者保留正文可讀性與站內 H1；`page-title-text.html` 的標題優先序是：front matter `title` → 第一個 H1 → humanized 檔名。
- 主題或 npm 相依若有升版，先執行 `git submodule update --init --recursive` 與 `hugo mod npm pack`，再重新安裝 npm 相依；否則 Hugo 可能提示 npm dependencies out of sync。
- 全站語系與內容以繁體中文（台灣）為主，`hugo.toml` 設定 `defaultContentLanguage = "zh-tw"` 與 `locale = "zh-tw"`，產出的 HTML 會使用 `lang="zh-tw"`。新增頁面、導覽文字與 commit message 都應維持同樣語系。
- `hugo.toml` 已開啟 `markup.goldmark.renderer.unsafe = true`，代表 Markdown 中可用原生 HTML。若調整這個設定，要先確認既有內容不會因此失效。
- 若需要提交 commit，請遵循 `.github/git-commit-instructions.md`：commit message 使用繁體中文（台灣）的 Conventional Commits；變更較大時，在標題下補充條列摘要。
