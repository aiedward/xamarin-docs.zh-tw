---
title: iOS 9 相容性
description: 即使您不打算立即將 iOS 9 功能新增至您的應用程式，您應該重建您的應用程式使用最新版 Xamarin。
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 2f22fdeaad1b276bb94d2b1ee5af4a6c24d22cb7
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350777"
---
# <a name="ios-9-compatibility"></a>iOS 9 相容性

_即使您不打算立即將 iOS 9 功能新增至您的應用程式，您應該重建您的應用程式使用最新版 Xamarin。_

> [!IMPORTANT]
> 此頁面上的資訊適用於客戶已在 windows 市集的應用程式目標設為 iOS 8 或更早版本，誰不已送出更新為 iOS 9 相容性。 如果您已經在使用最新的版本-Xcode 7 和您的應用程式開發的 Xamarin.iOS 9-請瀏覽[iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)。

當出現的第一個 iOS 9 beta 版時，我們用來識別兩個問題： 認定為無法啟動 iOS 9 上的舊版應用程式的 Xamarin 的較舊版本。

這兩個問題 (作為[我們的論壇上詳述](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) 是：

- 建置適用於 iOS 8 或更早版本，無法在 32 位元裝置上啟動的應用程式 (包括以建置應用程式[統一 API](~/cross-platform/macios/unified/index.md))。
- 未指定 P/Invoke 失敗的完整路徑。

更新您的 Xamarin 安裝最新穩定通道版本中，然後重建並重新部署您的應用程式，以修正這兩個問題。

_即使您不打算立即更新您的應用程式與 iOS 9 功能，我們建議您重新建置使用 Xamarin 的最新版本，並重新提交至 App Store_。



這可確保您的客戶在升級後，會在 iOS 9 上執行您的應用程式。
您可以繼續支援 iOS 8-重建在最新版本並不會影響應用程式的目標版本。

如果您有其他問題，測試您在 iOS 9 上的現有應用程式時，請參閱[改善相容性](#compat)下一節。


### <a name="updating-with-visual-studio"></a>使用 Visual Studio 更新

建議您明確檢查 Visual Studio 會更新為最新穩定版本。

## <a name="what-about-components-nugets-and-other-libraries"></a>元件、 Nuget，以及其他程式庫呢？

這麼做**不**需要等候任何您用來解決上述的兩個問題的 Nuget 或元件的新版本。
只要重新建置您的應用程式，最新的 Xamarin.iOS 穩定版本已經修正這些問題。

同樣地，元件廠商和 Nuget 作者也**不**才能提交新的組建，只是為了修正上述提到的兩個問題。 不過，如果有任何元件或 Nuget 會使用`UICollectionView`或載入檢視**Xib**檔案，更新*可能*必須解決 iOS 9 相容性問題下, 面所述。


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>改善您的程式碼中的相容性

還有少數的情況下，程式碼模式*使用*在舊版的重大 iOS 9 的 iOS 中運作。 以下是一些可能的問題 （和其解決方案），可能會發生在 iOS 9 上測試時：

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView 為 null 建構函式中

**原因：** 在 iOS 9`initWithFrame:`建構函式現在是必要的因為在為 iOS 9 中的行為變更[UICollectionView 文件狀態](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)。 如果您註冊指定的識別項的類別，必須建立新的儲存格的資料格現在已初始化。 藉由呼叫其`initWithFrame:`方法。

**修正：** 新增`initWithFrame:`建構函式如下：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例： [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)， [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView 無法與 coder init Xib/Nib 從載入檢視時

**原因：** `initWithCoder:`建構函式是從介面產生器 Xib 檔案載入檢視時呼叫。 如果這個建構函式不會匯出未受管理的程式碼無法呼叫我們 managed 的版本。 之前 （例如。 在 iOS 8)`IntPtr`建構函式已叫用來初始化檢視。

**修正：** 建立及匯出`initWithCoder:`建構函式如下：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相關的範例：[對談](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Dyld 訊息： 沒有快取映像名稱...

您可能會遇到當機記錄檔中的下列資訊：

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 這是 Apple 的原生連結器，當他們完成的私用的架構公用發生這種情況中的 bug （JavaScriptCore 已公開在 iOS 7，之前它是私用的架構），而應用程式的部署目標為 iOS 版本時framework 是私人的。 在此情況下 Apple 的連結器會使用私用的版本，而不是公用版本的 framework 的連結。

**修正：** 將會解決此問題適用於 iOS 9，但同時套用自行輕鬆因應措施： 只為目標的更新版本的 iOS 版本專案中 （您在此情況下可以嘗試 iOS 7）。 其他架構可能會表現出類似的問題，例如 WebKit 架構已在 iOS 8 中公開 （和以 iOS 7 為目標時，會導致此錯誤，因此您應該為目標 iOS 8 應用程式中使用 WebKit）。



## <a name="related-links"></a>相關連結

- [iOS 9 相容性版本資訊](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)
- [更新您的 Xamarin.iOS 應用程式到 iOS9 （影片）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
