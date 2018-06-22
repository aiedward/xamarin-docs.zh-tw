---
title: 簽署 Android 應用程式套件
ms.prod: xamarin
ms.assetid: 8E3EFBB2-F8AD-C126-5F32-7FD140791E53
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/21/2018
ms.openlocfilehash: 6a4164ea4a56ee7c1b3c1abd05f7b1bb95aede4f
ms.sourcegitcommit: 9f8e7393019791bbd6af4fefaa24a1602adabb4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2018
ms.locfileid: "34458797"
---
# <a name="signing-the-android-application-package"></a>簽署 Android 應用程式套件

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本節說明簽署 Visual Studio 所提供之 APK 的整合式發行工作流程。 在[準備要發行的應用程式](~/android/deploy-test/release-prep/index.md)中，使用**封存管理員**來建置應用程式，並將它放入封存以進行簽署和發行。 本節說明如何建立 Android 簽署身分識別、建立適用於 Android 應用程式的新簽署憑證，並透過「臨機操作」將封存的應用程式發行至磁碟。
產生的 APK 可以側載到 Android 裝置，而不需透過 App Store。

在[封存以供發行](~/android/deploy-test/release-prep/index.md#archive)中，[散發通道] 對話方塊顯示了兩個用於散發的選項。 選取 [臨機操作]：

[![[散發通道] 對話方塊](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在本節中，我們將使用 Visual Studio for Mac 的整合式發行工作流程來簽署 APK。 在[準備要發行的應用程式](~/android/deploy-test/release-prep/index.md)中，我們使用了**封存管理員**來建置應用程式，並將它放入封存以進行簽署和發行。 在本節中，我們將了解如何建立 Android 簽署身分識別、建立適用於 Android 應用程式的新簽署憑證，並透過「臨機操作」將封存的應用程式發行至磁碟。 產生的 APK 可以側載到 Android 裝置，而不需透過 App Store。

在[封存以供發行](~/android/deploy-test/release-prep/index.md#archive)中，[簽署並散發] 對話方塊顯示了兩個用於散發的選項。 選取 [臨機操作]，然後按一下 [下一步]：

[![[簽署並散發] 對話方塊](images/xs/01-select-ad-hoc-sml.png)](images/xs/01-select-ad-hoc.png#lightbox)

-----

<a name="newcertvs" />
<a name="newcert" />
<a name="newcertxs" />

## <a name="create-a-new-certificate"></a>建立新的憑證

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

選取 [臨機操作] 之後，Visual Studio 會開啟對話方塊的 [簽署身分識別] 頁面，如下一個螢幕擷取畫面所示。 若要發行 .APK，必須先使用簽署金鑰 (也稱為憑證) 來簽署它。

您可以按一下 [匯入] 按鈕，然後繼續[簽署 APK](#signapkvs)，以使用現有的憑證。 否則，按一下 [+] 按鈕來建立新的憑證：

[![臨機操作簽署身分識別](images/vs/02-ad-hoc-signing-identity-vs-sml.png)](images/vs/02-ad-hoc-signing-identity-vs.png#lightbox)

[Create Android Key Store] \(建立 Android 金鑰儲存區\) 對話方塊隨即顯示；使用此對話方塊來建立可用於簽署 Android 應用程式的新簽署憑證。 輸入所需的資訊，如此對話方塊中所示 (以紅色標示之處)：

[![[Create Android Key Store] \(建立 Android 金鑰儲存區\) 對話方塊](images/vs/03-create-android-key-store-vs-sml.png)](images/vs/03-create-android-key-store-vs.png#lightbox)

下列範例說明必須提供的資訊種類。 按一下 [建立] 來建立新的憑證：

[![建立新的憑證](images/vs/04-key-store-example-vs-sml.png)](images/vs/04-key-store-example-vs.png#lightbox)

產生的金鑰儲存區位於下列位置：

**C:\\Users\\*USERNAME*\\AppData\\Local\\Xamarin\\Mono for Android\\alias\\alias.keystore**

例如，上述步驟可能會在下列位置建立新的簽署金鑰：

**C:\\Users\\*USERNAME*\\AppData\\Local\\Xamarin\\Mono for Android\\chimp\\chimp.keystore**

> [!NOTE]
> 請務必將所產生的金鑰儲存區檔案備份到安全的位置。&ndash;解決方案不包含此檔案。 如果您遺失金鑰儲存區檔案 (例如，因為您移至另一部電腦，或重新安裝 Windows)，您將無法使用與舊版相同的憑證來簽署應用程式。

如需金鑰儲存區的詳細資訊，請參閱[尋找金鑰儲存區的 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

按一下 [臨機操作] 之後，Visual Studio for Mac 即會開啟 [Android 簽署識別] 對話方塊，如下一個螢幕擷取畫面所示。 若要發行 .APK，必須先使用簽署金鑰 (也稱為憑證) 來簽署它。 如果憑證已經存在，按一下 [匯入現有的金鑰] 按鈕來匯入它，然後繼續[簽署 APK](#signapkxs)；否則，按一下 [建立新的金鑰] 按鈕來建立新的憑證： 

[![[Android 簽署識別] 對話方塊](images/xs/02-android-signing-identity-sml.png)](images/xs/02-android-signing-identity.png#lightbox)

[建立新的憑證] 對話方塊會用來建立可用於簽署 Android 應用程式的新簽署憑證。 輸入必要資訊之後，按一下 [確定]：

[![[建立新的憑證] 對話方塊](images/xs/03-create-new-certificate-sml.png)](images/xs/03-create-new-certificate.png#lightbox)

產生的金鑰儲存區位於下列位置：

**~/Library/Developer/Xamarin/Keystore/alias/alias.keystore**

例如，上述步驟可能會在下列位置建立新的簽署金鑰：

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**


> [!NOTE]
> 請務必將所產生的金鑰儲存區檔案備份到安全的位置。&ndash;解決方案不包含此檔案。 如果您遺失金鑰儲存區檔案 (例如，因為您移至另一部電腦，或重新安裝 Mac)，您將無法使用與舊版相同的憑證來簽署應用程式。

如需金鑰儲存區的詳細資訊，請參閱[尋找金鑰儲存區的 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)。

-----

<a name="signapkvs" />

## <a name="sign-the-apk"></a>簽署 APK

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

按一下 [建立] 時，新的金鑰儲存區 (包含新的憑證) 將會儲存並列於 [簽署身分識別] 下方，如下一個螢幕擷取畫面所示。 若要在 Google Play 上發行應用程式，按一下[取消]，然後移至[發行至 Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)。
若要透過「臨機操作」發行，請選取要用來簽署的簽署身分識別，然後按一下 [另存新檔] 以發行要獨立散發的應用程式。 例如，此螢幕擷取畫面中已選取 **chimp** 簽署身分識別 (先前建立的)：

[![簽署身分識別範例](images/vs/05-save-as-vs-sml.png)](images/vs/05-save-as-vs.png#lightbox)

接著，[封存管理員] 會顯示發行進度。 當發行程序完成時，[另存新檔] 對話方塊隨即開啟，要求提供儲存所產生 .APK 檔案的位置：

[![[另存新檔] 對話方塊](images/vs/06-save-as-dialog-vs-sml.png)](images/vs/06-save-as-dialog-vs.png#lightbox)

瀏覽至想要的位置，然後按一下 [儲存]。 如果金鑰密碼是未知的，[簽署密碼] 對話方塊隨即出現，提示您提供所選取憑證的密碼：

[![[簽署密碼] 對話方塊](images/vs/07-signing-password-vs-sml.png)](images/vs/07-signing-password-vs.png#lightbox)

簽署程序完成之後，按一下 [開啟散發]：

[![[開啟散發] 按鈕](images/vs/08-open-distribution-sml.png)](images/vs/08-open-distribution.png#lightbox)

這會導致 Windows 檔案總管開啟包含所產生 APK 檔案的資料夾。 此時，Visual Studio 已將 Xamarin.Android 應用程式編譯為已完成散發準備的 APK。
下列螢幕擷取畫面會顯示已完成發行準備的應用程式 **MyApp.MyApp.apk** 範例：

[![Windows 檔案總管中顯示的 APK](images/vs/09-generated-app-vs-sml.png)](images/vs/09-generated-app-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


如此處所示，已將新的憑證新增至金鑰儲存區。 若要在 Google Play 上發行應用程式，按一下[取消]，然後移至[發行至 Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)。
否則，按一下 [下一步]，透過「臨機操作」發行應用程式 (適用於獨立散發)，如此範例所示：

[![[簽署並散發] 對話方塊](images/xs/04-select-identity-sml.png)](images/xs/04-select-identity.png#lightbox)

[發行為臨機操作] 對話方塊會在發行已簽署的應用程式之前，提供它的摘要。 如果此資訊正確，按一下 [發行]。

[![[發行為臨機操作] 對話方塊](images/xs/05-publish-ad-hoc-sml.png)](images/xs/05-publish-ad-hoc.png#lightbox)

[輸出 APK 檔案] 對話方塊會將 APK 儲存至指定的路徑。 按一下 [儲存] 。

![[輸出 APK 檔案] 對話方塊](images/xs/06-output-apk-file.png)

接著，輸入憑證的密碼 (在 [建立新的憑證] 對話方塊中使用的密碼)，然後按一下 [確定]： 

![輸入憑證密碼](images/xs/07-signing-certificate.png)

系統會使用該憑證來簽署 APK，並將它儲存到指定的位置。 按一下 [在 Finder 中顯示]：

[![[Publication Succeeded] \(已成功發行\) 對話方塊](images/xs/08-app-is-ready-sml.png)](images/xs/08-app-is-ready.png#lightbox)

這樣會將 Finder 開啟至已簽署 APK 檔案的位置：

[![Finder 中顯示的 APK](images/xs/09-show-in-finder-sml.png)](images/xs/09-show-in-finder.png#lightbox)

APK 已準備好從 Finder 複製並傳送到其最終目的地。 理想的作法是在 Android 裝置上安裝 APK，並在散發前加以試用。 如需發行「臨機操作」APK 的詳細資訊，請參閱[獨立發行](~/android/deploy-test/publishing/publishing-independently.md)。

-----



## <a name="next-steps"></a>後續步驟

簽署要發行的應用程式套件之後，必須將它發行。 後續各節將說明數種發行應用程式的方式。
