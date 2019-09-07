---
title: Xamarin.Mac 效能
description: 本文提供 Xamarin.Mac 應用程式的一些效能考量。 並討論現代化目標 Framework、連結器、AOT、委派、Cocoa API，以重複使用檢視和非同步程式碼。
ms.prod: xamarin
ms.assetid: 54B07DED-FDF2-49B2-A5FB-3A9357E65922
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 12a2152424fac4024d8b83adb0c80c2499ec8b1d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770109"
---
# <a name="xamarinmac-performance"></a>Xamarin.Mac 效能

## <a name="overview"></a>總覽

Xamarin.Mac 應用程式和 Xamarin.iOS 類似，而且適用許多相同的效能建議：

- [Xamarin.iOS 效能](~/ios/deploy-test/performance.md)
- [跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)

但還有一些實用的 macOS 特有建議。

## <a name="prefer-modern-target-framework"></a>建議使用新式目標架構

Xamarin.Mac 應用程式可以使用數個效能特性與功能各異的[目標 Framework](~/mac/platform/target-framework.md)。

可能的話，建議使用「現代」並搭配使用相依程式庫以增加支援。 只有現代目標 Framework 允許可大幅減少組件大小的連結。 這在啟用 AOT 時特別重要，因為「完整」組件的 AOT 編譯會產生大型的最終套件組合。

## <a name="enable-the-linker"></a>啟用連結器

在負載與 Just In Time (JIT) 兩者的啟動時間稍微會隨著最終二進位檔的大小呈線性變化。 最簡單的改善方法是使用[連結器](~/mac/deploy-test/linker.md)來移除無作用程式碼。

雖然這項建議主要適用於現代目標 Framework 使用者，使用[平台連結](~/mac/deploy-test/linker.md)也可以提升一定限度的效能。

## <a name="enable-aot-when-appropriate"></a>適時啟用 AOT

影響啟動效能的另一個層面是將組件以 JIT 方式編譯為機器碼。 預先編譯 (AOT) 可大幅縮減啟動時間，但有幾項缺點，請參閱 [AOT 文件](~/mac/internals/aot.md)的說明。

## <a name="ensure-performant-delegates"></a>確保委派效能

許多 Xamarin.Mac 應用程式都是以像是 `NSCollectionView`、`NSOutlineView` 或 `NSTableView` 的 Cocoa 檢視為主。 這些檢視經常是透過您提供給 Cocoa 的 `Delegate` 與 `DataSource` 類別回答要顯示什麼內容的問題來提供。

其中許多進入點都會經常叫用，在捲動時有時候會每秒叫用多次。

請確定這些函式傳回的值都可以輕鬆計算或使用已經快取的資訊，以免阻礙使用者介面。

## <a name="use-cocoa-provided-apis-for-reusing-views"></a>使用 Cocoa 提供的 API 以重複使用檢視

包含許多子檢視或子資料格 (例如 `NSCollectionView`、`NSOutlineView` 或 `NSTableView`) 的許多 Cocoa 檢視都提供可用來建立和重複使用檢視的 API。 這些都會建立共用項目集區，並防止在檢視間快速捲動時發生效能問題。

## <a name="use-async-and-do-not-block-the-ui"></a>使用非同步而不阻斷 UI

傳統型應用程式經常會處理大量資料，而且非常容易阻礙正在等待同步作業的 UI 執行緒。

可能的話，請使用[非同步](~/cross-platform/platform/async.md)和執行緒以免阻礙 UI。

對於長時間執行的作業，請考慮使用 [NSProgressIndicator](https://docs.microsoft.com/samples/xamarin/mac-samples/progressbarexample) 或 Apple 的 [HIG](https://developer.apple.com/macos/human-interface-guidelines/indicators/progress-indicators/) \(英文\) 中記載的其他選項來通知使用者。

## <a name="related-links"></a>相關連結

- [跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Xamarin.iOS 效能](~/ios/deploy-test/performance.md)
