---
title: 系結 iOS Swift 程式庫
description: '本檔說明如何建立快速程式碼的 c # 系結，讓您可以在 Xamarin iOS 應用程式中使用原生程式庫和 CocoaPods。'
ms.prod: xamarin
ms.assetid: 890EFCCA-A2A2-4561-88EA-30DE3041F61D
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: f678ec02ca5b9b47f6f78eea77547b038274190f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435518"
---
# <a name="bind-ios-swift-libraries"></a>系結 iOS Swift 程式庫

> [!IMPORTANT]
> 我們目前正在調查 Xamarin 平臺上的自訂系結使用方式。 請填寫 [**這份問卷**](https://www.surveymonkey.com/r/KKBHNLT) ，以通知未來的開發工作。

IOS 平臺（以及其原生語言和工具）不斷演進，而且有許多協力廠商程式庫是使用最新的供應專案所開發。 將程式碼和元件重複使用最大化是跨平臺開發的主要目標之一。 使用 Swift 建立之元件的能力，對 Xamarin 開發人員而言越來越重要，因為他們在開發人員之間的熱門程度會持續成長。 您可能已經熟悉系結一般 [目標 C](../binding-objective-c/walkthrough.md) 程式庫的程式。 現在提供其他檔，說明系結 [Swift 架構](walkthrough.md)的程式，因此 Xamarin 應用程式可以使用相同的方式來取用它們。 本檔的目的是要說明為 Xamarin 建立 Swift 系結的高階方法。

## <a name="high-level-approach"></a>高層級的方法

透過 Xamarin，您可以系結 Xamarin 應用程式可取用的任何協力廠商原生程式庫。 Swift 是新的語言，並為使用此語言建立的程式庫建立系結需要一些額外的步驟和工具。 此方法牽涉到下列四個步驟：

1. 建立原生程式庫
1. 準備 Xamarin 中繼資料，這可讓 Xamarin 工具產生 c # 類別
1. 使用原生程式庫和中繼資料建立 Xamarin 系結程式庫
1. 在 Xamarin 應用程式中使用 Xamarin 系結程式庫

下列各節將概述這些步驟，並提供其他詳細資料。

### <a name="build-the-native-library"></a>建立原生程式庫

第一個步驟是使用已建立的目標 C 標頭來準備原生 Swift 架構。 這個檔案是一種自動產生的標頭，會公開所需的 Swift 類別、方法和欄位，讓它們可透過 Xamarin 系結程式庫，同時存取目標 C 和最終 c #。 這個檔案位於下列路徑的架構內： ** \<FrameworkName> Framework/標頭/ \<FrameworkName> -Swift. h**。 如果公開的介面有所有必要的成員，您可以跳到下一個步驟。 否則，需要進一步的步驟才能公開這些成員。 此方法將取決於您是否可以存取 Swift framework 原始程式碼：

- 如果您可以存取程式碼，您可以使用屬性裝飾所需的 Swift 成員 () `@objc` ，並套用一些額外的規則，讓 Xcode build 工具知道這些成員應該公開給目標 C 世界和標頭。
- 如果您沒有原始程式碼存取權，則需要建立 proxy Swift 架構，以包裝原始 Swift 架構，並使用屬性定義應用程式所需的公用介面 `@objc` 。

### <a name="prepare-the-xamarin-metadata"></a>準備 Xamarin 中繼資料

第二個步驟是準備 API 定義介面，讓系結專案用來產生 c # 類別。 [目標 Sharpie](../../../cross-platform/macios/binding/objective-sharpie/index.md)工具和上述自動產生** \<FrameworkName> 的 Swift**標頭檔可以手動或自動建立這些定義。 產生中繼資料之後，應以手動方式進行驗證和驗證。

### <a name="build-the-xamarinios-binding-library"></a>建立 Xamarin iOS 系結程式庫

第三個步驟是建立特殊的專案-Xamarin 系結程式庫。 它會參考上一個步驟所準備的架構和中繼資料，以及各自的架構所依賴的任何其他相依性。 它也會使用取用的 Xamarin iOS 應用程式來處理所參考原生架構的連結。

### <a name="consume-the-xamarin-binding-library"></a>使用 Xamarin 系結程式庫

第四個和最後一個步驟是參考 Xamarin iOS 應用程式中的系結程式庫。 您可以在以 iOS 12.2 和更新版本為目標的 Xamarin iOS 應用程式中，使用原生程式庫。 針對以較低版本為目標的應用程式，則需要一些額外的步驟：

- 加入執行時間支援的 Swift dylib 相依性。 從 iOS 12.2 和 Swift 5.1 開始，語言成為 ABI (應用程式二進位介面) 穩定且相容。 這就是為什麼以較低 iOS 版本為目標的應用程式必須包含架構所使用的 Swift dylib 來處理相依性。 使用 [SwiftRuntimeSupport NuGet 套件](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) ，將必要的 dylib 相依性自動包含在產生的應用程式封裝中。
- 新增具有已簽署 dylib 來處理的 **SwiftSupport** 資料夾，此資料夾會在上傳過程中由 AppStore 進行驗證。 封裝應使用 Xcode 工具進行簽署並散發至 AppStore connect，否則將會自動拒絕。

## <a name="walkthrough"></a>逐步解說

上述方法會概述建立 Xamarin 的 Swift 系結所需的高階步驟。 當您準備這些系結時，需要考慮許多較低層級的步驟和進一步的詳細資料，包括對原生工具和語言的變更進行調整。 其目的是協助您深入瞭解此概念，以及此程式所牽涉的高階步驟。 如需詳細的逐步指南，請參閱 [Xamarin Swift](walkthrough.md) 系結逐步解說檔。

## <a name="related-links"></a>相關連結

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](../../../cross-platform/macios/binding/objective-sharpie/index.md)
- [Sharpie 中繼資料驗證](../../../cross-platform/macios/binding/objective-sharpie/platform/verify.md)
- [系結目標-C 架構](../binding-objective-c/walkthrough.md)
- [Gigya iOS SDK (Swift framework) ](https://developers.gigya.com/display/GD/Swift+SDK)
- [Swift 5.1 ABI 穩定性](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Xamarin UITest 自動化](/appcenter/test-cloud/uitest/)
- [Xamarin iOS UITest 設定](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [AppCenter Test Cloud](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [範例專案儲存機制](https://github.com/xamcat/xamarin-binding-swift-framework)