---
title: 發行至 Apple TV App Store
description: 本檔說明如何將應用程式發佈至 Apple 電視 App Store。 其中討論如何設定、布建、建立和提交以 Xamarin 建立的 tvOS 應用程式。
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: aaffaf3d66bb51d467c40bc3e5707a149154c0a7
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528739"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>發行至 Apple TV App Store

為了將應用程式散發到所有 Apple TV 裝置, Apple 要求應用程式必須透過*APPLE 電視 App store*發佈, 讓應用程式儲存 tvOS 應用程式的一個停止購物位置。 許多應用程式類型的開發人員可以資本化此單一發布點的巨大成功。 Apple 電視 App Store 是一種全包式解決方案, 為應用程式開發人員提供散發和付款系統。

將應用程式提交至 Apple TV App Store 的流程包括:

1. 建立「應用程式識別碼」和選取「權利」。
2. 建立散發佈建設定檔。
3. 使用此設定檔來建立您的應用程式。
4. 透過*ITunes Connect*提交您的應用程式。


在本文中, 我們將討論為 Apple 電視 App Store 散發布建、建立和提交應用程式所需的所有步驟。

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>在您提交應用程式前

在您提交應用程式以發行至 Apple TV App Store 後, 它會經過 Apple 的審查流程, 以確保其符合 Apple 的品質和內容指導方針。 若您的應用程式未符合這些方針，Apple 將會予以退件，而您則必須解決 Apple 列出的不合規問題，然後重新提交。
因此，熟悉這些方針並嘗試以其作為基準改寫應用程式，將使您擁有通過 Apple 檢閱的最佳機會。 Apple 的指導方針可在[App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)中取得, 並[準備您的應用程式提交新的 Apple TV](https://developer.apple.com/tvos/submit/)。

提交應用程式時有幾個特別需要注意的事項：

1. 請確定應用程式的描述項合應用程式中包含的功能。
2. 測試應用程式不會在正常使用情況下損毀。 這包括您所支援之每個 Apple TV 裝置的使用量。


Apple 也會維護一份 Apple 電視 App Store 提交秘訣的清單。 您可於此處閱讀這些提示：[Distributing on the App Store](https://developer.apple.com/appstore/resources/submission/tips.html) (在 App Store 上散發)。

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中設定應用程式

[ITunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa)是一套以 web 為基礎的工具套件, 可讓您在 Apple 電視 App Store 上管理 tvOS 應用程式。 您的 tvOS 應用程式必須在 iTunes Connect 中正確設定和設定, 才能提交給 Apple 進行審查, 最終也可以在 Apple 電視 App Store 中發行以供銷售或免費應用程式。

請執行下列動作：

1. 驗證 iTunes Connect 的**合約、稅金與銀行**區段有正確且最新的合約，以發行免費或付費的 iOS 應用程式。
2. 為應用程式建立新的**ITunes Connect 記錄**, 並指定其**顯示名稱**(如 Apple 電視 App Store 中所示)。
3. 選取 [售價] 或指明該應用程式將免費發行。
4. 在所支援的 Apple TV 裝置上, 提供應用程式的**App Store 圖示**(大圖示) 和螢幕擷取畫面。 如需詳細資訊, 請參閱我們[的使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)指南。
5. 提供清楚明瞭的應用程式**描述**, 包括其功能和對使用者的權益。
6. 提供**類別**、**子類別**和**關鍵字**, 以協助使用者在 Apple 電視 app Store 中尋找您的應用程式。
7. 應 Apple 要求提供您網站的**連絡**與**支援** URL。
8. 設定應用程式的**評**等, 供 Apple 電視 App Store 上的家長監護使用。
9. 設定選用 App Store 技術，例如 **Game Center** 與**在應用程式內購買**。

如需詳細資訊, 請參閱[在 ITunes Connect 中設定您的 TvOS 應用程式](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)檔。

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>準備 App Store 散發

若要將應用程式發佈至 Apple TV App Store, 您必須先建立它以供散發, 這牽涉到許多步驟。 下列各節涵蓋準備 tvOS 應用程式以進行發佈所需的所有專案, 使其可以建立並提交至 Apple TV App Store 以供審查和發行。

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>應用程式服務的佈建

Apple 提供選取的特殊應用程式服務 (也稱為權利), 可在您為其建立唯一識別碼時, 為您的 tvOS 應用程式啟用。 無論您是否使用自訂權利, 您仍然需要建立 tvOS 應用程式的唯一識別碼, 才能將其發佈至 Apple TV App Store。

建立應用程式識別碼並選擇性選取權利包含下列步驟，內容涵蓋使用 Apple 的 Web 架構 iOS 佈建入口網站：

1. 選取 [布建**開發**]。  > 
2. 按一下 [+] 按鈕，並為新應用程式提供**名稱**與**套件組合識別碼**。
3. 流覽至畫面底部, 然後選取 tvOS 應用程式所需的任何**應用程式服務**。
4. 按一下 [繼續] 按鈕，並遵循畫面指示來建立新的 應用程式識別碼。

在定義應用程式識別碼時, 除了選取並設定必要的應用程式服務之外, 您還需要編輯`Info.plist`和`Entitlements.plist`檔案, 在 tvOS 專案中設定應用程式識別碼和權利。

在 Visual Studio for Mac 中執行下列動作:

1. 在方案總管中按兩下 `Info.plist` 檔案將其開啟以進行編輯。
2. 在 [ **TvOS 應用程式目標**] 區段中, 填入應用程式的名稱, 並輸入您在定義應用程式識別碼時所建立的套件組合**識別碼**。
3. 將變更儲存至 `Info.plist` 檔案。
4. 在方案總管中按兩下 `Entitlements.plist` 檔案將其開啟以進行編輯。
5. 選取並設定 tvOS 應用程式所需的權利, 使其符合您在定義應用程式識別碼時所執行的安裝程式。
6. 將變更儲存至 `Entitlements.plist` 檔案。

如需詳細指示，請參閱我們的[佈建應用程式](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices)文件。 雖然這份檔是針對 iOS 所撰寫, 但使用相同的步驟來布建 tvOS 應用程式。

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>設定應用程式圖示、啟動映射和最上層影像

若要讓 Apple 接受 apple 電視 App Store 中包含的 tvOS 應用程式, 它需要適當的圖示、啟動, 以及將在其上執行的所有 Apple TV 裝置的主要貨位影像。 您必須新增必要的映射資產, 以編譯成檔案, 並`Assets.car`包含在 tvOS 應用程式的套件組合中, 然後才會上傳到 iTunes Connect。

如需詳細指示, 請參閱我們[的使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)檔。

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>建立與安裝散發設定檔

tvOS 會使用布建*設定檔*來控制特定應用程式組建的部署方式。 這些檔案包含用於簽署應用程式之憑證、應用程式識別碼，以及應用程式可安裝位置的相關資訊。 若為開發與臨機操作散發，佈建設定檔也會包含您可部署應用程式的允許裝置清單。 不過, 針對 Apple TV App Store 散發套件, 只會包含憑證和應用程式識別碼資訊, 因為公用散發的唯一機制是透過 Apple TV App Store。

佈建包含下列步驟，過程會使用 Apple 的 Web 架構 iOS 佈建入口網站：

1. 選取 [佈建] > [散發]。
2. 按一下 [ **+** ] 按鈕, 然後選取您想要建立為**Apple 電視 App Store**的散發配置檔案類型。
3. 從您要建立散發設定檔的下拉式清單中選取 [應用程式識別碼]。
4. 選取簽署應用程式所需的憑證。
5. 輸入新**散發設定檔**的**名稱**並產生設定檔。
6. 重新整理 Xcode 中的可用配置檔案清單。
7. 在 [ **App Store** _組建_設定] 的 Visual Studio 中, 選取發佈布建設定檔。

如需詳細指示，請參閱[建立散發設定檔](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile)與[在 Xamarin.iOS 專案中選取散發設定檔](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。 同樣地, 這兩份檔都是 iOS 特有的, 但 tvOS 應用程式會使用相同的技術。


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>為應用程式設定組建組態

根據預設, 當您建立新的 tvOS 應用程式時, 會自動為**Debug**和**Release**部署建立_組建_設定。 在執行您要提交給 Apple 的應用程式最終組建之前, 您必須先進行幾項修改, 才能進行基本**發行**設定。

請執行下列動作：

1. 以滑鼠右鍵按一下 [**方案總管**] 和 [選取]**選項**中的**專案名稱**, 將其開啟以供編輯。
2. 如果您的目標是特定版本的 tvOS, 請在 [ **tvOS Build**  >  **iOS SDK 版本**] 底下選取它。 如需 tvOS 支援的預覽版本, 請將此值保留為 [**預設**]。
3. 連結會去除未使用的方法、屬性、類別等, 以減少應用程式可散發的整體大小。在大部分情況下, 應該只保留給**連結架構 SDK**的預設值。 在某些情況下, 例如使用某些特定的協力廠商程式庫時, 您可能會強制將此值設定為 [**不要連結**], 以避免移除所需的元素。
4. 若要寄送 tvOS 應用程式, 您必須使用 LLVM 優化編譯器。 確定已核取 [**發行**設定] 下的 [**使用 LLVM 優化編譯器**] 方塊。
5. Apple 也需要 tvOS apps 使用 bitcode。 同樣地, 在 [**發行**] `--bitcode=asmonly`設定下, 將新增至 [**其他 mtouch 引數**] 方塊中。
6. [**優化 iOS 的 PNG 影像檔**] 核取方塊應會核取, 因為這有助於進一步減少應用程式的交付成果大小。
7. 不應啟用調試, 因為它會使組建*不*必要地變大。


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>建置與提交可散發項目

正確設定 tvOS 應用程式之後, 您現在已準備好進行最終發佈組建, 您將提交至 Apple 進行審查和發行。

#### <a name="build-your-archive"></a>建置封存

1. 在 Visual Studio for Mac 中選取 [發行] | [裝置] 組態：

    ![](app-store-publishing-images/buildxs01new.png "選取發行設定")
2. 從 [建置] 功能表選取 [Archive for Publishing] (封存以供發行)：

    [![](app-store-publishing-images/buildxs02new.png "選取 [Archive for Publishing] \(封存以供發行\)")](app-store-publishing-images/buildxs02new.png#lightbox)
3. 建立封存後，[封存] 檢視會隨即顯示：

    [![](app-store-publishing-images/buildxs03new.png "封存視圖")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>簽署並散發應用程式

每次建置應用程式進行封存，都會自動開啟 [封存] 檢視，顯示依解決方案分組的所有已封存專案。 根據預設，此檢視只會顯示目前開啟的解決方案。 若要查看所有具有封存的解決方案，請按一下 [Show all archives] (顯示所有封存) 選項。

建議您保留部署至客戶的封存 (App Store 或 Enterprise 部署)，以便日後代表所產生的任何偵錯資訊。

若要簽署應用程式並準備散發：

1. 選取 [**簽署並散發 ...** ], 如下所示:

    [![](app-store-publishing-images/buildxs04new.png ", 選取 [theSign 並散發 ...]")](app-store-publishing-images/buildxs04new.png#lightbox)
2. 如此將開啟發行精靈。 選取 **App Store** 散發管道以建立套件，然後開啟應用程式載入器：

    [![](app-store-publishing-images/distribute01.png "選取 App Store 散發通道")](app-store-publishing-images/distribute01.png#lightbox)
3. 在 [布建設定檔] 畫面上, 選取您的簽署身分識別和對應的布建設定檔, 或以另一個身分識別重新簽署:

    [![](app-store-publishing-images/distribute02.png "選取簽署身分識別和對應的布建設定檔")](app-store-publishing-images/distribute02.png#lightbox)
4. 驗證套件的詳細資料，然後按一下 [發佈] 以儲存您的 `.ipa` 套件：

    [![](app-store-publishing-images/distribute03.png "確認套件的詳細資料")](app-store-publishing-images/distribute03.png#lightbox)
5. 儲存 `.ipa` 後，即可準備透過應用程式載入器將應用程式上傳至 iTunes Connect：

    [![](app-store-publishing-images/distribute04.png "已透過應用程式載入器上傳至 iTunes Connect")](app-store-publishing-images/distribute04.png#lightbox)

建立與封存散發組建後，即可準備將您的應用程式提交至 iTunes Connect。

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>向 Apple 提交應用程式

散發組建完成後，您即可準備將 iOS 應用程式提交至 Apple 以供審查，並在 App Store 上發行。


當您儲存`.ipa`之後, Visual Studio for Mac 中的封存工作流程會自動開啟應用程式載入器:

1. 選取 [Deliver Your App] (傳遞您的應用程式)，並按一下 [選擇] 按鈕：

    [![](app-store-publishing-images/publishvs01.png "選取 [Deliver Your App] \(傳遞您的應用程式\)")](app-store-publishing-images/publishvs01.png#lightbox)

2. 在上方選取您建立的 ZIP 或 IPA 檔案，並按一下 [確定] 按鈕。
3. 應用程式載入器將會驗證檔案：

    [![](app-store-publishing-images/publishvs02.png "應用程式載入器驗證畫面")](app-store-publishing-images/publishvs02.png#lightbox)
4. 按一下 [下一步] 按鈕後，就會對 App Store 驗證應用程式：

    [![](app-store-publishing-images/publishvs03.png "針對 App Store 驗證的應用程式")](app-store-publishing-images/publishvs03.png#lightbox)
5. 按一下 [傳送] 按鈕將應用程式傳送至 Apple 進行審查。
6. 當檔案已成功上傳時，應用程式載入器會通知您。

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>iTunes Connect 狀態

如果您重新登入 iTunes Connect, 並從可用的應用程式清單中選取您的應用程式, iTunes Connect 中的狀態現在應該會顯示它正在**等待審核**(它可能會在處理時暫時讀取已**接收的上傳**):

[![](app-store-publishing-images/image21.png "ITunes Connect 中顯示等待審核的狀態")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>疑難排解

如果您在將 tvOS 應用程式提交至 Apple 電視 App Store 時遇到問題, 請參閱我們的[疑難排解](~/ios/tvos/troubleshooting.md)指南。 其中包含數個您可能會遇到的已知問題, 以及如何在 tvOS 中解決它們。

<a name="Summary" />

## <a name="summary"></a>總結

本文提供設定、建立和提交 Apple TV App Store 發行集之應用程式的逐步指南。 首先說明建立並安裝散發佈建設定檔所需的步驟。 接下來, 它會逐步解說如何使用 Visual Studio for Mac 來建立散發組建。 最後, 它會示範如何使用 iTunes Connect 和 Xcode 封存工具, 將應用程式提交至 Apple 電視 App Store。


## <a name="related-links"></a>相關連結

- [使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)
- [為新的 Apple TV 準備您的應用程式提交](https://developer.apple.com/tvos/submit/)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [常見的應用程式拒絕案例](https://developer.apple.com/app-store/review/rejections/)
- [App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)
