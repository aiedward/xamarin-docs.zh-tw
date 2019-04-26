---
title: Xamarin.mac 的目標 Framework
description: 本文章涵蓋的目標 framework （基底類別程式庫） 可用於 Xamarin.Mac 和 Xamarin.Mac 專案中使用它們的含意。
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 15c93126f80917df45a5b80fb84397dc6ef0d5fd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075615"
---
# <a name="target-framework-for-xamarinmac"></a>Xamarin.mac 的目標 Framework

_本文章涵蓋的目標 framework （基底類別程式庫） 可用於 Xamarin.Mac 和 Xamarin.Mac 專案中使用它們的含意。_

![目標 framework 選項 xamarin.mac](target-framework-images/select-target.png "Target xamarin.mac framework 選項")

## <a name="background"></a>背景

每個.NET 程式庫而定提供基底類別庫 (BCL) 功能。 此 BCL 中包含提供所有.NET 語言內都建的一般功能，例如 mscorlib、 System、 System.Net.Http 和 System.Xml 組件。

多年來，已開發這個 BCL，適用於不同使用案例的多個不同的版本。 「 桌面 」 的 BCL 中包含的一組豐富的程式庫，這可能是太其他使用案例中，重量級，雖然 mobile 著重確保 Api 安全的連結，以移除未使用的程式碼，可減少應用程式使用量。

其中一種不同的目標架構，更重要的影響是在指定的程式中的組件的所有*必須*目標相容的 BCL 組件。 如果這不是如此，您可以讓兩個連結的不同版本的組件**System.dll** disagreeing 指定型別的簽章的相關。 共用程式庫來選擇其中一個目標[.NET 標準 2](https://blog.xamarin.com/share-code-net-standard-2-0/)，這是常見的目標 Framework 中或特定的目標 framework 子集。

有三個目標 Framework 選項適用於 Xamarin.Mac，各有不同的優點和權衡取捨：

- **現代**（較舊的文件中稱為行動裝置） – 要進行最佳化的效能和大小哪些支援 Xamarin.iOS、 非常類似的子集。 此目標 Framework 是安全的連結器，因此這些專案可以藉由移除未使用的程式碼可大幅減少其最終使用量。

- **完整**（較舊的文件中稱為 XM 4.5 –） 非常類似的子集來 「 桌面 」 的 BCL，有幾個小型的移除。 由於目標 Framework 是幾乎完全相同，net45 （及更新版本），它可以輕鬆地使用許多的 nuget，不會提供任一 netstandard2，或特定 Xamarin.Mac 組建。 不過，由於 System.Configuration 使用量是與連結不相容。

- **不支援**（稱為系統較舊的文件中）-而是將連結至提供 Xamarin.Mac BCL，使用目前的系統安裝 mono。 這提供最完整的組件，包括一些已知會造成問題 (例如 System.Drawing)。 此選項只存在具有 「 最後的手段 」，並強烈建議使用它之前耗盡其他選項。 如同名稱所暗示，使用量被支援官方的支援管道。

## <a name="setting-the-target-framework"></a>設定目標 framework

若要變更 Xamarin.Mac 專案的目標 Framework 類型，執行下列作業：

1. 在 Visual Studio for Mac 中開啟 Xamarin.Mac 專案。
2. 在 [方案總管] 中，按兩下專案檔以開啟 [專案選項] 對話方塊。
3. 從**一般**索引標籤上，選取的型別**目標 Framework** ，符合您的應用程式需求：

  [![使用 [專案選項] 視窗來選擇目標 framework](target-framework-images/select-target-full.png "選擇目標 framework 中使用 [專案選項] 視窗")](target-framework-images/select-target-full-large.png#lightbox)

4. 按一下 [確定] 按鈕以儲存您的變更。

您應該**Clean** ，然後**重建**切換的目標 Framework 型別之後 Xamarin.Mac 專案。

## <a name="summary"></a>總結

本文簡要探討不同類型的目標架構 （基底類別程式庫） 供 Xamarin.Mac 應用程式，以及應該使用每一種架構的時候。


## <a name="related-links"></a>相關連結

- [iOS 和 Mac 共用的程式碼](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)
- [組件](~/cross-platform/internals/available-assemblies.md)
- [更新現有的 Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)
