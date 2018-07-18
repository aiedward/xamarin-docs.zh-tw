---
title: 發行至 Google Play
ms.prod: xamarin
ms.assetid: FB1CC234-3554-8566-48BD-2B9B3A28CC7F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 571b5bb37ee10bb83dceef058613f955a8b7bff9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772896"
---
# <a name="publishing-to-google-play"></a>發行至 Google Play

雖然有許多應用程式市場可供散發應用程式，但 Google Play 可說是 Android 應用程式世界中最大且瀏覽頻率最高的商店。 Google Play 提供一個可散發、廣告、銷售 Android 應用程式及分析這些應用程式銷售情況的單一平台。

本節將涵蓋 Google Play 專屬的主題，例如註冊成為發行者、收集資產以協助 Google Play 宣傳和廣告您的應用程式、在 Google Play 上為應用程式評分的指導方針，以及使用篩選來限制將應用程式部署至特定裝置。


## <a name="requirements"></a>需求

若要透過 Google Play 散發應用程式，必須建立開發人員帳戶。 此動作只需執行一次，需要支付單次費用 $25 美元。

所有應用程式都必須以 2033 年 10 月 22 日之後到期的密碼編譯金鑰簽署。

在 Google Play 上發行的 APK 大小上限為 100MB。 如果應用程式超出該大小，Google Play 將會允許透過「APK 擴充檔」提供額外的資產。 Android 擴充檔可允許 APK 有 2 個額外的檔案，每個檔案的大小上限為 2GB。 Google Play 會免費裝載並散發這些檔案。 擴充檔將在另一節中討論。

Google Play 並非全球都可使用。 部分地點不是支援的應用程式散發地點。



## <a name="becoming-a-publisher"></a>成為發行者

若要在 Google Play 上發行應用程式，必須要有發行者帳戶。 若要註冊發行者帳戶，請依照下列步驟：

1.  前往 [Google Play 開發人員控制台](https://play.google.com/apps/publish)。
1.  輸入您開發人員身分識別的相關基本資訊。
1.  閱讀並接受適用於您地區設定的「開發人員發佈協議」。
1.  支付 $25 美元註冊費。
1.  透過電子郵件確認驗證。
1.  建立帳戶之後，就可以使用 Google Play 來發行應用程式。


Google Play 並未支援全球所有國家/地區。 下列連結提供最新的國家/地區清單：

1.  [支援的開發人員和商家註冊地點](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=150324) &ndash; 這是開發人員可以註冊成為商家並銷售付費應用程式的所有國家/地區清單。

1.  [支援向 Google Play 使用者發佈商品的地點](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=138294) &ndash; 這是可散發應用程式的所有國家/地區清單。



### <a name="preparing-promotional-assets"></a>準備宣傳資產

為了在 Google Play 上有效宣傳和廣告應用程式，Google 允許開發人員提交宣傳資產，例如螢幕擷取畫面、圖形及影片。 然後 Google Play 會使用這些資產來廣告和宣傳應用程式。



#### <a name="launcher-icons"></a>啟動器圖示

「啟動器圖示」是代表應用程式的圖形。 每個啟動器圖示應該為 32 位元的 PNG，並且具有代表透明度的 Alpha 色板。 應用程式的圖示應該適用於一般化的螢幕密度，如以下清單所述：

-   **ldpi** (120dpi) &ndash; 36 x 36 px
-   **mdpi** (160dpi) &ndash; 48 x 48 px
-   **hdpi** (240dpi) &ndash; 72 x 72 px
-   **xhdpi** (320dpi) &ndash; 96 x 96 px


啟動器圖示是使用者在 Google Play 上所看到第一個應用程式相關項目，因此應該細心設計，讓啟動器圖示既具視覺吸引力又有意義。

啟動器圖示秘訣：

1.  **簡單且整齊**&ndash; 啟動器圖示應該保持簡單且整齊。 這意謂著從圖示中排除應用程式名稱。 較簡單的圖示較容易記住，且大小較小時也較容易辨識。

1.  **圖示不應該精簡**&ndash; 過度精簡的圖示將無法在所有背景上妥善地突顯。

1.  **使用 Alpha 色板**&ndash; 圖示應該使用 Alpha 色板，且不應該是完整畫面格影像。



#### <a name="high-resolution-application-icons"></a>高解析度應用程式圖示

Google Play 上的應用程式需要高畫質版本的應用程式圖示。 此圖示僅供 Google Play 使用，且不會取代應用程式啟動器圖示。 高解析度圖示的規格為：

1.  含 Alpha 色板的 32 位元 PNG
1.  512 x 512 像素
1.  大小上限為 1024 KB

[Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/) 是一個用來建立適當啟動器圖示和高解析度應用程式圖示的實用工具。



#### <a name="screen-shots"></a>螢幕擷取畫面

Google Play 要求應用程式最少要有 2 個、最多 8 個螢幕擷取畫面。 這些螢幕擷取畫面將顯示在 Google Play 中的應用程式詳細資料頁面上。

螢幕擷取畫面的規格為：

1.  無 Alpha 色板的 24 位元 PNG 或 JPG
1.  320 (寬) x 480 (高) 或 480 (寬) x 800 (高) 或 480 (寬) x 854 (高)。 橫向影像將受到裁剪。



#### <a name="promotional-graphic"></a>宣傳圖形

這是 Google Play 所使用的選用影像：

1.  這是無 Alpha 色板的 180 (寬) x 120 (高) 24 位元 PNG 或 JPG。
1.  美工圖案中無框線。



#### <a name="feature-graphic"></a>精選圖形

Google Play 精選區段所使用的圖形。 此圖形可以不搭配應用程式圖示來單獨顯示。

1.  無 Alpha 色板和透明度的 1024 (寬) x 500 (高) PNG 或 JPG。
1.  所有重要內容應該都放在 924x500 的框架內。 此框架外的像素可能因造型目的而受到裁剪。
1.  此圖形可以縮小：請使用大型文字並讓圖形保持簡單。



#### <a name="video-link"></a>影片連結

這是展示應用程式的 YouTube 影片 URL。 影片長度應該為 30 秒到 2 分鐘，並展示應用程式最精彩的部分。



### <a name="publishing-to-google-play"></a>發行至 Google Play

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Xamarin Android 7.0 導入整合式工作流程，可從 Visual Studio 將應用程式發行至 Google Play。 如果您使用 7.0 版之前的 Xamarin Android，就必須透過「Google Play 開發人員控制台」手動上傳您的 APK。 此外，您必須至少已上傳一個 APK，才能使用整合式工作流程。 如果您尚未上傳您的第一個 APK，就必須手動上傳它。 如需詳細資訊，請參閱[手動上傳 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。

[建立新的憑證](~/android/deploy-test/signing/index.md#newcert)說明如何建立新的憑證來簽署 Android 應用程式。 下一步是將已簽署的應用程式發行至 Google Play：

1. 登入您的 Google Play 開發人員帳戶，以建立連結到您 Google Play 開發人員帳戶的新專案。
2. 建立驗證您應用程式的 **OAuth 用戶端**。
3. 將產生的「用戶端識別碼」和「用戶端密碼」輸入至 Visual Studio。
4. 向 Visual Studio 註冊您的帳戶。
5. 使用您的憑證來簽署應用程式。
6. 將已簽署的應用程式發行至 Google Play。

在[封存以供發行](~/android/deploy-test/release-prep/index.md#archive)中，[散發通道] 對話方塊顯示了兩個用於散發的選項：[臨機操作] 和 [Google Play]。 如果改為顯示 [簽署身分識別] 對話方塊，請按一下 [上一步] 以返回 [散發通道] 對話方塊。 選取 [Google Play]，然後按一下 [下一步]：

[![[散發通道] 對話方塊](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

在 [簽署身分識別] 對話方塊中，選取在[建立新的憑證](~/android/deploy-test/signing/index.md#newcert)中建立的身分識別，然後按一下 [繼續]：

[![[簽署身分識別] 對話方塊](images/vs/02-select-identity-sml.png)](images/vs/02-select-identity.png#lightbox)

在 [Google Play 帳戶] 對話方塊中，按一下 [+] 按鈕以新增「Google Play 帳戶」：

[![[Google Play 帳戶] 對話方塊](images/vs/03-google-play-accounts-sml.png)](images/vs/03-google-play-accounts.png#lightbox)

在 [註冊 Google API 存取] 對話方塊中，您必須提供「用戶端識別碼」和「用戶端密碼」以提供對您 Google Play 開發人員帳戶的 API 存取權：

[![[註冊 Google API 存取] 對話方塊](images/vs/04-register-google-api-access-sml.png)](images/vs/04-register-google-api-access.png#lightbox)

下一節說明如何建立新的 Google API 專案並產生所需的「用戶端識別碼」和「用戶端密碼」。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Visual Studio for Mac 具有可將應用程式發行至 Google Play 的整合式工作流程。 如果您使用 5.9 版之前的 Xamarin Studio，就必須透過「Google Play 開發人員控制台」手動上傳您的 APK，然後使用 [發行至 Google Play] 對話方塊來進行後續的 APK 更新。 此外，您必須至少已上傳一個 APK，才能使用 [發行至 Google Play]。 如果您尚未上傳您的第一個 APK，就必須手動上傳它。 如需有關如何手動上傳 APK 的資訊，請參閱[手動上傳 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。

[建立新的憑證](~/android/deploy-test/signing/index.md#newcert)說明如何建立新的憑證來簽署 Android 應用程式。 下列步驟簡述如何將 Xamarin.Android 應用程式發行至 Google Play：

1. 登入您的 Google Play 開發人員帳戶，以建立連結到您 Google Play 開發人員帳戶的新專案。
2. 建立驗證您應用程式的「OAuth 用戶端」。
3. 將產生的「用戶端識別碼」和「用戶端密碼」輸入至 Visual Studio for Mac。
4. 向 Visual Studio for Mac 註冊您的帳戶。
5. 使用您的憑證來簽署應用程式。
6. 將已簽署的應用程式發行至 Google Play。

在[封存以供發行](~/android/deploy-test/release-prep/index.md#archive)中，[簽署並散發] 對話方塊顯示了兩個散發選項。 選取 [Google Play]，然後按一下 [下一步]：

[![[選取 Android 散發] 對話方塊](images/xs/01-select-google-play-sml.png)](images/xs/01-select-google-play.png#lightbox)

在 [Google Play API 帳戶] 對話方塊中，您必須提供「用戶端識別碼」和「用戶端密碼」以提供對您 Google Play 開發人員帳戶的 API 存取權：

[![[Google Play API 帳戶] 對話方塊](images/xs/02-google-play-api-account-sml.png)](images/xs/02-google-play-api-account.png#lightbox)

下一節說明如何建立新的 Google API 專案並產生所需的「用戶端識別碼」和「用戶端密碼」。

-----


#### <a name="create-a-google-api-project"></a>建立 Google API 專案

首先，登入您的 [Google Play 開發人員帳戶](https://play.google.com/apps/publish)。
如果您尚未擁有 Google Play 開發人員帳戶，請參閱[開始使用發佈](http://developer.android.com/distribute/googleplay/start.html)。
此外，Google Play 開發人員 API [入門](https://developers.google.com/android-publisher/getting_started) \(英文\) 也說明了如何使用 Google Play 開發人員 API。 登入「Google Play 開發人員控制台」之後，按一下 [設定]：

[![[設定] 圖示](images/01-google-play-developer-console-sml.png)](images/01-google-play-developer-console.png#lightbox)

在 [設定] 頁面上，選取 [API 存取]，然後按一下 [建立新專案] 按鈕：

[![[建立新專案] 按鈕](images/02-create-new-project-sml.png)](images/02-create-new-project.png#lightbox)

大約一分鐘之後，就會自動產生新的 API 專案並連結至您的「Google Play 開發人員控制台」帳戶。

下一步是為應用程式建立「OAuth 用戶端」(如果尚未建立)。 當使用者使用您的應用程式來要求存取其私有資料時，會使用您的「OAuth 用戶端識別碼」來驗證您的應用程式。
按一下 [建立 OAuth 用戶端] 以建立新的 OAuth 用戶端：

[![[建立 OAuth 用戶端] 按鈕](images/03-create-oauth-client-sml.png)](images/03-create-oauth-client.png#lightbox)

幾秒鐘之後，就會產生一個新的「用戶端識別碼」。 按一下 [在 Google 開發人員控制台中檢視]，以在「Google 開發人員控制台」中查看新的「用戶端識別碼」：

[![顯示的「用戶端識別碼」](images/04-generated-client-id-sml.png)](images/04-generated-client-id.png#lightbox)

「用戶端識別碼」會與其名稱和建立日期一起顯示。 按一下 [編輯 OAuth 用戶端] 圖示，以檢視您應用程式的用戶端密碼：

[![檢視應用程式憑證](images/05-google-developer-console-sml.png)](images/05-google-developer-console.png#lightbox)

OAuth 用戶端的預設名稱為「Google Play Android 開發人員」。 此名稱可以變更為 Xamarin.Android 應用程式，或任何適合的名稱。 在此範例中，「OAuth 用戶端」已變更為應用程式的名稱 **MyApp**：

[![顯示的用戶端識別碼和密碼](images/06-client-id-and-secret-sml.png)](images/06-client-id-and-secret.png#lightbox)

按一下 [儲存] 以儲存變更。 這會返回 [憑證] 頁面，只要按一下 [下載 JSON] 圖示，即可下載憑證：

[![[下載 JSON] 圖示](images/07-download-json-sml.png)](images/07-download-json.png#lightbox)

此 JSON 檔案包含您可以剪貼到下一個步驟之 [簽署並散發] 對話方塊中的「用戶端識別碼」和「用戶端密碼」。


#### <a name="register-google-api-access"></a>註冊 Google API 存取

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

使用「用戶端識別碼」和「用戶端密碼」來完成 Visual Studio for Mac 中的 [Google Play API 帳戶] 對話方塊。 您可以為此帳戶提供描述 &ndash; 這樣即可註冊多個 Google Play 帳戶並將未來的 APK 上傳到不同 Google Play 帳戶。 將「用戶端識別碼」和「用戶端密碼」貼到此對話方塊中，然後按一下 [註冊]：

[![[註冊 Google API 存取] 對話方塊](images/vs/05-enter-client-id-and-secret-sml.png)](images/vs/05-enter-client-id-and-secret.png#lightbox)

網頁瀏覽器將會開啟，並提示您登入「Google Play Android 開發人員」帳戶 (如果您尚未登入)。 登入之後，網頁瀏覽器中會顯示以下提示。
請按一下 [允許] 以授權應用程式：

[![[授權應用程式] 對話方塊](images/vs/06-authorize-app-sml.png)](images/vs/06-authorize-app.png#lightbox)

#### <a name="publish"></a>發行

按一下 [允許] 之後，瀏覽器會回報「已收到驗證碼。正在關閉...」，然後應用程式就會新增至 Visual Studio 的「Google Play 帳戶」清單中。 在 [Google Play 帳戶] 對話方塊中，按一下 [繼續]：

[![帳戶已新增至 [Google Play 帳戶]](images/vs/07-account-added-sml.png)](images/vs/07-account-added.png#lightbox)

接著，就會顯示 [Google Play 追蹤] 對話方塊。 Google Play 提供四個可能的追蹤方式來上傳您的應用程式：

* **Alpha** &ndash; 用來將非常初期的應用程式版本上傳到小份的測試清單中。
* **搶鮮版 (Beta)** &ndash; 用來將初期的應用程式版本上傳到較大份的測試清單中。
* **首度發行** &ndash; 可讓某個百分比的使用者接收已更新的應用程式版本；這可讓您在消除錯誤 (bug) 的時，慢慢提高百分比，例如從 10% 的使用者增加到 100% 的使用者。
* **生產環境** &ndash; 當應用程式已經準備就緒而可從 Google Play 商店完整散發時，請選取此追蹤方式。

請選擇要使用哪一個 Google Play 追蹤方式來上傳應用程式，然後按一下 [上傳]。 如果選取 [首度發行]，請務必輸入百分比值：

[![選取 [Alpha]、[搶鮮版 (Beta)]、[首度發行] 或 [生產環境]](images/vs/08-google-play-track-sml.png)](images/vs/08-google-play-track.png#lightbox)

如需有關 Google Play 測試和分段首度發行的詳細資訊，請參閱[設定 Alpha/Beta 版測試環境](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)。

接著，會顯示一個對話方塊，可供您輸入用來簽署憑證的密碼。
請輸入密碼，然後按一下 [確定]：

[![[簽署密碼] 對話方塊](images/vs/09-certificate-password-sml.png)](images/vs/09-certificate-password.png#lightbox)

[封存管理員] 會顯示上傳進度：

[![上傳 APK 進度](images/vs/10-uploading-apk-sml.png)](images/vs/10-uploading-apk.png#lightbox)

上傳完成時，Visual Studio 左下角會顯示完成狀態：

[![「已完成發行專案」訊息](images/vs/11-published-sml.png)](images/vs/11-published.png#lightbox)


### <a name="troubleshooting"></a>疑難排解

請注意，必須已將一個 APK 提交給 Google Play 商店，[發行至 Google Play] 才能運作。 如果尚未上傳 APK，「發行精靈」將會在 [錯誤] 窗格中顯示以下錯誤：

[![您必須手動上傳此應用程式的第一個 APK](images/vs/12-upload-error-sml.png)](images/vs/12-upload-error.png#lightbox)

發生此錯誤時，請透過「Google Play 開發人員控制台」手動上傳 APK (例如「臨機操作」組建)，然後使用 [散發通道] 對話方塊來進行後續的 APK 更新。  如需詳細資訊，請參閱[手動上傳 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。 APK 的版本代碼必須隨著每次上傳變更，否則將會發生以下錯誤：

[![已上傳版本代碼為 (1) 的 APK](images/vs/13-version-code-error-sml.png)](images/vs/13-version-code-error.png#lightbox)

若要解決此錯誤，請使用不同的版本號碼來重建應用程式，然後透過 [散發通道] 對話方塊將應用程式重新提交給 Google Play。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

使用「用戶端識別碼」和「用戶端密碼」來完成 Visual Studio for Mac 中的 [Google Play API 帳戶] 對話方塊。 您可以為此帳戶提供描述 &ndash; 這樣即可註冊多個 Google Play 帳戶並將未來的 APK 上傳到不同 Google Play 帳戶。 將「用戶端識別碼」和「用戶端密碼」貼到此對話方塊中，然後按一下 [註冊]：

[![[授權存取] 對話方塊](images/xs/10-register-sml.png)](images/xs/10-register.png#lightbox)

如果系統接受「用戶端識別碼」和「用戶端密碼」，就會顯示**註冊成功**訊息。 按一下 [下一步]：

[![「註冊成功」訊息](images/xs/11-registration-successful-sml.png)](images/xs/11-registration-successful.png#lightbox)

在 [Google Play 帳戶] 對話方塊中，選取一個 Google 帳戶和追蹤方式來上傳應用程式：

[![[選擇 Google 帳戶] 對話方塊](images/xs/12-choose-google-account-sml.png)](images/xs/12-choose-google-account.png#lightbox)

Google Play 提供四個可能的追蹤方式來上傳您的應用程式：

-   **Alpha** &ndash; 用來將非常初期的應用程式版本上傳到小份的測試清單中。

-   **搶鮮版 (Beta)** &ndash; 用來將初期的應用程式版本上傳到較大份的測試清單中。

-   **首度發行** &ndash; 可讓某個百分比的使用者接收已更新的應用程式版本；這可讓您在消除錯誤 (bug) 的時，慢慢提高百分比，例如從 10% 的使用者增加到 100% 的使用者。

-   **生產環境** &ndash; 當應用程式已經準備就緒而可從 Google Play 商店完整散發時，請選取此追蹤方式。

如需有關 Google Play 測試和分段首度發行的詳細資訊，請參閱[設定 Alpha/Beta 版測試環境](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)。

接著，選擇將用來簽署應用程式的簽署身分識別。
選取 [使用現有金鑰] 以使用現有的簽署身分識別，否則請參考[建立新的憑證](~/android/deploy-test/signing/index.md#newcert)指南，以了解有關建立新金鑰的資訊。 選取憑證來簽署應用程式之後，請按一下 [下一步]：

[![[Android 簽署識別] 對話方塊](images/xs/13-android-signing-identity-sml.png)](images/xs/13-android-signing-identity.png#lightbox)

此時已可將應用程式上傳到 Google Play。 [發行至 Google Play] 對話方塊會摘要列出有關您應用程式的資訊 &ndash; 請按一下 [發行] 以將應用程式發 行至 Google Play：

[![[發行至 Google Play] 對話方塊](images/xs/14-publish-to-google-play-sml.png)](images/xs/14-publish-to-google-play.png#lightbox)

請注意，必須已將一個 APK 提交給 Google Play 商店，[發行至 Google Play] 才能運作。 如果未上傳 APK，可能會發生以下錯誤：

> 「Google Play 要求您必須手動上傳此應用程式的第一個 APK。您可以針對此應用程式使用臨機操作的 APK。」

或

> 「找不到指定之套件名稱的應用程式。[404]」

若要解決此錯誤，請透過「Google Play 開發人員控制台」手動上傳 APK (例如「臨機操作」組建)，然後使用 [發行至 Google Play] 對話方塊來進行後續的 APK 更新。 如需有關如何手動上傳 APK 的資訊，請參閱[手動上傳 APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md)。

-----
