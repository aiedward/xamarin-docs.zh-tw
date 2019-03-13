---
title: 使用 TestFlight 散發 Xamarin.iOS 應用程式
description: TestFlight 現在由 Apple 所擁有，是對 Xamarin.iOS 應用程式進行 Beta 測試的主要方法。 本文會引導您完成 TestFlight 程序的所有步驟，從上傳應用程式到使用 iTunes Connect。
ms.prod: xamarin
ms.assetid: BA880768-2BC8-41E4-B57E-A56F8EED4690
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 87be250bdc425558a8e386a8209596e18f13b3ed
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120512"
---
# <a name="using-testflight-to-distribute-xamarinios-apps"></a>使用 TestFlight 散發 Xamarin.iOS 應用程式

_TestFlight 現在由 Apple 所擁有，是對 Xamarin.iOS 應用程式進行 Beta 測試的主要方法。本文會引導您完成 TestFlight 程序的所有步驟，從上傳應用程式到使用 iTunes Connect。_

Beta 測試是軟體開發週期不可或缺的一部分，且有許多跨平台應用程式供應項目皆可以簡化這個程序，例如 [HockeyApp](http://hockeyapp.net/features/) \(英文\)、[Applause](http://www.applause.com/mobile-app-testing) \(英文\)，以及適用於 Android 應用程式的 Google Play 原生應用程式「Beta 版測試」。 本文內容將著重於 Apple 的 TestFlight。

TestFlight 是 Apple 供 iOS 應用程式使用的 Beta 測試服務，並只能透過 [iTunes Connect](https://itunesconnect.apple.com/) 存取。 它目前可供 iOS 8.0 應用程式和更新版本使用。 TestFlight 可同時讓內部和外部使用者進行 Beta 測試，且由於會對外部使用者進行 Beta 應用程式審查，因此能在發佈至 App Store 時，使最終審查的程序變得更加輕鬆。


在此之前，二進位檔需在 Visual Studio for Mac 內產生，並上傳至 TestFlightApp 網站以散發給測試人員。 新的程序具有許多改進功能，可讓您在 App Store 中推出高品質並經過充分測試的應用程式。 例如: 


- 針對外部測試所進行的 Beta 應用程式審查，可確保最終 App Store 審查的成功機率更高，因為兩者都需要遵循 Apple 的指導方針。
- 在上傳之前，必須向 iTunes Connect 註冊應用程式。 這可確保佈建設定檔、名稱與憑證皆能完全相符。
- TestFlight 應用程式現在是實際的 iOS 應用程式，因此運作速度更快。
- 一旦 Beta 測試完成之後，將應用程式移至審查階段的程序將既快速又有效率，只需按一下按鈕便能完成。

## <a name="requirements"></a>需求

只有 iOS 8.0 或更新版本的應用程式才可以透過 TestFlight 進行測試。

所有測試人員都必須在版本至少為 iOS 8 的裝置上測試應用程式。 不過，最佳做法會要求您在所有的 iOS 版本上測試應用程式

## <a name="provisioning"></a>佈建

若要使用 TestFlight 測試您的組建，必須利用新的 Beta 權利建立 *App Store 散發設定檔*。 此權利允許透過 TestFlight 進行 Beta 測試，而且任何**新的** App Store 散發設定檔都會自動包含此權利。 您可以依照[建立散發設定檔](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile)指南中的逐步指示，產生新的設定檔。

您可以[在 Xcode 中驗證您的組建](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)時，確認發佈設定檔是否已包含 Beta 權利，如下所示：

[![](testflight-images/validate-build.png "向 Apple 提交應用程式")](testflight-images/validate-build.png#lightbox)


## <a name="testflight-workflow"></a>TestFlight 工作流程

下列工作流程會說明開始使用 TestFlight 進行應用程式 Beta 測試所需的步驟：

1. 針對新的應用程式，請建立 [iTunes Connect 記錄](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)。
2. 將您的應用程式[封存和發佈](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)至 iTunes Connect。
3. 管理 Beta 測試：
    - 新增中繼資料。
    - 新增內部使用者：
        - 最多 25 個使用者。
    - 新增外部使用者：
        - 最多 1000 個使用者。
        - 需要 Beta 測試審查，這需要符合 Apple 指導方針的規範。
4. 接收來自使用者的意見反應，對其採取動作，然後返回步驟 2。

## <a name="create-an-itunes-connect-record"></a>建立 iTunes Connect 記錄

1.  使用 Apple 開發人員認證登入 [iTunes Connect 入口網站](https://itunesconnect.apple.com/)。
2.  選取 [我的應用程式]：

    [![](testflight-images/my-apps.png "選取 [我的應用程式]")](testflight-images/my-apps.png#lightbox)


3.  在 [我的應用程式] 畫面上，按一下畫面左上角的 **+** 按鈕以加入新的應用程式。 如果您有 Mac 和 iOS 開發人員帳戶，系統會提示您在此處選擇新的應用程式類型。

您將會看到 [新 iOS 應用程式] 提交視窗，其中必須包含與您應用程式的 Info.plist 完全相同的資訊

如需有關建立新 iTunes Connect 記錄的詳細資訊，請參閱[建立 iTunes Connect 記錄](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南。



###  <a name="completing-the-new-ios-app-submission-form"></a>完成新 iOS 應用程式提交表單

此表單應該反映您應用程式的 Info.plist 檔案中的確切資訊，如下所示：

[![](testflight-images/infoplist.png "應用程式的 Info.plist")](testflight-images/infoplist.png#lightbox)
[ ![](testflight-images/newiosapp.png "iTunes Connect 上的表單")](testflight-images/newiosapp.png#lightbox)

-  **名稱**：設定應用程式套件組合時所使用的描述性名稱。 這必須完全符合您 `Info.plist` 中的**應用程式名稱**項目。
-  **主要語言**：應用程式內使用的基礎語言。 這通常是您本身所使用的語言。
-  **套件組合識別碼**：列出您開發人員帳戶上建立之所有應用程式識別碼的下拉式功能表。
    *   **套件組合識別碼尾碼**：如果您已選取萬用字元套件組合識別碼 (亦即結尾為 * 的識別碼，如以上範例所示)，螢幕將會出現額外的方塊，提示您輸入套件組合識別碼尾碼。 在該範例中，[套件組合識別碼] 為 `mobi.chkn.*`，[尾碼] 則為 **PageView**。 這些在 `Info.plist` 中，就會組成 [套件組合識別碼]。
-  **版本**：所上傳之應用程式的版本號碼。 這是由開發人員所選擇。
-  **SKU**：SKU 是應用程式的唯一識別碼，使用者將看不到這個識別碼。 它與產品識別碼類似。 在上述範例中，我已經選擇日期，以及該日期的版本號碼。


## <a name="upload-your-app"></a>上傳您的應用程式

一旦建立 iTunes Connect 記錄之後，您就可以上傳新的組建。 請記住，組建必須要有新的 Beta 權利。

首先，請在 IDE 中建置[最終可散發項目](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)，然後透過應用程式載入器或 Xcode 中的封存功能[將您的應用程式提交到 Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

###  <a name="create-an-archive"></a>建立封存

 若要在 Visual Studio for Mac 中建置二進位檔，您必須使用 [封存] 功能。 以滑鼠右鍵按一下專案，然後選取 [封存以供發行]，如下所示：

 [![](testflight-images/new-archive.png "選取 [Archive for Publishing] \(封存以供發行\)")](testflight-images/new-archive.png#lightbox)


 如需詳細資訊，請參閱[建置可散發項目](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)指南。

###  <a name="sign-and-distribute-your-app"></a>簽署並散發應用程式

 建立封存將會自動開啟 [封存檢視]，顯示依方案分組的所有已封存專案。 若要簽署應用程式並準備散發，請選取 [簽署並散發]，如下所示：

[![](testflight-images/archive-view.png "建立封存將會自動開啟 [封存檢視]")](testflight-images/archive-view.png#lightbox)

 如此將開啟發行精靈。 選取 [App Store] 散發通道以建立套件，然後開啟應用程式載入器。 在 [佈建設定檔] 畫面上，選取您的簽署身分識別與佈建設定檔，或以另一個身分識別重新簽署。 確認套件的詳細資料，然後按一下 [發佈] 以儲存您的 `.ipa`

[![](testflight-images/group.png "選取您的簽署身分識別與佈建設定檔，或以另一個身分識別重新簽署")](testflight-images/group.png#lightbox)

 如需有關這些步驟的詳細資訊，請參閱[向 Apple 提交應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)一節。

###  <a name="submitting-your-build"></a>提交您的組建
 發佈精靈將會開啟應用程式載入器程式，以允許您將組建上傳至 iTunes Connect。 選取 [提交您的應用程式] 選項，然後上傳於上方所建立的 `.ipa` 檔案。 應用程式載入器將會驗證您的組建，並將其上傳至 iTunes Connect。

 如需有關這些步驟的詳細資訊，請參閱[向 Apple 提交應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)一節。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

###  <a name="building-your-final-distributable"></a>建置最終可散發項目
 由於適用於 Visual Studio 的 Xamarin 外掛程式不支援封存 Xamarin.iOS 應用程式以發佈至 App Store，有兩個選項可從 Visual Studio 發佈 iOS 應用程式。 這些是：

1. 上傳透過「建置臨機操作 IPA」命令所建立的 IPA。
1. 上傳壓縮的 `.app` 套件組合。

 [建置可散發項目](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)指南中同時包含這兩個選項的指示。

###  <a name="submitting-your-build"></a>提交您的組建
 若要將您的應用程式提交到 Apple，您必須移至組建主機，並使用隨 Xcode 安裝的應用程式載入器程式。 如需有關存取應用程式載入器的詳細資訊，請參閱 Apple 的[存取應用程式載入器](http://help.apple.com/itc/apploader/#/apdATD1E927-D1E1A1303-D1E927A1126)指南。

開啟之後，選取 [提交您的應用程式] 選項，然後上傳於上方所建立的壓縮檔或 `.ipa` 檔案。 應用程式載入器將會驗證您的組建，並將其上傳至 iTunes Connect。

 如需有關這些步驟的詳細資訊，請參閱[向 Apple 提交應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)一節。

-----


[發佈到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) 指南會更詳細地說明所有上述步驟，如需更深入了解 App Store 的提交程序，請參閱此指南。

回到 iTunes Connect 的 [我的應用程式] 區段之後，您應該會發現您的應用程式已經成功上傳。 現在您已經準備就緒，可以執行 Beta 測試了！

## <a name="manage-beta-testing"></a>管理 Beta 測試

### <a name="add-metadata"></a>新增中繼資料

若要開始使用 TestFlight，請瀏覽至應用程式的 [發行前版本] 索引標籤。 您應該會看到三個索引標籤，顯示 [組建]、[內部測試人員] 和 [外部測試人員] 的清單，如下所示：

[![](testflight-images/app-uploaded.png "[組建]、[內部測試人員] 和 [外部測試人員] 索引標籤")](testflight-images/app-uploaded.png#lightbox)

若要將中繼資料新增至您的應用程式，請按一下組建編號，然後按一下 [TestFlight]：

[![](testflight-images/metadata.png "新增中繼資料")](testflight-images/metadata.png#lightbox)

在 [測試資訊] 底下，您可以為測試人員提供關於您應用程式的重要資訊，例如：

- 測試的內容
- 應用程式的描述。
- 行銷 URL：這將會提供您要新增之應用程式的相關資訊。
- 隱私權原則 URL：提供貴公司隱私權原則相關資訊的 URL。
- 意見反應電子郵件。

請注意，內部測試人員並**不**需要此中繼資料，但您**必須**將它提供給外部測試人員。

<a name="beta-testing" />

### <a name="enable-beta-testing"></a>啟用 Beta 測試

當您準備好開始測試應用程式時，請開啟您版本的 [TestFlight Beta 測試] 開關：

[![](testflight-images/turn-on-testing.png "開啟 [TestFlight Beta 測試] 開關")](testflight-images/turn-on-testing.png#lightbox)

每個組建的有效期都是從您開啟 TestFlight Beta 開關之日起的 **60 天**。 您可以在 [測試資訊] 頁面上看到每個組建剩餘的天數：

[![](testflight-images/daysleft.png "[測試資訊] 頁面")](testflight-images/daysleft.png#lightbox)

您可以隨時關閉測試。

### <a name="internal-testers"></a>內部測試人員

內部測試人員是在 iTunes Connect 中，獲指派下列其中一個角色的開發小組成員：

-  **系統管理員**：系統管理員負責在 iTunes Connect 中新增和管理新使用者。
-  **法務**：Team 代理程式將會是唯一獲指派法務角色的系統管理員使用者。 此角色可讓他們簽署法律合約。
-  **技術**：技術使用者可以變更與應用程式相關的大部分屬性。 例如，編輯應用程式資訊、上傳二進位檔，以及傳送應用程式以供複審。

每個組建最多可與 25 個成員共用。

若要新增測試人員，請瀏覽至 iTunes Connect 主畫面上的 [使用者及角色]：

[![](testflight-images/users-and-roles.png "iTunes Connect 主畫面上的 [使用者及角色]")](testflight-images/users-and-roles.png#lightbox)

現有的 iTunes Connect 使用者將會出現在清單中。 若要選取這些使用者，請按一下其名稱，開啟 [內部測試人員] 開關，然後按一下 [儲存]：

[![](testflight-images/internal-tester.png "開啟 [內部測試人員] 開關")](testflight-images/internal-tester.png#lightbox)

若要新增不在清單中的使用者，請選取 [使用者] 旁邊的 **+** 按鈕，並提供 [名字]、[姓氏] 和電子郵件地址以建立帳戶。 該使用者必須確認其電子郵件，才能啟用帳戶：

[![](testflight-images/add-new-user.png "新增使用者")](testflight-images/add-new-user.png#lightbox)

如果您返回 [我的應用程式] > [發行前版本] > [內部測試人員]，現在將會看到已針對 TestFlight 內部 Beta 測試新增的使用者：

[![](testflight-images/select-users.png "已針對 TestFlight 內部 Beta 測試新增的使用者清單")](testflight-images/select-users.png#lightbox)

您可以選取這些測試人員的名稱，然後按一下 [邀請] 按鈕來邀請他們。 他們將會收到一封電子郵件，其中包含測試應用程式的邀請。

您可以在 [內部測試人員] 頁面的 [狀態] 欄中查看其邀請的狀態：

[![](testflight-images/status-added.png "邀請狀態")](testflight-images/status-added.png#lightbox)


### <a name="external-testers"></a>外部測試人員

邀請外部測試人員進行應用程式 Beta 測試之前，必須先通過 Beta 應用程式審查，因此必須符合 [App Store 審查方針](https://developer.apple.com/app-store/review/guidelines/) \(英文\)。

若要提交應用程式以供審查，按一下您組建旁邊的 [提交以供 Beta 應用程式審查] 文字，如下圖所示：

[![](testflight-images/beta-app-review.png "提交以供 Beta 應用程式審查")](testflight-images/beta-app-review.png#lightbox)

若要讓您的應用程式通過審查，您必須在 [TestFlight Beta 資訊] 頁面上輸入所有必要的中繼資料。

您現在可以開始準備邀請，並透過於 [外部測試人員] 索引標籤中輸入外部測試人員的電子郵件、[名字] 和 [姓氏]，新增最多 2000 個外部測試人員。 您輸入的電子郵件不一定要是其 Apple ID，這只是接收邀請所使用的電子郵件。

[![](testflight-images/add-external.png "邀請測試人員")](testflight-images/add-external.png#lightbox)

如果您有大量的外部測試人員，則可以使用 [匯入檔案] 連結來匯入每行皆包含下列格式的 `CSV` 檔案：

``` 
first name, last name, email address
```

您也可以將外部測試人員新增到不同的群組，以協助使您的測試人員保持井然有序。

一旦您輸入外部測試人員的詳細資料之後，請按一下 [新增]，並確認使用者已同意您邀請他們：

[![](testflight-images/confirm-consent.png "確認您經過使用者同意可以邀請他們")](testflight-images/confirm-consent.png#lightbox)

只有在 Beta 應用程式審查成功之後，您才能夠將邀請傳送給外部測試人員。 此時，組建頁面上 [外部] 底下的文字會變更為 [傳送邀請]。 按一下這個文字以將邀請傳送給您已經新增的所有測試人員。

如果您的應用程式被拒絕，您必須修正顯示在 [解決中心] 中的問題，然後重新提交整個已更新的二進位檔以供審查。

## <a name="as-a-beta-tester"></a>身為 Beta 測試人員

一旦您邀請測試人員之後，他們就會收到類似以下螢幕擷取畫面中的電子郵件：

[![](testflight-images/tester-email.png "範例邀請電子郵件")](testflight-images/tester-email.png#lightbox)

當他們按一下 [在 TestFlight 中開啟] 按鈕之後，您的應用程式將會在 TestFlight 應用程式中開啟；如果尚未下載該應用程式，系統會將他們導向至 App Store，並允許他們下載該應用程式。

一旦您的應用程式在 TestFlight 中開啟之後，它將會顯示測試內容的詳細資料，並提示測試人員將您的應用程式安裝至其 iOS 8.0 (或更新版本) 的裝置上：

[![](testflight-images/install-app.png "TestFlight 將會顯示測試內容的詳細資料")](testflight-images/install-app.png#lightbox)

測試組建將會顯示在裝置的主畫面上，並在應用程式名稱前方顯示橘色的點來表示。

測試人員可以透過 TestFlight 應用程式提供意見反應，而您將能透過於中繼資料中所提供的電子郵件地址接收到此資訊。

### <a name="beta-testing-complete"></a>Beta 測試完成

一旦完成 Beta 測試之後，您將可以提交您的應用程式，以供 Apple 進行 App Store 審查。 此程序可在 iTunes Connect 中以非常簡單的方式完成，只要按一下 [提交以供審查] 按鈕即可，如下所示：

[![](testflight-images/submit-for-review.png "按一下 [提交以供審查] 按鈕")](testflight-images/submit-for-review.png#lightbox)

## <a name="summary"></a>總結

本文已探討如何透過 iTunes Connect 使用 Apple 的 TestFlight Beta 測試。 其中已涵蓋如何將新組建上傳至 iTunes Connect，以及如何邀請內部和外部 Beta 測試人員使用該應用程式。

## <a name="related-links"></a>相關連結

- [建立 iTunes Connect Record](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [發行至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [佈建適用於 App Store 散發的應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#provisioning)
- [使用 Apple TestFlight Beta](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/BetaTestingTheApp.html#//apple_ref/doc/uid/TP40011225-CH35-SW2)
