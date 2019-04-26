---
title: 建置跨平台應用程式概觀
description: 本文件提供建置跨平台應用程式的高階概觀。 它討論的值C#，設計模式，例如 MVC/MVVM 與原生 Ui。
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 1eb308e0095c29d8ab0d0bdf1f74b807fd2ab97f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275595"
---
# <a name="building-cross-platform-applications-overview"></a>建置跨平台應用程式概觀

本指南將介紹 Xamarin 平台以及如何建構最大化程式碼重複使用，並在所有主要行動平台上提供高品質的原生體驗的跨平台應用程式： iOS、 Android 和 Windows Phone。

但重點在於產能和公用程式 （非遊戲應用程式），是生產力應用程式和遊戲的應用程式，通常適用於這份文件中使用的方法。 請參閱[MonoGame 文件的簡介](~/graphics-games/monogame/introduction/index.md)或查看[Visual Studio Tools for Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity)跨平台遊戲開發指引。

片語 「 寫入-一次，所有位置執行 」 通常用來讚美優點，以及在單一程式碼基底執行未經修改多個平台上。 雖然它的好處是重複使用程式碼，該方法通常會導致具有最低公分母的功能集的應用程式和泛型尋找的使用者介面不符合妥善至任何目標平台。

Xamarin 不只是 「 寫入-所有位置一次，執行 「 平台，因為其中一個其優點是能夠實作針對每個平台的原生使用者介面。 不過，仔細考量的設計就仍然可以共用大部分的非使用者介面的程式碼，並取得這兩個領域的最佳： 一次，撰寫資料儲存體和商務邏輯程式碼，並呈現每個平台的原生 Ui。 本文件討論一般的架構方法，來達成此目標。

建立 Xamarin 跨平台應用程式的關鍵要點的摘要如下：

-   **使用C#**  -撰寫您的應用程式C#。 現有的程式碼撰寫的C#可以移植到 iOS 和 Android 使用 Xamarin，很容易，並很明顯地使用於 Windows 應用程式。
-   **使用 MVC 或 MVVM 設計模式**-開發您的應用程式使用者介面使用的模型/檢視/控制器模式。 建構您的應用程式使用的模型/檢視/控制器方法或 ViewModel/檢視模型/方法沒有清楚的區隔，「 模型 」 和其他人之間。 判斷您的應用程式的哪些部分會使用原生使用者介面項目，每個平台 (iOS、 Android、 Windows、 Mac)，並以此做為指導方針您應用程式分割成兩個元件：「 核心 」 和 「 使用者介面 」。
-   **建置原生 Ui** -每個 OS 特定應用程式提供不同的使用者介面的一層 (實作C#的原生 UI 協助設計工具):

1.  在 iOS 上，使用 UIKit Api 來建立原生外觀利用應用程式，選擇性地以視覺化方式建立 UI 的 Xamarin iOS 設計工具。
1.  在 Android 上，使用 Android.Views 建立原生外觀應用程式，利用 Xamarin 的 UI 設計工具。
1.  在 Windows 上您將使用 XAML 的展示層，在 Visual Studio 或 Blend 的 UI 設計工具中建立。
1.  在 Mac 上，您將使用的展示層，在 Xcode 中建立的分鏡腳本。

Xamarin.Forms 專案支援所有平台，並可讓您建立可以跨平台使用 Xamarin.Forms XAML 共用的使用者介面。 

這主要是以程式碼數量會保留在共用的程式碼和程式碼數量是使用者介面中特定取決的重複使用程式碼數量。 核心程式碼是任何項目不會直接與使用者互動，而是提供服務的應用程式，將會收集並顯示這項資訊的組件。

若要增加的重複使用程式碼數量，您可以採用在所有這些系統中提供常見的服務，例如的跨平台元件：

1.   [SQLite net](https://www.nuget.org/packages/sqlite-net-pcl/)對於本機的 SQL 儲存體，
1.   [Xamarin 外掛程式](https://xamarin.com/plugins)來存取裝置特有的功能，包括相機、 連絡人和地理位置
1.   [NuGet 套件](https://nuget.org)這類，是與 Xamarin 專案中，相容[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)，
1.  使用.NET framework 功能的網路、 web 服務、 IO 和更多功能。


其中某些元件會實作*Tasky*案例研究。

 <a name="Separate_Reusable_Code_into_a_Core_Library" />


## <a name="separate-reusable-code-into-a-core-library"></a>可重複使用的程式碼分為核心程式庫

所遵循的責任區隔的分層應用程式架構，然後再將移至可重複使用的核心程式庫的平台無從驗證的核心功能，您可以充分發揮跨平台，共用為下圖的程式碼說明：

 ![](overview-images/layers2.png "分層應用程式架構，然後再將移至可重複使用的核心程式庫的平台無從驗證的核心功能所遵循的責任區隔的原則，您可以充分發揮跨平台共用的程式碼")

 <a name="Case_Studies" />


## <a name="case-studies"></a>案例研究

沒有 – 之中的一個案例研究*Tasky Pro*。 每個案例研究將討論在真實世界的範例中的這份文件中所述的概念的實作。 程式碼是開放原始碼，可在[github](https://github.com/xamarin/mobile-samples/)。
