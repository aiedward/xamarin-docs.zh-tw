---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: 桌面應用程式移植指引
description: 如何將現有的 Windows Forms 或 WPF 應用程式分離，以建立可在 macOS、iOS、Android 及 UWP/Windows 10 上執行的跨平臺應用程式的簡單說明。
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 0bb5a7b851a80cb92881968729e561caeaf48d12
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456843"
---
# <a name="desktop-app-porting-guidance"></a>桌面應用程式移植指引

大部分的應用程式程式碼可分類為下列其中一個區域：

- 使用者介面程式碼 (例如 windows 和按鈕) 
- 協力廠商控制項 (例如 圖表) 
- 商務邏輯 (例如 驗證規則) 
- 本機資料儲存和存取
- Web 服務和遠端資料存取

對於以 c # (或 Visual Basic.NET 撰寫的 Windows Forms 和 WPF 應用程式) 的商務邏輯、本機資料存取和 web 服務程式代碼，都可以跨平臺共用。

## <a name="net-portability-analyzer"></a>.NET Portability Analyzer

Visual Studio 2017 和更新版本支援適用于 Windows) 的 [.net 可攜性分析器](/dotnet/articles/standard/portability-analyzer) (下載，可檢查您現有 [的](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) 應用程式，並告訴您有多少程式碼可以「依原樣」移植至其他平臺。 您可以從這個 [Channel 9 影片](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer)中深入瞭解。

您也可以從 [GitHub 上的可攜性分析器](https://github.com/Microsoft/dotnet-apiport) 下載命令列工具，用來提供相同的報表。

## <a name="x-of-my-code-is-portable-what-next"></a>「我的程式碼的 x% 是可移植的。 接下來呢？」

希望分析器能以可攜性顯示大部分的程式碼，但在每個應用程式中，一定會有一些 _無法_ 移至其他平臺的部分。

不同的程式碼區塊可能會落在這些 bucket 中的其中一個，如下所述：

- 重複可用的便攜程式碼
- 需要變更的程式碼
- 無法移植且需要重新撰寫的程式碼

### <a name="re-useable-portable-code"></a>重複可用的便攜程式碼

針對所有平臺上可用的 Api 所撰寫的 .NET 程式碼，可以跨平臺進行跨平臺變更。 在理想的情況下，您將能夠將所有程式碼移至可移植的類別庫、共用程式庫或 .NET Standard 程式庫，然後在現有的應用程式中進行測試。

然後，您可以將該共用程式庫新增至其他平臺的應用程式專案， (例如 Android、iOS、macOS) 。

### <a name="code-that-requires-changes"></a>需要變更的程式碼

某些 .NET Api 可能無法在所有平臺上使用。 如果您的程式碼中有這些 Api，您將需要重新撰寫這些區段，以使用跨平臺 Api。

其中的範例包括使用可在 .NET 4.6 中使用的反映 Api，但在所有平臺上都無法使用。

使用可移植 Api 重新撰寫程式碼之後，您應該能夠將該程式碼封裝在共用的程式庫中，並在現有的應用程式中進行測試。

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>無法移植且需要重新撰寫的程式碼

不太可能跨平臺的程式碼範例包括：

- 例如，在 Android 或 iOS 的專案中無法使用**消費者介面**– WINDOWS FORMS 或 WPF 畫面。 您的使用者介面需要重新撰寫，並使用此控制項做為參考的 [比較](~/cross-platform/desktop/controls/index.md) 。

- **平臺特定的儲存** 程式碼，依賴平臺特定的技術 (例如本機 SQL Server Express 資料庫) 。 您必須使用跨平臺替代 (（例如適用于資料庫引擎) 的 SQLite）來重新撰寫此程式。
某些檔案系統作業可能也需要調整，因為 UWP 的 Api 與 Android 和 iOS 的 Api 稍微不同 (例如， 有些檔案系統會區分大小寫，其他檔則不會) 。

- **協力廠商元件** –檢查應用程式中的協力廠商元件是否可在其他平臺上使用。 有些（例如非視覺效果的 NuGet 套件）可能可供使用，但其他 (特別是圖表或媒體播放機等視覺控制項) 

## <a name="tips-for-making-code-portable"></a>使程式碼可攜的秘訣

- 相依性**插入**-為每個平臺提供不同的執行方式，以及

- 多**層式方法**– MVVM、MVC、MVP 或其他的模式，可協助您將可移植的程式碼與平臺特定程式碼分開。

- **訊息** -您可以在程式碼中使用訊息傳入，以消除應用程式不同部分之間的互動。