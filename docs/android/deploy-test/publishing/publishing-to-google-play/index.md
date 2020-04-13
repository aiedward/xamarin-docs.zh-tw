---
title: 發行至 Google Play
ms.prod: xamarin
ms.assetid: FB1CC234-3554-8566-48BD-2B9B3A28CC7F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 04e83fc68218216fe36cce67e43b83e8ad8feaa5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "80070670"
---
# <a name="publishing-to-google-play"></a>發行至 Google Play

雖然有許多應用程式市場可供散發應用程式，但 Google Play 可說是 Android 應用程式世界中最大且瀏覽頻率最高的商店。 Google Play 提供一個可散發、廣告、銷售 Android 應用程式及分析這些應用程式銷售情況的單一平台。

本節將涵蓋 Google Play 專屬的主題，例如註冊成為發行者、收集資產以協助 Google Play 宣傳和廣告您的應用程式、在 Google Play 上為應用程式評分的指導方針，以及使用篩選來限制將應用程式部署至特定裝置。

## <a name="requirements"></a>需求

若要透過 Google Play 散發應用程式，必須建立開發人員帳戶。 此動作只需執行一次，需要支付單次費用 $25 美元。

所有應用程式都必須以 2033 年 10 月 22 日之後到期的密碼編譯金鑰簽署。

在 Google Play 上發行的 APK 大小上限為 100MB。 如果應用程式超出該大小，Google Play 將會允許透過「APK 擴充檔」** 提供額外的資產。 Android 擴充檔可允許 APK 有 2 個額外的檔案，每個檔案的大小上限為 2GB。 Google Play 會免費裝載並散發這些檔案。 擴充檔將在另一節中討論。

Google Play 並非全球都可使用。 部分地點不是支援的應用程式散發地點。

## <a name="becoming-a-publisher"></a>成為發行者

若要在 Google Play 上發行應用程式，必須要有發行者帳戶。 若要註冊發行者帳戶，請依照下列步驟：

1. 前往 [Google Play 開發人員控制台](https://play.google.com/apps/publish)。
1. 輸入您開發人員身分識別的相關基本資訊。
1. 閱讀並接受適用於您地區設定的「開發人員發佈協議」。
1. 支付 $25 美元註冊費。
1. 透過電子郵件確認驗證。
1. 建立帳戶之後，就可以使用 Google Play 來發行應用程式。

Google Play 並未支援全球所有國家/地區。 最新的國家名單可在以下連結中找到:

1. [支援的開發者&amp;商家註冊](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=150324)&ndash;位置 這是所有開發人員可以註冊為商家並出售付費應用程式的國家/地區的清單。

1. [支援向 Google Play 使用者發佈商品的地點](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=138294) &ndash; 這是可散發應用程式的所有國家/地區清單。

### <a name="preparing-promotional-assets"></a>準備宣傳資產

為了在 Google Play 上有效宣傳和廣告應用程式，Google 允許開發人員提交宣傳資產，例如螢幕擷取畫面、圖形及影片。 然後 Google Play 會使用這些資產來廣告和宣傳應用程式。

#### <a name="launcher-icons"></a>啟動器圖示

「啟動器圖示」** 是代表應用程式的圖形。 每個啟動器圖示應該為 32 位元的 PNG，並且具有代表透明度的 Alpha 色板。 應用程式的圖示應該適用於一般化的螢幕密度，如以下清單所述：

- **ldpi** (120dpi) &ndash; 36 x 36 px
- **mdpi** (160dpi) &ndash; 48 x 48 px
- **hdpi** (240dpi) &ndash; 72 x 72 px
- **xhdpi** (320dpi) &ndash; 96 x 96 px

啟動器圖示是使用者在 Google Play 上所看到第一個應用程式相關項目，因此應該細心設計，讓啟動器圖示既具視覺吸引力又有意義。

啟動器圖示秘訣：

1. **簡單整潔的**&ndash;啟動器圖示應保持簡單和整潔。 這意謂著從圖示中排除應用程式名稱。 較簡單的圖示較容易記住，且大小較小時也較容易辨識。

1. **圖示不應該是薄**&ndash;過度薄的圖示不會在所有背景上脫穎而出。

1. **使用 Alpha 通道**&ndash;圖示應使用 Alpha 通道,並且不應是全幀圖像。

#### <a name="high-resolution-application-icons"></a>高解析度應用程式圖示

Google Play 上的應用程式需要高畫質版本的應用程式圖示。 此圖示僅供 Google Play 使用，且不會取代應用程式啟動器圖示。 高解析度圖示的規格為：

1. 含 Alpha 色板的 32 位元 PNG
1. 512 x 512 像素
1. 大小上限為 1024 KB

[Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/) 是一個用來建立適當啟動器圖示和高解析度應用程式圖示的實用工具。

#### <a name="screenshots"></a>螢幕擷取畫面

Google 播放需要至少 2 個和最多 8 個應用程式的螢幕截圖。 它們將顯示在 Google Play 中應用程式的詳細資訊頁面上。

螢幕擷取的規格包括:

1. 無 Alpha 色板的 24 位元 PNG 或 JPG
1. 320 (寬) x 480 (高) 或 480 (寬) x 800 (高) 或 480 (寬) x 854 (高)。 橫向影像將受到裁剪。

#### <a name="promotional-graphic"></a>宣傳圖形

這是 Google Play 所使用的選用影像：

1. 這是無 Alpha 色板的 180 (寬) x 120 (高) 24 位元 PNG 或 JPG。
1. 美工圖案中無框線。

#### <a name="feature-graphic"></a>精選圖形

Google Play 精選區段所使用的圖形。 此圖形可以不搭配應用程式圖示來單獨顯示。

1. 無 Alpha 色板和透明度的 1024 (寬) x 500 (高) PNG 或 JPG。
1. 所有重要內容應該都放在 924x500 的框架內。 此框架外的像素可能因造型目的而受到裁剪。
1. 此圖形可以縮小：請使用大型文字並讓圖形保持簡單。

#### <a name="video-link"></a>影片連結

這是展示應用程式的 YouTube 影片 URL。 影片長度應該為 30 秒到 2 分鐘，並展示應用程式最精彩的部分。

### <a name="publishing-to-google-play"></a>發行至 Google Play

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Xamarin Android 7.0 導入整合式工作流程，可從 Visual Studio 將應用程式發行至 Google Play。 如果您使用 7.0 版之前的 Xamarin Android，就必須透過「Google Play 開發人員控制台」手動上傳您的 APK。 此外，您必須至少已上傳一個 APK，才能使用整合式工作流程。 如果您尚未上傳您的第一個 APK，就必須手動上傳它。 如需詳細資訊，請參閱[手動上傳 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。

[建立新的憑證](~/android/deploy-test/signing/index.md#newcert)說明如何建立新的憑證來簽署 Android 應用程式。 下一步是將已簽署的應用程式發行至 Google Play：

1. 登入您的 Google Play 開發人員帳戶，以建立連結到您 Google Play 開發人員帳戶的新專案。
2. 建立驗證您應用程式的「OAuth 用戶端」****。
3. 將產生的「用戶端識別碼」和「用戶端密碼」輸入至 Visual Studio。
4. 向 Visual Studio 註冊您的帳戶。
5. 使用您的憑證來簽署應用程式。
6. 將已簽署的應用程式發行至 Google Play。

在[封存以供發行](~/android/deploy-test/release-prep/index.md#archive)中，[散發通道]**** 對話方塊顯示了兩個用於散發的選項：[臨機操作]**** 和 [Google Play]****。 如果改為顯示 [簽署身分識別]**** 對話方塊，請按一下 [上一步]**** 以返回 [散發通道]**** 對話方塊。 選擇**Google 播放**:

[![分發通路對話框](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

在 [簽署身分識別]**** 對話方塊中，選取在[建立新的憑證](~/android/deploy-test/signing/index.md#newcert)中建立的身分識別，然後按一下 [繼續]****：

[![簽署識別對話框](images/vs/02-select-identity-sml.png)](images/vs/02-select-identity.png#lightbox)

在 [Google Play 帳戶]**** 對話方塊中，按一下 [+]**** 按鈕以新增「Google Play 帳戶」：

[![谷歌播放帳戶對話框](images/vs/03-google-play-accounts-sml.png)](images/vs/03-google-play-accounts.png#lightbox)

在 [註冊 Google API 存取]**** 對話方塊中，您必須提供「用戶端識別碼」__ 和「用戶端密碼」__ 以提供對您 Google Play 開發人員帳戶的 API 存取權：

[![註冊 Google API 存取對話框](images/vs/04-register-google-api-access-sml.png)](images/vs/04-register-google-api-access.png#lightbox)

下一節說明如何建立新的 Google API 專案並產生所需的「用戶端識別碼」__ 和「用戶端密碼」__。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

Visual Studio for Mac 具有可將應用程式發行至 Google Play 的整合式工作流程。

[建立新的憑證](~/android/deploy-test/signing/index.md#newcert)說明如何建立新的憑證來簽署 Android 應用程式。 下列步驟簡述如何將 Xamarin.Android 應用程式發行至 Google Play：

1. 登入您的 Google Play 開發人員帳戶，以建立連結到您 Google Play 開發人員帳戶的新專案。
2. 建立驗證您應用程式的「OAuth 用戶端」__。
3. 將產生的「用戶端識別碼」__ 和「用戶端密碼」__ 輸入至 Visual Studio for Mac。
4. 向 Visual Studio for Mac 註冊您的帳戶。
5. 使用您的憑證來簽署應用程式。
6. 將已簽署的應用程式發行至 Google Play。

在[封存以供發行](~/android/deploy-test/release-prep/index.md#archive)中，[簽署並散發]**** 對話方塊顯示了兩個散發選項。 選取 [Google Play]****，然後按一下 [下一步]****：

[![選擇 Android 分發對話框](images/xs/01-select-google-play-sml.png)](images/xs/01-select-google-play.png#lightbox)

在 [Google Play API 帳戶]**** 對話方塊中，您必須提供「用戶端識別碼」__ 和「用戶端密碼」__ 以提供對您 Google Play 開發人員帳戶的 API 存取權：

[![谷歌播放API帳戶對話框](images/xs/02-google-play-api-account-sml.png)](images/xs/02-google-play-api-account.png#lightbox)

下一節說明如何建立新的 Google API 專案並產生所需的「用戶端識別碼」__ 和「用戶端密碼」__。

-----

#### <a name="create-a-google-api-project"></a>建立 Google API 專案

首先，登入您的 [Google Play 開發人員帳戶](https://play.google.com/apps/publish)。
如果您尚未擁有 Google Play 開發人員帳戶，請參閱[開始使用發佈](https://developer.android.com/distribute/googleplay/start.html)。
此外，Google Play 開發人員 API [入門](https://developers.google.com/android-publisher/getting_started) \(英文\) 也說明了如何使用 Google Play 開發人員 API。 登入 Google Play 開發者主控台後,按下 **「建立應用程式**」 :

[![建立新項目按鈕](images/01-create-new-project-sml.png)](images/01-create-new-project.png#lightbox)

創建新專案後,它將連結到您的 Google Play 開發者控制台帳戶。

下一步是為應用程式建立「OAuth 用戶端」(如果尚未建立)。 當使用者使用您的應用程式來要求存取其私有資料時，會使用您的「OAuth 用戶端識別碼」來驗證您的應用程式。

移至 [設定]**** 頁面。

[![設定圖示](images/02-google-play-developer-console-sml.png)](images/02-google-play-developer-console.png#lightbox)

在 **「設定」** 頁中,選擇**API 存取**並按下 **「建立 OAUTH 客戶端**」建立新的 OAuth 客戶端:

[![建立 OAuth 客戶端按鈕](images/03-create-oauth-client-sml.png)](images/03-create-oauth-client.png#lightbox)

幾秒鐘之後，就會產生一個新的「用戶端識別碼」。 按一下 [在 Google 開發人員控制台中檢視]****，以在「Google 開發人員控制台」中查看新的「用戶端識別碼」：

[![顯示客戶端識別碼](images/04-generated-client-id-sml.png)](images/04-generated-client-id.png#lightbox)

「用戶端識別碼」會與其名稱和建立日期一起顯示。 按一下 [編輯 OAuth 用戶端]**** 圖示，以檢視您應用程式的用戶端密碼：

[![查看應用認證](images/05-google-developer-console-sml.png)](images/05-google-developer-console.png#lightbox)

OAuth 用戶端的預設名稱為「Google Play Android 開發人員」**。 此名稱可以變更為 Xamarin.Android 應用程式，或任何適合的名稱。 在此範例中，「OAuth 用戶端」已變更為應用程式的名稱 **MyApp**：

[![顯示客戶端識別碼與機密](images/06-client-id-and-secret-sml.png)](images/06-client-id-and-secret.png#lightbox)

按一下 [儲存]**** 來儲存變更。 這將返回到**認證中**,其中透過單擊 **「下載 JSON」** 圖示下載認證:

[![下載 JSON 圖示](images/07-download-json-sml.png)](images/07-download-json.png#lightbox)

此 JSON 檔案包含您可以剪貼到下一個步驟之 [簽署並散發]**** 對話方塊中的「用戶端識別碼」和「用戶端密碼」。

#### <a name="register-google-api-access"></a>註冊 Google API 存取

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

使用「用戶端識別碼」和「用戶端密碼」來完成 Visual Studio for Mac 中的 [Google Play API 帳戶]**** 對話方塊。 您可以為此帳戶提供描述 &ndash; 這樣即可註冊多個 Google Play 帳戶並將未來的 APK 上傳到不同 Google Play 帳戶。 將「用戶端識別碼」和「用戶端密碼」貼到此對話方塊中，然後按一下 [註冊]****：

[![註冊 Google API 存取對話框](images/vs/05-enter-client-id-and-secret-sml.png)](images/vs/05-enter-client-id-and-secret.png#lightbox)

網頁瀏覽器將會開啟，並提示您登入「Google Play Android 開發人員」帳戶 (如果您尚未登入)。 登入之後，網頁瀏覽器中會顯示以下提示。
請按一下 [允許]**** 以授權應用程式：

[![授權套用對話框](images/vs/06-authorize-app-sml.png)](images/vs/06-authorize-app.png#lightbox)

#### <a name="publish"></a>發佈

按下「**允許」** 後,瀏覽器將報告_已收到驗證碼。關閉中..._ 並將該應用程式添加到可視化工作室中的 Google Play 帳戶清單中。 在 [Google Play 帳戶]**** 對話方塊中，按一下 [繼續]****：

[![新增到 Google Play 帳號的帳戶](images/vs/07-account-added-sml.png)](images/vs/07-account-added.png#lightbox)

接著，就會顯示 [Google Play 追蹤]**** 對話方塊。 Google Play 提供了五種可能上傳應用的軌道:

- **內部**&ndash;用於快速分發應用以進行內部測試和質量保證檢查。
- **Alpha**&ndash;用於將應用的早期版本上傳到測試人員的小清單。
- **Beta**&ndash;用於將應用的早期版本上傳到更大的測試人員清單。
- **用於**&ndash;向 Google Play 商店進行完整分發的生產。
- **自定義**&ndash;用於通過電子郵件地址創建測試人員清單,與特定使用者測試應用的預發佈版本。

請選擇要使用哪一個 Google Play 追蹤方式來上傳應用程式，然後按一下 [上傳]****。

[![新增到 Google Play 帳號的帳戶](images/vs/08-google-play-track-sml.png)](images/vs/07-account-added.png#lightbox)

有關 Google Play 測試的詳細資訊,請參閱[設定開啟/關閉/內部測試](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)。

接著，會顯示一個對話方塊，可供您輸入用來簽署憑證的密碼。
輸入密碼,然後按下 **「確定**」:

[![簽署密碼對話框](images/vs/09-certificate-password-sml.png)](images/vs/09-certificate-password.png#lightbox)

[封存管理員]**** 會顯示上傳進度：

[![上傳 APK 進度](images/vs/10-uploading-apk-sml.png)](images/vs/10-uploading-apk.png#lightbox)

上傳完成時，Visual Studio 左下角會顯示完成狀態：

[![發行專案已完成的訊息](images/vs/11-published-sml.png)](images/vs/11-published.png#lightbox)

### <a name="troubleshooting"></a>疑難排解

如果您在選擇 Google Play 曲目時看不到自定義曲目,請確保在 Google Play 開發人員控制臺上為該曲目創建了一個版本。 有關如何創建版本的說明,請參閱[準備&推出版本](https://support.google.com/googleplay/android-developer/answer/7159011?hl=en)。

請注意，必須已將一個 APK 提交給 Google Play 商店，[發行至 Google Play]**** 才能運作。 如果尚未上傳 APK，「發行精靈」將會在 [錯誤]**** 窗格中顯示以下錯誤：

[![您必須手動上傳此應用程式的第一個 APK](images/vs/12-upload-error-sml.png)](images/vs/12-upload-error.png#lightbox)

發生此錯誤時,透過 Google Play 開發人員主控台手動上傳 APK(如臨時版本),並使用**分發通路**對話框進行後續 APK 更新。  如需詳細資訊，請參閱[手動上傳 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。 APK 的版本代碼必須隨著每次上傳變更，否則將會發生以下錯誤：

[![具有版本代碼 (1) 的 APK 已更新](images/vs/13-version-code-error-sml.png)](images/vs/13-version-code-error.png#lightbox)

若要解決此錯誤，請使用不同的版本號碼來重建應用程式，然後透過 [散發通道]**** 對話方塊將應用程式重新提交給 Google Play。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

使用「用戶端識別碼」和「用戶端密碼」來完成 Visual Studio for Mac 中的 [Google Play API 帳戶]**** 對話方塊。 您可以為此帳戶提供描述 &ndash; 這樣即可註冊多個 Google Play 帳戶並將未來的 APK 上傳到不同 Google Play 帳戶。 將「用戶端識別碼」和「用戶端密碼」貼到此對話方塊中，然後按一下 [註冊]****：

[![授權存取對話框](images/xs/03-register-sml.png)](images/xs/03-register.png#lightbox)

如果系統接受「用戶端識別碼」和「用戶端密碼」，就會顯示**註冊成功**訊息。 點選 **「下一步**」 :

[![註冊成功訊息](images/xs/04-registration-successful-sml.png)](images/xs/04-registration-successful.png#lightbox)

在 [Google Play 帳戶]**** 對話方塊中，選取一個 Google 帳戶和追蹤方式來上傳應用程式：

[![選擇 Google 帳號對話框](images/xs/05-choose-google-account-sml.png)](images/xs/05-choose-google-account.png#lightbox)

Google Play 提供了五種可能上傳應用的軌道:

- **內部**&ndash;用於快速分發應用以進行內部測試和質量保證檢查。
- **Alpha**&ndash;用於將應用的早期版本上傳到測試人員的小清單。
- **Beta**&ndash;用於將應用的早期版本上傳到更大的測試人員清單。
- **用於**&ndash;向 Google Play 商店進行完整分發的生產。
- **自定義**&ndash;用於通過電子郵件地址創建測試人員清單,與特定使用者測試應用的預發佈版本。

有關 Google Play 測試的詳細資訊,請參閱[設定 Alpha/Beta 測試](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)。

接著，選擇將用來簽署應用程式的簽署身分識別。
選取 [使用現有金鑰]**** 以使用現有的簽署身分識別，否則請參考[建立新的憑證](~/android/deploy-test/signing/index.md#newcert)指南，以了解有關建立新金鑰的資訊。 選取憑證來簽署應用程式之後，請按一下 [下一步]****：

[![Android 簽署識別對話框](images/xs/06-android-signing-identity-sml.png)](images/xs/06-android-signing-identity.png#lightbox)

此時已可將應用程式上傳到 Google Play。 [發行至 Google Play]**** 對話方塊會摘要列出有關您應用程式的資訊 &ndash; 請按一下 [發行]**** 以將應用程式發 行至 Google Play：

[![發布 Google 播放對話框](images/xs/07-publish-to-google-play-sml.png)](images/xs/07-publish-to-google-play.png#lightbox)

### <a name="troubleshooting"></a>疑難排解

如果您在選擇要上傳應用的 Google Play 軌道時看不到自定義曲目,請確保在 Google Play 開發人員控制臺上為該曲目創建了一個版本。 有關如何創建版本的說明,請參閱[準備&推出版本](https://support.google.com/googleplay/android-developer/answer/7159011?hl=en)。

請注意，必須已將一個 APK 提交給 Google Play 商店，[發行至 Google Play]**** 才能運作。 如果未上傳 APK，可能會發生以下錯誤：

> _Google Play 要求您手動上傳此應用程式的第一個 APK。為此,您可以使用臨時 APK。_

或

> 「找不到指定之套件名稱的應用程式。[404]」__

要解決此錯誤,請透過 Google Play 開發人員主控台手動上傳 APK(如臨時版本),並使用 **「發布到 Google 播放**」對話框進行後續 APK 更新。 如需有關如何手動上傳 APK 的資訊，請參閱[手動上傳 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。

-----
