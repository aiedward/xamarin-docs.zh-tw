---
title: 其他 iOS 9 架構變更
description: 本文件說明在 iOS 9 中所導入的其他架構變更。 它討論 AVFoundation、 AVKit 和 CloudKit。
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f7106c9b23fa71b32043ef8190691d798c36370d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672569"
---
# <a name="additional-ios-9-frameworks-changes"></a>其他 iOS 9 架構變更

_本文章涵蓋其他的次要變更或現有的架構，適用於 iOS 9 的增強功能。_

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 標誌")](additional-framework-changes-images/ios9.png#lightbox)

IOS 的主要變更，除了 Apple 方面所做的修改以及數個現有的架構的改善 iOS 9。

## <a name="avfoundation-framework-additions"></a>AVFoundation Framework 新增功能

在 AVFoundation framework 中， [AVSpeechSynthesisVoice](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechSynthesisVoice/)類別現在可讓您除了語言識別項所指定的語音。

比方說，下列程式碼會取得所有可用的語音的清單：

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

您可以藉由設定它，然後使用其中一個清單中的語音`Voice`執行個體的屬性[AVSpeachUtterance](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechUtterance/)類別。

[AVQueuePlayer](https://developer.xamarin.com/api/type/AVFoundation.AVQueuePlayer/)類別現可支援混合的網際網路串流和以檔案為基礎的媒體在佇列中。 舊版無法佇列媒體型別相同。

如需詳細資訊，請參閱 Apple [AVSpeechSynthesisVoice 參考](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)。

## <a name="avkit-framework-additions"></a>AVKit Framework 新增功能

若要使用新的圖片中圖片 (PIP) 」 功能，包括新 AVKit framework`AVPictureInPictureController`並[AVPlayerViewController](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/)類別：

- **AVPictureInPictureController** -這個類別可讓 iOS 9 應用程式以回應使用者啟動中浮動、 可調整大小的 PIP 視窗，在 iPad 上的影片的播放。
- **AVPlayerViewController** -管理`AVPlayer`控制器用來呈現中浮動、 可調整大小的 PIP 視窗，在 iPad 上的影片。

如需詳細資訊，請參閱我們[適用於 iPad 的多工](~/ios/platform/introduction-to-ios9/index.md#multitasking)文件和 Apple [AVPictureInPictureController 參考](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController)和[AVPlayerViewController 參考](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>CloudKit Web Services 簡介

此 CloudKit 架構簡化了應用程式的開發該存取 iCloud。 這包括擷取應用程式資料和資產的權限，以及能夠安全地儲存應用程式資訊。 此套件可讓使用者的匿名層藉由允許其 iCloud 識別碼的應用程式的存取，而不需要共用個人資訊。

新_CloudKit Web 服務_framework 提供的 JavaScript 程式庫 (CloudKit JS)，可以將其納入您的網站提供相同的 CloudKit 基礎資料和與您的 Xamarin.iOS 應用程式的內容存取權。

> [!IMPORTANT]
> 您可以存取、 呈現或更新內容，從使用 CloudKit JS CloudKit 資料庫之前，您必須擁有先前定義該資料庫的結構描述。




如需詳細資訊，請參閱下列文件：

- [CloudKit 簡介](~/ios/data-cloud/intro-to-cloudkit.md)-我們在 Xamarin.iOS 應用程式中使用 CloudKit 的簡介。
- [CloudKit 快速入門](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)-Apple CloudKit 簡介。
- [CloudKit JS 參考](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359)-Apple 的 CloudKit JS 文件。
- [CloudKit Web 服務參考](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240)-Apple 的參考，描述 CloudKit HTTP 介面。
- [CloudKit 目錄：（Cocoa 和 JavaScript） 的 CloudKit 簡介](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599)-使用 CloudKit 和 CloudKit JS Apple 的範例應用程式。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="foundation-framework-additions"></a>Foundation 架構新增項目

Apple 在 iOS 9 中包含 Foundation 架構的下列變更：

### <a name="changes-to-nsbundle"></a>NSBundle 的變更

已進行下列變更來[NSBundle](xref:Foundation.NSBundle)適用於 iOS 9 的類別：

* `GetPreservationPriorityForTag (NSString tag)` -將目前的保留優先權取得具有指定標記的資源。 有效值範圍內`0.0`至`1.0`，將會清除資源，以最低優先權。
* `SetPreservationPriorityForTag (double priority, NSSet tags)` -設定具有指定標記的資源的目前保留優先權。 有效值範圍內`0.0`至`1.0`，將會清除資源，以最低優先權。

如需詳細資訊，請參閱 Apple [NSBundle 參考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)。

### <a name="changes-to-nsprocessinfo"></a>NSProcessInfo 的變更

在 iOS 裝置上執行每個處理序的單一_程序資訊代理程式_(PIA)。 使用[NSProcessInfo](xref:Foundation.NSProcessInfo)類別，以提供目前的 PIA 和控制電源和熱管理指定的處理序的相關資訊。

比方說，以控制自動終止的處理程序，您可以使用下列程式碼：

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

如需詳細資訊，請參閱 Apple [NSProcessInfo 參考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)。

### <a name="reacting-to-low-power-mode"></a>低度電源模式回應

使用`LowPowerModeEnabled`的屬性[NSProcessInfo](xref:Foundation.NSProcessInfo)類別，以判斷是否已在 iOS 裝置上執行的應用程式上啟用低度電源模式。 例如: 

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>HealthKit 架構變更

Apple 會包含下列變更[HealthKit](https://developer.xamarin.com/api/namespace/HealthKit/)中 iOS 9 架構：

- 支援大量刪除和 HealthKit 資料庫中的項目刪除追蹤。 請參閱 Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject)， [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery)並[HKHealthStore 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708)如需詳細資訊。
- 新的追蹤類別 」 和 「 特性新增至`HKQuantityTypeIdentifier`類別 (例如`UVExposure`) 以及`HKCategoryTypeIdentifier`類別 (例如`OvulationTestResult`)。 請參閱 Apple [HealthKit 常數參考](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710)如需詳細資訊。

請參閱我們[HealthKit 簡介](~/ios/platform/healthkit.md)如需有關使用 HealthKit 在 Xamarin.iOS 中的文件。

## <a name="local-authentication-framework-changes"></a>本機驗證架構變更

Apple 會包含下列變更[本機驗證](https://developer.xamarin.com/api/namespace/LocalAuthentication/)中 iOS 9 架構：

- 使用`EvaluateAccessControl`並`EvaluatePolicy`種[LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/)類別，您現在可以嘗試重複使用 Touch ID 符合從上一個成功解除鎖定。
- 能夠取得一份目前已註冊的手指。
- 追蹤何時新增或移除從驗證手指的支援。
- 若要使用的能力_驗證內容_Keychain 呼叫與支援中的評估鑰匙圈存取控制清單。
- 取消從程式碼的使用者提示的能力。

請參閱我們[Touch ID 的簡介](~/ios/platform/touchid.md)如需有關使用 Touch ID，在 Xamarin.iOS 中的文件。

### <a name="lacontext-changes"></a>LAContext 變更

已進行下列變更來[LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/)適用於 iOS 9 的類別：

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -傳回的最大可重複使用 touch ID 驗證的時間量。
- **EvaluatedPolicyDomainState** -取得或設定評估的原則狀態。
- **MaxBiometryFailures** -已在 iOS 9 中已停用。
- **TouchIdAuthenticationAllowableReuseDuration**取得或設定可重複使用 touch ID 驗證的時間量。
- **EvaluateAccessControl** -以非同步方式評估驗證原則。
- **使其失效**-指定的 touch ID 驗證會導致無效。
- **IsCredentialSet** -傳回`true`如果目前所設定的認證。
- **SetCredentialType**設定給定的認證類型。

請參閱 Apple [LAContext 參考](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:)如需詳細資訊。

## <a name="mapkit-framework-changes"></a>MapKit 架構變更

Apple 會包含下列變更[MapKit](https://developer.xamarin.com/api/namespace/MapKit/)中 iOS 9 架構：

- MapKit 現在提供支援，直接在傳輸指示啟動對應應用程式和查詢傳輸抵達 (ETA) 使用的預估時間[MKLaunchOptions](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/)並[MKDirections](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/)類別。
- MapKit 所傳回的搜尋結果並[CLGeocoder](https://developer.xamarin.com/api/type/CoreLocation.CLGeocoder/)類別也可以提供結果的時區。
- 您現在可以完全自訂您 iOS 的應用程式使用所提供的對應註釋`DetailCalloutAccessoryView`的屬性[MKAnnotationView](https://developer.xamarin.com/api/type/MapKit.MKAnnotationView/)類別。

請參閱我們[iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)並[逐步解說-瀏覽註釋及重疊 MapKit 中的](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md)文件，如需有關使用對應和 Xamarin.iOS 與 Apple中的註解[CLGeocoder 參考](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder)如需詳細資訊。

## <a name="passkit-framework-additions"></a>PassKit 架構新增項目

Apple 會包含下列變更[PassKit](https://developer.xamarin.com/api/namespace/PassKit/)中 iOS 9 架構：

- Apple Pay 」 現在支援儲存區金融和信用卡，以及探索卡片。 請參閱**付款網路**一節的 Apple [PKPaymentRequest 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832)如需詳細資訊。
- 從直接在 Xamarin.iOS 應用程式，您現在可以將付款網路和卡片簽發者加入 Apple Pay。 請參閱 Apple [PKAddPaymentPassViewController 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116)如需詳細資訊。

請參閱我們[PassKit 簡介](~/ios/platform/passkit.md)如需有關使用 PassKit 在 Xamarin.iOS 中的文件。

## <a name="safari-services-framework-additions"></a>Safari Services Framework 新增功能

Apple 會包含下列變更[Safari 服務](https://developer.xamarin.com/api/namespace/SafariServices/)中 iOS 9 架構：

- 您現在可以使用新[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)類別，以顯示 web 內容在 Xamarin.iOS 應用程式中的。 它讓您能夠分享在 Safari 應用程式中的網站資料和 cookie，並包含數個 Safari 的功能 （例如，讀取器和自動填入）。 [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)功能**完成**在完成檢視 web 內容時，會傳回使用者到您的應用程式的按鈕。

因為[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)來顯示 web 內容的單一頁面的類別量身訂做，您應該考慮使用它來取代任何[WKWebKit](xref:WebKit.WKWebView)或[UIWebView](xref:UIKit.UIWebView)對現有的 Xamarin.iOS 應用程式內的控制項。

### <a name="displaying-a-website"></a>顯示網站

下列程式碼會呼叫的範例[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)從另一個檢視控制器內：

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>UIKit 架構變更

Apple 已包含了許多增強功能的數個項目[UIKit](xref:UIKit)適用於 iOS 9 的架構。 下列各節將詳細說明這些變更。

### <a name="3d-touch-events"></a>3D 觸控事件

IOS 9 和 iPhone 6s 和 iPhone 6s 新手此外，3D 觸控，請將您的 iOS 應用程式的壓力機密的筆勢。 如此一來，在 iOS 9 （或更新版本） 上執行應用程式和 iOS 裝置可以支援的 3D 觸控，如果變更在壓力會導致`TouchesMoved`會引發事件。

此行為變更，因為您的 iOS 應用程式應該準備`TouchesMoved`頻率，叫用事件即使 X / Y 座標維持不變。

如需詳細資訊，請參閱我們[3D 觸控簡介](~/ios/platform/3d-touch.md)指南。

### <a name="document-open-in-place-functionality"></a>文件就地開啟功能

使用其中一種`FinishedLaunching (application, launchOptions)`或是`WillFinishLaunching (Application, launchOptions)`方法[UIApplicationDelegate](xref:UIKit.UIApplicationDelegate)類別，您現在可以開啟文件，並修改在 （相對於處理複本） 的位置。

若要支援就地開放的新功能，將`LSSupportsOpeningDocumentsInPlace`Xamarin.iOS 應用程式的關鍵**Info.plist**檔案，其值為`YES`。

請參閱 Apple [UIApplicationDelegate 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)如需詳細資訊。

### <a name="enhanced-touch-events"></a>增強型的觸控事件

Apple 已在 iOS 9 中，以提供觸控事件數個增強功能。 這些包括使用觸控預測，並顯示重新整理之間的中繼修飾存取的能力。

請參閱 Apple[適用於 iOS 的事件處理指南](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html)如需詳細資訊。

### <a name="fetching-tailored-content"></a>正在擷取量身訂做的內容

新`NSDataAsset`類別可讓 Xamarin.iOS 應用程式擷取到記憶體和 iOS 裝置上目前執行的圖形功能量身訂做的內容。

### <a name="new-layout-anchors"></a>新的版面配置錨點

新`NSLayoutAnchor`和`NSLayoutDimension`版面配置錨點的類別使用的新錨點屬性[UIView](xref:UIKit.UIView)類別 (例如`LeadingAnchor`和`WidthAnchor`) 若要在 iOS 9 中簡化版面配置。

請參閱我們[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)如需有關使用自動版面配置和大小類別中的 Xamarin.iOS 應用程式和 Apple 的文件[NSLayoutAnchor 參考](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor)， [NSLayoutDimension 參考](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension)並[UIView 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView)如需詳細資訊。

### <a name="new-readable-content-margins"></a>新增可讀取的內容邊界

新`UILayoutGuide`類別可用來提供可讀取的內容邊界，並定義繪製的區域內檢視的內容。 請參閱 Apple [UILayoutGuide 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide)如需詳細資訊。

### <a name="text-input-in-notifications-modifications"></a>在 通知修改的文字輸入

[UIUserNotificationAction](xref:UIKit.UIUserNotificationAction)類別具有新`Behavior`可用來支援通知的文字輸入的屬性。

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate 變更

雖然不會正式 apple 已被取代，它們建議取代的所有呼叫`FinishedLaunching (UIApplication application)`方法[UIApplicationDelegate](xref:UIKit.UIApplicationDelegate)使用的類別`FinishedLaunching (UIApplication application, NSDictionary launchOptions)`或`WillFinishLaunching (UIApplication application, NSDictionary launchOptions)`方法。

請參閱 Apple [UIApplicationDelegate 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)如需詳細資訊。

### <a name="uikit-dynamics-changes"></a>UIKit Dynamics 變更

Apple 在 iOS 9 中包含 UIKit Dynamics 的下列變更：

- Dynamics 現在支援非矩形的衝突界限。
- 新的可自訂`UIFieldBehavior`類別用來支援各種不同的欄位類型。
- 已新增其他附件`UIAttachmentBehavior`類別。

請參閱 Apple [UIAttachment 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior)如需詳細資訊。

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView 和 UIDatePicker 變更

在 iOS 9 之前[UIPickerView](xref:UIKit.UIPickerView)並[UIDatePicker](xref:UIKit.UIDatePicker)控制項已不可調整大小，並會自動調整大小來填滿其容器 （通常是寬度與應用程式的 iOS 裝置的寬度在上執行）。

在 iOS 9 中，此自動調整大小不會再發生，並將呈現控制項，在所有的 iOS 裝置，不論螢幕大小及方向 320 點寬度。

若要更正這種情況下，使用自動版面配置和大小類別釘選到父容器 （檢視） 的邊緣控制項的寬度，並指定所需的高度。 請參閱我們[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)如需有關在 Xamarin.iOS 應用程式中使用自動版面配置和大小類別的文件。

### <a name="new-uitextinputassistantitem-class"></a>新的 UITextInputAssistantItem 類別

使用新`UITextInputAssistantItem`類別中的版面配置列按鈕群組_快顯列_。 快顯列是新的區域可在 螢幕小鍵盤提供輸入的快速鍵。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
