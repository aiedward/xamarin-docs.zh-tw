---
title: 其他 iOS 9 架構變更
description: 本文涵蓋了其他的次要變更或現有的架構，適用於 iOS 9 的增強功能。
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 0ae286ddbc61f48cbdd257dc453a2d9680bba703
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="additional-ios-9-frameworks-changes"></a>其他 iOS 9 架構變更

_本文涵蓋了其他的次要變更或現有的架構，適用於 iOS 9 的增強功能。_

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 標誌")](additional-framework-changes-images/ios9.png#lightbox)

IOS 的重大變更，除了 Apple 已進行修改，以及數個現有架構的增強功能中 iOS 9。

## <a name="av-foundation-framework-additions"></a>AV 基礎架構新增項目

AV 基礎架構，在[AVSpeechSynthesisVoice](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechSynthesisVoice/)類別現在可讓您指定語音除了語言識別項。

例如，下列程式碼會取得一份所有可用的語音：

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

您可以藉由設定為，然後使用其中一個從清單語音`Voice`屬性的執行個體[AVSpeachUtterance](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechUtterance/)類別。

[AVQueuePlayer](https://developer.xamarin.com/api/type/AVFoundation.AVQueuePlayer/)類別現可支援混合的網際網路資料流和檔案為基礎的媒體在佇列中。 舊版無法只佇列相同類型的媒體。

如需詳細資訊，請參閱 Apple [AVSpeechSynthesisVoice 參考](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)。

## <a name="avkit-framework-additions"></a>AVKit 架構新增項目

若要使用新的圖片中圖片 (PIP) 」 功能，包括新 AVKit framework`AVPictureInPictureController`和[AVPlayerViewController](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/)類別：

- **AVPictureInPictureController** -這個類別可讓 iOS 9 應用程式以回應使用者啟動的浮動、 可調整大小的 PIP 視窗在 iPad 上視訊播放。
- **AVPlayerViewController** -管理`AVPlayer`控制器用來在 iPad 的浮動、 可調整大小 PIP 視窗中的視訊，呈現。

如需詳細資訊，請參閱我們[適用於 iPad 的多工](~/ios/platform/introduction-to-ios9/index.md#multitasking)文件集和 Apple [AVPictureInPictureController 參考](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController)和[AVPlayerViewController 參考](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>CloudKit Web Services 簡介

CloudKit 架構來簡化應用程式的開發該存取 icloud 的功能。 這包括應用程式資料和資產的權限，以及能夠以安全地儲存應用程式資訊的擷取。 此套件可讓使用者的匿名層藉由允許存取其 iCloud Id 的應用程式，而不需要共用個人資訊。

新_CloudKit Web 服務_framework 提供的 JavaScript 程式庫 (CloudKit JS)，可以併入您的網站以提供存取相同的 CloudKit 基礎資料和與您 Xamarin.iOS 應用程式的內容。

> [!IMPORTANT]
> 您可以存取、 呈現或更新內容，從使用 CloudKit JS CloudKit 資料庫之前，您必須是先前定義定義該資料庫的結構描述。




如需詳細資訊，請參閱下列文件：

- [簡介 CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -我們使用 CloudKit Xamarin.iOS 應用程式中的簡介。
- [CloudKit 快速入門](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)-CloudKit 的 Apple 的簡介。
- [CloudKit JS 參考](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359)-Apple CloudKit JS 文件。
- [CloudKit Web 服務參考](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240)-Apple 的參考，描述 CloudKit HTTP 介面。
- [CloudKit 類別目錄： 簡介 （Cocoa 和 JavaScript） 的 CloudKit](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) -使用 CloudKit 和 CloudKit JS Apple 的範例應用程式。

> [!IMPORTANT]
> Apple[提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)可協助開發人員適當地處理歐盟的一般資料保護規定 (GDPR)。

## <a name="foundation-framework-additions"></a>Mfc 架構新增項目

Apple iOS 9 中包含的基礎架構的下列變更：

### <a name="changes-to-nsbundle"></a>NSBundle 的變更

若要進行下列變更，就有[NSBundle](https://developer.xamarin.com/api/type/Foundation.NSBundle/) ios 9 的類別：

* `GetPreservationPriorityForTag (NSString tag)` -取得具有指定標記的資源的目前保留優先權。 有效值為範圍中`0.0`來`1.0`，將會清除具有最低優先順序的資源。
* `SetPreservationPriorityForTag (double priority, NSSet tags)` -設定具有指定標記的資源的目前保留優先權。 有效值為範圍中`0.0`來`1.0`，將會清除具有最低優先順序的資源。

如需詳細資訊，請參閱 Apple [NSBundle 參考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)。

### <a name="changes-to-nsprocessinfo"></a>NSProcessInfo 的變更

在 iOS 裝置上執行每個處理序的單一、_程序資訊代理程式_(PIA)。 使用[NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/)類別，以提供目前的 PIA 和控制電源和熱力管理特定的處理序的相關資訊。

比方說，以控制自動終止處理程序，您可以使用下列程式碼：

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

如需詳細資訊，請參閱 Apple [NSProcessInfo 參考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)。

### <a name="reacting-to-low-power-mode"></a>對低度電源模式回應

使用`LowPowerModeEnabled`屬性[NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/)類別，以判斷是否已在 iOS 裝置上執行應用程式上啟用低度電源模式。 例如: 

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>HealthKit 架構變更

Apple 包含下列變更以[HealthKit](https://developer.xamarin.com/api/namespace/HealthKit/) iOS 9 中的架構：

- 支援大量刪除和 HealthKit 資料庫中的項目刪除追蹤。 請參閱 Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject)， [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery)和[HKHealthStore 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708)如需詳細資訊。
- 新的追蹤類別和特性加入至`HKQuantityTypeIdentifier`類別 (例如`UVExposure`) 以及`HKCategoryTypeIdentifier`類別 (例如`OvulationTestResult`)。 請參閱 Apple [HealthKit 常數參考](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710)如需詳細資訊。

請參閱我們[簡介 HealthKit](~/ios/platform/healthkit.md)如需有關使用 HealthKit Xamarin.iOS 中的文件。

## <a name="local-authentication-framework-changes"></a>本機驗證架構變更

Apple 包含下列變更以[本機驗證](https://developer.xamarin.com/api/namespace/LocalAuthentication/)iOS 9 中的架構：

- 使用`EvaluateAccessControl`和`EvaluatePolicy`方法[LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/)類別，您現在可以重複使用 Touch ID 符合從上一個成功解除鎖定的嘗試。
- 若要取得目前已註冊的指清單功能。
- 支援新增或移除從驗證手指時加以追蹤。
- 能夠使用_驗證內容_Keychain 呼叫及評估 [金鑰鏈存取控制項的支援] 中列出。
- 取消的能力從程式碼的使用者提示。

請參閱我們[簡介 Touch ID](~/ios/platform/touchid.md)如需有關使用 Touch ID Xamarin.iOS 中的文件。

### <a name="lacontext-changes"></a>LAContext 變更

若要進行下列變更，就有[LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) ios 9 的類別：

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -傳回的最大可重複使用 touch ID 驗證的時間量。
- **EvaluatedPolicyDomainState** -取得或設定評估原則的狀態。
- **MaxBiometryFailures** -已在 iOS 9 中已停用。
- **TouchIdAuthenticationAllowableReuseDuration**取得或設定可重複使用 touch ID 驗證的時間量。
- **EvaluateAccessControl** -以非同步方式評估驗證原則。
- **使**-失效給定的 touch ID 驗證。
- **IsCredentialSet** -傳回`true`如果目前設定認證。
- **SetCredentialType**設定給定的認證類型。

請參閱 Apple [LAContext 參考](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:)如需詳細資訊。

## <a name="mapkit-framework-changes"></a>MapKit 架構變更

Apple 包含下列變更以[MapKit](https://developer.xamarin.com/api/namespace/MapKit/) iOS 9 中的架構：

- MapKit 現在提供支援，直接在傳輸方向啟動對應應用程式和查詢傳輸抵達 （知道） 使用的預估時間[MKLaunchOptions](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/)和[MKDirections](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/)類別。
- MapKit 所傳回的搜尋結果和[CLGeocoder](https://developer.xamarin.com/api/type/CoreLocation.CLGeocoder/)類別也可以提供結果的時區。
- 您現在可以完全自訂地圖顯示您 iOS 應用程式使用的註解`DetailCalloutAccessoryView`屬性[MKAnnotationView](https://developer.xamarin.com/api/type/MapKit.MKAnnotationView/)類別。

請參閱我們[iOS 對應](~/ios/user-interface/controls/ios-maps/index.md)和[逐步解說-瀏覽註解和 MapKit 中的覆疊](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md)文件，如需有關使用地圖和註解中 Xamarin.iOS 和 Apple [CLGeocoder 參考](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder)如需詳細資訊。

## <a name="passkit-framework-additions"></a>PassKit 架構新增項目

Apple 包含下列變更以[PassKit](https://developer.xamarin.com/api/namespace/PassKit/) iOS 9 中的架構：

- Apple Pay 現在支援存放區的付款和信用卡以及探索卡。 請參閱**付款網路**Apple 的區段[PKPaymentRequest 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832)如需詳細資訊。
- 從直接在 Xamarin.iOS 應用程式，您現在可以加入付款網路和卡簽發者 Apple Pay。 請參閱 Apple [PKAddPaymentPassViewController 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116)如需詳細資訊。

請參閱我們[簡介 PassKit](~/ios/platform/passkit.md)如需有關使用 PassKit Xamarin.iOS 中的文件。

## <a name="safari-services-framework-additions"></a>Safari 服務架構新增項目

Apple 包含下列變更以[Safari 服務](https://developer.xamarin.com/api/namespace/SafariServices/)iOS 9 中的架構：

- 您現在可以使用新[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)顯示 Xamarin.iOS 應用程式中的網頁內容的類別。 它可讓您分享 Safari 應用程式的網站資料和 cookie，並包括數個 Safari 的功能 （例如讀取器和自動填入）。 [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)功能**完成**在完成檢視 web 內容時，會傳回使用者加入至應用程式的按鈕。

因為[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)類別專供顯示 web 內容的單一頁面時，您應該考慮使用它來取代任何[WKWebKit](https://developer.xamarin.com/api/type/WebKit.WKWebView/)或[UIWebView](https://developer.xamarin.com/api/type/UIKit.UIWebView/)現有 Xamarin.iOS 應用程式中的控制項。

### <a name="displaying-a-website"></a>顯示網站

下列程式碼是呼叫的範例[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)從另一個檢視控制器內：

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>UIKit 架構變更

Apple 已包含了許多增強功能的數個項目[UIKit](https://developer.xamarin.com/api/namespace/UIKit/) ios 9 的架構。 下列各節將詳細說明這些變更。

### <a name="3d-touch-events"></a>3D Touch 事件

IOS 9 iPhone 6s 和 iPhone 6s 的新手再加上，3D Touch 將不足的壓力機密筆勢加入至您的 iOS 應用程式。 如此一來，如果您的應用程式正在執行 iOS 9 （或以上） 上，而且能夠支援 3D Touch 的 iOS 裝置，不足的壓力中變更會使`TouchesMoved`會引發事件。 

由於此項變更的行為，您的 iOS 應用程式應該做好準備`TouchesMoved`事件要叫用次數更頻繁，即使 X / Y 座標中未變更。 

如需詳細資訊，請參閱我們[簡介 3D Touch](~/ios/platform/3d-touch.md)指南。

### <a name="document-open-in-place-functionality"></a>文件開啟在就地功能

使用`FinishedLaunching (application, launchOptions)`或`WillFinishLaunching (Application, launchOptions)`方法[UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/)類別，您現在可以開啟文件，並修改在位置 （相對於處理複本）。

若要支援就地開啟的新功能，將`LSSupportsOpeningDocumentsInPlace`Xamarin.iOS 應用程式的金鑰**Info.plist**值是檔案`YES`。

請參閱 Apple [UIApplicationDelegate 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)如需詳細資訊。

### <a name="enhanced-touch-events"></a>增強的觸控事件

Apple 已提供 iOS 9 的數個增強功能觸控事件。 這些包括使用觸控預測，並取得要顯示重新整理之間的中繼工作的存取權的能力。

請參閱 Apple[適用於 iOS 的事件處理指南](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html)如需詳細資訊。

### <a name="fetching-tailored-content"></a>擷取量身訂做的內容

新`NSDataAsset`類別可讓 Xamarin.iOS 應用程式擷取到的記憶體和 iOS 裝置上目前執行的圖形功能量身訂做的內容。

### <a name="new-layout-anchors"></a>新的版面配置起點

新`NSLayoutAnchor`和`NSLayoutDimension`配置錨點類別使用新的錨點屬性[UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)類別 (例如`LeadingAnchor`和`WidthAnchor`) 若要在 iOS 9 簡化版面配置。

請參閱我們[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)文件，如需有關在 Xamarin.iOS 應用程式與 Apple 中使用的自動版面配置和大小類別[NSLayoutAnchor 參考](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor)， [NSLayoutDimension 參考](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension)和[UIView 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView)如需詳細資訊。

### <a name="new-readable-content-margins"></a>新的可讀取內容邊界

新`UILayoutGuide`類別可以用來提供可讀取的內容邊界，以及定義繪製內容的區域內的檢視。 請參閱 Apple [UILayoutGuide 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide)如需詳細資訊。

### <a name="text-input-in-notifications-modifications"></a>修改通知中的文字輸入

[UIUserNotificationAction](https://developer.xamarin.com/api/type/UIKit.UIUserNotificationAction/)類別具有新`Behavior`可以用來支援從通知的文字輸入的屬性。

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate 變更

雖然不會正式 apple 已被取代，建議取代所有呼叫`FinishedLaunching (UIApplication application)`方法[UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/)類別其中一種`FinishedLaunching (UIApplication application, NSDictionary launchOptions)`或`WillFinishLaunching (UIApplication application, NSDictionary launchOptions)`方法。

請參閱 Apple [UIApplicationDelegate 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate)如需詳細資訊。

### <a name="uikit-dynamics-changes"></a>UIKit Dynamics 變更

Apple iOS 9 中包含 UIKit Dynamics 的下列變更：

- Dynamics 現在會提供非矩形衝突界限的支援。
- 新的、 可自訂`UIFieldBehavior`類別用來支援各種不同的欄位類型。
- 已加入其他附件`UIAttachmentBehavior`類別。

請參閱 Apple [UIAttachment 參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior)如需詳細資訊。

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView 和 UIDatePicker 變更

在 iOS 9 之前, [UIPickerView](https://developer.xamarin.com/api/type/UIKit.UIPickerView/)和[UIDatePicker](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/)控制項已不可調整大小，並會自動調整大小以填滿其容器 （通常的寬度與應用程式的 iOS 裝置的寬度在上執行）。

在 iOS 9，此自動調整大小不再發生而控制項將會呈現所有 iOS 裝置，不論螢幕大小和方向的 320 點寬度。

若要更正這種情況下，使用自動配置和大小類別來釘選到父容器 （檢視） 的邊緣控制項的寬度，以及指定所需的高度。 請參閱我們[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)文件的 Xamarin.iOS 應用程式中使用自動配置和大小類別的詳細資訊。

### <a name="new-uitextinputassistantitem-class"></a>新 UITextInputAssistantItem 類別

使用新`UITextInputAssistantItem`版面配置列按鈕群組中的類別_快顯列_。 快顯列是新的區域所提供之螢幕小鍵盤提供輸入的快速鍵。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
