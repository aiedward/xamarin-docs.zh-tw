---
title: Xamarin 中的 tvOS 簡介
description: 本檔會連結各種指南和範例，示範如何使用 Xamarin 建立 tvOS 應用程式。 這些指南會討論各種功能，例如使用者介面開發、資料儲存、圖示等。
ms.prod: xamarin
ms.assetid: 14345503-1742-41F5-B2EF-EE31AB7C3516
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: e61aaa6a2071797f905bf7398576f2045a46faea
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030690"
---
# <a name="introduction-to-tvos-in-xamarin"></a>Xamarin 中的 tvOS 簡介

## <a name="introducing-tvos"></a>TvOS 簡介

Apple 已發行第5代的 Apple TV 硬體，也就是 Apple TV 4K，它會根據 iOS 11 執行最新版本的 tvOS 作業系統。

Apple TV 平臺開放給開發人員使用，讓他們能夠建立豐富的沉浸式應用程式，並透過 Apple 電視的內建 App Store 發行。

如需 tvOS 的詳細資訊，請參閱[消費者入門](~/ios/tvos/get-started/index.md)檔。

> [!VIDEO https://youtube.com/embed/Q04oIYymfGM]

**使用 Xamarin 的 tvOS 影片**

## <a name="documentation"></a>文件

下列檔將協助您開始使用 Xamarin 來建立 tvOS 應用程式：

- [TvOS 11 簡介](~/ios/tvos/platform/introduction-to-tvos11.md)-這篇文章說明 tvOS 11 for Xamarin. tvOS 開發人員提供的新功能。
- [TvOS 10 簡介](~/ios/tvos/platform/introduction-to-tvos10/index.md)-這篇文章介紹 tvOS 10 中適用于 Xamarin. tvOS 開發人員的所有新的和已修改的 api 和功能。
- [TvOS 9 簡介](~/ios/tvos/platform/tvos9.md)-這篇文章介紹 tvOS 9 中適用于 Xamarin. tvOS 開發人員的所有新的和已修改的 api 和功能。 
- [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)–本指南會逐步解說如何建立您的第一個 tvOS 應用程式，而在此過程中會介紹開發工具鏈，包括 Visual Studio for Mac、Xcode 和 Interface Builder。 它也引進了會將 UI 控制項公開給程式碼的「輸出」和「動作」，最後，它會說明如何建立、執行和測試 tvOS 應用程式。
- 使用[圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)-本文涵蓋在 tvOS 應用程式內設計和使用圖示和影像。
- 使用[導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)–本文涵蓋焦點的概念，以及如何用它來呈現和處理 tvOS 應用程式內的導覽。
- [資源和資料儲存](~/ios/tvos/app-fundamentals/resources-data-storage.md)–本文涵蓋在 tvOS 應用程式中使用資源和持續性資料儲存。
- [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)–本文涵蓋支援 tvOS 應用程式中新的 Siri 遠端和藍牙遊戲控制器。
- [使用者介面](~/ios/tvos/user-interface/index.md)–一般使用者體驗（UX）涵蓋範圍，包括使用者介面（UI）控制項，在使用 tvOS 時，請使用 Xcode 的 INTERFACE BUILDER 和 UX 設計原則。
- [部署、測試和計量](~/ios/tvos/deploy-test/index.md)–本節涵蓋用來測試應用程式以及如何散發的主題。 這裡的主題包含一些工具，例如用來進行偵錯工具、部署至測試人員，以及如何將應用程式發行至 Apple TV App Store 等。
- [支援的元件](~/ios/tvos/internals/assemblies.md)–這是 xamarin 針對您的 tvOS 應用程式所支援的元件清單。
- [支援和不支援](~/ios/tvos/internals/frameworks.md)的架構-這是 xamarin 針對您的 tvOS 應用程式所支援的架構清單。

## <a name="sample-projects"></a>範例專案

以 Xamarin 建立的範例 tvOS 應用程式：

- [Hello，tvOS](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-hello-tvos) –此範例會在 tvOS 中執行簡單的「Hello World」應用程式，並提供使用 tvOS 的基本概念。
- [tvAlerts](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvalerts) –此範例說明如何在 tvOS 應用程式中使用警示。
- [tvButtons](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvbuttons) –此範例說明如何使用按鈕，這是 tvOS 應用程式。
- [tvRemote](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvremote) –此範例會提供數種方式，讓您的 tvOS 應用程式可以與 Siri 遠端互動以流覽您的使用者介面。
- [tvCollection](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvcollection) –此範例示範如何在 tvOS 應用程式中使用集合視圖控制器。
- [tvNavBars](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvnavbars) –此範例示範如何在 tvOS 應用程式中使用巡覽列。
- [tvPages](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvpages) –此範例示範如何使用 tvOS 應用程式中的頁面控制項。
- [tvProgress](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvprogress) –此範例示範如何在 tvOS 應用程式中使用進度指示器。
- [tvSplit](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvsplit) –此範例示範如何在 tvOS 應用程式中使用分割視圖控制器。
- [tvStackView](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvstackview) -此範例示範如何在 tvOS 應用程式中使用堆疊 Views。
- [UICatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-uicatalog) –示範如何在 tvOS 上的 UIKit 架構中使用許多視圖和控制項。 如果您要尋找系統所提供的特定控制項或視圖，請參閱此範例。

此外，Apple 提供下列範例應用程式，可轉碼至C#以搭配 Xamarin 對 tvOS 應用程式的支援：

- [DemoBots：使用 SpriteKit 和 GameplayKit 建立跨平臺遊戲](https://developer.apple.com/library/prerelease/tvos/samplecode/DemoBots/)

## <a name="known-issues-and-troubleshooting"></a>已知問題與疑難排解

如果您在使用 Xamarin 建立 tvOS 時遇到任何問題，請查看[版本](https://docs.microsoft.com/xamarin/ios/release-notes/)資訊、 [xamarin iOS 論壇](https://forums.xamarin.com/categories/ios)、 [Xamarin Bugzilla 追蹤](https://bugzilla.xamarin.com/query.cgi?product=iOS)器和[GitHub](https://github.com/xamarin/xamarin-macios/issues)以瞭解現有的問題。

[在 GitHub 上](https://github.com/xamarin/xamarin-macios/issues)報告新的問題和建議。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [使用 Xamarin 建立適用于 tvOS 的應用程式（影片）](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
