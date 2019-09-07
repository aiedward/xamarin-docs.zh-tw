---
title: Xamarin 的目標 Framework
description: 本文涵蓋 Xamarin. Mac 適用的目標 framework （基類庫），以及在您的 Xamarin 專案中使用它們的含意。
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: a612c2c23ceff13ea1d602465573514547628e55
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769799"
---
# <a name="target-framework-for-xamarinmac"></a>Xamarin 的目標 Framework

_本文涵蓋 Xamarin. Mac 適用的目標 framework （基類庫），以及在您的 Xamarin 專案中使用它們的含意。_

![Xamarin 的目標 framework 選項](target-framework-images/select-target.png "Xamarin 的目標 framework 選項")

## <a name="background"></a>背景

每個 .NET 程式或程式庫都取決於基類程式庫（BCL）所提供的功能。 此 BCL 包括 mscorlib、System、System .Net 和 system.string 等元件，提供內建于所有 .NET 語言的通用功能。

多年來，已開發出多個不同版本的 BCL，並針對不同的使用案例進行優化。 「桌面」 BCL 包含一組更豐富的程式庫，對其他使用案例而言可能是太重，而 mobile 則著重于確保 Api 可安全地進行連結，這會移除未使用的程式碼以減少應用程式的使用量。

這些不同的目標架構有一個比較重要的影響，就是指定程式中的所有元件都*必須*以相容的 BCL 元件為目標。 如果不是這種情況，您可以將兩個元件連結至不同版本的**disagreeing，** 以瞭解特定類型的簽章。 共用程式庫可以目標為[.NET Standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/)，也就是目標 framework 的一般子集，或特定的目標架構。

有三個適用于 Xamarin 的目標 Framework 選項，各有不同的優點和取捨：

- **現代化**（在舊版檔中稱為 Mobile）–與 Xamarin 的功能非常相似的子集，針對效能和規模進行高度調整。 此目標架構是連結器安全的，因此，這些專案可以藉由移除未使用的程式碼，大幅減少其最終使用量。

- **完整**（在舊版檔中稱為 XM 4.5）–與「桌面」 BCL 非常相似的子集，只有幾個小部分的移除。 當目標 Framework 與 net45 （及更新版本）幾乎完全相同時，它可以輕鬆地取用許多未提供 netstandard2 或特定 Xamarin 組建的 nuget。 不過，由於系統的設定使用方式，與連結不相容。

- **不支援**（在舊版檔中稱為系統）–您可以使用目前安裝的 mono，而不是連結至 Xamarin 所提供的 BCL。 這會提供一組最完整的元件，包括某些已知的問題（例如，System.object）。 此選項只會有「最後的手段」，強烈建議您在使用其他選項之前，先加以耗盡。 顧名思義，官方支援通道不支援使用方式。

## <a name="setting-the-target-framework"></a>設定目標 framework

若要變更為 Xamarin 專案的目標 Framework 類型，請執行下列動作：

1. 在 Visual Studio for Mac 中開啟 Xamarin.Mac 專案。
2. 在 [方案總管] 中，按兩下專案檔以開啟 [專案選項] 對話方塊。
3. 從 [**一般**] 索引標籤中，選取符合您應用程式需求的**目標架構**類型：

    [![使用 [專案選項] 視窗選擇目標 framework](target-framework-images/select-target-full.png "使用 [專案選項] 視窗選擇目標 framework")](target-framework-images/select-target-full-large.png#lightbox)

4. 按一下 [確定] 按鈕以儲存您的變更。

在切換目標 Framework 類型之後，您應該**清除**並**重建**您的 Xamarin. Mac 專案。

## <a name="summary"></a>總結

本文簡要說明了 Xamarin. Mac 應用程式可使用的不同類型的目標 framework （基類庫），以及每一種類型的架構。

## <a name="related-links"></a>相關連結

- [iOS 和 Mac 程式碼共用](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)
- [組件](~/cross-platform/internals/available-assemblies.md)
- [更新現有的 Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)
