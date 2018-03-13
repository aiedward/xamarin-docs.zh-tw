---
title: "目標 Framework"
description: "本文涵蓋的目標架構 （基底類別程式庫） 可供 Xamarin.Mac 和 Xamarin.Mac 專案中使用它們的含意。"
ms.topic: article
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: f657fc3dd87d5c39d442a863e4acc00ac320b00d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="target-framework"></a>目標 Framework

_本文涵蓋的目標架構 （基底類別程式庫） 可供 Xamarin.Mac 和 Xamarin.Mac 專案中使用它們的含意。_

![目標 framework 選項 Xamarin.Mac](target-framework-images/select-target.png "Target Xamarin.Mac 的架構選項")

## <a name="background"></a>背景

每個.NET 程式或文件庫取決於提供基底類別程式庫 (BCL) 功能。 此 BCL 包含提供所有的.NET 語言內建的一般功能，例如 mscorlib、 System、 System.Net.Http 和 System.Xml 組件。

多年來，所開發這個 BCL，針對不同的使用案例最佳化的多個不同的版本。 「 桌面 」 BCL 包含豐富的程式庫時行動著重於確保應用程式開發介面而言安全的連結，可能會太重量級其他使用案例，它會移除未使用的程式碼，若要減少應用程式使用量。

其中一個這些不同的目標架構，更重要的影響是，所有指定的程式中的組件*必須*相容 BCL 組件為目標。 如果這不是大小寫，您可能會針對不同版本所連結的兩個組件**System.dll** disagreeing 給定類型的簽章的相關。 共用媒體櫃可以是目標[.NET 標準 2](https://blog.xamarin.com/share-code-net-standard-2-0/)，這是常見的目標架構或特定目標 framework 子集。

有三個的目標 Framework 選項供 Xamarin.Mac 各有不同的優點和取捨：

- **現代**（較舊的文件中稱為行動裝置） – 高度調整，讓效能和大小哪些乘冪 Xamarin.iOS，非常類似的子集。 此目標 Framework 是安全的連結器，讓這些專案可以藉由移除未使用的程式碼可大幅減少其最終使用量。

- **完整**（較舊的文件中稱為 XML 4.5） – 要 「 桌面 」 BCL，有幾個小型移除非常類似的子集。 目標 Framework 幾乎完全相同 net45 至 （和更新版本），它可以輕鬆地取用許多 nugets 是沒有任一 netstandard2 或特定 Xamarin.Mac 建置。 不過，由於 System.Configuration 使用量它是與連結不相容。

- **不支援**（稱為系統較舊的文件中） – 改為連結至 BCL Xamarin.Mac 所提供，會使用目前的系統安裝單聲道。 這提供最充分的組件，包括一些已知問題 (例如 System.Drawing)。 此選項只存在有 「 最後手段"，而且強烈建議使用它之前耗盡其他選項。 正如其名，正式支援管道不支援使用方式。

## <a name="setting-the-target-framework"></a>設定目標 framework

若要變更 Xamarin.Mac 專案的目標 Framework 類型，執行下列作業：

1. 在 Visual Studio for Mac 中開啟 Xamarin.Mac 專案。
2. 在 [方案總管] 中，按兩下專案檔以開啟 [專案選項] 對話方塊。
3. 從**一般**索引標籤上，選取的類型**目標 Framework** ，符合您的應用程式需求：

  [![使用專案選項 視窗中選擇目標 framework](target-framework-images/select-target-full.png "使用選擇的目標 framework 專案選項視窗")](target-framework-images/select-target-full-large.png#lightbox)

4. 按一下 [確定] 按鈕以儲存您的變更。

您應該**清除**然後**重建**Xamarin.Mac 專案之後切換的目標 Framework 類型。

## <a name="summary"></a>總結

本文簡要探討不同類型的目標架構 （基底類別程式庫） 供 Xamarin.Mac 應用程式，以及應該使用每種類型的架構時使用。


## <a name="related-links"></a>相關連結

- [iOS 和 Mac 的程式碼共用](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)
- [組件](~/cross-platform/internals/available-assemblies.md)
- [更新現有的 Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)
