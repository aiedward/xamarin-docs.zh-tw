---
title: iOS 8 簡介
description: 在 iOS 8 中，Apple 提供了新架構和 Api 的眾多，可供激發和取悅開發人員使用。 在本指南中，我們將介紹這些新的 Api，並瞭解 iOS 8 可以如何讓開發人員和使用者受益。
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 2da018b3595850582331280909fa327cee4ff6e0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031811"
---
# <a name="introduction-to-ios-8"></a>iOS 8 簡介

_在 iOS 8 中，Apple 提供了新架構和 Api 的眾多，可供激發和取悅開發人員使用。在本指南中，我們將介紹這些新的 Api，並瞭解 iOS 8 可以如何讓開發人員和使用者受益。_

iOS 7 會以視覺化方式，從第一個 iPhone OS 直接變更整個 iOS 使用者介面，從使用者和開發人員都有預期。 IOS 8 藉由為開發人員提供許多架構來繼續進行，讓使用者可以從 iPhone 直接控制其生活的各個層面。 例如，您可以使用*HealthKit*來分析健康情況和適用性，密碼會 Sal 使用*LocalAuthentication*的生物識別驗證，*應用程式擴充*功能會開啟協力廠商應用程式之間的通道，以及*HomeKit*允許將您的房屋轉變成未來的家庭。 

如果 iOS 7 是關於讓滿意使用者，則 iOS 8 著重于讓滿意開發人員，其中涵蓋了這些好吃新工具的完整範圍。 

本指南介紹適用于 Xamarin iOS 開發人員的新 Api。  

在 iOS 8 中也有幾個已淘汰的 Api，這在本檔的結尾有詳細說明。

## <a name="requirements"></a>需求

在 Visual Studio for Mac 中建立 iOS 8 應用程式需要下列各項：

- **Xcode 7 和 ios 8 或更新版本**– Apple 的最新 Xcode 和 ios api 必須在開發人員的電腦上安裝及設定。
- **Visual Studio for Mac** –應該在使用者裝置上安裝和設定最新版本的 Visual Studio for Mac。
- **ios 8 裝置或**模擬器–執行最新版 ios 8 進行測試的 ios 裝置。

## <a name="home-and-leisure"></a>家用和休閒

iOS 8 已透過使用 HomeKit 和 HealthKit，協助您將 Apple 和 iOS 裝置直接放在您家中的核心。 在本節中，我們將探討這些新架構的工作，以及如何將它們整合到您的 Xamarin iOS 應用程式中。

## <a name="homekit"></a>HomeKit

從 iPhone 控制您的設備並不是新的技術應用程式;許多連線-家用產品都可以透過 iOS 應用程式來控制。 不過，HomeKit 現在會藉由推廣家庭自動化裝置的常見通訊協定，以及讓特定制造商（例如 iHome、Philips 和 Honeywell）提供公用 API，來進一步執行此步驟。 對使用者來說，這表示他們可以從一個應用程式中順暢地控制其家裡的每個層面。 他們不知道它們使用的是 Philips 的色調燈泡，也不是一種嵌套的鬧鐘。 使用者也可以將許多智慧型家庭程式連結在一起，以「場景」的方式運作。

有了 HomeKit，協力廠商應用程式和 Siri 就可以探索附屬元件，並將其新增至其個人的家庭設定資料庫，編輯並處理此資料，以及與配件和其服務進行通訊，以執行動作。

### <a name="configuration"></a>Configuration

下圖顯示 HomeKit 配件設定的基本階層：

![](introduction-to-ios8-images/image1.png "This diagram shows the basic hierarchy of the configuration of HomeKit accessories")

若要開始使用 HomeKit，開發人員必須確認其布建設定檔已選取 HomeKit 服務。 Apple 也為開發人員提供了適用于 Xcode 的 HomeKit 模擬器增益集。 這可以在[Apple 開發人員中心](https://developer.apple.com/downloads/index.action)的 [`Hardware IO Tools for Xcode`] 底下找到。 

如需詳細資訊，請參閱我們的[HomeKit](~/ios/platform/homekit.md)指南。

## <a name="healthkit"></a>HealthKit

HealthKit 是 iOS 8 中引進的一種架構，可為健康相關的資訊提供集中、協調及安全的資料存放區。 作業系統會確保健全狀況資訊的隱私權和安全性，並透過健康情況應用程式來提供使用者的儀表板。 透過使用者的許可權，應用程式可以讀取和寫入各種不同的健全狀況資訊。

如需在您的 Xamarin iOS 應用程式中使用此功能的詳細資訊，請參閱[HealthKit 簡介](~/ios/platform/healthkit.md)指南。

## <a name="extending-iphone-functionality"></a>擴充 iPhone 功能
有了 iOS8，開發人員就能更充分掌控可以使用其應用程式的人員，以及增加協力廠商應用程式之間更開放通訊的功能。 應用程式延伸模組和檔選擇器等功能開啟了如何在 Apple 生態系統中使用應用程式的可能性。

### <a name="app-extensions"></a>應用程式延伸模組
應用程式延伸模組（oversimplify）是協力廠商應用程式彼此通訊的方式。 為了維持高安全性標準，並堅持沙箱化應用程式的完整性，這項通訊不會直接在應用程式之間進行。 相反地，它是由中間的*延伸*模組所執行。

建立應用程式延伸模組的第一個步驟是定義正確的擴充點，這對於確保正確 Api 的行為和可用性很重要。 若要在 Visual Studio for Mac 中建立應用程式延伸模組，請將新專案新增至您的方案，以將它加入至現有的應用程式。

在 [**新增專案**] 對話方塊中**C#** ，流覽至 > **iOS** > **Unified API** > **延伸**模組，如下列螢幕擷取畫面所示：

![](introduction-to-ios8-images/image2.png "Creating a new extension")

[新增專案] 對話方塊提供了七個新的專案範本，可用來建立應用程式延伸模組，如下所述。 請注意，許多延伸模組與 iOS 中的其他新 Api 有關，例如檔選擇器：

- **動作**–這可讓開發人員建立獨特的自訂動作按鈕，讓使用者能夠執行特定工作
- **自訂鍵盤**–這可讓開發人員新增自己的自訂，以加入內建的 Apple 鍵盤範圍。 Swype 常用的鍵盤，會使用它將鍵盤帶到 iOS。
- 檔**選擇器**–這包含檔選擇器視圖控制器，可讓使用者存取應用程式沙箱外的檔案。
- 檔**選擇器檔案提供者**–這會使用檔選擇器為檔案提供安全的儲存區。
- **相片編輯**–這會展開相片應用程式中 Apple 提供的篩選器和編輯工具，讓使用者在編輯其相片時擁有更多控制權和更多選項。
- **今天**–這讓應用程式能夠在 [通知中心] 的 [今天] 區段中顯示 widget。

如需在 Xamarin 中使用應用程式延伸模組的詳細資訊，請參閱[應用程式擴充功能簡介](~/ios/platform/extensions.md)指南。

### <a name="touch-id"></a>Touch ID

在 iOS 7 中引進 Touch ID 做為驗證使用者的方式，類似于密碼。 不過，它僅限於解除鎖定裝置、使用 App Store、使用 iTunes，以及僅驗證 iCloud keychain 

現在有兩種方式可以使用 Touch ID 作為 iOS 8 應用程式中使用本機驗證 API 的驗證機制。 目前無法使用本機驗證從遠端進行驗證。

首先，它會透過使用新的 Keychain 存取控制清單（Acl）來協助現有的 Keychain 服務。 Keychain 資料可透過使用者指紋的成功驗證來解除鎖定。

其次，LocalAuthentication 提供兩種方法，在本機驗證您的應用程式。 開發人員應該使用 `CanEvaluatePolicy` 來判斷裝置是否能夠接受 Touch ID，然後 `EvaluatePolicy` 啟動驗證作業。

如需 Touch ID 的詳細資訊，以及瞭解如何將它整合到 Xamarin iOS 應用程式，請參閱[TouchID 指南簡介](~/ios/platform/touchid.md)。

### <a name="document-picker"></a>文件選擇器

檔選擇器可與使用者 iCloud 磁片磁碟機搭配使用，以允許使用者開啟在不同應用程式中建立的檔案、匯入及操作這些檔案，並將它們再次匯出回來。 這會為使用者建立直覺的工作流程，因此有更好的體驗。 iCloud 同步會進一步執行此動作，在某個應用程式中所做的任何變更也會在您所有的裝置上一致地反映。

若要深入瞭解檔選擇器，並瞭解如何將它整合到 Xamarin iOS 應用程式，請參閱[檔選擇器指南簡介](~/ios/platform/document-picker.md)。

### <a name="handoff"></a>Handoff

遞交（屬於較大的持續性功能的一部分）會進一步進一步整合 OS X 和 iOS。 這包括跨平臺 AirDrop、讓 iPhone 呼叫的能力、iPad 和 Mac 上的 SMS，以及 iPhone 的網際網路共用功能。

遞交適用于 iOS 8 和 Yosemite，而且需要有 iCloud 帳戶才能登入您想要使用的所有不同裝置。 它應該與大部分預先安裝的 Apple 應用程式搭配使用，包括 Safari、iWork、地圖、行事曆和連絡人。

如需詳細資訊，請參閱我們的[遞交](~/ios/platform/handoff.md)指南。

## <a name="unified-storyboards"></a>整合的 Storyboard
iOS 8 包含新的簡單易用機制來建立使用者介面，也就是整合的分鏡腳本。 透過單一分鏡腳本來涵蓋各種不同的硬體螢幕大小，您可以在真正的「設計一次，使用許多」樣式中建立快速且回應迅速的觀點。

在 iOS8 之前，開發人員使用 `UIInterfaceOrientation` 來區分直向和橫向模式，並 `UIInterfaceIdiom` 來區別 iOS 裝置。 在 iOS8 中，您不再需要為 iPhone 和 iPad 裝置建立個別的分鏡腳本—方向和裝置是使用*大小類別*來決定。

每個裝置都是由大小類別在垂直和水準軸中定義，而 iOS 8 中有兩種大小類別：

- **一般**-這適用于大型螢幕大小（例如 iPad）或小工具，可提供大尺寸的印象（例如 UIScrollView
- **Compact** -這適用于較小的裝置（例如 iPhone）。 此大小會考慮裝置的方向。

如果同時使用這兩個概念，結果會是 2 x 2 方格，定義可用於不同方向的各種可能大小，如下圖所示：

![](introduction-to-ios8-images/image3.png "A diagram representing the 2 x 2 grid that defines the different possible sizes that can be used in both the differing orientations")

如需有關大小類別的詳細資訊，請參閱整合分鏡腳本[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)。

## <a name="photo-kit"></a>相片套件
相片套件是新的架構，可讓應用程式查詢系統映射庫，並建立自訂使用者介面來查看和修改其內容。 其中包含一些代表影像和影片資產的類別，以及一系列的資產，例如專輯和資料夾。

如需詳細資訊，請參閱我們的[PhotoKit](~/ios/platform/photokit.md)指南。

## <a name="games"></a>遊戲

<a name="scenekit" />

### <a name="scene-kit"></a>場景套件

場景套件是可簡化使用3D 圖形的3D 場景圖形 API。 它最初是在 OS X 10.8 中引進，現在已進入 iOS 8。 使用場景套件建立沉浸式3D 視覺效果，而休閒3D 遊戲不需要 OpenGL 的專業知識。 以常見的場景圖形概念為基礎，場景套件會抽象化 OpenGL 和 OpenGL ES 的複雜性，讓您很容易就能將3D 內容新增至應用程式。 不過，如果您是 OpenGL 專家，場景套件也具有直接與 OpenGL 結合的絕佳支援。 它也包含許多輔助3D 圖形的功能，例如物理，並與數個其他 Apple 架構（例如核心動畫、核心影像和 Sprite 套件）緊密整合。

如需詳細資訊，請參閱我們的[SceneKit](~/ios/platform/gaming/scenekit.md)檔。

<a name="spritekit" />

### <a name="sprite-kit"></a>Sprite 套件

來自 Apple 的2D 遊戲架構 Sprite 套件，在 iOS 8 和 OS X Yosemite 中有一些有趣的新功能。 其中包括與場景套件、著色器支援、光源、陰影、條件約束、一般地圖產生和物理增強功能的整合。 特別的是，新的物理功能讓您很容易就能在遊戲中加入實際的效果。

如需詳細資訊，請參閱我們的[SpriteKit](~/ios/platform/gaming/spritekit.md)檔。

## <a name="other-changes"></a>其他變更
除了上述 iOS 8 中所述的主要變更之外，Apple 還進一步更新了許多現有的架構。 以下詳細說明如下：

- **[核心映射](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** – Apple 已擴充其影像處理架構，方法是新增對偵測矩形區域的更佳支援，以及影像內的 QR 代碼。 Mike Bluestein 在他的 blog 文章中探討了[iOS 8 的影像偵測](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>已被取代的 API
在 iOS 8 中的所有改良功能，已淘汰一些 Api。 以下詳述其中的部分。

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)** –用來註冊遠端通知的方法和屬性已被取代。 這些是 registerForRemoteNotificationTypes 和 enabledRemoteNotificationTypes。
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)** –特性和大小類別已取代用來描述介面方向的方法和屬性。 如需如何使用這些方法的詳細資訊，請參閱整合腳本的[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)。

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)** –這已由 iOS8 中的 UISearchController 所取代。

## <a name="summary"></a>總結
在本文中，我們探討了 Apple 在 iOS 8 中引進的一些新功能。

## <a name="related-links"></a>相關連結

- [UIKitEnhancements （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-uikitenhancements)
- [應用程式擴充功能簡介](~/ios/platform/extensions.md)
- [CloudKit 簡介](~/ios/data-cloud/intro-to-cloudkit.md)
- [檔選擇器簡介](~/ios/platform/document-picker.md)
- [HealthKit 簡介](~/ios/platform/healthkit.md)
- [手動相機控制項簡介](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [TouchID 簡介](~/ios/platform/touchid.md)
- [整合分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)
