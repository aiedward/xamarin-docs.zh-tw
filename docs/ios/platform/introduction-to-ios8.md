---
title: "IOS 8 的簡介"
description: "使用 iOS 8，Apple 提供的新架構和 Api 來激發和天堂開發人員。 本指南中我們將介紹這些新的 Api，並請參閱如何開發人員和使用者有哪些好處 iOS 8。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 3a77d1a3b597667d8944156b040c2819a5c79ca2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-8"></a>IOS 8 的簡介

_使用 iOS 8，Apple 提供的新架構和 Api 來激發和天堂開發人員。本指南中我們將介紹這些新的 Api，並請參閱如何開發人員和使用者有哪些好處 iOS 8。_

iOS 7 以視覺化方式會從哪些使用者和開發人員所預期，直接從第一個 iPhone OS 來變更整個 iOS 使用者介面。 IOS 8 會繼續以這個方法提供的許多架構開發人員而言，這可讓使用者控制其直接從其 iPhone 的生命週期的幾乎各個層面。 比方說與分析健全狀況和符合*HealthKit*，密碼是使用生物識別驗證過時*LocalAuthentication*，*應用程式擴充功能*開啟第 3 個合作對象應用程式之間的通訊通道和*HomeKit*允許能夠開啟您的房子到未來的首頁。 

如果 iOS 7 有關 delighting 使用者，iOS 8 著重於 delighting 開發人員一套完整的這些美味的新工具。 

本指南介紹新的 Api Xamarin.iOS 開發人員。  

另外還有一些 iOS 8，將會詳細說明這份文件結尾中已被取代的 Api。

## <a name="requirements"></a>需求

下列需要 Visual Studio 中建立 iOS 8 應用程式，適用於 Mac:

- **Xcode 7 和 iOS 8 或更新版本**– Apple 的最新安裝 Xcode 和 iOS 應用程式開發介面需要安裝和設定開發人員的電腦上。
- **Visual Studio for Mac** – 應安裝及設定使用者裝置上最新版本的 Visual Studio for Mac。
- **iOS 8 裝置或模擬器**– 執行來測試 iOS 8 的最新版本的 iOS 裝置。

## <a name="home-and-leisure"></a>首頁和休閒餐旅集團

iOS 8 有助於穩固地直接到透過使用 HomeKit 和 HealthKit 在家中的計劃 Apple，並將 iOS 裝置。 在本節中，我們將探討如何同時這些新的架構運作，以及它們如何整合到 Xamarin.iOS 應用程式。

## <a name="homekit"></a>HomeKit

控制您從您的 iPhone 裝置不是技術的新的應用程式。透過 iOS 應用程式可以控制許多連接家用產品。 不過 HomeKit 現在會進一步透過升級擸裝置的一般通訊協定，以及藉由特定製造商 iHome，例如 Philips 和 Honeywell 可用公用 API。 給使用者，這表示它們可以控制幾乎各個層面及其首頁順暢地從一個應用程式內。 它是讓他們知道他們使用 Philips 色調燈泡或巢狀警示不相關。 使用者也可以鏈結許多智慧首頁的處理程序一起"場景"。

HomeKit，與協力廠商應用程式和 Siri 可探索附屬應用程式並將它們加入到其個人的主要組態資料庫、 編輯和作用於此資料，和與附屬應用程式和其執行動作的服務進行通訊。

### <a name="configuration"></a>組態

下圖顯示基本的 HomeKit 附屬應用程式的組態階層架構：

![](introduction-to-ios8-images/image1.png "這個圖表會顯示基本的 HomeKit 附屬應用程式的組態階層架構")
 
若要開始使用 HomeKit，開發人員必須確定其佈建設定檔具有所選取的 HomeKit 服務。 Apple 也提供了開發人員的 HomeKit 模擬器增益集，Xcode。 在中找到此[Apple 開發人員中心](https://developer.apple.com/downloads/index.action)下`Hardware IO Tools for Xcode`。 

如需詳細資訊，請參閱我們[HomeKit](~/ios/platform/homekit.md)指南。

## <a name="healthkit"></a>HealthKit

HealthKit 是 iOS 8 的健全狀況的相關資訊提供集中式、 協調，且安全的資料存放區中導入的架構。 作業系統可確保隱私權和安全性的健全狀況資訊和健全狀況應用程式時，使用者的儀表板。 使用使用者的權限、 應用程式可以讀取並寫入各式各樣的健全狀況資訊。

如需有關如何使用這個 Xamarin.iOS 應用程式中的詳細資訊，請參閱[簡介 HealthKit](~/ios/platform/healthkit.md)指南。

## <a name="extending-iphone-functionality"></a>擴充功能的 iPhone
IOS8，與開發人員會提供更多控制誰可以使用其應用程式和增加的功能更開啟之間進行通訊協力廠商應用程式。 應用程式擴充功能和文件選擇器等功能開啟世界的應用程式可以如何使用 Apple 的生態系統中的可能性。

### <a name="app-extensions"></a>應用程式擴充功能
應用程式擴充功能，以 oversimplify，是讓第三方應用程式之間的通訊方式。 若要維持高安全性標準，以及義務沙箱化應用程式的完整性，此通訊不會發生直接在應用程式之間。 相反地，它由執行*延伸*中間。

建立應用程式擴充功能的第一個步驟是定義正確的擴充點，這是很重要的確保的行為和正確的應用程式開發介面的可用性。 若要建立 Visual Studio for Mac 應用程式擴充功能，將它加入現有的應用程式由您方案中加入新的專案。

在**新專案**對話方塊瀏覽至**C#** > **iOS** > **統一的 API**  >  **擴充功能**，如以下螢幕擷取畫面所示：

![](introduction-to-ios8-images/image2.png "建立新的擴充功能")
 
新增專案 對話方塊會提供七個新的專案範本建立應用程式擴充功能，並於下方討論。 請注意，許多延伸模組與其他新的 Api，在 iOS 中，例如文件選擇器相關聯：

- **動作**– 這可讓開發人員建立可讓使用者執行特定工作的唯一的自訂動作按鈕
- **自訂鍵盤**– 這可讓開發人員將加入的範圍加入自己自訂的其中一個內建 Apple 鍵盤。 常用鍵盤 Swype 會使用此鍵盤帶入 iOS。
- **文件選擇器**– 這包含文件選擇器檢視控制器可讓使用者存取應用程式的沙箱以外的檔案。
- **文件選擇器檔案的提供者**– 這會使用文件選擇器的檔案提供安全的儲存體。
- **相片編輯**– 此篩選會展開並編輯相片應用程式，以讓使用者有更多的控制和更多選項時編輯他們的相片中已由 Apple 提供的工具。
- **現今**– 這可讓應用程式通知中心 [今日] 部分中顯示的 widget。

如需 Xamarin 中使用應用程式擴充功能的詳細資訊，請參閱[應用程式延伸模組簡介](~/ios/platform/extensions.md)指南。

### <a name="touch-id"></a>Touch ID

Touch ID iOS 7 中引進來驗證使用者，密碼類似。 不過，已解除鎖定裝置、 使用應用程式存放區、 使用 iTunes，並驗證 iCloud keychain 只限制 

現在有兩種方式可使用 Touch ID 做為驗證機制使用本機的驗證 API 的 iOS 8 應用程式中。 您正在不可能使用本機驗證遠端驗證。

首先，它可協助透過使用新的金鑰鏈存取控制清單 (Acl) 的現有金鑰鏈服務。 Keychain 資料可以是與成功驗證的使用者指紋解除鎖定。

其次，LocalAuthentication 提供兩種方法來驗證您的應用程式在本機。 開發人員應該使用`CanEvaluatePolicy`來判斷裝置是否能夠接受 Touch ID，然後`EvaluatePolicy`啟動驗證作業。

Touch ID 和了解如何將其整合到 Xamarin.iOS 應用程式的詳細資訊，請參閱[簡介至 TouchID](~/ios/platform/touchid.md)輔助線。

### <a name="document-picker"></a>文件選擇器

一次回文件選擇器適用於使用者的 iCloud 磁碟機，以允許使用者開啟不同的應用程式中已建立、 匯入以及操作它們並將它們匯出的檔案。 這會建立具直覺性的工作流程，並因此更好的經驗，為使用者。 進一步 iCloud 同步會採用此一步驟中，任何一個應用程式中所做的變更也會反映一致地跨所有裝置。

若要深入了解文件選擇器更深入，並了解如何將其整合到 Xamarin.iOS 應用程式，請參閱[文件選擇器簡介](~/ios/platform/document-picker.md)指南。

### <a name="handoff"></a>遞交

遞交，是較大的持續性功能的一部分，會優先整合 OS X 和 iOS 的進一步的步驟。 這包括跨平台 AirDrop，才會呼叫 iPhone、 iPad 和 Mac 和增強功能網際網路共用功能從您的 iPhone 上的 SMS 能力。

遞交 iOS 8 和 Yosemite，搭配運作，且需要使用您想要使用的 iCloud 帳戶來登入不同的裝置。 它應該搭配預先安裝的 Apple 應用程式，包括 Safari、 iWork、 對應、 行事曆和連絡人。

如需詳細資訊，請參閱我們[遞交](~/ios/platform/handoff.md)指南。

## <a name="unified-storyboards"></a>統一的分鏡腳本
iOS 8 包含新建立的使用者介面使用的機制更簡單 — 統一的分鏡腳本。 可以涵蓋各種不同硬體的螢幕大小的單一分鏡腳本與可以建立快速且回應迅速的檢視表中，則為 true 的 「 一次設計，使用多個 」 樣式。

在之前 iOS8，開發人員使用`UIInterfaceOrientation`區別縱向或橫向模式和`UIInterfaceIdiom`區別 iOS 裝置。 在 iOS8 就不再需要建立個別的分鏡腳本，適用於 iPhone 和 iPad 裝置 — 方向和裝置由使用*大小類別*。

每個裝置類別定義的大小，在垂直和水平軸，並有兩種類型的大小在 iOS 8 中的類別：

- **一般**-這是大型的螢幕大小 （例如 iPad) 或小工具，可讓大型 （例如 UIScrollView 印象
- **Compact** -這是針對較小裝置 （例如 iPhone)。 此大小會考慮到裝置的方向。

如果同時使用兩個概念，則結果會是 2 x 2 方格，定義可用於這兩個不同的方向，不同可能大小，如下列圖表所示：

![](introduction-to-ios8-images/image3.png "代表這兩個不同方向進行定義可以用不同可能大小的 2 x 2 方格圖表")
 
如需有關大小類別的詳細資訊，請參閱[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)。

## <a name="photo-kit"></a>相片套件
相片套件是一個新的架構，可讓應用程式來查詢系統映像庫建立自訂使用者介面來檢視和修改其內容。 它包含的數字，表示影像和視訊資產，以及資產，例如專輯和資料夾的集合類別。

如需詳細資訊，請參閱我們[PhotoKit](~/ios/platform/photokit.md)指南。

## <a name="games"></a>遊戲

<a name="scenekit" />

### <a name="scene-kit"></a>場景套件

場景套件是 3D 場景的圖形，可簡化使用 3D 圖形 API。 它引進 OS X 10.8，和 iOS 8 現在有了。 使用場景套件建立沈浸式 3D 視覺效果和 3D 的休閒遊戲不需要 OpenGL 的專業知識。 常見的場景圖形概念上建置，場景套件抽象化 OpenGL 和 OpenGL ES，因此很容易就能加入 3D 內容至應用程式的複雜性。 不過，如果您是 OpenGL 專家，場景套件會有很棒支援直接與 OpenGL 以及繫結。 也包含許多功能，可補充 3D 圖形，例如物理，並充分整合數個其他 Apple 架構，例如核心動畫、 Core 映像和套件精靈。

如需詳細資訊，請參閱我們[SceneKit](~/ios/platform/gaming/scenekit.md)文件。

<a name="spritekit" />

### <a name="sprite-kit"></a>套件精靈

精靈套件，2D 遊戲，向 Apple 架構有一些有趣的新功能，在 iOS 8 和 OS X Yosemite。 這些包括整合場景套件、 著色器支援、 光源、 陰影、 條件約束、 法線貼圖產生與物理增強功能。 特別是，新物理功能使遊戲中加入實際的效果很容易。

如需詳細資訊，請參閱我們[SpriteKit](~/ios/platform/gaming/spritekit.md)文件。

## <a name="other-changes"></a>其他變更
為上述的 iOS 8 中的重大變更，以及 Apple 此外已更新許多現有的架構。 這些詳述如下：

- **[核心映像](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** – Apple 所加入的矩形區域，偵測更佳的支援已擴充其映像處理架構和 QR 代碼映像內。 Mike Bluestein 瀏覽這在他的部落格 post 詳述[偵測中 iOS 8 的映像](http://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>已被取代的 API
具有所有所做的改進 iOS 8 中，已取代的 Api。 其中某些如下所述。

- **[Uiapplication #](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  – 已取代的方法和屬性，用來註冊遠端通知。 這些是 registerForRemoteNotificationTypes enabledRemoteNotificationTypes。
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  – 特性和大小類別已被取代的方法和屬性，用來描述介面方向。 請參閱[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)如需有關如何使用這些項目。

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  – 這已被取代 UISearchController iOS8 中。

## <a name="summary"></a>總結
本文章中我們探討了一些由 Apple iOS 8 所引進的新功能。



## <a name="related-links"></a>相關連結

- [UIKitEnhancements （範例）](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [應用程式延伸模組簡介](~/ios/platform/extensions.md)
- [CloudKit 簡介](~/ios/data-cloud/intro-to-cloudkit.md)
- [文件選擇器簡介](~/ios/platform/document-picker.md)
- [HealthKit 簡介](~/ios/platform/healthkit.md)
- [手動攝影機控制項簡介](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [TouchID 簡介](~/ios/platform/touchid.md)
- [統一的分鏡腳本的簡介](~/ios/user-interface/storyboards/unified-storyboards.md)
