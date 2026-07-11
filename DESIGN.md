---
title: 三民國中 AI 半導體研習營 — 互動教學網頁設計規範
owner: 陳泓光
theme: dark, interactive, teaching
version: 1.0
targets: [github-pages, claude-artifact]
tokens:
  base_font_px: 16
  body_font_px: 17
  type_scale_ratio: 1.25
  spacing_base_px: 8
  radius_scale_px: [4, 8, 12, 9999]
  accents: [ai-青/teal, eda-琥珀/amber, game-綠/green]
  contrast_targets: { body: "4.5:1", large: "3:1", nontext: "3:1" }
---

# DESIGN.md — 深色互動教學網頁設計規範

這份文件是全站唯一的設計真實來源（single source of truth）。所有頁面（GitHub Pages 教材、claude.ai Artifact 示範）都照這裡的 token 與規則產出。**規範的目的是「限制」**：先定義能用什麼、禁用什麼，一致性本身就是專業感的來源。改了這份文件就要同步 code；過時的規範等於沒有規範。

---

## 1. 設計原則（先讀這五條）

1. **系統先於裝飾。** 先把四張表定死——色階、字級、間距、圓角——UI 一律只用語意 token，禁止臨時取 `#hex`、`13px`、`22px` 這種一次性數值。克制勝過堆料。
2. **每個決定都要能回答「為什麼是這個值」。** 用機械規則寫下來（明確 hex、字型名、比例、間距），而不是「做得更 premium 一點」這種形容詞。散文式指示比不給還糟。
3. **深色靠層次，不靠發光。** elevation 用「表面越高越亮＋細邊框」表達，**禁止彩色 glow、禁止紫藍漸層 hero、禁止 cyan-on-dark 螢光**。這是最容易露餡的 AI 指紋。
4. **一個系統貫穿全站。** 單一卡片系統、單一字型家族、單一間距模數。三個單元（AI／EDA／遊戲）**只切換 `--accent`，中性系統完全不動**——這樣既有主題辨識度，又是同一套系統。
5. **內容為王的閱讀體驗。** 這是教學網頁，不是 landing page。CJK 行高/行長、對比、左對齊優先；動效只用來「揭露一個步驟、示範一個結果」，不為動而動。

---

## 2. 色彩

### 2.1 中性色階（帶冷色相偏移，全站共用）

深色不用純黑。基底用近 `#0d1117`（帶一點藍味的深灰），中性灰整條往藍相（H≈218）偏、低飽和，避免死灰。所有中性都從這 13 階取，**禁止用 `lighten()/darken()` 即時算色**。

```css
:root{
  /* Neutral — 越上層越亮＝越靠近使用者（用亮度做 elevation） */
  --n-0:#0d1117;  /* app 背景（不是純黑） */
  --n-1:#12171f;
  --n-2:#171d26;  /* surface-1：卡片、面板 */
  --n-3:#1e2530;  /* surface-2：巢狀區塊、表格斑馬列、程式碼區 */
  --n-4:#27303c;  /* surface-3：dropdown / popover / modal */
  --n-5:#323c4a;  /* border-strong */
  --n-6:#3f4a5a;
  --n-7:#566172;
  --n-8:#6e7a8a;  /* text-muted（僅次要、非關鍵、或大字） */
  --n-9:#8b97a6;
  --n-10:#a8b3c1; /* text-secondary */
  --n-11:#c7cfd9;
  --n-12:#e6eaf0; /* text-primary（off-white，不是純白，降眩光/halation） */
}
```

### 2.2 語意 token（UI 只准用這一層）

```css
:root{
  --bg:            var(--n-0);
  --surface-1:     var(--n-2);
  --surface-2:     var(--n-3);
  --surface-3:     var(--n-4);

  --text-primary:  var(--n-12);
  --text-secondary:var(--n-10);
  --text-muted:    var(--n-8);   /* 對比 ~4.5:1 邊界，只給非關鍵/大字 */

  --border-subtle: var(--n-3);   /* 分隔線、卡片內細線 */
  --border-default:var(--n-4);   /* 卡片、輸入框外框 */
  --border-strong: var(--n-5);   /* 強調邊界、focus 底線 */
}
```

**規則：頁面裡永不出現 primitive（`--n-*`）與生 hex，一律用語意 token。** 換底色只改一處。

### 2.3 主色系統：三單元 = 三個可互換的 accent

陳泓光現有三套主色（AI 青、EDA 琥珀、遊戲綠）不是三套設計，而是**同一個系統的三個 accent 值**。做法：每頁在 `<html>` 或單元容器上掛 `data-theme`，只重映射 `--accent-*`；中性、字級、間距、圓角、陰影全部不變。

> 為什麼這樣就不 AI 味：AI slop 是「每個元件各有花樣」。這裡反過來——**主色只用在互動與主要元素（主按鈕、連結、選取態、資料強調），介面 ~90% 由中性灰撐起**。需要強調時用 accent 的實心色，而不是漸層或 glow。

```css
/* 預設 = AI 青（teal，刻意避開霓虹 cyan） */
:root, [data-theme="ai"]{
  --accent-solid:  #127a6a;  /* 主按鈕/主要實心塊（白字 5.2:1 過關） */
  --accent-hover:  #1fa08b;
  --accent-text:   #3fd0bb;  /* 連結、深底上的強調文字 */
  --accent-bg:     #0f2e2a;  /* 極淡的 accent 底（callout、選取列） */
  --accent-border: #265a52;  /* accent 邊框、focus ring */
  --accent-contrast:#ffffff; /* 疊在 --accent-solid 上的文字色 */
}
[data-theme="eda"]{           /* EDA 琥珀（amber 是亮色階 → 上面用深字） */
  --accent-solid:  #e0a72c;
  --accent-hover:  #f0b93c;
  --accent-text:   #f0c05a;
  --accent-bg:     #332708;
  --accent-border: #6a5320;
  --accent-contrast:#1a1204;  /* 近黑，疊在亮琥珀上 */
}
[data-theme="game"]{          /* 遊戲綠（刻意偏草綠 H≈146，避開 emerald「safe green」slop） */
  --accent-solid:  #1f7a4f;   /* 白字 5.3:1 過關 */
  --accent-hover:  #2ea86a;
  --accent-text:   #57c184;
  --accent-bg:     #123425;
  --accent-border: #2c6043;
  --accent-contrast:#ffffff;
}
```

**避開紫藍漸層 AI 味的四條硬規則：**
- 禁 `#6366F1 → #8B5CF6` 這類 indigo/violet 漸層，禁 hero 上的彩色 box-shadow glow。要層次就用 §2.1 的亮度階。
- 被禁紫之後**不要逃到 emerald**（那是模型第二個 safe 預設）；遊戲綠刻意選草綠 H≈146 而非 emerald H≈160。
- 漸層若真要用，只當「刻意的少量點綴」（例如一條 1px accent 分隔線），不是預設塗層。
- 主色在深底上一律**降飽和**（比淺色主題低約 20 點），高飽和會「視覺震動」與暈光。

### 2.4 功能狀態色（與主題無關，用於驗證/callout，少量）

```css
:root{
  --danger:#e5675f;  --danger-bg:#3a1a18;  --danger-border:#6e2f2b;
  --warning:#e0a13a; --warning-bg:#332708; --warning-border:#6a5320;
  --success:#3fb37f; --success-bg:#123425; --success-border:#2c6043;
  --info:#4f9dda;    --info-bg:#132a3d;     --info-border:#2a4f6e;
}
```
注意：當某頁主題是 **EDA 琥珀** 時，`--warning` 與主色同色相會混淆——該頁的 warning 改用「圖示＋文字」區分，不只靠顏色。狀態一律「顏色＋圖示＋文字」三重線索，別只靠顏色（無障礙）。

### 2.5 對比驗證（不可略）
每一組「前景 / 背景」都要用 WebAIM Contrast Checker 實測，別靠肉眼：內文 ≥ 4.5:1、大字（≥18px 或 14px 粗、CJK 等效）≥ 3:1、非文字（邊框/圖示/圖表色塊）≥ 3:1。按鈕文字比照內文用 **4.5:1**（不套「大字 3:1」——CJK／17px 未達大字門檻）。已實測：AI `#127a6a` 5.2:1、Game `#1f7a4f` 5.3:1、EDA 近黑字疊琥珀 8.6:1，皆過關。改色後務必重跑 WebAIM。

---

## 3. 字型

### 3.1 兩套 font stack：GitHub webfont vs Artifact 系統字型

這是本專案的關鍵差異，**兩個部署目標的 `font-family` 不同**：

| 目標 | 能否載入 webfont | 字型策略 |
|---|---|---|
| **GitHub Pages** | 可以（`@font-face` / Google Fonts） | 用有辨識度的超級字族：**IBM Plex Sans + IBM Plex Sans TC + IBM Plex Mono**。單一家族＝天然和諧，且有繁中與等寬成員，並刻意避開已成新 slop 的 Inter / Geist / Space Grotesk。 |
| **claude.ai Artifact** | **不行**（CSP 封鎖外部字型/CDN，一切要 inline） | **只能用系統字型**。用系統 sans + 繁中 fallback；程式碼用系統 mono。不要試圖 `@import` webfont（會被擋，靜默 fallback 反而更亂）。 |

```css
/* GitHub Pages（可載 webfont） */
:root{
  --font-sans: "IBM Plex Sans", "IBM Plex Sans TC", "Noto Sans TC",
               system-ui, sans-serif;
  --font-mono: "IBM Plex Mono", ui-monospace, "Cascadia Code",
               Consolas, "Noto Sans TC", monospace;
}
/* claude.ai Artifact（只能系統字型） */
:root[data-runtime="artifact"]{
  --font-sans: system-ui, -apple-system, "Segoe UI", Roboto,
               "PingFang TC", "Microsoft JhengHei", "Noto Sans TC", sans-serif;
  --font-mono: ui-monospace, "SF Mono", "Cascadia Code", Consolas,
               "PingFang TC", "Microsoft JhengHei", monospace;
}
```

繁中注意：
- 一律用 **TC 地區變體**（PingFang TC / Microsoft JhengHei / Noto Sans TC / IBM Plex Sans TC），**切勿用 SC**，字形與台灣標準不同。用 `lang="zh-Hant"` 觸發正確字型。
- **中西混排**：西文字型放前、CJK 放後，讓數字/英文走拉丁字型、漢字落到 CJK。混排時西文常顯偏小，選 x-height 較高的西文字型即可視覺等高。
- GitHub Pages 用 Noto Sans TC 時，Google Fonts 會自動切片（把 5–20MB 壓到每頁數百 KB），加 `font-display: swap`。目標每頁 CJK < 500KB。
- 中文不用斜體、不依賴瀏覽器合成假粗/假斜（用字型自帶的 400/500/700 字重）。一般不對漢字加 `letter-spacing`；真要加必須同步加大行高。

### 3.2 等比 type scale（比例 1.25，Major Third）

以 root 16px 為基準，內文刻意設 **17px** 增進 CJK 可讀性。全站字級只從這張表取，禁止隨手打不在刻度上的值。

```css
:root{
  --text-caption: 0.8125rem; /* 13px  圖說、標籤、表格次要 */
  --text-sm:      0.9375rem; /* 15px  輔助說明 */
  --text-body:    1.0625rem; /* 17px  內文（CJK 舒適下限） */
  --text-lead:    1.25rem;   /* 20px  導言 / h4 */
  --text-h3:      1.5rem;    /* 24px */
  --text-h2:      1.9375rem; /* 31px */
  --text-h1:      2.4375rem; /* 39px */
  --text-display: 3.0625rem; /* 49px  單元封面大標，慎用 */
}
```

字重層級（四級，避免無限加大）：
```css
:root{
  --fw-regular:400;  /* 內文 */
  --fw-medium:500;   /* UI、次強調 */
  --fw-semibold:600; /* 主按鈕、標籤 */
  --fw-bold:700;     /* 標題、關鍵強調 */
}
```
**層級優先用「字重＋顏色」而非一路放大字級**，也**不要用 emoji 當標題撐重點**。

### 3.3 行高與行長

```css
:root{
  --lh-tight:1.2;   /* h1/h2 大標 */
  --lh-heading:1.3; /* h3/h4 */
  --lh-body:1.75;   /* CJK 內文（筆畫密，需比拉丁 1.5 更鬆） */
  --lh-code:1.6;
}
```
- **行長**：CJK 每行約 35–40 全形字上限。內文容器一律加 `max-width`（見 §4）。行太長回行困難、太短打斷節奏。
- 長文一律**左對齊**；**置中只留給單元封面短標題或單句英雄語**。整段內文置中是最典型的業餘/模板訊號。

---

## 4. 間距與版面

### 4.1 8pt 間距系統

單一基準 8px（含 4px 半階），所有 `margin / padding / gap` 只從這組取，杜絕 `13px`、`22px`。

```css
:root{
  --space-1:4px;  --space-2:8px;   --space-3:12px; --space-4:16px;
  --space-5:24px; --space-6:32px;  --space-7:48px; --space-8:64px;
  --space-9:96px;
}
```
用間距表達關係（proximity）：**群組內部間距小、群組之間間距大**；標題貼近它所屬段落、遠離上一段。消除「模稜兩可的間距」——任何空白都要看得出誰屬於誰。留白是刻意排的材料，不是元素之間剩下的空洞。

### 4.2 內容寬度（分層，不是全部滿版）

```css
:root{
  --w-prose:  42em;    /* ~680px 純文字說明欄（≈35–40 CJK 字/行） */
  --w-content:960px;   /* 一般內容＋側欄 */
  --w-wide:   1200px;  /* 資料表、儀表板 */
  --w-full:   100%;    /* 全幅互動畫布/示範 */
}
```
文字閱讀區用 `--w-prose` 置中留白；互動示範才用 `--w-wide/full`。**不要為了填滿螢幕硬撐滿版。**

### 4.3 版面骨架
- 用不對稱、明確的網格構圖，避免「置中大標＋一顆 CTA＋下面三個 icon 卡」的 slop 骨架。
- 深色教學頁若文字量大，考慮**深色殼＋較亮的閱讀區塊**（`--surface-1`）承載長文，並把內文對比、字級、行高拉足——純深色不利大量文字。

---

## 5. 圓角與陰影（elevation）

```css
:root{
  --radius-sm:4px;   /* 行內 code、tag、輸入框 */
  --radius-md:8px;   /* 按鈕、小卡 */
  --radius-lg:12px;  /* 卡片、面板（上限，別到 24px+ 變 soft blob） */
  --radius-full:9999px; /* pill / 頭像，少量 */

  /* 深色優先用「表面提亮＋邊框」造深度；陰影低透明、帶 y-offset、絕不彩色 */
  --shadow-sm:0 1px 2px rgba(0,0,0,.40);
  --shadow-md:0 4px 12px rgba(0,0,0,.45);
  --shadow-lg:0 12px 32px rgba(0,0,0,.50);
}
```
- **elevation 階**：bg → surface-1（+border-subtle）→ surface-2（+border-default）→ surface-3（+`--shadow-md`，給 dropdown/modal）。貼近表面用 border，越浮起才加陰影。
- 圓角按元件語意分配，**不要每個元素都套同一個 16px 圓角＋同一片 0.1 淡影**。

---

## 6. 元件

### 6.1 卡片（單一系統，殺掉左色條）
- **全站只有一種卡片**：`--surface-1` 底 + `--border-default` 1px + `--radius-lg` + `--space-5/6` padding。
- **禁止卡片左邊 3–4px 彩色直條**（公認最可靠的 AI tell）。要標示單元就用一致的頂邊 1px accent 線、或整塊 `--accent-bg` 底、或乾脆只靠標題——**選一種貫穿到底**。
- 禁止「icon 置頂＋標題＋一段字」的整齊複製卡陣。層級用留白、字重、細分隔線做，不用彩色裝飾。

```css
.card{
  background:var(--surface-1); border:1px solid var(--border-default);
  border-radius:var(--radius-lg); padding:var(--space-5);
}
```

### 6.2 按鈕（不是每顆都填色）
```css
.btn{font:var(--fw-medium) var(--text-body)/1 var(--font-sans);
     border-radius:var(--radius-md); padding:var(--space-3) var(--space-4);
     transition:background var(--dur-fast) var(--ease-standard);}
.btn-primary  {background:var(--accent-solid); color:var(--accent-contrast); font-weight:var(--fw-semibold);}
.btn-primary:hover{background:var(--accent-hover);}
.btn-secondary{background:var(--surface-2); color:var(--text-primary); border:1px solid var(--border-default);}
.btn-ghost    {background:transparent; color:var(--accent-text);}
```
一個畫面通常只有一顆 primary（accent 實心），其餘用 secondary/ghost。**別把每個按鈕都填成主色。**

### 6.3 表格（EDA 資料尤其用得到）
- 用**水平分隔線**（`--border-subtle`），不要重網格；斑馬列用 `--surface-2`（可選）。
- **數字右對齊、用 `--font-mono`**；表頭 `--fw-medium`、可 sticky。
- padding 取自間距刻度（`--space-3`）。不要左色條、不要每列圓角。

### 6.4 程式碼區（AI/EDA 內容核心）
```css
.code-block{background:var(--surface-2); border:1px solid var(--border-subtle);
  border-radius:var(--radius-md); padding:var(--space-4);
  font:var(--text-sm)/var(--lh-code) var(--font-mono);}
:not(pre) > code{background:var(--surface-2); border-radius:var(--radius-sm);
  padding:2px 6px; color:var(--text-primary);}
```
語法高亮用**克制的低飽和配色**（呼應中性系統），不要霓虹。行內 code 與區塊 code 同一套視覺語言。

### 6.5 圖示
用一套**同 stroke/同尺寸網格的線性 icon**（如 Octicons/Lucide 風格），尺寸對齊間距模數。**禁止用彩色 emoji 當章節標記或導覽圖示**——那是最直接的生成痕跡。

---

## 7. 動效

```css
:root{
  --dur-fast:120ms; --dur-base:200ms; --dur-slow:320ms;
  --ease-standard:cubic-bezier(.2,0,0,1);   /* 進場/一般 */
  --ease-out:cubic-bezier(0,0,.2,1);        /* hover/離場 */
}
```
- **有理由才動**：微互動只放在主要 CTA、表單、狀態切換、「示範一個步驟/揭露一個結果」。互動教學的動效要服務理解，不是裝飾。
- hover 用 smooth `ease-out`，**不要彈跳（bounce）**、不要每個元素同一套 fade-in、不要 hover 圖片放大。
- **尊重 `prefers-reduced-motion`**：
```css
@media (prefers-reduced-motion: reduce){
  *{animation-duration:.01ms !important; animation-iteration-count:1 !important;
    transition-duration:.01ms !important; scroll-behavior:auto !important;}
}
```

---

## 8. 無障礙

- **對比**：內文 ≥ 4.5:1、大字/CJK 等效 ≥ 3:1、非文字元素 ≥ 3:1。每個 token 對都跑 WebAIM。
- **焦點樣式**：永不 `outline:none` 而不補。用可見 focus ring：
```css
:focus-visible{outline:2px solid var(--accent-text); outline-offset:2px; border-radius:inherit;}
```
- **鍵盤**：所有互動元素可 Tab 到達、有可見焦點；**互動小遊戲/示範必須可純鍵盤操作**（方向鍵/Enter/Space），並在頁面標示操作鍵。
- **觸控目標** ≥ 44×44px。
- 支援 `prefers-color-scheme` 與 `prefers-reduced-motion`；資訊不可只靠顏色（配文字/圖示/形狀）。

---

## 9. AI 味檢查清單（發佈前逐條核對，全部要「否」）

**視覺**
- [ ] 有紫/靛藍漸層 hero（`#6366F1→#8B5CF6` 類）？
- [ ] 深底上有彩色 glow / cyan 螢光 / 發光 box-shadow？
- [ ] 用純黑底（#000）或純白細字？
- [ ] 卡片左邊有 3–4px 彩色直條？
- [ ] 「icon 置頂＋標題＋一段字」的整齊複製卡陣？
- [ ] 圓角 24px+ 變 soft blob，或全站齊頭同一圓角/同一淡影？
- [ ] 玻璃擬態 frosted glass 純裝飾？
- [ ] 逃到 emerald 當「safe green」？
- [ ] 改用暖米＋襯線＋赭紅那套——那只是另一個 AI 預設，不算脫離。

**字型**
- [ ] 全站無腦 Inter（或 Geist / Space Grotesk）？
- [ ] 只有一個排版決定、看不出刻意配過字？
- [ ] 用 emoji 當標題/章節/導覽 icon？
- [ ] 繁中誤用 SC 字形？

**版面**
- [ ] 「置中大標＋一顆 CTA＋三個 icon 卡」骨架？
- [ ] 整段內文置中？
- [ ] 全大寫 eyebrow（前一點、後拖細線）、H1 上方 badge、全大寫 section label？

**互動 / 文案**
- [ ] 每個元素同一套 fade-in、對話框 bounce、hover 放大？
- [ ] 空話標語（Build the future / all-in-one / supercharge / world-class）？
- [ ] 連續 em-dash 濫用、一句格言收尾？自問「我（講師）本人真的會這樣講嗎」。

---

## 10. 落地順序（給後續每一頁）

1. 貼上 §2–§7 的 `:root` token（依 GitHub / Artifact 選對 `--font-*`）。
2. 每個單元頁掛 `data-theme="ai|eda|game"`，其餘全部共用。
3. 先用中性灰把間距、對比、字重、層級做對，**最後才加最少量的主色**。
4. 發佈前跑 §9 檢查清單＋WebAIM 對比實測。

> 一句話總結：脫離 AI 感的關鍵不是「換一組更好看的預設」，而是每個決定都能回答「為什麼是這個值」——機械規則、單一系統貫穿全站、留白與層次刻意排布。克制勝過堆料。