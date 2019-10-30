---
title: iOS 9 相容性
description: 即使您不打算將 iOS 9 功能直接新增至您的應用程式，您還是應該使用最新版本的 Xamarin 重建您的應用程式。
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: cbea7686c2ec96492f9531e1ff30d1686db1a4c0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031766"
---
# <a name="ios-9-compatibility"></a>iOS 9 相容性

_即使您不打算將 iOS 9 功能直接新增至您的應用程式，您還是應該使用最新版本的 Xamarin 重建您的應用程式。_

> [!IMPORTANT]
> 此頁面上的資訊適用于已在 App Store 以 iOS 8 或更早版本為目標的應用程式，但尚未提交適用于 iOS 9 相容性更新的客戶。 如果您已經在使用最新版本-Xcode 7 和 Xamarin。 iOS 9-針對您的應用程式開發，請流覽[iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)。

當第一個 iOS 9 Beta 版出現時，我們發現有兩個舊版 Xamarin 的問題顯示為無法在 iOS 9 上啟動的繼承應用程式。

這兩個問題（如[我們的論壇所詳述](https://forums.xamarin.com/discussion/comment/131529/#Comment_131529)）是：

- IOS 8 或更早版本的應用程式組建無法在32位裝置（包括以[Unified API](~/cross-platform/macios/unified/index.md)建立的應用程式）上啟動。
- 未指定完整路徑，P/Invoke 失敗。

將 Xamarin 安裝更新至最新的穩定通道版本，然後重建並重新部署您的應用程式，即可解決這兩個問題。

_即使您不打算立即使用 iOS 9 功能更新您的應用程式，我們仍建議您使用最新版本的 Xamarin 重新建立，並重新提交至 App Store_。

這可確保您的應用程式會在您的客戶升級後於 iOS 9 上執行。
您可以繼續支援 iOS 8-使用最新版本重建並不會影響應用程式的目標版本。

如果您在 iOS 9 上測試現有的應用程式時有進一步的問題，請閱讀下面的[改善相容性](#compat)一節。

### <a name="updating-with-visual-studio"></a>使用 Visual Studio 更新

建議您明確檢查 Visual Studio 是否已更新為最新的穩定版本。

## <a name="what-about-components-nugets-and-other-libraries"></a>那麼元件、Nuget 和其他程式庫呢？

您**不**需要等候您所使用之任何元件或 nuget 的新版本，來解決上述的兩個問題。
只要使用最新穩定版本的 Xamarin 來重新建立應用程式，就可以解決這些問題。

同樣地，元件廠商和 Nuget 作者**不**需要提交新組建，就能修正上述兩個問題。 不過，如果有任何元件或 Nuget 使用來自**Xib**檔案的 `UICollectionView` 或載入視圖，則*可能*需要進行更新，才能解決下列所述的 iOS 9 相容性問題。

<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>改善程式碼中的相容性

有幾種程式碼模式*可用*來在 ios 9 中的舊版 ios 中工作。 以下是在 iOS 9 上進行測試時可能會發生的一些可能問題（及其解決方案）：

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell 中的 ContentView 是 null。

**原因：** 在 iOS 9 中，由於 iOS 9 中的行為變更是[UICollectionView 檔的狀態](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)，因此現在需要 `initWithFrame:` 的函式。 如果您註冊了指定識別碼的類別，而且必須建立新的資料格，則現在會藉由呼叫其 `initWithFrame:` 方法來初始化資料格。

**修正：** 新增 `initWithFrame:` 的函數，如下所示：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例： [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)、 [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>從 Xib/筆尖載入視圖時，UIView 無法從當中初始化

**原因：** `initWithCoder:` 的函式是從 Interface Builder Xib 檔載入視圖時所呼叫的函式。 如果此函式不是匯出的，非受控碼就無法呼叫其受管理的版本。 先前（例如 在 iOS 8 中）已叫用 `IntPtr` 的「檢查程式」來初始化 view。

**修正：** 建立並匯出 `initWithCoder:` 的函式，如下所示：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例：[聊天](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

### <a name="dyld-message-no-cache-image-with-name"></a>Dyld 訊息：沒有名稱為的快取映射 。

在記錄檔中，您可能會遇到下列資訊的損毀：

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 這是 Apple 原生連結器中的錯誤（bug），這會在公開公用架構（在 iOS 7 中設為公用的 JavaScriptCore，在其為私用架構之前），而且應用程式的部署目標是在架構為私用時，用於 iOS 版本。 在此情況下，Apple 的連結器會與架構的私用版本（而不是公用版本）連結。

**修正：** 這適用于 iOS 9，但有一個簡單的因應措施，您可以在此同時套用：僅以您專案中較新的 iOS 版本為目標（在此案例中，您可以試用 iOS 7）。 其他架構可能會展示類似的問題，例如，WebKit 架構在 iOS 8 中是公開的，因此以 iOS 7 為目標會導致此錯誤; 您應該以 iOS 8 為目標，以在您的應用程式中使用 WebKit）。

## <a name="related-links"></a>相關連結

- [iOS 9 相容性版本資訊](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [iOS 9 簡介](~/ios/platform/introduction-to-ios9/index.md)
- [將您的 Xamarin iOS 應用程式更新為 iOS9 （影片）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
