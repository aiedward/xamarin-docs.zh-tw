---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: 桌面應用程式移植指引
description: 簡單說明如何將現有的 Windows Forms 或 WPF 應用程式分離, 以建立跨平臺應用程式, 以在 macOS、iOS、Android 及 UWP/Windows 10 上執行。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: bdea1c472d95c86037056a2905679b43e12e0468
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120384"
---
# <a name="desktop-app-porting-guidance"></a>桌面應用程式移植指引

大部分的應用程式代碼都可以分類為下列其中一個區域:

- 使用者介面程式碼 (例如 視窗和按鈕)
- 協力廠商控制項 (例如 圖表
- 商務邏輯 (例如 驗證規則)
- 本機資料儲存和存取
- Web 服務和遠端資料存取

針對以C# (或 Visual Basic.NET) 撰寫的 WINDOWS FORMS 和 WPF 應用程式, 您可以在各平臺間共用非預期的商務邏輯、本機資料存取和 web 服務代碼。

## <a name="net-portability-analyzer"></a>.NET 可攜性分析器

Visual Studio 2017 和更新版本支援[.net 可攜性分析器](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer)([適用于 Windows 的下載](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)), 可以檢查您現有的應用程式, 並告訴您有多少程式碼可以「依原樣」移植到其他平臺。 您可以從這個[Channel 9 影片](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer)深入瞭解。

您也可以從[GitHub 上的可攜性分析器](https://github.com/Microsoft/dotnet-apiport)下載命令列工具, 並用來提供相同的報表。

## <a name="x-of-my-code-is-portable-what-next"></a>「我的程式碼的 x% 是可移植的。 接下來呢？

希望分析器顯示的程式碼中有很大的部分是可移植的, 但一定會有一些_無法_移至其他平臺的應用程式部分。

不同的程式碼區塊可能會落在這些值區的其中一個, 詳細說明如下:

- 重複可用的可移植程式碼
- 需要變更的程式碼
- 不可移植且需要重新撰寫的程式碼

### <a name="re-useable-portable-code"></a>重複可用的可移植程式碼

針對所有平臺上可用的 Api 所撰寫的 .NET 程式碼, 跨平臺不會變更。 在理想的情況下, 您可以將所有的程式碼移到可移植的類別庫、共用程式庫或 .NET Standard 程式庫中, 然後在現有的應用程式中進行測試。

然後, 可以將該共用程式庫新增至其他平臺 (例如 Android、iOS、macOS) 的應用程式專案。

### <a name="code-that-requires-changes"></a>需要變更的程式碼

有些 .NET Api 可能無法在所有平臺上使用。 如果您的程式碼中有這些 Api, 您必須重新撰寫這些區段以使用跨平臺 Api。

範例包括使用 .NET 4.6 中提供的反映 Api, 但在所有平臺上都不提供。

使用可攜的 Api 重新撰寫程式碼之後, 您應該能夠將該程式碼封裝在共用的程式庫中, 並在現有的應用程式中進行測試。

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>不可移植且需要重新撰寫的程式碼

不太可能跨平臺的程式碼範例包括:

- 例如, 您無法在 Android 或 iOS 的專案中使用**使用者介面**-WINDOWS FORMS 或 WPF 畫面。 您的使用者介面需要重新撰寫, 使用此[控制項](~/cross-platform/desktop/controls/index.md)做為參考。

- **平臺特定的儲存**程式碼, 依賴平臺特定的技術 (例如本機 SQL Server Express 資料庫)。 您必須使用跨平臺的替代方法 (例如, 適用于資料庫引擎的 SQLite) 來重新撰寫此程式。
某些檔案系統作業可能也需要調整, 因為 UWP 與 Android 和 iOS 有些許不同的 Api (例如 某些檔案系統會區分大小寫, 有些則不區分。

- **協力廠商元件**–檢查應用程式中的協力廠商元件是否可在其他平臺上使用。 有些 (如非視覺效果 NuGet 套件) 可能可以使用, 但其他部分 (尤其是圖表或媒體播放機等視覺控制項)

## <a name="tips-for-making-code-portable"></a>讓程式碼可攜的秘訣

- 相依性**插入**–為每個平臺提供不同的執行方式, 以及

- **分層方法**– MVVM、MVC、MVP 或其他可協助您將可移植程式碼與平臺特定程式碼分開的模式。

- **訊息**–您可以在程式碼中使用訊息傳遞, 以在應用程式的不同部分之間進行重複的互動。
