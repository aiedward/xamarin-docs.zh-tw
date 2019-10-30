---
title: Xamarin for tvOS 支援的元件
description: 為了協助您瞭解 tvOS 應用程式可用的功能，本檔提供 Xamarin 針對 tvOS 開發所支援的元件清單。
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 371440f2e1ab28e802bf2d184b3e17d073a0c774
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030677"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Xamarin for tvOS 支援的元件

## <a name="supported-assemblies"></a>支援的元件

這是 Xamarin 針對您的 tvOS 應用程式所支援的元件清單。詳細清單如下所示。  一些值得注意的遺漏，包括 `System.EnterpriseServices`、ASP.NET 堆疊和 Windows Forms。

|Assembly|Added|API 相容性|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|適用于編譯器寫入器。|
|Mono.Data.Sqlite.dll|1.2|SQLite 的 ADO.NET 提供者;請參閱[限制](~/ios/data-cloud/system.data.md)。|
|Mono.Data.Tds.dll|1.2|TDS 通訊協定支援;用於[SqlClient](xref:System.Data.SqlClient) [中的資料。](~/ios/data-cloud/system.data.md)|
|Mono.Security.dll|1.0|密碼編譯 Api。|
|monotouch.dll|1.0|此元件包含[ C# CocoaTouch API](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8)的系結。|
|mscorlib.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|OpenGL/OpenAL 物件導向 Api，已[擴充以提供 iPhone 裝置支援](xref:OpenGLES)。|
|System.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的類型： <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.workflow.componentmodel.activity. 設計</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>系統 .Net。快取</li> <li>System.Net.Mail</li> <li>系統 .Net. Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>系統. 計時器</li></ul>|
|System.Core.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.Net 3.5](https://msdn.microsoft.com/library/ms229335.aspx)，[部分功能已移除](~/ios/data-cloud/system.data.md)。|
|System.web. Data. Client .dll|3.x|完整的 oData 用戶端。|
|System.Drawing|1.0|System. 繪圖 API-僅 Classic API。<br />_在 Xamarin .NET 4.5 或行動架構的 Unified API 中不支援 Drawing。_|
|System.Json.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)中提供的[WCF](https://docs.microsoft.com/xamarin/cross-platform/data-cloud/web-services/)堆疊|
|System.ServiceModel.Web.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的類型： <ul><li>系統</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.Net 3.5](https://msdn.microsoft.com/library/ms229335.aspx);[System. 資料](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data)支援的一部分。|
|System.Web.Services|1.1|來自 .NET 3.5 設定檔的[基本 Web 服務](https://docs.microsoft.com/xamarin/cross-platform/data-cloud/web-services/)，已移除伺服器功能。|
|System.Xml.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>可攜式類別庫

除了 Mac 系結之外，tvOS 也可以使用[.Net 可移植類別庫](~/cross-platform/app-fundamentals/pcl.md)。

## <a name="related-links"></a>相關連結

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
