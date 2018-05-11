---
title: 建置跨平台應用程式概觀
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e6e2b4f9d5a482a3c8c24dc5b059db2457b0f301
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="building-cross-platform-applications-overview"></a>建置跨平台應用程式概觀

這個指南介紹 Xamarin 平台，以及如何設計來最大化程式碼重複使用，並在所有主要行動平台上提供高品質原生體驗的跨平台應用程式架構： iOS、 Android 和 Windows Phone。

這份文件中使用的方法是通常適用於生產力應用程式和遊戲的應用程式，但是重點在於產能和公用程式 （非遊戲應用程式）。 請參閱[MonoGame 文件的簡介](~/graphics-games/monogame/introduction/index.md)或簽出[Visual Studio Tools for Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity)指導方針開發跨平台遊戲。

片語 「 寫入-一次，執行每個地方 」 通常用來 extol 優點，以及在單一程式碼基底執行未修改多個平台上。 時有重複使用程式碼的優點，方法通常會導致具有最低公分功能集的應用程式並不符合泛型尋找使用者介面運用到任何目標平台。

Xamarin 不只是 「 寫入-一次，執行每個地方 「 平台，因為其優點的其中一個是實作特別針對每個平台的原生使用者介面的能力。 不過，謹慎設計，仍然有可能共用與大部分的非使用者介面程式碼，並取得這兩個領域的最佳結果： 一次，撰寫資料儲存與商務邏輯程式碼，並在每個平台呈現原生 Ui。 本文件討論一般的架構方法，來達成這個目標。

建立 Xamarin 的跨平台應用程式的重點摘要如下：

-   **使用 C#** -C# 撰寫您的應用程式。 以 C# 撰寫的現有程式碼可以移植到 iOS 和 Android 非常輕鬆地使用 Xamarin，顯然是 Windows 應用程式中使用。
-   **利用 MVC 或 MVVM 設計模式**-開發您的應用程式使用者介面使用的模型/檢視/控制器模式。 使用模型/檢視/控制器的方法或 ViewModel/檢視模型/方法的應用程式架構設計人員沒有清楚的區隔，「 模型 」 和其他成員之間。 判斷您的應用程式的哪些部分會使用每個平台 (iOS、 Android、 Windows、 Mac) 的原生使用者介面項目，並使用這個當做指導方針您應用程式分割成兩個元件: 「 核心 」 和 「 使用者介面 」。
-   **建立原生 Ui** -每個作業系統特定應用程式提供不同的使用者介面層 (實作在 C# 的協助的原生 UI 設計工具):

1.  在 iOS 上，使用 UIKit Api 來建立原生應用程式，您可以選擇使用 Xamarin 的 iOS 設計工具以視覺化方式建立您的 UI。
1.  在 Android 上，使用 Android.Views 建立原生應用程式，利用 Xamarin 的 UI 設計工具。
1.  在 Windows 上您將使用 XAML 的展示層，在 Visual Studio 或 Blend 的 UI 設計工具中建立。
1.  在 Mac 上，您將使用展示層，並在 Xcode 中建立分鏡腳本。

Xamarin.Forms 專案都支援所有平台，並可讓您建立可共用的跨平台使用 Xamarin.Forms XAML 使用者介面。 

主要是根據有多少程式碼會保留共用的核心，而且有多少程式碼使用者介面中特定取決的重複使用程式碼數量。 核心程式碼可以是任何項目不會直接與使用者互動，但會提供部分應用程式，將會收集和顯示這項資訊的服務。

若要增加的程式碼重複使用，您可以採用跨平台元件，提供一般服務在所有這些系統，例如：

1.   [SQLite net](https://www.nuget.org/packages/sqlite-net-pcl/)的本機 SQL 存放裝置，
1.   [Xamarin 外掛程式](https://xamarin.com/plugins)來存取裝置的特定功能，包括攝影機、 連絡人和地理位置，
1.   [NuGet 封裝](https://nuget.org)，例如會與 Xamarin 專案相容[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)，
1.  使用.NET framework 功能的網路功能、 web 服務、 IO 等等。


其中某些元件會實作*Tasky*案例研究。

 <a name="Separate_Reusable_Code_into_a_Core_Library" />


## <a name="separate-reusable-code-into-a-core-library"></a>可重複使用程式碼分隔為核心程式庫

依照依分層應用程式架構，然後繼續移至可重複使用核心程式庫的無從驗證平台的核心功能劃分責任的原則，您可以充分發揮程式碼共用跨平台下, 圖為說明：

 ![](overview-images/layers2.png "依照依分層應用程式架構，然後繼續移至可重複使用核心程式庫的無從驗證平台的核心功能劃分責任的原則，您可以最大化跨平台共用的程式碼")

 <a name="Case_Studies" />


## <a name="case-studies"></a>案例研究

沒有一個案例研究伴隨這份文件 – *Tasky Pro*。 每個案例研究討論的概念文件中的真實世界範例中所述實作。 程式碼是開放原始碼並可使用[github](https://github.com/xamarin/mobile-samples/)。
