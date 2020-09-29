---
title: iOS 8 簡介
description: 在 iOS 8 中，Apple 提供了眾多的新架構和 Api，可供激發和取悅開發人員使用。 在本指南中，我們將介紹這些新的 Api，並瞭解 iOS 8 如何能讓開發人員和使用者受益。
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 87b828221582db37ded66d075bdc933b0743b793
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436168"
---
# <a name="introduction-to-ios-8"></a>iOS 8 簡介

_在 iOS 8 中，Apple 提供了眾多的新架構和 Api，可供激發和取悅開發人員使用。在本指南中，我們將介紹這些新的 Api，並瞭解 iOS 8 如何能讓開發人員和使用者受益。_

iOS 7 以視覺化方式變更整個 iOS 使用者介面，就像是使用者和開發人員預期的一樣，直接從第一個 iPhone 作業系統來看。 IOS 8 藉由提供許多適用于開發人員的架構來繼續進行，讓使用者可以直接從其 iPhone 控制其生活的各個層面。 例如，您可以使用 *HealthKit*來分析健康情況和適用性、使用 *LocalAuthentication*以生物特徵辨識驗證 sal 密碼、 *應用程式延伸* 模組會開啟協力廠商應用程式之間的通道，而 *HomeKit* 則可讓您將房子變成未來的家裡。 

如果 iOS 7 與讓滿意使用者有關，則 iOS 8 著重于讓滿意的開發人員，其中包含各種好吃的新工具。 

本指南介紹適用于 Xamarin iOS 開發人員的新 Api。  

另外還有幾個在 iOS 8 中已淘汰的 Api，在本檔的結尾有詳細說明。

## <a name="requirements"></a>需求

在 Visual Studio for Mac 中建立 iOS 8 應用程式需要下列各項：

- **Xcode 7 和 ios 8 或更新版本** – Apple 的最新 Xcode 和 ios api 必須安裝並設定于開發人員的電腦上。
- **Visual Studio for Mac** –應該在使用者裝置上安裝並設定最新版本的 Visual Studio for Mac。
- **ios 8 裝置或** 模擬器–執行最新版本 ios 8 以進行測試的 ios 裝置。

## <a name="home-and-leisure"></a>家用和休閒

iOS 8 已協助您透過使用 HomeKit 和 HealthKit，將 Apple 和 iOS 裝置直接放在您家裡的核心中。 在本節中，我們將探討這些新架構的運作方式，以及如何將其整合到您的 Xamarin iOS 應用程式。

## <a name="homekit"></a>HomeKit

從 iPhone 控制您的設備並不是新的技術應用程式;許多連線的家用產品都可以透過 iOS 應用程式來控制。 不過，HomeKit 現在藉由推廣家用自動化裝置的常見通訊協定，以及將公用 API 提供給特定制造商（例如 iHome、Philips 和 Honeywell），來更進一步進行此步驟。 對使用者來說，這表示他們可以從一個應用程式內順暢地控制家裡的各個層面。 這並不相關，因為他們知道他們使用的是 Philips 的色調燈泡，或有一項功能。 使用者也可以將許多智慧型家用程式組成「場景」。

有了 HomeKit，協力廠商應用程式和 Siri 可以探索配件，並將其新增至其個人的家用設定資料庫、編輯並處理此資料，以及與配件及其服務通訊以執行動作。

### <a name="configuration"></a>設定

下圖顯示 HomeKit 配件設定的基本階層：

![下圖顯示 HomeKit 配件設定的基本階層](introduction-to-ios8-images/image1.png)

若要開始使用 HomeKit，開發人員必須確認其布建設定檔已選取 HomeKit 服務。 Apple 也為開發人員提供了適用于 Xcode 的 HomeKit 模擬器增益集。 這可以在 [Apple 開發人員中心](https://developer.apple.com/downloads/index.action)的底下找到 `Hardware IO Tools for Xcode` 。 

如需詳細資訊，請參閱我們的 [HomeKit](~/ios/platform/homekit.md) 指南。

## <a name="healthkit"></a>HealthKit

HealthKit 是在 iOS 8 中導入的架構，可針對健康情況相關資訊提供集中、協調及安全的資料存放區。 作業系統可確保健康狀態資訊的隱私權和安全性，以及使用者的健康狀態應用程式。 使用使用者的許可權，應用程式可以讀取和寫入各式各樣的健康情況資訊。

如需在您的 Xamarin iOS 應用程式中使用此功能的詳細資訊，請參閱 [HealthKit 指南簡介](~/ios/platform/healthkit.md) 。

## <a name="extending-iphone-functionality"></a>擴充 iPhone 功能
使用 iOS8，開發人員可以更充分地掌控可以使用其應用程式的人員，並提高協力廠商應用程式之間更開放式通訊的功能。 應用程式擴充功能和檔選擇器等功能可讓您瞭解如何在 Apple 的生態系統中使用應用程式的可能性。

### <a name="app-extensions"></a>應用程式擴充功能
應用程式延伸模組 oversimplify 是協力廠商應用程式彼此通訊的方式。 為了維持高安全性標準並保有沙箱化應用程式的完整性，此通訊不會直接在應用程式之間進行。 相反地，它是由中間的 *延伸* 模組所執行。

建立應用程式延伸模組的第一個步驟是定義正確的擴充點，這對確保正確 Api 的行為和可用性很重要。 若要在 Visual Studio for Mac 中建立應用程式擴充功能，請將新的專案新增至您的方案，以將其新增至現有的應用程式。

在 [**新增專案**] 對話方塊中，流覽至**c #**  >  **iOS**  >  **Unified API**  >  **擴充**功能，如下列螢幕擷取畫面所示：

![建立新的擴充功能](introduction-to-ios8-images/image2.png)

[新增專案] 對話方塊提供七個新的專案範本來建立應用程式延伸模組，如下所述。 請注意，許多擴充功能與 iOS 中的其他新 Api 相關，例如檔選擇器：

- **動作** -這可讓開發人員建立獨特的自訂動作按鈕，讓使用者執行特定工作
- **自訂鍵盤** -這可讓開發人員加入自己的自訂，以新增至內建的 Apple 鍵盤範圍。 Swype 常用的鍵盤會使用此鍵盤將鍵盤帶入 iOS。
- **檔選擇器** –這包含檔選擇器視圖控制器，可讓使用者存取應用程式沙箱以外的檔案。
- **檔選擇器檔案提供者** -這會使用檔選擇器為檔案提供安全的儲存。
- **相片編輯** –這會擴充 Apple 在相片應用程式中所提供的篩選器和編輯工具，讓使用者在編輯相片時提供更多的控制和更多選項。
- **今天** –這讓應用程式能夠在 [通知中心] 的 [Today] 區段中顯示 widget。

如需在 Xamarin 中使用應用程式延伸模組的詳細資訊，請參閱 [應用程式延伸模組簡介](~/ios/platform/extensions.md) 指南。

### <a name="touch-id"></a>Touch ID

在 iOS 7 中引進了觸控識別碼作為驗證使用者的方法，類似于密碼。 但是，它僅限用來解除鎖定裝置、使用 App Store、使用 iTunes，以及驗證 iCloud keychain 

現在有兩種方式可在 iOS 8 應用程式中使用「觸控識別碼」作為驗證機制，以使用本機驗證 API。 目前無法使用本機驗證來進行遠端驗證。

首先，它會透過使用新的 Keychain 存取控制清單 (Acl) 來輔助現有的 Keychain 服務。 Keychain 資料可透過成功驗證使用者指紋來解除鎖定。

其次，LocalAuthentication 提供兩種方法來在本機驗證您的應用程式。 開發人員應該使用 `CanEvaluatePolicy` 來判斷裝置是否能夠接受 TOUCH ID，然後 `EvaluatePolicy` 開始進行驗證作業。

如需 Touch ID 的詳細資訊，並瞭解如何將它整合到 Xamarin iOS 應用程式中，請參閱《 [xamarin. ios 》中的 TOUCH id 和臉部識別碼](~/ios/platform/touch-id-face-id.md) 。

### <a name="document-picker"></a>文件選擇器

檔選擇器可與使用者 iCloud 磁片磁碟機搭配使用，以允許使用者開啟在不同應用程式中建立的檔案、匯入並操作它們，然後再將它們匯出回來。 這會為使用者建立直覺化的工作流程，進而提供更好的體驗。 iCloud 同步會進一步進行此步驟，在單一應用程式中所做的任何變更也會在所有裝置上一致地反映出來。

若要深入瞭解檔選擇器，以及瞭解如何將它整合到 Xamarin iOS 應用程式中，請參閱 [檔選擇器](~/ios/platform/document-picker.md) 指南的簡介。

### <a name="handoff"></a>Handoff

這是較大型持續性功能的一部分，可進一步加強作業系統 X 和 iOS 的整合。 這包括跨平臺 AirDrop、取得 iPhone 通話的能力、iPad 和 Mac 上的 SMS，以及您 iPhone 的網際網路共用功能改進。

交付可與 iOS 8 和 Yosemite 搭配運作，而且需要 iCloud 帳戶才能登入您想要使用的所有不同裝置。 它應該適用于大部分預先安裝的 Apple 應用程式，包括 Safari、iWork、地圖、行事曆和連絡人。

如需詳細資訊，請參閱我們的 [交付](~/ios/platform/handoff.md) 指南。

## <a name="unified-storyboards"></a>整合的 Storyboard
iOS 8 包含新的簡單易用機制，可讓您建立使用者介面—整合的分鏡腳本。 您可以使用單一分鏡腳本來涵蓋所有不同的硬體螢幕大小，以真正的「設計一次，使用許多」樣式來建立快速且回應式的觀點。

在 iOS8 之前，開發人員用 `UIInterfaceOrientation` 來區分直向和橫向模式，以及 `UIInterfaceIdiom` 區別 iOS 裝置。 在 iOS8 中，您不再需要為 iPhone 和 iPad 裝置建立個別的分鏡腳本，而是使用 *大小類別*來決定方向和裝置。

每個裝置都是由大小類別定義，位於垂直和水準軸，而在 iOS 8 中有兩種類型的大小類別：

- [**一般**]-這適用于大型螢幕大小 (例如 iPad) 或小工具，可讓您瞭解大型大小 (例如 UIScrollView
- **Compact** -這適用于較小的裝置 (例如 iPhone) 。 此大小會考慮裝置的方向。

如果同時使用這兩個概念，則結果會是 2 x 2 個方格，其中定義可用於不同方向的不同可能大小，如下圖所示：

![表示 2 x 2 方格的圖表，其中定義可用於不同方向的不同可能大小](introduction-to-ios8-images/image3.png)

如需大小類別的詳細資訊，請參閱 [整合](~/ios/user-interface/storyboards/unified-storyboards.md)分鏡腳本簡介。

## <a name="photo-kit"></a>相片套件
相片套件是一個新的架構，可讓應用程式查詢系統映射庫，並建立自訂使用者介面來查看和修改其內容。 它包含一些類別，這些類別代表影像和影片資產，以及專輯和資料夾等資產集合。

如需詳細資訊，請參閱我們的 [PhotoKit](~/ios/platform/photokit.md) 指南。

## <a name="games"></a>遊戲

<a name="scenekit"></a>

### <a name="scene-kit"></a>場景套件

場景套件是3D 場景圖形 API，可簡化使用3D 圖形的程式。 它是在 OS X 10.8 中首次引進，現在已進入 iOS 8。 使用場景套件建立沉浸式3D 視覺效果和休閒3D 遊戲並不需要使用 OpenGL 的專業知識。 場景套件是以常見的場景圖形概念為基礎，可將 OpenGL 和 OpenGL ES 的複雜度抽象化，讓您很容易就能將3D 內容新增至應用程式。 但是，如果您是 OpenGL 專家，場景套件也有絕佳的支援，可直接與 OpenGL 系結。 它也包含許多可補充3D 圖形的功能，例如物理，並與其他數個 Apple 架構（例如核心動畫、核心影像和 Sprite 套件）非常妥善地整合。

如需詳細資訊，請參閱我們的 [SceneKit](~/ios/platform/gaming/scenekit.md) 檔。

<a name="spritekit"></a>

### <a name="sprite-kit"></a>Sprite 套件

來自 Apple 的2D 遊戲架構的 Sprite 套件，在 iOS 8 和 OS X Yosemite 中有一些有趣的新功能。 這些包括與場景套件、著色器支援、光源、陰影、條件約束、標準地圖產生和物理增強功能的整合。 尤其是，新的物理功能讓您很容易就能將實際效果新增到遊戲中。

如需詳細資訊，請參閱我們的 [SpriteKit](~/ios/platform/gaming/spritekit.md) 檔。

## <a name="other-changes"></a>其他變更
此外，除了上述的 iOS 8 主要變更之外，Apple 還更新了許多現有的 framework。 以下詳述：

- **[核心影像](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** -Apple 已在其影像處理架構上擴充，方法是針對矩形區域的偵測以及影像內的 QR 代碼新增更好的支援。 Mike Bluestein 在 iOS 8 中探索此文章的標題為 [影像偵測](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>已被取代的 API
在 iOS 8 中所做的所有改進之後，已淘汰許多 Api。 其中一些詳細資訊如下所述。

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)** –用來註冊遠端通知的方法和屬性已淘汰。 這些都是 registerForRemoteNotificationTypes 和 enabledRemoteNotificationTypes。
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)** –特性和大小類別已取代用來描述介面方向的方法和屬性。 如需有關如何使用這些方法的詳細資訊，請參閱整合分鏡腳本的 [簡介](~/ios/user-interface/storyboards/unified-storyboards.md) 。

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)** –這已由 iOS8 中的 UISearchController 所取代。

## <a name="summary"></a>摘要
在本文中，我們探討了 Apple 在 iOS 8 中引進的一些新功能。

## <a name="related-links"></a>相關連結

- [UIKitEnhancements (範例) ](/samples/xamarin/ios-samples/ios8-uikitenhancements)
- [應用程式延伸模組簡介](~/ios/platform/extensions.md)
- [CloudKit 簡介](~/ios/data-cloud/intro-to-cloudkit.md)
- [檔選擇器簡介](~/ios/platform/document-picker.md)
- [HealthKit 簡介](~/ios/platform/healthkit.md)
- [手動相機控制項簡介](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [使用 Xamarin 的 Touch ID 和臉部識別碼](~/ios/platform/touch-id-face-id.md)
- [整合分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)