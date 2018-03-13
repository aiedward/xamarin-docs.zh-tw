---
title: "發行至 Apple TV 應用程式市集"
description: "這篇文章會示範如何設定、 建置及發行 Xamarin.tvOS 應用程式透過 Apple TV App Store 的發佈。 它包含涵蓋如何準備您的應用程式進行發佈、 如何使用 Apple 的工具來送出您的應用程式，供檢閱，以及，最後，如何將您的應用程式發行至 Apple TV App Store 的逐步指南。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: abb8ee30828e5d9856b9fd72cca8adb669959818
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="publishing-to-the-apple-tv-app-store"></a>發行至 Apple TV 應用程式市集

_這篇文章會示範如何設定、 建置及發行 Xamarin.tvOS 應用程式透過 Apple TV App Store 的發佈。它包含涵蓋如何準備您的應用程式進行發佈、 如何使用 Apple 的工具來送出您的應用程式，供檢閱，以及，最後，如何將您的應用程式發行至 Apple TV App Store 的逐步指南。_

順序應用程式散發給 Apple TV 的所有裝置，需要透過發佈的應用程式 Apple *Apple TV App Store*，讓應用程式市集 tvOS 應用程式的單一窗購物位置。 許多類型的應用程式的開發人員可以大寫發佈這一點 massive 成功。 Apple TV App Store 是周全的解決方案，提供應用程式開發人員發佈和付款系統。

提交至 Apple TV 應用程式市集的應用程式的程序包含：

1. 建立「應用程式識別碼」和選取「權利」。
2. 建立散發佈建設定檔。
3. 若要建置您的應用程式中使用此設定檔。
4. 正在提交您的應用程式，透過*iTunes Connect*。


在本文中，我們將討論佈建、 建置和送出的 Apple TV 應用程式市集散發應用程式所需的所有步驟。

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>在您提交應用程式前

提交至 Apple TV 應用程式市集的發行集的應用程式後，它將經歷檢閱程序由 Apple 以確保其符合 Apple 指導方針的品質和內容。 若您的應用程式未符合這些方針，Apple 將會予以退件，而您則必須解決 Apple 列出的不合規問題，然後重新提交。
因此，熟悉這些方針並嘗試以其作為基準改寫應用程式，將使您擁有通過 Apple 檢閱的最佳機會。 Apple 的指導方針位於[應用程式市集審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)和[準備您的應用程式提交新的 Apple tv](https://developer.apple.com/tvos/submit/)。

提交應用程式時有幾個特別需要注意的事項：

1. 請確定應用程式的描述符合應用程式中包含的功能。
2. 測試應用程式不會損毀，在正常使用。 這包括您支援每個 Apple TV 裝置上的使用方式。


Apple 也會維護一份 Apple TV 應用程式市集提交提示。 您可於此處閱讀這些提示：[Distributing on the App Store](https://developer.apple.com/appstore/resources/submission/tips.html) (在 App Store 上散發)。

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中設定應用程式

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa)是一套 web 型工具，以及其他項目，管理 Apple TV App Store 上的應用程式 tvOS。 Xamarin.tvOS 應用程式必須是正確的安裝程式，它可以提交至 Apple，以便檢閱及最後，會釋出銷售或 Apple TV App Store 中免費應用程式之前，在 iTunes Connect 中設定。

請執行下列動作：

1. 驗證 iTunes Connect 的**合約、稅金與銀行**區段有正確且最新的合約，以發行免費或付費的 iOS 應用程式。
2. 建立新**iTunes Connect 記錄**應用程式，並指定其**顯示名稱**（如同 Apple TV App Store 中所見）。
3. 選取 [售價] 或指明該應用程式將免費發行。
4. 提供**應用程式存放區圖示**（大型圖示） 和應用程式的動作，它支援 Apple TV 之裝置上的螢幕擷取畫面。 請參閱我們[使用圖示和映像](~/ios/tvos/app-fundamentals/icons-images.md)指南以取得詳細資料。
5. 提供明確的簡潔**描述**應用程式包括它的功能，而終端使用者獲得好處。
6. 提供**類別**，**子類別目錄**，和**關鍵字**可協助使用者在 Apple TV App Store 中尋找您的應用程式。
7. 應 Apple 要求提供您網站的**連絡**與**支援** URL。
8. 設定您的應用程式**分級**，這由 Apple TV App Store 上的家長監護。
9. 設定選用 App Store 技術，例如 **Game Center** 與**在應用程式內購買**。

如需詳細資訊，請參閱我們[在 iTunes Connect 中設定您的應用程式的 tvOS](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)文件。

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>準備 App Store 散發

將應用程式發行至 Apple TV 應用程式市集，您需要來建置應用程式散發，這牽涉到許多步驟。 下列各節涵蓋了準備 Xamarin.tvOS 應用程式發行集，因此可以建立並提交至 Apple TV 應用程式市集，供您檢閱和版本所需的所有項目。

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>應用程式服務的佈建

Apple 提供的特殊應用程式服務，也稱為權利，將可啟動 tvOS 應用程式中，當您為其建立的唯一識別碼。 不論您使用自訂的權利，或沒有，您仍需建立 Xamarin.tvOS 應用程式的唯一識別碼，才能將它發行 Apple TV App Store 上。

建立應用程式識別碼並選擇性選取權利包含下列步驟，內容涵蓋使用 Apple 的 Web 架構 iOS 佈建入口網站：

1. 選取**佈建** > **開發**。
2. 按一下 [+] 按鈕，並為新應用程式提供**名稱**與**套件組合識別碼**。
3. 捲動到畫面底部，然後選取任何**應用程式服務**，將需要 Xamarin.tvOS 應用程式。
4. 按一下 [繼續] 按鈕，並遵循畫面指示來建立新的 應用程式識別碼。

除了選取並設定必要的應用程式服務，定義您的應用程式識別碼時，您也需要設定應用程式識別碼和權利 Xamarin.tvOS 專案中編輯兩者`Info.plist`和`Entitlements.plist`檔案。

依下列方式在 Visual Studio for Mac:

1. 在方案總管中按兩下 `Info.plist` 檔案將其開啟以進行編輯。
2. 在**tvOS 應用程式目標**區段中，填寫您的應用程式的名稱並輸入**配套識別碼**時建立定義應用程式識別碼。
3. 將變更儲存至 `Info.plist` 檔案。
4. 在方案總管中按兩下 `Entitlements.plist` 檔案將其開啟以進行編輯。
5. 選取和設定，以符合您執行上述定義應用程式識別碼。 當安裝程式，為您 Xamarin.tvOS 應用程式所需的權利
6. 將變更儲存至 `Entitlements.plist` 檔案。

如需詳細指示，請參閱我們的[佈建應用程式](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices)文件。 雖然這份文件針對 iOS 撰寫，相同的步驟可用來佈建 Xamarin.tvOS 應用程式。

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>設定應用程式圖示，啟動映像和高層貨架映像

會接受由 Apple Apple TV App Store 中包含的 tvOS 應用程式，它需要適當的圖示、 啟動和上架所有將在執行的 Apple TV 裝置的映像。 您必須加入將會編譯成必要的影像資產`Assets.car`檔案，然後才能上傳至 iTunes Connect Xamarin.tvOS 應用程式的組合中所包含。

如需詳細指示，請參閱我們[使用圖示和映像](~/ios/tvos/app-fundamentals/icons-images.md)文件。

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>建立與安裝散發設定檔

使用 tvOS*佈建設定檔*控制部署特定應用程式建置的方式。 這些檔案包含用於簽署應用程式之憑證、應用程式識別碼，以及應用程式可安裝位置的相關資訊。 若為開發與臨機操作散發，佈建設定檔也會包含您可部署應用程式的允許裝置清單。 不過，Apple TV 應用程式市集散發，唯一的憑證和應用程式識別碼資訊會包含在內，因為公用通訊群組的唯一機制是透過 Apple TV App Store。

佈建包含下列步驟，過程會使用 Apple 的 Web 架構 iOS 佈建入口網站：

1.  選取 [佈建] > [散發] 。
2.  按一下 **+** 按鈕，然後選取您想要做為建立的散發設定檔的類型**Apple TV App Store**。
3.  從您要建立散發設定檔的下拉式清單中選取 [應用程式識別碼]。
4.  選取簽署該應用程式所需的憑證。
5.  輸入新**散發設定檔**的**名稱**並產生設定檔。
6.  重新整理在 Xcode 中可用的設定檔的清單。
7.  選取佈建設定檔，Visual Studio 中的分佈**App Store** _組建組態_。

如需詳細指示，請參閱[建立散發設定檔](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile)與[在 Xamarin.iOS 專案中選取散發設定檔](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。 同樣地，這些文件都 iOS 專屬，但是 tvOS 應用程式使用相同的技術。


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>為應用程式設定組建組態

根據預設，當您建立新的 Xamarin.tvOS 應用程式，_組建組態_會自動建立兩個**偵錯**和**發行**部署。 執行您將提交您的應用程式的最後一個組建至 Apple 之前, 有一些修改，您必須對基底**發行**組態。

請執行下列動作：

1. 以滑鼠右鍵按一下**專案名稱**中**方案總管 中**和選取範圍**選項**來開啟它們來進行編輯。
2. 如果您的目標 tvOS 的特定版本，選取在**tvOS 組建** > **iOS SDK 版本**。 TvOS 支援預覽版本，請保留此值設定為**預設**。
3. 連結會減少整體大小應用程式的 「 可散布移除未使用的方法、 屬性、 類別、 等等，和在大部分情況下應該保持為預設值的**連結 framework SDK 只**。 在某些情況下，例如當使用某些特定第 3 個合作對象文件庫，您可能會強制將此值設定為**不要連結**保留所需的項目移除。
4. 若要出貨 Xamarin.tvOS 應用程式，您將需要使用 LLVM 最佳化編譯器。 請確認**使用最佳化編譯器 LLVM**下核取方塊**發行**組態。
5. Apple 也需要 tvOS 應用程式使用 bitcode。 下一次**發行**組態中，加入`--bitcode=asmonly`至**其他 mtouch 引數**方塊。
6. **適用於 iOS 的最佳化 PNG 影像檔**應該選取核取方塊，因為這將有助於進一步減少您的應用程式的交付項目大小。
7. 偵錯應該*不*啟用會使得組建不必要地大。


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>建置與提交可散發項目

Xamarin.tvOS 應用程式正確設定，現在您已經準備好進行最終發佈組建，您將提交至 Apple，供您檢閱和版本。

#### <a name="build-your-archive"></a>建置封存

1. 在 Visual Studio for Mac 中選取 [發行] | [裝置] 組態：

    ![](app-store-publishing-images/buildxs01new.png "選取 [發行] 組態")
2. 從 [建置] 功能表選取 [Archive for Publishing] (封存以供發行)：

    [![](app-store-publishing-images/buildxs02new.png "選取發行的封存")](app-store-publishing-images/buildxs02new.png#lightbox)
3. 建立封存後，[封存] 檢視會隨即顯示：

    [![](app-store-publishing-images/buildxs03new.png "封存檢視")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>簽署並散發應用程式

每次建置應用程式進行封存，都會自動開啟 [封存] 檢視，顯示依解決方案分組的所有已封存專案。 根據預設，此檢視只會顯示目前開啟的解決方案。 若要查看所有具有封存的解決方案，請按一下 [Show all archives] (顯示所有封存) 選項。

建議您保留部署至客戶的封存 (App Store 或 Enterprise 部署)，以便日後代表所產生的任何偵錯資訊。

若要簽署應用程式並準備散發：

1. 選取**簽署並散發...**，如下所示：

    [![](app-store-publishing-images/buildxs04new.png "選取 theSign 與分佈...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. 如此將開啟發行精靈。 選取 **App Store** 散發管道以建立套件，然後開啟應用程式載入器：

    [![](app-store-publishing-images/distribute01.png "選取的應用程式市集發佈頻道")](app-store-publishing-images/distribute01.png#lightbox)
3. 在佈建設定檔畫面上，選取您簽署識別和對應佈建設定檔，或使用其他身分識別重新簽署：

    [![](app-store-publishing-images/distribute02.png "選取的簽署識別和對應佈建設定檔")](app-store-publishing-images/distribute02.png#lightbox)
4. 驗證套件的詳細資料，然後按一下 [發佈]  以儲存您的 `.ipa` 套件：

    [![](app-store-publishing-images/distribute03.png "請確認封裝的詳細資料")](app-store-publishing-images/distribute03.png#lightbox)
5. 儲存 `.ipa` 後，即可準備透過應用程式載入器將應用程式上傳至 iTunes Connect：

    [![](app-store-publishing-images/distribute04.png "上傳至 iTunes Connect 透過應用程式載入器")](app-store-publishing-images/distribute04.png#lightbox)

建立與封存散發組建後，即可準備將您的應用程式提交至 iTunes Connect。

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>向 Apple 提交應用程式

散發組建完成後，您即可準備將 iOS 應用程式提交至 Apple 以供審查，並在 App Store 上發行。


保存工作流程，適用於 Mac 的 Visual Studio 中將應用程式載入器會自動開啟，當您儲存`.ipa`:

2. 選取 [Deliver Your App] (傳遞您的應用程式)，並按一下 [選擇] 按鈕：

    [![](app-store-publishing-images/publishvs01.png "選取傳遞您的應用程式")](app-store-publishing-images/publishvs01.png#lightbox)

3. 在上方選取您建立的 ZIP 或 IPA 檔案，並按一下 [確定] 按鈕。
4. 應用程式載入器將會驗證檔案：

    [![](app-store-publishing-images/publishvs02.png "應用程式載入器驗證畫面")](app-store-publishing-images/publishvs02.png#lightbox)
5. 按一下 [下一步] 按鈕後，就會對 App Store 驗證應用程式：

    [![](app-store-publishing-images/publishvs03.png "正在驗證應用程式市集應用程式")](app-store-publishing-images/publishvs03.png#lightbox)
6. 按一下 [傳送] 按鈕將應用程式傳送至 Apple 進行審查。
7. 當檔案已成功上傳時，應用程式載入器會通知您。

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>iTunes Connect 狀態

如果您重新登入 iTunes Connect，並從可用的應用程式清單中選取您的應用程式，在 iTunes Connect 中的狀態現在應該會顯示它是**等待檢閱**(它可能會暫時讀取**上傳收到**它處理時）：

[![](app-store-publishing-images/image21.png "在 iTunes 狀態連線顯示等候檢閱")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>疑難排解

如果您有提交至 Apple TV 應用程式市集應用程式 Xamarin.tvOS 的問題，請參閱我們[疑難排解](~/ios/tvos/troubleshooting.md)指南。 它包含許多可能會遇到的已知的問題及如何解決 Xamarin.tvOS。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章提供設定、 建置和送出 Apple TV App Store 的發行集的應用程式的逐步指引。 首先說明建立並安裝散發佈建設定檔所需的步驟。 接下來，它會逐步解說如何建立發佈的組建使用 Visual Studio for Mac。 最後，它示範如何使用 iTunes Connect 和 Xcode 封存工具來提交至 Apple TV 應用程式市集的應用程式。


## <a name="related-links"></a>相關連結

- [使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)
- [準備您的應用程式提交新的 Apple tv](https://developer.apple.com/tvos/submit/)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [常見的應用程式拒絕案例](https://developer.apple.com/app-store/review/rejections/)
- [App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)
