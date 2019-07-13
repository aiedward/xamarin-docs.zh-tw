---
title: 發行至 Apple TV App Store
description: 本文件說明如何將應用程式發行至 Apple TV App Store。 它討論如何設定、 佈建、 建置和送出使用 Xamarin 建置的 tvOS 應用程式。
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 73ee7fc3c28fc7a8476010e8bf7567b3e5ef590d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865081"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>發行至 Apple TV App Store

發佈至 Apple TV 的所有裝置的應用程式，Apple 要求應用程式必須透過*Apple TV App Store*，讓應用程式市集的 tvOS 應用程式一次購物地點。 許多類型的應用程式的開發人員可以在此單一散發點的大成功獲得龐大大寫。 Apple TV App Store 是周全的解決方案，提供應用程式開發人員散發與支付系統。

提交至 Apple TV App Store 應用程式的程序牽涉到：

1. 建立「應用程式識別碼」  和選取「權利」  。
2. 建立散發佈建設定檔。 
3. 若要建置您的應用程式中使用此設定檔。
4. 提交您的應用程式，透過*iTunes Connect*。


在本文中，我們將涵蓋所有佈建、 建置和提交 Apple TV App Store 散發應用程式所需的步驟。

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>在您提交應用程式前

提交至 Apple TV App Store 的發行集的應用程式後，會透過檢閱程序由 Apple 以確保其符合 Apple 的品質與內容的指導方針。 若您的應用程式未符合這些方針，Apple 將會予以退件，而您則必須解決 Apple 列出的不合規問題，然後重新提交。
因此，熟悉這些方針並嘗試以其作為基準改寫應用程式，將使您擁有通過 Apple 檢閱的最佳機會。 Apple 的指導方針可[App Store 審核指南](https://developer.apple.com/appstore/resources/approval/guidelines.html)並[準備您的應用程式提交新的 Apple tv](https://developer.apple.com/tvos/submit/)。

提交應用程式時有幾個特別需要注意的事項：

1. 請確定應用程式的描述符合應用程式中的功能。
2. 測試應用程式不會在正常使用情況下損毀。 這包括您在支援每個 Apple TV 裝置上的使用方式。


Apple 也會維護一份 Apple TV App Store 提交提示。 您可於此處閱讀這些提示：[Distributing on the App Store](https://developer.apple.com/appstore/resources/submission/tips.html) (在 App Store 上散發)。

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中設定應用程式

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa)是 web 型工具，以及其他項目，管理您的 tvOS 應用程式，Apple TV App Store 上的套件。 Xamarin.tvOS 應用程式必須是正確的安裝程式，它可以提交給 Apple 進行審查，最終會是銷售或發行在 Apple TV App Store 的免費應用程式之前，在 iTunes Connect 中設定。

請執行下列動作：

1. 驗證 iTunes Connect 的**合約、稅金與銀行**區段有正確且最新的合約，以發行免費或付費的 iOS 應用程式。
2. 建立新**iTunes Connect Record**應用程式，並指定其**顯示名稱**（如同在 Apple TV App Store 中所見）。
3. 選取 [售價]  或指明該應用程式將免費發行。
4. 提供**App Store 圖示**（大型圖示） 和您在動作中，它支援在 Apple TV 裝置上的應用程式的螢幕擷取畫面。 請參閱我們[使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)指南以取得詳細資料。
5. 提供明確、 簡潔**描述**應用程式中，包括其功能與終端使用者的權益。
6. 提供**分類**，**子類別目錄**，並**關鍵字**來協助使用者在 Apple TV App Store 中尋找您的應用程式。
7. 應 Apple 要求提供您網站的**連絡**與**支援** URL。
8. 設定您的應用程式**分級**，這由 Apple TV App Store 上的家長監護。
9. 設定選用 App Store 技術，例如 **Game Center** 與**在應用程式內購買**。

如需詳細資訊，請參閱我們[在 iTunes Connect 中設定您的 tvOS 應用程式](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)文件。

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>準備 App Store 散發

若要將應用程式發行至 Apple TV App Store 中，必須先建置散發，其中包含許多步驟。 下列各節涵蓋準備發行集的 Xamarin.tvOS 應用程式，使其可建置，並將它提交至 Apple TV App Store 進行審查與發行所需的所有項目。

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>應用程式服務的佈建

Apple 提供的特殊應用程式服務，又稱權利，可為您的 tvOS 應用程式啟動時為其建立的唯一識別碼。 不論您使用自訂權利，您仍然必須建立 Xamarin.tvOS 應用程式的唯一識別碼，它可以在 Apple TV App Store 上發行之前。

建立應用程式識別碼並選擇性選取權利包含下列步驟，內容涵蓋使用 Apple 的 Web 架構 iOS 佈建入口網站：

1. 選取 **佈建** > **開發**。
2. 按一下 [+]  按鈕，並為新應用程式提供**名稱**與**套件組合識別碼**。
3. 捲動到畫面底部，然後選取任何**應用程式服務**Xamarin.tvOS 應用程式所需。
4. 按一下 [繼續]  按鈕，並遵循畫面指示來建立新的 應用程式識別碼。

除了選取並設定所需的應用程式服務，定義您的應用程式識別碼時，您也需要 Xamarin.tvOS 專案中設定應用程式識別碼與權利，藉由編輯兩者`Info.plist`和`Entitlements.plist`檔案。

依下列方式在 Visual Studio for Mac:

1. 在方案總管  中按兩下 `Info.plist` 檔案將其開啟以進行編輯。
2. 在  **tvOS 應用程式目標**一節，填寫您的應用程式的名稱，然後輸入**套件組合識別碼**時建立定義應用程式識別碼。
3. 將變更儲存至 `Info.plist` 檔案。
4. 在方案總管  中按兩下 `Entitlements.plist` 檔案將其開啟以進行編輯。
5. 選取並設定需要您 Xamarin.tvOS 應用程式，使其符合您定義應用程式識別碼。 當執行上述安裝程式的權利
6. 將變更儲存至 `Entitlements.plist` 檔案。

如需詳細指示，請參閱我們的[佈建應用程式](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices)文件。 雖然這份文件所撰寫適用於 iOS，相同的步驟用來佈建 Xamarin.tvOS 應用程式。

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>設定應用程式圖示、 啟動影像與 Top Shelf 影像

若要使 Apple 接受 Apple TV 應用程式市集中的包含 tvOS 應用程式，它需要適當的圖示、 啟動及 Top Shelf 影像所有將會在執行的 Apple TV 裝置。 您將需要新增所需的影像資產，將會編譯成`Assets.car`檔案，然後才能上傳至 iTunes Connect Xamarin.tvOS 應用程式的組合中所包含。

如需詳細指示，請參閱我們[使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)文件。

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>建立與安裝散發設定檔

使用 tvOS*佈建設定檔*控制部署的特定應用程式組建的方式。 這些檔案包含用於簽署應用程式之憑證、應用程式識別碼  ，以及應用程式可安裝位置的相關資訊。 若為開發與臨機操作散發，佈建設定檔也會包含您可部署應用程式的允許裝置清單。 不過，Apple TV App Store 散發，唯一的憑證和應用程式識別碼資訊都包含在內，因為公開散發機制是透過 Apple TV App Store。

佈建包含下列步驟，過程會使用 Apple 的 Web 架構 iOS 佈建入口網站：

1.  選取 [佈建]   > [散發]  。
2.  按一下  **+** 按鈕，然後選取您想要建立為的散發設定檔的型別**Apple TV App Store**。
3.  從您要建立散發設定檔的下拉式清單中選取 [應用程式識別碼]  。
4.  選取簽署應用程式所需的憑證。
5.  輸入新**散發設定檔**的**名稱**並產生設定檔。
6.  重新整理在 Xcode 中可用的設定檔清單。
7.  選取佈建設定檔，在 Visual Studio 中的分佈**App Store** _組建組態_。

如需詳細指示，請參閱[建立散發設定檔](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile)與[在 Xamarin.iOS 專案中選取散發設定檔](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。 同樣地，這些文件都 iOS 專屬，但是 tvOS 應用程式使用相同的技巧。


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>為應用程式設定組建組態

根據預設，當您建立新的 Xamarin.tvOS 應用程式中，_組建組態_會自動建立兩個**偵錯**並**版本**部署。 在執行之前您要提交您的應用程式的最後一個組建至 Apple，有幾項修改，您必須對基底**發行**組態。

請執行下列動作：

1. 以滑鼠右鍵按一下**專案名稱**中**方案總管]** 和 [選取項目**選項**將其開啟進行編輯。
2. 如果您的目標特定 tvOS 版本，請選取它底下**tvOS 組建** > **iOS SDK 版本**。 TvOS 支援預覽版本，請將此值設定為**預設**。
3. 連結可縮小整體應用程式的可散布移除未使用的方法、 屬性、 類別、 等等，並在大部分情況下應將其保持預設值**連結 framework SDK 只**。 在某些情況下，例如當使用一些特定第 3 個合作對象的程式庫，您可能會將此值設定為強制**不要連結**保留所需的項目移除。
4. 出貨 Xamarin.tvOS 應用程式，您必須使用 LLVM 最佳化編譯器。 請確認**使用 LLVM 最佳化編譯器**下方核取方塊**發行**組態。
5. Apple 也需要 tvOS 應用程式使用 bitcode。 一次**發行**組態中，新增`--bitcode=asmonly`要**其他 mtouch 引數** 方塊中。
6. **適用於 iOS 的最佳化 PNG 影像檔**應該選取核取方塊，因為這將能進一步減少您的應用程式交付項目大小。
7. 偵錯應該*不*會啟用，因為它會使組建產生不必要的較大。


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>建置與提交可散發項目

Xamarin.tvOS 應用程式設定正確，您就準備好最終散發建置，您將提交給 Apple 進行審查與發行。

#### <a name="build-your-archive"></a>建置封存

1. 在 Visual Studio for Mac 中選取 [發行] | [裝置]  組態：

    ![](app-store-publishing-images/buildxs01new.png "選取發行組態")
2. 從 [建置]  功能表選取 [Archive for Publishing] (封存以供發行)  ：

    [![](app-store-publishing-images/buildxs02new.png "選取 [Archive for Publishing] \(封存以供發行\)")](app-store-publishing-images/buildxs02new.png#lightbox)
3. 建立封存後，[封存]  檢視會隨即顯示：

    [![](app-store-publishing-images/buildxs03new.png "[封存] 檢視")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>簽署並散發應用程式

每次建置應用程式進行封存，都會自動開啟 [封存] 檢視  ，顯示依解決方案分組的所有已封存專案。 根據預設，此檢視只會顯示目前開啟的解決方案。 若要查看所有具有封存的解決方案，請按一下 [Show all archives] (顯示所有封存)  選項。

建議您保留部署至客戶的封存 (App Store 或 Enterprise 部署)，以便日後代表所產生的任何偵錯資訊。

若要簽署應用程式並準備散發：

1. 選取**簽署並散發...** ，如下所示：

    [![](app-store-publishing-images/buildxs04new.png "選取 theSign 並散發...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. 如此將開啟發行精靈。 選取 **App Store** 散發管道以建立套件，然後開啟應用程式載入器：

    [![](app-store-publishing-images/distribute01.png "選取 App Store 散發通道")](app-store-publishing-images/distribute01.png#lightbox)
3. 在 [佈建設定檔] 畫面中，選取您簽署身分識別與對應的佈建設定檔，或使用其他身分識別重新簽署：

    [![](app-store-publishing-images/distribute02.png "選取簽署身分識別與對應的佈建設定檔")](app-store-publishing-images/distribute02.png#lightbox)
4. 驗證套件的詳細資料，然後按一下 [發佈]  以儲存您的 `.ipa` 套件：

    [![](app-store-publishing-images/distribute03.png "確認封裝的詳細資料")](app-store-publishing-images/distribute03.png#lightbox)
5. 儲存 `.ipa` 後，即可準備透過應用程式載入器將應用程式上傳至 iTunes Connect：

    [![](app-store-publishing-images/distribute04.png "上傳至 iTunes Connect，透過應用程式載入器")](app-store-publishing-images/distribute04.png#lightbox)

建立與封存散發組建後，即可準備將您的應用程式提交至 iTunes Connect。

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>向 Apple 提交應用程式

散發組建完成後，您即可準備將 iOS 應用程式提交至 Apple 以供審查，並在 App Store 上發行。


Visual Studio for Mac 中的封存工作流程會自動開啟應用程式載入器，在您儲存`.ipa`:

1. 選取 [Deliver Your App] (傳遞您的應用程式)  ，並按一下 [選擇]  按鈕：

    [![](app-store-publishing-images/publishvs01.png "選取 [Deliver Your App] \(傳遞您的應用程式\)")](app-store-publishing-images/publishvs01.png#lightbox)

2. 在上方選取您建立的 ZIP 或 IPA 檔案，並按一下 [確定]  按鈕。
3. 應用程式載入器將會驗證檔案：

    [![](app-store-publishing-images/publishvs02.png "應用程式載入器驗證畫面")](app-store-publishing-images/publishvs02.png#lightbox)
4. 按一下 [下一步]  按鈕後，就會對 App Store 驗證應用程式：

    [![](app-store-publishing-images/publishvs03.png "正在驗證對 App Store 應用程式")](app-store-publishing-images/publishvs03.png#lightbox)
5. 按一下 [傳送]  按鈕將應用程式傳送至 Apple 進行審查。
6. 當檔案已成功上傳時，應用程式載入器會通知您。

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>iTunes Connect 狀態

如果您重新登入 iTunes Connect，並從可用的應用程式清單中選取您的應用程式，iTunes Connect 中的狀態現在應該會顯示它是**等候檢閱**(它可能會暫時讀取**已收到上傳**在處理時）：

[![](app-store-publishing-images/image21.png "在 iTunes 中的狀態可讓您連線顯示等候檢閱")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>疑難排解

如果您有應用程式提交 Xamarin.tvOS 至 Apple TV App Store 的問題，請參閱我們[疑難排解](~/ios/tvos/troubleshooting.md)指南。 它包含數個您可能會遇到的已知的問題及如何解決這些問題中 Xamarin.tvOS。

<a name="Summary" />

## <a name="summary"></a>總結

本文提供設定、 建置和提交應用程式，以 Apple TV App Store 發行的逐步指南。 首先說明建立並安裝散發佈建設定檔所需的步驟。 接下來，則逐步解說如何使用 Visual Studio for Mac 建立散發組建。 最後，它說明了如何使用 iTunes Connect 和 Xcode 封存工具應用程式提交至 Apple TV App Store。


## <a name="related-links"></a>相關連結

- [使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)
- [準備您的應用程式提交新的 Apple tv](https://developer.apple.com/tvos/submit/)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [常見的應用程式拒絕案例](https://developer.apple.com/app-store/review/rejections/)
- [App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)
