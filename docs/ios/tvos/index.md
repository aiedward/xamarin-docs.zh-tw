---
title: Xamarin 中的 tvOS 簡介
description: 本檔會連結至示範如何使用 Xamarin 組建 tvOS 應用程式的各種指南和範例。 這些指南會討論各種功能，例如使用者介面開發、資料儲存、圖示等。
ms.prod: xamarin
ms.assetid: 14345503-1742-41F5-B2EF-EE31AB7C3516
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 2fc6c7fcd1c255e1cfd87e2e7cf91c92f17d8465
ms.sourcegitcommit: 342cfbd2502ad92cadada4fa9aec669b99d7830a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96604439"
---
# <a name="introduction-to-tvos-in-xamarin"></a>Xamarin 中的 tvOS 簡介

## <a name="introducing-tvos"></a>TvOS 簡介

Apple 已發行第5代的 Apple TV 硬體，也就是 Apple TV 4K，根據 iOS 11 執行最新版的 tvOS 作業系統。

Apple TV 平臺開放給開發人員使用，讓他們可以建立豐富、沉浸式的應用程式，並透過 Apple TV 的內建 App Store 來發行這些應用程式。

如需 tvOS 的詳細資訊，請參閱 [消費者入門](~/ios/tvos/get-started/index.md) 檔。

> [!VIDEO https://youtube.com/embed/Q04oIYymfGM]

**tvOS 與 Xamarin 影片**

## <a name="documentation"></a>文件

下列檔將協助您開始使用 Xamarin 來建立 tvOS 應用程式：

- [TvOS 11 簡介](~/ios/tvos/platform/introduction-to-tvos11.md) -本文說明 tvOS 11 中適用于 Xamarin tvOS 開發人員的新功能。
- [TvOS 10 簡介](~/ios/tvos/platform/introduction-to-tvos10/index.md) -本文將介紹適用于 Xamarin tvOS 開發人員的 tvOS 10 中所有新的和修改過的 api 和功能。
- [TvOS 9 簡介](~/ios/tvos/platform/tvos9.md) -本文將介紹適用于 Xamarin tvOS 開發人員的 tvOS 9 中所有新的和修改過的 api 和功能。
- [TvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md) –本指南將逐步解說如何建立您的第一個 tvOS 應用程式，並在此程式仲介紹開發工具鏈，包括 Visual Studio for Mac、Xcode 和 Interface Builder。 它也引進了可將 UI 控制項公開到程式碼的輸出和動作，最後還說明如何建立、執行及測試 tvOS 應用程式。
- 使用[圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)-本文涵蓋在 tvOS 應用程式內設計和使用圖示和影像。
- 使用[導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)：本文涵蓋焦點的概念，以及如何在 tvOS 應用程式內呈現和處理導覽。
- [資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md) -本文涵蓋在 tvOS 應用程式中使用資源和持續性資料儲存。
- [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md) -本文涵蓋在 tvOS 應用程式中支援新的 Siri 遠端和藍牙遊戲控制器。
- [消費者介面](~/ios/tvos/user-interface/index.md) –一般使用者體驗 (UX) 涵蓋範圍（包括消費者介面 (UI) 控制項）在使用 tvOS 時，請使用 Xcode 的 INTERFACE BUILDER 和 UX 設計原則。
- [部署、測試和度量](~/ios/tvos/deploy-test/index.md) –本節涵蓋用來測試應用程式的主題，以及散發該應用程式的方式。 這裡的主題包括用於偵錯工具的工具、部署至測試人員，以及如何將應用程式發佈至 Apple TV App Store。
- [支援的元件](~/ios/tvos/internals/assemblies.md) -這是 xamarin 針對您的 tvOS 應用程式所支援的元件清單。
- [支援和不支援](~/ios/tvos/internals/frameworks.md) 的架構-這是 xamarin 針對您的 tvOS 應用程式所支援的架構清單。

## <a name="sample-projects"></a>範例專案

以 Xamarin 建立的 tvOS 應用程式範例：

- [Hello，tvOS](/samples/xamarin/ios-samples/tvos-hello-tvos) –此範例會在 tvOS 中執行簡單的 "Hello World" 應用程式，並提供使用 tvOS 的基本概念。
- [tvAlerts](/samples/xamarin/ios-samples/tvos-tvalerts) –此範例說明如何在 tvOS 應用程式中使用警示。
- [tvButtons](/samples/xamarin/ios-samples/tvos-tvbuttons) –此範例說明如何使用按鈕，這是 tvOS 應用程式。
- [tvRemote](/samples/xamarin/ios-samples/tvos-tvremote) –此範例提供數種方式，讓您的 Xamarin. tvOS 應用程式可與 Siri 遠端互動以流覽您的消費者介面。
- [tvCollection](/samples/xamarin/ios-samples/tvos-tvcollection) –此範例示範如何在 tvOS 應用程式中使用集合查看控制器。
- [tvNavBars](/samples/xamarin/ios-samples/tvos-tvnavbars) –此範例說明如何在 tvOS 應用程式中使用導覽列。
- [tvPages](/samples/xamarin/ios-samples/tvos-tvpages) –此範例說明如何在 tvOS 應用程式中使用頁面控制項。
- [tvProgress](/samples/xamarin/ios-samples/tvos-tvprogress) –此範例說明如何在 tvOS 應用程式中使用進度指標。
- [tvSplit](/samples/xamarin/ios-samples/tvos-tvsplit) –此範例示範如何在 tvOS 應用程式中使用分割視圖控制器。
- [tvStackView](/samples/xamarin/ios-samples/tvos-tvstackview) -此範例說明如何在 tvOS 應用程式中使用堆疊流覽。
- [UICatalog](/samples/xamarin/ios-samples/tvos-uicatalog) –示範如何在 tvOS 上的 UIKit 架構中使用許多視圖和控制項。 如果您要尋找系統提供的特定控制項或視圖，請參閱此範例。

此外，Apple 提供下列可轉碼至 c # 的範例應用程式，以使用 Xamarin 的 tvOS apps 支援：

- [DemoBots：使用 SpriteKit 和 GameplayKit 建立跨平臺遊戲](https://developer.apple.com/library/prerelease/tvos/samplecode/DemoBots/)

## <a name="known-issues-and-troubleshooting"></a>已知問題與疑難排解

如果您在使用 Xamarin 建立 tvOS 時遇到任何問題，請查看 [版本](/xamarin/ios/release-notes/)資訊、 [Xamarin、iOS 論壇](https://forums.xamarin.com/categories/ios)、 [Xamarin Bugzilla 追蹤](https://bugzilla.xamarin.com/query.cgi?product=iOS)程式和 [GitHub](https://github.com/xamarin/xamarin-macios/issues) 是否有任何問題。

[在 GitHub 上](https://github.com/xamarin/xamarin-macios/issues)報告新問題和建議。

## <a name="xamarintvos-on-qa"></a>TvOS on Q&A 

詢問有關使用 Xamarin 在 [Q&A](/answers/topics/dotnet-tvos.html)中建立和部署 tvOS 應用程式的問題。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [使用 Xamarin 建立適用于 tvOS 的應用程式 (影片) ](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
