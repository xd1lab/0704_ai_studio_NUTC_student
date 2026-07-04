# 下午 F · Excel／Google 試算表免程式碼批次處理（prompt 與公式）

> **使用時機**：下午壓軸。把上午學到的「分類 / 清洗」能力搬進 **Google 試算表**，示範「像拉公式一樣往下拉，一次處理整欄」。
> **情境**：財報數字清洗（去除全形符號、統一單位）與自動分類（把雜亂的「產業別」歸到「產業大類」；同一技巧亦可用於會計科目分類）。

---

## ⚠️ 先看：三條路徑總覽

「Google 試算表用 AI」的機制**因帳號方案（個人 Gmail / Google Workspace / 教育版）與地區而異**，同一步驟不一定人人可用。請先用自己的 Google 帳號確認下列三條路徑哪一條能用。

| 路徑 | 需要條件 | 穩定度 | 建議定位 |
|---|---|---|---|
| ① Workspace 內建 Gemini（側邊欄 / Help me organize） | 具備 Gemini 的 Google Workspace 方案 | 視方案 | 展示「零程式、內建 AI」概念 |
| ② `=AI()` 之類的 Sheets AI 函式 | 該帳號已開通此函式（逐步推廣中，非人人有） | 中，且各帳號差異大 | 若可用，最直覺、最有「拉公式」感 |
| ③ Apps Script 串接 Gemini API（自訂函式） | 任何帳號 + 一組 Gemini API Key | 高（最可控） | **主力備援**，一定能動 |

> **結論建議**：以 **③ Apps Script** 為主力（最可控、任何帳號都能動），②/① 若你的帳號可用則更省事。

---

## 一、建議參數（AI 端）

- 在 Apps Script 呼叫 Gemini 時，`temperature` 設 **0 ~ 0.2**：清洗與分類要**穩定、可重現**。
- 回應請要求「**只輸出結果字串，不要多餘說明**」，否則儲存格會被塞入整段解釋。

---

## 二、路徑 ①：Google Workspace 內建 Gemini（觀念示範）

**操作（實際位置以當天畫面為準）**：開啟 Google 試算表 → 右上角或側邊的 **Gemini** 圖示 → 在側邊欄用自然語言下指令，例如：

```
這一欄 B 是雜亂的產業別名稱，請幫我在 C 欄產生對應的「產業大類」，
分類只能是：科技電子、傳統製造、金融、服務民生、生技醫療；無法判斷填「待確認」。
```

- **優點**：完全零程式。
- **限制**：能力 / 可用性依 Workspace 方案而定；有時是「協助你在對話中整理」而非「逐格寫入公式」。

---

## 三、路徑 ②：`=AI()` 之類 Sheets AI 函式（若該帳號可用）

部分帳號已可在儲存格直接用類似函式（名稱與參數依 Google 當前版本為準，例如 `=AI(...)`）。用起來就像一般公式，**可以往下拉**。

**示意（請以你帳號實際支援的函式名稱與語法為準）**：

```
=AI("把這個產業別歸到產業大類：科技電子/傳統製造/金融/服務民生/生技醫療 其中之一，只回類別：" & B2)
```

往下拉填滿整欄，即可批次分類。

**財報數字清洗示意**：

```
=AI("將這個字串轉為純數字：移除逗號、全形符號、貨幣符號與『元/千元』等單位文字，只輸出阿拉伯數字，無法轉換就回空白：" & B2)
```

> ⚠️ **提醒**：`=AI()` 目前**並非所有帳號都有**，且推廣中、語法可能調整；帳號沒有就走路徑 ③。

---

## 四、路徑 ③：Apps Script 串接 Gemini API（主力備援，一定能動）

思路：寫一個**自訂函式** `GEMINI(prompt)`，之後在儲存格就能像 `=GEMINI(...)` 一樣呼叫並往下拉。需要一組 **Gemini API Key**（在 Google AI Studio 取得）。

### 步驟
1. Google 試算表 → 上方選單 **擴充功能（Extensions）→ Apps Script**。
2. 貼上下方程式碼，把 `YOUR_GEMINI_API_KEY` 換成你的金鑰（正式用途建議改用「指令碼屬性」保存，見下方安全提醒）。
3. 存檔、授權（第一次執行會要求授權）。
4. 回到試算表，用 `=GEMINI(...)` 呼叫。

### 最小可用範本（貼進 Apps Script）

```javascript
// ⚠️ 需要一組 Gemini API Key（在 Google AI Studio 取得）。
// 教學示範可先直接填；正式使用請改用「指令碼屬性」保存金鑰（見檔案下方安全提醒）。
const GEMINI_API_KEY = 'YOUR_GEMINI_API_KEY';

// 模型名稱請以 Google AI Studio 當前提供者為準（如 gemini-3.1-flash-lite、gemini-2.5-flash 等）。
const GEMINI_MODEL = 'gemini-2.5-flash';

/**
 * 自訂函式：在儲存格用 =GEMINI("你的提示 " & A2) 呼叫。
 * @param {string} prompt 要送給 Gemini 的完整提示文字
 * @return {string} 模型回覆（已去除前後空白）
 * @customfunction
 */
function GEMINI(prompt) {
  if (!prompt) return '';

  const url = 'https://generativelanguage.googleapis.com/v1beta/models/'
    + GEMINI_MODEL + ':generateContent?key=' + GEMINI_API_KEY;

  const payload = {
    contents: [{ parts: [{ text: String(prompt) }] }],
    generationConfig: {
      temperature: 0.1,        // 清洗/分類要穩定，用低溫
      maxOutputTokens: 256
    }
  };

  const options = {
    method: 'post',
    contentType: 'application/json',
    payload: JSON.stringify(payload),
    muteHttpExceptions: true
  };

  try {
    const res = UrlFetchApp.fetch(url, options);
    const code = res.getResponseCode();
    if (code !== 200) {
      return '錯誤(' + code + ')：' + res.getContentText().slice(0, 120);
    }
    const data = JSON.parse(res.getContentText());
    const text = data
      && data.candidates
      && data.candidates[0]
      && data.candidates[0].content
      && data.candidates[0].content.parts
      && data.candidates[0].content.parts[0]
      && data.candidates[0].content.parts[0].text;
    return text ? String(text).trim() : '（無回應）';
  } catch (e) {
    return '例外：' + e;
  }
}
```

> **配額提醒**：自訂函式若「整欄同時觸發」會瞬間送出大量請求，可能撞到 API 速率限制或逾時。示範時**先在少數幾格試**，再逐步往下拉；量大時考慮分批。

---

## 五、財報情境：具體公式 / 用法（可直接複製）

> 下列把「提示語」直接寫進儲存格，配合 `=GEMINI(...)`（路徑③）或 `=AI(...)`（路徑②）使用。假設原始資料在 **B 欄**，結果放 **C 欄**，於 `C2` 輸入後往下拉。

### 情境 1：財報數字清洗（去符號、統一為純數字）
`C2`：
```
=GEMINI("你是資料清洗助理。把下面字串轉為純數字：移除千分位逗號、全形數字轉半形、移除貨幣符號與『元、千元、NT$、$』等單位或文字，只輸出阿拉伯數字（可含小數點與負號），若本來就是空白或無法轉換則輸出空字串，不要加任何說明：" & B2)
```
輸入 `１,２３４,５６７ 元` → 輸出 `1234567`。

### 情境 2：產業別自動分類（受控類別，對應本 CSV 的「產業別」欄）

> 本 CSV 沒有會計科目欄，故示範「把細產業別歸到產業大類」；**同一招只要換掉類別清單，即可用於會計科目分類**（資產／負債／權益／收入／費用）。

`C2`：
```
=GEMINI("把下面的產業別歸到產業大類其中之一，只能回：科技電子、傳統製造、金融、服務民生、生技醫療；若無法判斷回『待確認』。只回類別、不要說明：" & B2)
```
輸入 `半導體` → `科技電子`；輸入 `觀光` → `服務民生`；輸入 `鋼鐵` → `傳統製造`。

### 情境 3：稅務屬性標註（自訂標籤）
`C2`：
```
=GEMINI("下面是一筆費用科目，請判斷在營利事業所得稅上通常屬於哪一類，只能回：可全額認列、有限額規定、原則不得認列、需個案判斷 其中之一，只回標籤不要說明：" & B2)
```
> ⚠️ 此為**教學示意**，實際稅務認列以現行法令與個案事實為準；輸出僅供初步分流，請人工覆核。

### 情境 4：一格產生多欄結果（進階，看帳號支援）
若使用支援表格輸出的函式，可要求「同時回產業大類與信心度，用逗號分隔」，再用「資料 → 分割文字為欄」拆開。若走 `=GEMINI`，較單純的作法是**每欄一條公式**（分類一欄、清洗一欄），最穩定。

---

## 六、安全提醒

- **API Key 是機密**：不要把含金鑰的試算表隨意分享。教學後請**撤換金鑰**，或正式使用時改用 Apps Script 的「**指令碼屬性（Script Properties）**」保存，例如：
  ```javascript
  const GEMINI_API_KEY = PropertiesService.getScriptProperties().getProperty('GEMINI_API_KEY');
  ```
  再到 Apps Script 的專案設定加入該屬性，避免金鑰寫死在程式碼裡。
- **敏感資料**：練習請用**去識別化 / 虛構**的財報資料，避免上傳真實納稅人 / 客戶個資到外部 API。
- **輸出要覆核**：AI 分類 / 清洗結果是**初稿**，金額與稅務判斷務必人工核對。

---

## 七、常見坑（故障排除）
- **坑 1：金鑰沒填 / 填錯** → 儲存格顯示 `錯誤(400/403)`。檢查金鑰、模型名稱、是否已授權。
- **坑 2：模型名稱過期** → Google 模型名稱會更新。以 **AI Studio 當前提供的模型**為準（如 `gemini-3.1-flash-lite` / `gemini-2.5-flash`）。
- **坑 3：整欄一次觸發撞配額 / 逾時** → 先少量測試再往下拉；量大分批。
- **坑 4：`=AI()` 帳號沒有** → 這是最常見的狀況；沒有就走 ③。
- **坑 5：輸出夾帶說明文字** → 提示語務必寫「只回結果、不要說明」，並用低溫。
- **坑 6：自訂函式回傳不即時更新** → Apps Script 自訂函式不會像內建函式即時重算，改動來源後可重新輸入公式或重整。
- **坑 7：把 AI 清洗結果當定案** → 金額、稅務分類要人工覆核，AI 產出是加速用的初稿。
