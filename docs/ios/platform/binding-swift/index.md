---
title: 系結 iOS Swift 程式庫
description: '本檔描述如何建立 Swift 程式碼的 c # 系結，讓您可以在 Xamarin iOS 應用程式中使用原生程式庫和 CocoaPods。'
ms.prod: xamarin
ms.assetid: 890EFCCA-A2A2-4561-88EA-30DE3041F61D
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 72ab1d9f10ee308313569528d152d5930a258207
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852975"
---
# <a name="bind-ios-swift-libraries"></a>系結 iOS Swift 程式庫

> [!IMPORTANT]
> 我們目前正在調查 Xamarin 平臺上的自訂系結使用方式。 請接受[**這份問卷調查**](https://www.surveymonkey.com/r/KKBHNLT)，以通知未來的開發工作。

IOS 平臺以及其原生語言和工具會不斷演進，而且有許多協力廠商程式庫是使用最新的產品開發的。 最大化程式碼和元件重複使用是跨平臺開發的主要目標之一。 將以 Swift 建立的元件重複使用的功能，對於 Xamarin 開發人員而言越來越重要，因為他們在開發人員之間的熱門程度會持續成長。 您可能已經熟悉將一般的[目標 C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)程式庫系結的程式。 其他檔現已提供說明系結[Swift 架構](walkthrough.md)的程式，因此 Xamarin 應用程式可以透過相同的方式取用它們。 本檔的目的是要說明為 Xamarin 建立 Swift 系結的高階方法。

## <a name="high-level-approach"></a>高階方法

透過 Xamarin，您可以系結任何協力廠商原生程式庫，以供 Xamarin 應用程式取用。 Swift 是新語言，而建立使用此語言之程式庫的系結需要一些額外的步驟和工具。 此方法包含下列四個步驟：

1. 建立原生程式庫
1. 準備 Xamarin 中繼資料，以啟用 Xamarin 工具來產生 c # 類別
1. 使用原生程式庫和中繼資料建立 Xamarin 系結程式庫
1. 在 Xamarin 應用程式中使用 Xamarin 系結程式庫

下列各節將概述這些步驟和其他詳細資料。

### <a name="build-the-native-library"></a>建立原生程式庫

第一個步驟是使用已建立的目標-C 標頭來準備原生 Swift 架構。 此檔案是自動產生的標頭，會公開所需的 Swift 類別、方法和欄位，使其可透過 Xamarin 系結程式庫同時存取目標 C 和 c #。 這個檔案位於架構中的下列路徑底下： ** \<FrameworkName> . Framework/標頭/ \<FrameworkName> -Swift. h**。 如果公開的介面具有所有必要的成員，您可以跳到下一個步驟。 否則，需要進一步的步驟來公開這些成員。 此方法將取決於您是否可以存取 Swift 架構原始程式碼：

- 如果您有程式碼的存取權，您可以使用屬性裝飾所需的 Swift 成員 `@objc` ，並套用一些額外的規則，讓 Xcode build 工具知道這些成員應該會公開給目標-C 世界和標頭。
- 如果您沒有原始程式碼的存取權，您必須建立 proxy Swift 架構，它會包裝原始的 Swift 架構，並使用屬性來定義應用程式所需的公用介面 `@objc` 。

### <a name="prepare-the-xamarin-metadata"></a>準備 Xamarin 中繼資料

第二個步驟是準備 API 定義介面，以供系結專案用來產生 c # 類別。 [目標 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)工具和前述自動產生** \<FrameworkName> 的 Swift**標頭檔可以手動或自動建立這些定義。 一旦產生中繼資料，就應該以手動方式進行驗證和驗證。

### <a name="build-the-xamarinios-binding-library"></a>建立 Xamarin iOS 系結程式庫

第三個步驟是建立一個特殊的專案-Xamarin. iOS 系結程式庫。 它會參考在上一個步驟準備的架構和中繼資料，以及個別架構所依賴的任何其他相依性。 它也會處理參考的原生架構與使用中的 Xamarin iOS 應用程式的連結。

### <a name="consume-the-xamarin-binding-library"></a>使用 Xamarin 系結程式庫

第四個和最後一個步驟是參考 Xamarin iOS 應用程式中的系結程式庫。 在以 iOS 12.2 和更新版本為目標的 iOS 應用程式中，可使用原生程式庫就已足夠。 針對以較低版本為目標的應用程式，需要一些額外的步驟：

- 新增 Swift dylib 相依性以進行執行時間支援。 從 iOS 12.2 和 Swift 5.1 開始，語言變得是 ABI （應用程式二進位介面）穩定且相容。 這就是為什麼任何以較低 iOS 版本為目標的應用程式，都必須包含架構所使用的 Swift dylib 來處理相依性。 使用[SwiftRuntimeSupport NuGet 套件](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)，自動將必要的 dylib 相依性包含在產生的應用程式封裝中。
- 新增**SwiftSupport**資料夾與已簽署的 dylib 來處理，這會在上傳過程中由 AppStore 進行驗證。 封裝應該使用 Xcode 工具進行簽署並散發至 AppStore connect，否則會自動遭到拒絕。

## <a name="walkthrough"></a>逐步介紹

上述方法概述為 Xamarin 建立 Swift 系結所需的高層級步驟。 在實務上準備這些系結時，需要考慮許多較低層級的步驟，還有進一步的詳細資料，包括適應原生工具和語言的變更。 其目的是協助您更深入瞭解此概念，以及此程式所牽涉的高階步驟。 如需詳細的逐步指南，請參閱[Xamarin Swift Binding](walkthrough.md)逐步解說檔。

## <a name="related-links"></a>相關連結

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)
- [Sharpie 中繼資料驗證](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)
- [系結目標-C 架構](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)
- [Gigya iOS SDK （Swift 架構）](https://developers.gigya.com/display/GD/Swift+SDK)
- [Swift 5.1 ABI 穩定性](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Xamarin UITest 自動化](https://docs.microsoft.com/appcenter/test-cloud/uitest/)
- [UITest 設定](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [AppCenter 測試雲端](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [範例專案存放庫](https://github.com/xamcat/xamarin-binding-swift-framework)
