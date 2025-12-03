# TWA Android App Project Guide (TWA Android 應用程式專案指南)

本指南旨在協助 AI Agent 理解如何利用 **Temple** 技術，將現有的網頁應用程式 (Web App) "https://ai-temple.vercel.app/" 轉換為 **Android 應用程式 (Android App)**。

## 1. 專案目標 (Project Goal)

將 "AI Temple" 網站封裝為一個原生的 Android App，並發布到 Google Play Store。這將使用 Google 推薦的 **Bubblewrap** 工具來生成 TWA 專案。

## 2. 前置需求 (Prerequisites)

在開始之前，請確保開發環境已安裝以下工具：

*   **Node.js**: 版本 12 或以上 (建議使用 LTS 版本)。
*   **Java Development Kit (JDK)**: 版本 8 或 11。
*   **Android Studio**: 用於安裝 **Android SDK** 和管理 **Android Virtual Device (AVD)** (模擬器)。
*   **Bubblewrap CLI**: Google 官方提供的 TWA 生成工具。

### 安裝 Bubblewrap CLI
在終端機 (Terminal) 執行以下指令：
```bash
npm install -g @bubblewrap/cli
```

## 3. 製作流程 (Implementation Steps)

### 步驟一：初始化專案 (Initialize Project)

1.  建立一個新的資料夾用於存放 Android 專案檔案。
2.  在該資料夾中開啟終端機。
3.  執行初始化指令，並指定網頁的 **Web App Manifest** URL：

```bash
bubblewrap init --manifest https://ai-temple.vercel.app/manifest.json
```

**注意 (Note)**: 如果網站沒有 `manifest.json`，Bubblewrap 會詢問相關資訊以手動建立。

### 步驟二：配置應用程式資訊 (Configure App Details)

Bubblewrap 會詢問一系列問題，請依據需求回答：

*   **Domain**: `ai-temple.vercel.app`
*   **Application Name**: AI Temple (或您想要的 App 名稱)
*   **Short Name**: AI Temple
*   **Application ID (Package Name)**: 例如 `com.aitemple.twa` (這是 App 的唯一識別碼)
*   **Display Mode**: `standalone` (全螢幕體驗)
*   **Status Bar Color**: 選擇與網站主題搭配的顏色
*   **Icon**: 確保 Manifest 中有提供 512x512 的圖示，或在設定過程中提供。
*   **KeyStore**: 如果是第一次建立，Bubblewrap 會協助建立一個新的簽署金鑰 (Signing Key)。**請務必妥善備份 KeyStore 檔案和密碼！**

### 步驟三：建置 APK/AAB (Build APK/AAB)

配置完成後，執行以下指令來建置 App：

```bash
bubblewrap build
```

這將會產生：
*   `app-release-signed.apk`: 用於直接安裝到手機測試的安裝檔。
*   `app-release-bundle.aab`: 用於上傳到 Google Play Console 的發布檔。

## 4. 驗證與關聯 (Verification & Association)

為了讓 TWA 正常運作（不顯示瀏覽器網址列），必須建立 App 與網站之間的信任關聯。

### Digital Asset Links (數位資產連結)

1.  在建置過程中，Bubblewrap 會顯示一段 **Digital Asset Links** 的資訊 (SHA-256 Fingerprint)。
2.  您也可以使用 `keytool` 指令查看 KeyStore 的指紋。
3.  建立一個名為 `assetlinks.json` 的檔案，內容如下 (請替換為您的實際 Package Name 和 SHA-256 指紋)：

```json
[
  {
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      "namespace": "android_app",
      "package_name": "com.aitemple.twa",
      "sha256_cert_fingerprints": [
        "YOUR_SHA256_FINGERPRINT_HERE"
      ]
    }
  }
]
```

4.  將此檔案上傳到網站的 `.well-known` 目錄下，使其可以通過以下網址存取：
    `https://ai-temple.vercel.app/.well-known/assetlinks.json`

## 5. 測試 (Testing)

1.  將 Android 手機連接到電腦，並開啟 **開發者模式 (Developer Mode)** 和 **USB 偵錯 (USB Debugging)**。
2.  執行以下指令安裝 App：
    ```bash
    bubblewrap install
    ```
3.  在手機上開啟 App。如果 `assetlinks.json` 設定正確，App 應該會以全螢幕模式開啟，不會顯示瀏覽器網址列。

## 6. 常見問題 (FAQ)

*   **Q: 為什麼還看得到網址列？**
    *   A: 請檢查 `assetlinks.json` 是否正確部署，且 SHA-256 指紋是否與簽署 APK 的 KeyStore 相符。瀏覽器快取也可能導致驗證延遲。
*   **Q: 如何更新 App？**
    *   A: 修改 `twa-manifest.json` 中的 `appVersionName` 和 `appVersionCode`，然後再次執行 `bubblewrap build`。

---
**給 AI Agent 的指令 (Instructions for AI Agent):**
當您被要求執行此專案時，請依照上述步驟，協助使用者檢查環境、執行 Bubblewrap 指令，並指導使用者完成 `assetlinks.json` 的部署。
