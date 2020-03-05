---
title: 其他 iOS 9 架構變更
description: 本檔說明 iOS 9 中引進的其他架構變更。 其中討論 AVFoundation、AVKit 和 CloudKit。
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: fd9bced0d2185fd9bd0d18932921c101b2ed207c
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292950"
---
# <a name="additional-ios-9-frameworks-changes"></a>其他 iOS 9 架構變更

_本文涵蓋適用于 iOS 9 的現有架構的其他、次要變更或增強功能。_

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 Logo")](additional-framework-changes-images/ios9.png#lightbox)

除了 iOS 的主要變更之外，Apple 也已針對 iOS 9 中的數個現有架構進行修改和改進。

## <a name="avfoundation-framework-additions"></a>新增 AVFoundation 架構

在 AVFoundation 架構中， [AVSpeechSynthesisVoice](xref:AVFoundation.AVSpeechSynthesisVoice)類別現在除了語言外，還可讓您指定語音依據識別碼。

例如，下列程式碼會取得所有可用語音的清單：

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

然後您可以從清單中使用其中一個語音，其方式是將它設定為[AVSpeachUtterance](xref:AVFoundation.AVSpeechUtterance)類別之實例的 `Voice` 屬性。

[AVQueuePlayer](xref:AVFoundation.AVQueuePlayer)類別現在支援在佇列中混合使用網際網路串流和以檔案為基礎的媒體。 先前的版本只能將相同類型的媒體排到佇列。

如需詳細資訊，請參閱 Apple 的[AVSpeechSynthesisVoice 參考](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)。

## <a name="avkit-framework-additions"></a>新增 AVKit 架構

若要使用新的相片圖片（PIP）功能，AVKit 架構包含新的 `AVPictureInPictureController` 和[AVPlayerViewController](xref:AVKit.AVPlayerViewController)類別：

- **AVPictureInPictureController** -此類別可讓 iOS 9 應用程式回應使用者在 iPad 上浮動且可調整大小的 PIP 視窗中，啟動影片的播放。
- **AVPlayerViewController** -管理用於在 iPad 上以浮動、可調整大小的 PIP 視窗呈現影片的 `AVPlayer` 控制器。

如需詳細資訊，請參閱我們[針對 iPad 的多工](~/ios/platform/introduction-to-ios9/index.md#multitasking)檔和 Apple 的[AVPictureInPictureController 參考](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController)和[AVPlayerViewController 參考](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController)。

## <a name="introducing-cloudkit-web-services"></a>CloudKit Web 服務簡介

CloudKit 架構可簡化存取 iCloud 之應用程式的開發。 這包括抓取應用程式資料和資產許可權，以及能夠安全地儲存應用程式資訊。 此套件可讓使用者透過其 iCloud 識別碼存取應用程式，而不需要共用個人資訊，以提供匿名層。

新的_CloudKit Web 服務_架構提供 JavaScript 程式庫（CloudKit JS），可以併入您的網站中，以提供與您的 Xamarin iOS 應用程式相同之 CloudKit 資料和內容的存取權。

> [!IMPORTANT]
> 您必須先定義該資料庫的架構，才能存取、使用 CloudKit JS 來呈現或更新 CloudKit 資料庫的內容。

如需詳細資訊，請參閱下列檔：

- [CloudKit 簡介](~/ios/data-cloud/intro-to-cloudkit.md)-我們在 Xamarin iOS 應用程式中使用 CloudKit 的簡介。
- [CloudKit 快速入門](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)-Apple 簡介 CloudKit。
- [CLOUDKIT JS 參考](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359)-Apple 的 CloudKit JS 檔。
- [CloudKit Catalog： CloudKit （Cocoa 和 JavaScript）簡介](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599)-使用 CloudKit 和 CloudKit JS 的 Apple 範例應用程式。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

## <a name="foundation-framework-additions"></a>新增的基礎架構

Apple 在 iOS 9 中的基礎架構包含下列變更：

### <a name="changes-to-nsbundle"></a>NSBundle 的變更

IOS 9 的[NSBundle](xref:Foundation.NSBundle)類別已進行下列變更：

- `GetPreservationPriorityForTag (NSString tag)`-取得具有指定標記之資源的目前保留優先順序。 有效值在 `0.0` 要 `1.0`的範圍內，將會先清除優先順序最低的資源。
- `SetPreservationPriorityForTag (double priority, NSSet tags)`-設定具有指定標記之資源的目前保留優先順序。 有效值在 `0.0` 要 `1.0`的範圍內，將會先清除優先順序最低的資源。

如需詳細資訊，請參閱 Apple 的[NSBundle 參考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)。

### <a name="changes-to-nsprocessinfo"></a>NSProcessInfo 的變更

在 iOS 裝置上執行的每個進程都有單一的_進程資訊代理程式_（PIA）。 使用[NSProcessInfo](xref:Foundation.NSProcessInfo)類別來提供目前 PIA 的相關資訊，並控制指定進程的電源和冷卻管理。

例如，若要控制進程的自動終止，您可以使用下列程式碼：

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

如需詳細資訊，請參閱 Apple 的[NSProcessInfo 參考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)。

### <a name="reacting-to-low-power-mode"></a>回應低電源模式

使用[NSProcessInfo](xref:Foundation.NSProcessInfo)類別的 `LowPowerModeEnabled` 屬性，判斷是否已在執行應用程式的 iOS 裝置上啟用低電源模式。 例如：

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>HealthKit 架構變更

Apple 在 iOS 9 中的[HealthKit](xref:HealthKit)架構包含下列變更：

- 支援 HealthKit 資料庫中的專案大量刪除和刪除追蹤。 如需詳細資訊，請參閱 Apple 的[HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject)、 [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery)和[HKHealthStore 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708)。
- 新的追蹤分類和特性已新增至 `HKQuantityTypeIdentifier` 類別（例如 `UVExposure`）和 `HKCategoryTypeIdentifier` 類別（例如 `OvulationTestResult`）。 

如需在 Xamarin 中使用 HealthKit 的詳細資訊，請參閱我們的[HealthKit 簡介](~/ios/platform/healthkit.md)檔。

## <a name="local-authentication-framework-changes"></a>本機驗證架構變更

Apple 在 iOS 9 中對[本機驗證](xref:LocalAuthentication)架構包含下列變更：

- 使用[LACoNtext](xref:LocalAuthentication.LAContext)類別的 `EvaluateAccessControl` 和 `EvaluatePolicy` 方法，您現在可以重複使用先前成功的解除鎖定嘗試的觸控識別碼相符專案。
- 取得目前已註冊手指清單的能力。
- 支援在從驗證新增或移除手指時進行追蹤。
- 在 Keychain 呼叫中使用_驗證內容_，以及支援評估 Keychain 存取控制清單的能力。
- 能夠從程式碼取消使用者提示。

如需詳細資訊，請參閱[TOUCH id 與 Xamarin. iOS 的臉部識別碼](~/ios/platform/touch-id-face-id.md)。

### <a name="lacontext-changes"></a>LACoNtext 變更

IOS 9 的[LACoNtext](xref:LocalAuthentication.LAContext)類別已進行下列變更：

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -傳回 touch ID 驗證可以重複使用的最大時間量。
- **EvaluatedPolicyDomainState** -取得或設定已評估原則的狀態。
- **MaxBiometryFailures** -已于 iOS 9 中進行過折舊。
- **TouchIdAuthenticationAllowableReuseDuration**取得或設定觸控識別碼驗證可以重複使用的時間量。
- **EvaluateAccessControl** -以非同步方式評估驗證原則。
- **失效**-使指定的 touch ID 驗證失效。
- **IsCredentialSet** -如果目前已設定認證，則會傳回 `true`。
- **SetCredentialType**設定指定的認證類型。

如需詳細資訊，請參閱 Apple 的[LACoNtext 參考](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:)。

## <a name="mapkit-framework-changes"></a>MapKit 架構變更

Apple 在 iOS 9 中的[MapKit](xref:MapKit)架構包含下列變更：

- MapKit 現在支援使用[MKLaunchOptions](xref:MapKit.MKLaunchOptions)和[MKDirections](xref:MapKit.MKLaunchOptions)類別，直接將地圖應用程式啟動至傳輸方向，以及查詢抵達的傳輸估計時間（ETA）。
- MapKit 和[CLGeocoder](xref:CoreLocation.CLGeocoder)類別所傳回的搜尋結果也可以提供結果的時區。
- 您現在可以使用[MKAnnotationView](xref:MapKit.MKAnnotationView)類別的 `DetailCalloutAccessoryView` 屬性，完全自訂 iOS 應用程式所呈現的地圖批註。

請參閱我們的[IOS 地圖](~/ios/user-interface/controls/ios-maps/index.md)和[逐步解說-流覽 MapKit 檔中的注釋和重](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md)迭，以取得有關使用 Xamarin 和 Apple [CLGeocoder Reference](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder)中地圖和注釋的詳細資訊，以取得詳細資訊。

## <a name="passkit-framework-additions"></a>新增 PassKit 架構

Apple 在 iOS 9 中的[PassKit](xref:PassKit)架構包含下列變更：

- Apple Pay 現在同時支援商店付款和信用卡，以及探索卡片。 如需詳細資訊，請參閱 Apple [PKPaymentRequest 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832)的**付款網路**一節。
- 直接從 Xamarin iOS 應用程式內，您現在可以將付款網路和卡片簽發者新增至 Apple Pay。 如需詳細資訊，請參閱 Apple 的[PKAddPaymentPassViewController 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116)。

如需在 Xamarin 中使用 PassKit 的詳細資訊，請參閱我們的[PassKit 簡介](~/ios/platform/passkit.md)檔。

## <a name="safari-services-framework-additions"></a>Safari 服務架構新增專案

Apple 在 iOS 9 中的[Safari 服務](xref:SafariServices)架構包含下列變更：

- 您現在可以使用新的[SFSafariViewController](xref:SafariServices.SFSafariViewController)類別，在 Xamarin iOS 應用程式中顯示 web 內容。 它可讓您與 Safari 應用程式共用網站資料和 cookie，並包含數個 Safari 的功能（例如，讀取器和自動填入）。 [SFSafariViewController](xref:SafariServices.SFSafariViewController)的特色是 [**完成**] 按鈕，會在使用者完成流覽 web 內容時，將他們傳回給您的應用程式。

由於[SFSafariViewController](xref:SafariServices.SFSafariViewController)類別是針對顯示網頁內容的單一頁面而量身打造，因此您應該考慮使用它來取代現有 Xamarin iOS 應用程式中的任何[WKWebKit](xref:WebKit.WKWebView)或[UIWebView](xref:UIKit.UIWebView)控制項。

### <a name="displaying-a-website"></a>顯示網站

以下程式碼是從另一個 view controller 內部呼叫[SFSafariViewController](xref:SafariServices.SFSafariViewController)的範例：

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>UIKit 架構變更

Apple 已包含許多適用于 iOS 9 的[UIKit](xref:UIKit) framework 元素的增強功能。 下列各節將詳細說明這些變更。

### <a name="3d-touch-events"></a>3D Touch 事件

IOS 9 和 iPhone 6s 和 iPhone 6s Plus 的新手，3D Touch 將壓力機密手勢新增至您的 iOS 應用程式。 因此，如果您的應用程式在 iOS 9 （或更新版本）上執行，而且 iOS 裝置能夠支援 3D Touch，壓力的變更將導致引發 `TouchesMoved` 事件。

由於行為的變更，您的 iOS 應用程式應該準備好讓 `TouchesMoved` 事件更頻繁地叫用，即使 X/Y 座標並未變更也是一樣。

如需詳細資訊，請參閱我們的[3D Touch 指南簡介](~/ios/platform/3d-touch.md)。

### <a name="document-open-in-place-functionality"></a>檔開放位置功能

藉由使用[UIApplicationDelegate](xref:UIKit.UIApplicationDelegate)類別的 `FinishedLaunching (application, launchOptions)` 或 `WillFinishLaunching (Application, launchOptions)` 方法，您現在可以開啟檔並就地修改（而不是使用複本）。

若要支援新的開啟位置功能，請將 `LSSupportsOpeningDocumentsInPlace` 金鑰新增至您的**plist**檔案，其值為 `YES`。

如需詳細資訊，請參閱 Apple 的[UIApplicationDelegate 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)。

### <a name="enhanced-touch-events"></a>增強型觸控事件

Apple 在 iOS 9 中提供了許多觸控事件的增強功能。 這些功能包括能夠使用觸控預測，以及取得在顯示重新整理之間的中繼接觸存取權。

如需詳細資訊，請參閱[適用于 iOS 的 Apple 事件處理指南](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html)。

### <a name="fetching-tailored-content"></a>正在提取量身打造的內容

新的 `NSDataAsset` 類別可讓 Xamarin iOS 應用程式提取專為其目前執行所在之 iOS 裝置的記憶體和圖形功能量身打造的內容。

### <a name="new-layout-anchors"></a>新的版面配置錨點

新的 `NSLayoutAnchor` 和 `NSLayoutDimension` 版面配置錨點類別會使用[UIView](xref:UIKit.UIView)類別的新錨點屬性（例如 `LeadingAnchor` 和 `WidthAnchor`），讓 iOS 9 中的版面配置變得更容易。

如需有關在 Xamarin iOS 應用程式中使用自動調整和大小類別的詳細資訊，請參閱我們的[整合](~/ios/user-interface/storyboards/unified-storyboards.md)分鏡指令檔簡介和 Apple 的[NSLayoutAnchor 參考](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor)、 [NSLayoutDimension 參考](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension)和[UIView 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView)，以取得詳細資訊。

### <a name="new-readable-content-margins"></a>新的可讀取內容邊界

新的 `UILayoutGuide` 類別可以用來提供可讀取的內容邊界，並定義視圖內部內容的繪製區域。 如需詳細資訊，請參閱 Apple 的[UILayoutGuide 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide)。

### <a name="text-input-in-notifications-modifications"></a>通知修改中的文字輸入

[UIUserNotificationAction](xref:UIKit.UIUserNotificationAction)類別具有新的 `Behavior` 屬性，可用於支援來自通知的文字輸入。

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate 變更

雖然 Apple 不會正式淘汰，但會建議使用 `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` 或 `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` 方法來取代[UIApplicationDelegate](xref:UIKit.UIApplicationDelegate)類別之 `FinishedLaunching (UIApplication application)` 方法的所有呼叫。

如需詳細資訊，請參閱 Apple 的[UIApplicationDelegate 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)。

### <a name="uikit-dynamics-changes"></a>UIKit Dynamics 變更

Apple 在 iOS 9 中的 UIKit Dynamics 包含下列變更：

- Dynamics 現在提供非矩形衝突界限的支援。
- 新的、可自訂的 `UIFieldBehavior` 類別是用來支援各種欄位類型。
- 已將其他附件類型新增至 `UIAttachmentBehavior` 類別。

如需詳細資訊，請參閱 Apple 的[UIAttachment 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior)。

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView 和 UIDatePicker 變更

在 iOS 9 之前， [UIPickerView](xref:UIKit.UIPickerView)和[UIDatePicker](xref:UIKit.UIDatePicker)控制項無法調整大小，並會自動調整以填滿其容器的寬度（通常是應用程式執行所在之 iOS 裝置的寬度）。

在 iOS 9 中，不會再進行此自動調整大小，而不論螢幕大小和方向為何，控制項都會在所有 iOS 裝置上以320點寬度呈現。

若要修正這種情況，請使用 [自動設定] 和 [大小] 類別，將控制項的寬度釘選到父容器的邊緣（view），並指定所需的高度。 如需在 Xamarin iOS 應用程式中使用自動版面配置和大小類別的詳細資訊，請參閱我們的整合分鏡指令檔[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)。

### <a name="new-uitextinputassistantitem-class"></a>新增 UITextInputAssistantItem 類別

在_快捷方式_列中，使用新的 `UITextInputAssistantItem` 類別來配置橫條圖按鈕群組。 快捷方式列是可在螢幕鍵盤上提供輸入快速鍵的新區域。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
