---
title: 建立跨平臺應用程式總覽
description: '本檔提供建立跨平臺應用程式的高階總覽。 它討論 c # 的價值、設計模式（例如 MVC/MVVM）和原生 Ui。'
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: de8c66e6a89f035b8370a2139361d3e942aa9c09
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571203"
---
# <a name="building-cross-platform-applications-overview"></a>建立跨平臺應用程式總覽

本指南介紹 Xamarin 平臺，以及如何架構跨平臺應用程式，以最大化程式碼重複使用，並在所有主要行動平臺上提供高品質的原生體驗： iOS、Android 和 Windows Phone。

本檔中使用的方法通常適用于生產力應用程式和遊戲應用程式，但重點在於生產力和公用程式（非遊戲應用程式）。 如需跨平臺遊戲開發的指引，請參閱[MonoGame 檔簡介](~/graphics-games/monogame/introduction/index.md)或查看[Visual Studio Tools for Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity) 。

「寫入一次，隨處執行」一詞通常用來讚揚在多個平臺上未修改的單一程式碼基底的優點。 雖然它具有程式碼重複使用的優點，但這種方法通常會導致具有最低標準的功能集和一般外觀的使用者介面，而不適合任何目標平臺的應用程式。

Xamarin 不只是「寫入一次，隨處執行」平臺，因為其中一個優點是能夠針對每個平臺執行原生使用者介面。 不過，有了審慎的設計，仍然可以共用大部分的非使用者介面程式碼，並充分利用這兩種方式：撰寫您的資料儲存體和商務邏輯程式碼一次，並在每個平臺上呈現原生 Ui。 本檔討論達成此目標的一般架構方法。

以下是建立 Xamarin 跨平臺應用程式的重點摘要：

- **使用 c #** -以 c # 撰寫您的應用程式。 以 c # 撰寫的現有程式碼可以非常輕鬆地使用 Xamarin 移植到 iOS 和 Android，而且很明顯在 Windows 應用程式中使用。
- **利用 MVC 或 MVVM 設計模式**-使用模型/視圖/控制器模式來開發應用程式的使用者介面。 使用模型/視圖/控制器方法，或是模型/視圖/ViewModel 方法，讓「模型」和其餘部分清楚區隔，來架構您的應用程式。 判斷應用程式的哪些部分會使用每個平臺的原生使用者介面專案（iOS、Android、Windows、Mac），並使用此做為將應用程式分割成兩個元件的指導方針：「核心」和「使用者介面」。
- **建立原生 ui** -每個作業系統特定的應用程式都會提供不同的使用者介面層（在 c # 中以原生 UI 設計工具的協助來執行）：

1. 在 iOS 上，使用 UIKit Api 來建立原生外觀的應用程式，並選擇性地利用 Xamarin 的 iOS 設計工具，以視覺化方式建立 UI。
1. 在 Android 上，使用 [Android. Views] 建立原生外觀的應用程式，利用 Xamarin 的 UI 設計工具。
1. 在 Windows 上，您將使用 XAML 展示層，在 Visual Studio 或 Blend 的 UI 設計工具中建立。
1. 在 Mac 上，您將使用展示層的分鏡腳本，在 Xcode 中建立。

所有平臺都支援 Xamarin. form 專案，並可讓您建立使用者介面，以使用 Xamarin 形式跨平臺共用。 

重複使用的程式碼量主要取決於共用核心中保留的程式碼數量，以及使用者介面特有的程式碼數量。 核心程式代碼不會直接與使用者互動，而是會提供應用程式元件的服務，以收集和顯示這項資訊。

若要增加重複使用的程式碼數量，您可以採用跨平臺元件來提供所有這些系統的泛型服務，例如：

1. 適用于本機 SQL 儲存體的[SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/) ，
1. 用來存取裝置特定功能（包括相機、連絡人和地理位置）的[Xamarin 外掛程式](https://xamarin.com/plugins)
1. 與 Xamarin 專案相容的[NuGet 套件](https://nuget.org)，例如[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)。
1. 使用 .NET framework 功能進行網路、web 服務、IO 等等。

其中一些元件會在*Tasky*案例研究中執行。

 <a name="Separate_Reusable_Code_into_a_Core_Library"></a>

## <a name="separate-reusable-code-into-a-core-library"></a>將可重複使用的程式碼分隔成核心程式庫

藉由將您的應用程式架構分層，然後將平臺中立的核心功能移至可重複使用的核心程式庫，將責任分離的原則納入考慮，您可以將跨平臺的程式碼共用最大化，如下圖所示：

 ![](overview-images/layers2.png "By following the principle of separation of responsibility by layering your application architecture and then moving core functionality that is platform agnostic into a reusable core library, you can maximize code sharing across platforms")

 <a name="Case_Studies"></a>

## <a name="case-studies"></a>個案研究

本檔隨附一份案例研究– *Tasky Pro*。 每個案例研究都會討論在實際範例中，此檔中所述的概念的執行。 此程式碼為開放原始碼，並可在[github](https://github.com/xamarin/mobile-samples/)上取得。
