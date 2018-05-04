---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: 桌面應用程式移植指南
description: 如何將現有的 Windows Form 或 WPF 應用程式中，若要建立跨平台應用程式，到 macOS、 iOS、 Android 與 UWP/Windows 10 上執行分離的簡單說明。
ms.technology: xamarin-crossplatform
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 35ffb9c92b5d3faf48f3e76a6702c2518ca80538
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="desktop-app-porting-guidance"></a>桌面應用程式移植指南

大部分的應用程式程式碼可分類成下列其中一個下列區域：

* （例如使用者介面程式碼 windows 和按鈕）
* 第 3 個合作對象控制項 （例如。 圖表）
* 商務邏輯 （例如。 驗證規則）
* 本機資料儲存和存取
* Web 服務和遠端資料存取

Windows Form 和 WPF 應用程式使用 C# （或 Visual Basic.NET） 撰寫令人意外數量的商務邏輯，本機資料存取和 web 服務程式碼可以跨平台共用。

## <a name="net-portability-analyzer"></a>.NET portability Analyzer

Visual Studio 2015 和 2017年支援[.NET Portability Analyzer](https://docs.microsoft.com/en-us/dotnet/articles/standard/portability-analyzer) ([下載適用於 Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) 這可以查看您現有的應用程式，並告訴您有多少程式碼可以移植 「 現狀 」 到其他平台. 您可以深入了解從這個[Channel 9 影片](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer)。

此外，還有命令列工具可以從下載[GitHub 上的可攜性分析器](https://github.com/Microsoft/dotnet-apiport)且用來提供相同的報表。

## <a name="x-of-my-code-is-portable-what-next"></a>"x %我的程式碼有可攜性。 接下來呢？ 」

分析器希望顯示大型部分程式碼的可攜性，但那里一定會每個應用程式的某些部分，_無法_移到其他平台。

不同的程式碼區塊可能都會歸類到其中的這些貯體下, 面將更多詳細的說明：

* 重複使用的可攜式程式碼
* 需要變更的程式碼
* 不是可攜式且需要重新撰寫程式碼

### <a name="re-useable-portable-code"></a>重複使用的可攜式程式碼

跨平台不變，可以採取撰寫 Api 可針對所有平台的.NET 程式碼。 在理想情況下，您可以將所有這段程式碼移至可攜式類別庫、 共用文件庫或.NET 標準程式庫，並接著測試該現有應用程式內。

該共用的程式庫可以再加入其他平台 （例如 Android、 iOS、 macOS） 的應用程式專案。

### <a name="code-that-requires-changes"></a>需要變更的程式碼

某些.NET Api 可能無法在所有平台上使用。 如果您的程式碼中，有這些 Api，您必須重新撰寫這些區段，以使用跨平台應用程式開發介面。

包括的範例使用反映 api，可用於.NET 4.6，但並不適用於所有平台。

您已重新撰寫使用可攜式應用程式開發介面的程式碼之後，您應該能夠封裝共用的程式庫中的程式碼並測試您現有的應用程式中。

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>不是可攜式且需要重新撰寫程式碼

不可能會跨平台的程式碼的範例包括：

- **使用者介面**– Windows Forms 或 WPF 螢幕不能在 Android 或 iOS 專案，例如。 您的使用者介面必須重寫，使用這個[控制項比較](~/cross-platform/desktop/controls/index.md)做為參考。

- **平台特定的儲存體**-依賴特定平台技術 （例如本機的 SQL Server Express 資料庫） 的程式碼。 您必須重新撰寫此使用 （例如資料庫引擎的 SQLite) 替代的跨平台。
有些檔案系統作業也可能需要調整，因為 UWP 有稍微不同的 Api 以 Android 和 iOS （例如 有些檔案系統區分大小寫和其他項目不是）。

- **第 3 個合作對象元件**– 請檢查是否在應用程式中的第 3 個合作對象元件可以在其他平台。 有些非視覺 NuGet 封裝，可能會使用，但其他人 （特別是視覺控制項，類似圖表或媒體播放機）

## <a name="tips-for-making-code-portable"></a>讓可攜式程式碼的秘訣

- **相依性插入**– 提供不同的實作，每個平台，並

- **分層方法**– 是否 MVVM、 MVC、 MVP 或某些其他模式，可協助您的可攜式程式碼則會與平台專屬程式碼分開。

- **傳訊**– 您可以在程式碼中使用訊息傳遞，並解除結合的應用程式的不同部分之間的互動。
