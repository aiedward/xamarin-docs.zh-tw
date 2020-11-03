---
title: 建立跨平臺應用程式總覽
description: '本檔提供建立跨平臺應用程式的概要說明。 其中討論 c # 的價值、設計模式（例如 MVC/MVVM）和原生 Ui。'
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: bdf641ca15919958627850e000c117eeedd12de9
ms.sourcegitcommit: 836d54779190b1bef1b43bc0c2016c9b3034bfda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93281244"
---
# <a name="building-cross-platform-applications-overview"></a>建立跨平臺應用程式總覽

本指南介紹 Xamarin 平臺，以及如何設計跨平臺應用程式，以充分利用程式碼重複使用，並在所有主要行動平臺上提供高品質的原生體驗： iOS、Android 及 Windows Phone。

本檔中使用的方法通常適用于生產力應用程式和遊戲應用程式，但焦點在於生產力和公用程式 (非遊戲應用程式) 。 如需跨平臺遊戲開發的指引，請參閱 [Visual Studio Tools for Unity](/visualstudio/cross-platform/visual-studio-tools-for-unity) 。

「只寫入一次，隨處執行」一詞通常用來讚揚在多個平臺上執行未修改的單一程式碼基底的優點。 雖然它有重複使用程式碼的優點，但這種方法通常會導致應用程式具有最低標準的功能集，以及不適合任何目標平臺的一般使用者介面。

Xamarin 並不只是「一次就能在任何地方執行」平臺，因為它的其中一個優點就是能夠專門為每個平臺執行原生使用者介面。 不過，若要進行精心設計，您仍然可以共用大部分的非使用者介面程式碼，並充分運用這兩個領域：撰寫您的資料儲存和商務邏輯程式碼一次，並在每個平臺上呈現原生 Ui。 本檔討論達成此目標的一般架構方法。

以下是建立 Xamarin 跨平臺應用程式之重點的摘要：

- **使用 c** #-以 c # 撰寫您的應用程式。 以 c # 撰寫的現有程式碼可以非常輕鬆地使用 Xamarin 移植到 iOS 和 Android，並顯然在 Windows 應用程式中使用。
- **利用 MVC 或 MVVM 設計模式** -使用模型/視圖/控制器模式來開發應用程式的消費者介面。 使用模型/視圖/控制器方法或模型/視圖/ViewModel 方法來設計您的應用程式，在「模型」與其余部分之間有清楚的分隔。 判斷您的應用程式哪些部分將使用每個平臺的原生使用者介面專案 (iOS、Android、Windows、Mac) ，並使用此作為指導方針，將應用程式分割成兩個元件：「核心」和「使用者介面」。
- **組建原生** ui：每個作業系統專用的應用程式都會提供不同的使用者介面層 (在 c # 中執行，並提供原生 UI 設計工具) 的協助：

1. 在 iOS 上，使用 UIKit Api 建立原生外觀的應用程式，並選擇性地使用 Xamarin 的 iOS designer 以視覺化方式建立您的 UI。
1. 在 Android 上，使用 [Android] 來建立原生查詢的應用程式，並利用 Xamarin 的 UI 設計工具。
1. 在 Windows 上，您將會使用 XAML 做為展示層，並在 Visual Studio 或 Blend 的 UI 設計工具中建立。
1. 在 Mac 上，您將使用在 Xcode 中建立之展示層的分鏡腳本。

所有平臺都支援 Xamarin 專案，並可讓您建立可使用 Xamarin XAML 跨平臺共用的使用者介面。

程式碼重複使用的數量主要取決於共用核心中保留的程式碼數量，以及使用者介面特定的程式碼數量。 核心程式代碼是指不會直接與使用者互動的任何內容，而是會提供應用程式元件的服務，以收集和顯示這項資訊。

若要增加重複使用的程式碼量，您可以採用跨平臺元件，在所有這些系統中提供泛型服務，例如：

1. 本機 SQL 儲存體的[SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/)
1. [Xamarin 外掛程式](https://xamarin.com/plugins) ，可存取裝置特定功能，包括相機、連絡人和地理位置
1. 與 Xamarin 專案相容的[NuGet 套件](https://nuget.org)，例如[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)、
1. 使用 .NET framework 功能進行網路、web 服務、IO 等等。

其中有些元件是在 *Tasky* 案例研究中執行的。

 <a name="Separate_Reusable_Code_into_a_Core_Library"></a>

## <a name="separate-reusable-code-into-a-core-library"></a>將可重複使用的程式碼分隔為核心程式庫

藉由將您的應用程式架構分層，然後將平臺中立的核心功能移至可重複使用的核心程式庫，以遵循責任分離的原則，您可以將跨平臺的程式碼共用優化，如下圖所示：

 ![藉由將您的應用程式架構分層，然後將平臺中立的核心功能移至可重複使用的核心程式庫，來遵循責任分離的原則，您可以將跨平臺的程式碼共用優化](overview-images/layers2.png)

 <a name="Case_Studies"></a>

## <a name="case-studies"></a>個案研究

本檔中有一份個案研究– *Tasky Pro* 。 每個案例研究都會討論在真實世界範例中，這份檔中所述概念的實作為。 程式碼是開放原始碼，並可在 [github](https://github.com/xamarin/mobile-samples/)上取得。
