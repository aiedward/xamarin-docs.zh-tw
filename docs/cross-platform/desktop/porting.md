---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: 傳統型應用程式的移轉指導
description: 如何將現有的 Windows Form 或 WPF 應用程式，來建立 macOS、 iOS、 Android 與 UWP 和 Windows 10 上執行的跨平台應用程式的分離的簡單說明。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 3d3af9c78b7486e7ebfb063a3cb00fabdbd0f5b7
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617536"
---
# <a name="desktop-app-porting-guidance"></a>傳統型應用程式的移轉指導

大部分的應用程式程式碼可以歸類為下列其中一個下列區域：

* （例如使用者介面程式碼。 windows 和按鈕）
* 第 3 個合作對象控制項 （例如。 圖表）
* 商務邏輯 （例如。 驗證規則）
* 本機資料儲存和存取
* Web 服務和遠端資料存取

使用所撰寫的 Windows Form 和 WPF 應用程式C#（或 Visual Basic.NET） 可以跨平台共用商務邏輯、 本機資料存取和 web 服務程式碼有令人驚訝數量。

## <a name="net-portability-analyzer"></a>.NET portability Analyzer

Visual Studio 2015 和 2017年的支援[.NET Portability Analyzer](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer) ([下載適用於 Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) 這可以查看您現有的應用程式，並告訴您多少程式碼可以移植 「 現狀 」 到其他平台. 您可以深入了解從此[Channel 9 影片](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer)。

另外還有命令列工具可以從下載[GitHub 上的可攜性分析器](https://github.com/Microsoft/dotnet-apiport)並用來提供相同的報表。

## <a name="x-of-my-code-is-portable-what-next"></a>"x 是可攜式的我的程式碼的百分比。 接下來呢？ 」

「 分析器 」 希望顯示大型部分程式碼可攜性，但是那里一定會每個應用程式的某些部分所_無法_移到其他平台。

不同區塊的程式碼都可能屬於這些貯體，並詳細說明如下所述的其中一個：

* 重複使用的可攜式程式碼
* 需要變更程式碼
* 就無法攜，而且需要重新撰寫的程式碼

### <a name="re-useable-portable-code"></a>重複使用的可攜式程式碼

在所有平台對提供的 Api 撰寫的.NET 程式碼可以進行跨平台保持不變。 在理想情況下，您將能夠將所有這段程式碼移至可攜式類別庫、 共用程式庫或.NET Standard 程式庫，然後將它測試您現有的應用程式內。

該共用的程式庫可以再新增其他平台 （例如 Android、 iOS、 macOS） 的應用程式專案。

### <a name="code-that-requires-changes"></a>需要變更程式碼

某些.NET Api 可能無法用於所有平台。 如果這些 Api 會存在於您的程式碼，您必須重新撰寫這些區段，以使用跨平台 Api。

包括的範例使用反映 api，可用於.NET 4.6，但並不適用於所有平台。

您已重新撰寫程式碼使用的可攜式 Api 之後，您應該能夠封裝共用的程式庫中的程式碼並測試您現有的應用程式內。

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>就無法攜，而且需要重新撰寫的程式碼

不可能是跨平台的程式碼的範例包括：

- **使用者介面**– Windows Form 或 WPF 螢幕不能在 Android 或 iOS 專案，例如。 您的使用者介面必須重新撰寫，以此[控制項的比較](~/cross-platform/desktop/controls/index.md)做為參考。

- **平台特定的儲存體**-使用平台特定技術，（例如本機的 SQL Server Express 資料庫） 的程式碼。 您必須重新撰寫此使用 （例如 SQLite 資料庫引擎) 的跨平台替代方案。
有些檔案系統作業也可能需要調整，因為 UWP 有稍微不同的 Api，以 Android 和 iOS （例如。 有些檔案系統區分大小寫和其他項目不是）。

- **第 3 個合作對象元件**– 請檢查您的應用程式中的第 3 個合作對象元件是否適用於其他平台。 有些非視覺 NuGet 套件，可能會使用，但其他人 （尤其是視覺控制項，如圖表或媒體的播放器等）

## <a name="tips-for-making-code-portable"></a>使程式碼可攜的秘訣

- **相依性插入**– 提供不同的實作，每個平台，以及

- **分層方法**– 是否 MVVM、 MVC、 MVP、 或某些其他模式，可協助您區隔可攜式的程式碼從平台特定程式碼。

- **傳訊**– 您可以在程式碼中使用訊息傳遞，解除結合的應用程式的不同部分之間的互動。
