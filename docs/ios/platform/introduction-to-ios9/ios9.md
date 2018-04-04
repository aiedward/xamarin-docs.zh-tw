---
title: iOS 9 相容性
description: 即使您不打算立即將 iOS 9 功能加入至您的應用程式，您應該在重建您的應用程式的 Xamarin 的最新版本。
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b7cbec3f064e6000f991fb0f9ce256415f6ce5dd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="ios-9-compatibility"></a>iOS 9 相容性

_即使您不打算立即將 iOS 9 功能加入至您的應用程式，您應該在重建您的應用程式的 Xamarin 的最新版本。_

> [!IMPORTANT]
> 此頁面上的資訊是已經在 windows 市集應用程式的客戶目標 iOS 8 或更早版本，人員尚未已提交的 iOS 9 相容性更新。 如果您已經使用最新的版本-Xcode 7 和 Xamarin.iOS 9-應用程式開發，請造訪[簡介 iOS 9](~/ios/platform/introduction-to-ios9/index.md)。

當出現第一個 iOS 9 beta 版時，我們用來識別兩個問題： 認定為較舊的應用程式無法啟動 iOS 9 上的 Xamarin 的舊版本。

這兩個問題 (做為[我們的論壇上詳述](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) 是：

- 建置 iOS 8 或更早版本，無法在 32 位元裝置上啟動所需的應用程式 (包括以建置應用程式[統一的 API](~/cross-platform/macios/unified/index.md))。
- 未指定 P/Invoke 失敗的完整路徑。

更新您的 Xamarin 安裝最新穩定通道版本中，然後重建並重新部署您的應用程式中，修正這兩個問題。

_即使您不打算立即更新您的應用程式與 iOS 9 功能，建議您在使用 Xamarin 的最新版本重新建置並重新提交至應用程式市集_。



這可確保您的客戶在升級後，將 iOS 9 上執行您的應用程式。
您可以繼續支援 iOS 8-重建最新版本並不會影響應用程式的目標版本。

如果您測試您在 iOS 9 上的現有應用程式時有其他問題，請參閱[改善相容性](#compat)下一節。


### <a name="updating-with-visual-studio"></a>使用 Visual Studio 更新

建議您明確檢查 Visual Studio 會更新為最新穩定版本。

## <a name="what-about-components-nugets-and-other-libraries"></a>為何元件、 Nugets 和其他文件庫？

您執行**不**需要等待新任何的版本元件或 Nugets 您用來解決上述兩個問題。
只要重新建置應用程式與 Xamarin.iOS 最新穩定版本已修正這些問題。

同樣地，元件廠商以及 Nuget 作者都**不**才能提交新的組建，只是為了修正上述兩個問題。 不過，如果有任何元件或 Nuget 使用`UICollectionView`或從檢視載入**Xib**檔案、 更新*可能*必須解決 iOS 9 相容性問題，如下所述。


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>改善您的程式碼中的相容性

沒有少數的情況下，程式碼模式，*用*重大 iOS 9 的 iOS 的舊版本中工作。 以下是一些可能的問題 （和其解決方法），可能會發生在 iOS 9 上測試時：

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView 中為 null 的建構函式

**原因：**在 iOS 9`initWithFrame:`建構函式現在是必要項目，因為在為 iOS 9 中的行為變更[UICollectionView 文件狀態](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)。 如果您註冊指定的識別項的類別，並且必須建立新的儲存格，資料格現在藉由呼叫初始化其`initWithFrame:`方法。

**修正：**新增`initWithFrame:`建構函式，就像這樣：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例： [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)， [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView 無法初始化與編碼器 Xib/Nib 從載入檢視時

**原因：** `initWithCoder:`建構函式是從介面產生器 Xib 檔案載入檢視時呼叫。 如果這個建構函式不會匯出 unmanaged 程式碼無法呼叫它的我們受管理的版本。 之前 （例如。 在 iOS 8)`IntPtr`建構函式已叫用來初始化檢視。

**修正：**建立和匯出`initWithCoder:`建構函式，就像這樣：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相關的範例：[交談](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Dyld 訊息： 未快取映像名稱...

您可能會遇到的損毀記錄檔中的下列資訊：

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：**這是 Apple 的原生連結器，以致它們公開私用的 framework 中的 bug （JavaScriptCore 已公開在 iOS 7，之前它是私用架構），和應用程式的部署目標是 iOS 版本時架構是私人的。 在此情況下 Apple 的連結器會使用私用的版本，而不是公用版本的 framework 的連結。

**修正：**這將會解決 ios 9，但沒有簡單的解決方法，您可以同時套用自行： 只為目標的更新版本的 iOS 版本專案中 （您在此情況下可以嘗試 iOS 7）。 其他架構可能會表現出類似的問題，例如 WebKit framework 已公開在 iOS 8 （和目標 iOS 7 時，會導致此錯誤，因此您應將目標設 iOS 應用程式中使用 WebKit 8）。



## <a name="related-links"></a>相關連結

- [iOS 9 相容性版本資訊](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)
- [Xamarin.iOS 應用程式更新至 iOS9 （影片）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
