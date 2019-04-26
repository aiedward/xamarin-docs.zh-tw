---
title: iOS 8 簡介
description: Ios 8，Apple 提供眾多新的架構和 Api，可激發創意和開發人員會非常喜歡這樣。 本指南中我們將介紹這些新的 Api，並了解 iOS 8 助益開發人員和使用者。
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 9299322eb20561444262c2b2ba87191d2bddcde4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317613"
---
# <a name="introduction-to-ios-8"></a>iOS 8 簡介

_Ios 8，Apple 提供眾多新的架構和 Api，可激發創意和開發人員會非常喜歡這樣。本指南中我們將介紹這些新的 Api，並了解 iOS 8 助益開發人員和使用者。_

iOS 7 會從哪些使用者和開發人員已移至預期，直接從第一個 iPhone OS，以視覺化方式變更整個 iOS 使用者介面。 IOS 8 會繼續以此方式，方法是提供的許多架構開發人員，可讓使用者控制其直接從他們的 iPhone 的生命週期的各個層面。 比方說與分析健康情況和適用性*HealthKit*，密碼是使用生物識別驗證已過時*LocalAuthentication*，*應用程式擴充功能*開啟之間的通訊通道第 3 方應用程式，並*HomeKit*能讓您將您的房子變成未來的首頁。 

關於滿意的使用者 iOS 7 時，iOS 8 著重於滿意的開發人員提供一套完整的這些美味的新工具。 

本指南適用於 Xamarin.iOS 開發人員介紹新的 Api。  

另外還有幾個 iOS 8，詳述結尾的這份文件中已被取代的 Api。

## <a name="requirements"></a>需求

需要以下項目 for Mac 在 Visual Studio 中建立 iOS 8 應用程式：

- **Xcode 7 和 iOS 8 或更新版本**– Apple 的最新 Xcode 和 iOS Api 需要安裝並設定開發人員電腦上。
- **Visual Studio for Mac** – 應該安裝並設定使用者裝置上的 Visual Studio for Mac 的最新版本。
- **iOS 8 的裝置或模擬器**– 執行 iOS 8 的測試的最新版本的 iOS 裝置。

## <a name="home-and-leisure"></a>家庭與休閒

iOS 8 協助才能穩固地植 Apple 和 iOS 裝置，直接進入您的首頁，透過使用 HomeKit 和 HealthKit 的核心。 在本節中，我們將探討這些查看這兩種新的架構運作，以及它們如何整合到您的 Xamarin.iOS 應用程式。

## <a name="homekit"></a>HomeKit

控制您的應用裝置，從您的 iPhone 不是技術的新的應用程式;透過 iOS 應用程式，您可以控制許多連線-首頁產品。 不過 HomeKit 現在能更進一步提升家用自動化裝置的常見通訊協定，以及讓特定製造商 iHome，例如 Philips 和 Honeywell 都可以使用公用 API。 給使用者，這表示他們可以控制順暢地從其首頁的幾乎各個層面內一個應用程式。 它是讓他們知道他們正在使用 Philips Hue 燈泡或巢狀警示不相關。 使用者也可以鏈結多個智慧首頁的處理程序一起 「 場景 」。

與 HomeKit，第三方應用程式和 Siri 可探索附屬應用程式並將它們新增至其個人的主要組態資料庫、 編輯和採取這項資料，和與附屬應用程式和其執行動作的服務進行通訊。

### <a name="configuration"></a>組態

下圖顯示基本階層 HomeKit 配件的組態：

![](introduction-to-ios8-images/image1.png "下圖顯示基本階層 HomeKit 配件的組態")
 
若要開始使用 HomeKit，開發人員必須確定其佈建設定檔已選取的 HomeKit 服務。 Apple xcode 也提供開發人員提供的 HomeKit 模擬器增益集。 這篇[Apple Developer Center](https://developer.apple.com/downloads/index.action)下方`Hardware IO Tools for Xcode`。 

如需詳細資訊，請參閱我們[HomeKit](~/ios/platform/homekit.md)指南。

## <a name="healthkit"></a>HealthKit

HealthKit 是在 iOS 8 的健全狀況的相關資訊提供集中式、 協調及安全的資料存放區中導入的架構。 隱私權和安全性健全狀況資訊和健康狀態應用程式時，使用者的儀表板，可確保作業系統。 使用使用者的權限、 應用程式可以讀寫各式各樣的健康情況資訊。

如需有關如何在 Xamarin.iOS 應用程式中使用此的詳細資訊，請參閱[HealthKit 簡介](~/ios/platform/healthkit.md)指南。

## <a name="extending-iphone-functionality"></a>擴充功能的 iPhone
使用 iOS8，賦予開發人員正在能夠進一步控制誰可以使用其應用程式和增加的功能更開放之間進行通訊的協力廠商應用程式。 應用程式擴充功能和文件選擇器等功能開啟各種可能性的應用程式如何使用 Apple 的生態系統中。

### <a name="app-extensions"></a>應用程式擴充功能
應用程式擴充功能，以 oversimplify，可讓協力廠商應用程式彼此進行通訊。 若要維持高安全性標準，以及支持的沙箱化應用程式的完整性，此通訊不會直接在應用程式之間。 相反地，它由*延伸模組*中間。

建立應用程式擴充功能的第一個步驟是定義正確的擴充點，這點很重要，以確保正確的 Api 的可用性與行為。 若要建立 Visual Studio for Mac 應用程式擴充功能，將它加入現有的應用程式藉由將新的專案新增至您的解決方案。

在 [**新的專案**] 對話方塊瀏覽至**C#**  >  **iOS** > **統一 API**  > **延伸模組**，如以下螢幕擷取畫面所示：

![](introduction-to-ios8-images/image2.png "建立新的延伸模組")
 
新增專案 對話方塊會提供七種新的專案範本建立應用程式擴充功能，與下面會討論。 請注意，許多擴充功能與其他新的 Api，在 iOS 中，例如文件選擇器：

- **動作**– 這可讓開發人員建立可讓使用者執行特定工作的唯一的自訂動作按鈕
- **自訂鍵盤**– 這可讓開發人員新增的範圍內建 Apple 鍵盤，加上自己的自訂項。 熱門的鍵盤，Swype 會使用此鍵盤帶入 iOS。
- **文件選擇器**– 這包含文件選擇器檢視控制器，可讓使用者存取應用程式的沙箱外的檔案。
- **文件選擇器檔案提供者**– 這會使用文件選擇器的檔案提供安全的儲存體。
- **相片編輯**– 這的篩選會展開並編輯已由 Apple 提供，相片應用程式，讓使用者有更多控制權和更多選項時編輯他們的相片中的工具。
- **立即**– 這可讓應用程式的通知中心的 [今天] 區段中顯示小工具。

如需有關如何在 Xamarin 中使用應用程式擴充功能的詳細資訊，請參閱[應用程式延伸模組簡介](~/ios/platform/extensions.md)指南。

### <a name="touch-id"></a>Touch ID

Touch ID 做為驗證使用者時，引進在 iOS 7 — 密碼類似。 不過，已解除鎖定裝置、 使用應用程式存放區、 使用 iTunes，並驗證 iCloud keychain 只限制 

現在有兩種方式可為 iOS 8 應用程式中使用本機的驗證 API 的驗證機制使用 Touch ID。 您目前不可以使用本機驗證遠端驗證。

首先，它可協助透過使用新的金鑰鏈存取控制清單 (Acl) 的現有金鑰鏈服務。 可成功進行驗證的使用者指紋解除鎖定 Keychain 的資料。

其次，LocalAuthentication 提供兩種方法來驗證您的應用程式在本機。 開發人員應該使用`CanEvaluatePolicy`來判斷裝置是否能夠接受 Touch ID，然後`EvaluatePolicy`開始驗證作業。

在 Touch ID，並了解如何整合至 Xamarin.iOS 應用程式的詳細資訊，請參閱[介紹到 TouchID](~/ios/platform/touchid.md)輔助線。

### <a name="document-picker"></a>文件選擇器

文件選擇器的運作方式與使用者的 iCloud 磁碟機，以允許使用者開啟不同的應用程式中已建立、 匯入和操作並將它們匯出的檔案再恢復。 這會建立在直覺式工作流程，並因此更好的體驗，為使用者。 iCloud 同步還附有一個有進一步 — 一個應用程式中所做的任何變更也會反映一致地跨所有裝置。

若要深入了解更多的深入了解，文件選擇器，並了解如何整合至 Xamarin.iOS 應用程式，請參閱[文件選擇器簡介](~/ios/platform/document-picker.md)指南。

### <a name="handoff"></a>Handoff

遞移式，也就是較大的持續性功能的一部分，會達到整合 OS X 與 iOS 的進一步的步驟。 這包括跨平台 AirDrop，取得的呼叫 iPhone、 iPad 和 Mac 和改進網際網路共用功能從您的 iPhone 上的 SMS 的能力。

遞移式與 iOS 8 和 Yosemite，搭配運作，而且需要您想要使用不同的裝置登入 iCloud 帳戶。 它應該使用預先安裝的 Apple 應用程式，包括 Safari、 iWork、 地圖、 行事曆和連絡人。

如需詳細資訊，請參閱我們[遞移式](~/ios/platform/handoff.md)指南。

## <a name="unified-storyboards"></a>整合的 Storyboard
iOS 8 包含新建立的使用者介面使用機制的工作變得更容易，統一的分鏡腳本。 與單一分鏡腳本可以涵蓋各種不同的硬體螢幕大小，可以建立快速且回應迅速的檢視，則為 true 的 「 設計一次，使用許多 」 樣式。

在之前 iOS8，開發人員使用`UIInterfaceOrientation`區別直向和橫向模式和`UIInterfaceIdiom`區別 iOS 裝置。 IOS8 中不再需要建立個別的分鏡腳本，適用於 iPhone 和 iPad 裝置 — 方向和裝置由使用決定*大小類別*。

每個裝置為類別所定義的大小，在垂直和水平軸，並有兩種類型的大小在 iOS 8 中的類別：

- **一般**-這是用於小工具 （例如 UIScrollView 一個大型的印象，或是大螢幕大小 （例如 iPad)
- **Compact** -這是較小的裝置 （例如 iPhone)。 此大小會考慮到裝置的方向。

如果兩個概念會搭配使用，結果會是一個 2x2 方格，其中定義可用於這兩個不同的方向，不同的可能大小，如下圖所示：

![](introduction-to-ios8-images/image3.png "代表定義這兩個不同的方向可以用不同的可能大小的 2 x 2 方格圖表")
 
如需有關大小類別的詳細資訊，請參閱[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)。

## <a name="photo-kit"></a>相片套件
相片套件是新的架構，可讓應用程式來查詢系統映像庫，並建立自訂使用者介面來檢視和修改其內容。 它包含數個類別，代表影像和視訊資產，以及資產，例如專輯和資料夾的集合。

如需詳細資訊，請參閱我們[PhotoKit](~/ios/platform/photokit.md)指南。

## <a name="games"></a>遊戲

<a name="scenekit" />

### <a name="scene-kit"></a>Scenekit

Scenekit 是 3D 場景圖形 API，可簡化的 3D 圖形。 它最初是在 OS X 10.8，現在已有 iOS 8。 使用 Scenekit 建立擬真 3D 視覺效果和 3D 的休閒遊戲不需要 OpenGL 的專業知識。 在一般的場景圖形概念建置，Scenekit 複雜性抽象化的 OpenGL 和 OpenGL ES，因此很容易就能新增 3D 內容的應用程式。 不過，如果您是 OpenGL 專家，Scenekit 有繫結，直接與 OpenGL 也有絕佳的支援。 也包含許多功能可補充 3D 圖形，例如物理條件，並非常適合整合數個其他 Apple 的架構，例如 Core Animation、 Core 映像和 Sprite Kit。

如需詳細資訊，請參閱我們[SceneKit](~/ios/platform/gaming/scenekit.md)文件。

<a name="spritekit" />

### <a name="sprite-kit"></a>Spritekit

Spritekit，Apple 的 2D 遊戲架構有一些有趣的新功能，在 iOS 8 和 OS X Yosemite。 這些包括整合 Scenekit、 著色器的支援、 光源、 shadows、 條件約束、 法線貼圖產生與物理增強功能。 特別是，新的物理功能讓很容易在遊戲中加入實際的效果。

如需詳細資訊，請參閱我們[SpriteKit](~/ios/platform/gaming/spritekit.md)文件。

## <a name="other-changes"></a>其他變更
在上述的 iOS 8 中的重大變更，以及 Apple 此外已更新許多現有的架構。 這些詳述如下：

- **[Core 映像](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** – Apple 已經擴充其映像處理架構時，藉由新增更好的支援，來偵測的矩形區域，並 QR 代碼映像內。 Mike Bluestein 所探討的是這在他的部落格文章標題[在 iOS 8 中的映像偵測](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>已被取代的 API
所有的改良在 iOS 8 中，使用數個 Api 已被取代。 其中某些如下所述。

- **[Uiapplication #](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  – 已取代的方法和屬性用來註冊遠端通知。 這些是 registerForRemoteNotificationTypes 和 enabledRemoteNotificationTypes。
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  – 特性和大小類別已被取代的方法和屬性用來描述介面方向。 請參閱[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)如需有關如何使用這些項目。

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  – 這已被取代 UISearchController iOS8 中。

## <a name="summary"></a>總結
在本文中我們探討一些推出 apple iOS 8 的新功能。



## <a name="related-links"></a>相關連結

- [UIKitEnhancements （範例）](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [應用程式延伸模組簡介](~/ios/platform/extensions.md)
- [CloudKit 簡介](~/ios/data-cloud/intro-to-cloudkit.md)
- [文件選擇器簡介](~/ios/platform/document-picker.md)
- [HealthKit 簡介](~/ios/platform/healthkit.md)
- [手動相機控制項簡介](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [TouchID 簡介](~/ios/platform/touchid.md)
- [統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)
