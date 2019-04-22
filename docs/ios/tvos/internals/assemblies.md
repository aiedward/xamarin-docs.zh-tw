---
title: TvOS 的 xamarin 的組件支援
description: 為了協助釐清 tvOS 應用程式可用的功能，這份文件會提供 Xamarin 支援 tvOS 開發的組件清單。
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: df50b4280335001f2d27ff23a91e4098eed3ba99
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870205"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>TvOS 的 xamarin 的組件支援

## <a name="supported-assemblies"></a>支援的組件

這是一份支援 xamarin Xamarin.tvOS 應用程式的組件。這些項目的詳細的清單如下所示。  包含一些值得注意的疏忽`System.EnterpriseServices`，Windows.Forms 與 ASP.NET 堆疊。

|Assembly|Added|API 相容性|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|編譯器寫入器。|
|Mono.Data.Sqlite.dll|1.2|SQLite; 的 ADO.NET 提供者請參閱[限制](~/ios/data-cloud/system.data.md)。|
|Mono.Data.Tds.dll|1.2|TDS 通訊協定支援，用於[System.Data.SqlClient](xref:System.Data.SqlClient)內支援[System.Data](~/ios/data-cloud/system.data.md)。|
|Mono.Security.dll|1.0|密碼編譯 Api。|
|monotouch.dll|1.0|這個組件包含[C# 繫結至 CocoaTouch API](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8)。|
|mscorlib.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|OpenGL/OpenAL 物件導向 Api[延伸提供 iPhone 裝置支援](xref:OpenGLES)。|
|System.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的型別： <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)，[移除某些功能](~/ios/data-cloud/system.data.md)。|
|System.Data.Service.Client.dll|3.x|完整的 oData 用戶端。|
|System.Drawing|1.0|System.Drawing API-傳統的 API。<br />_統一的 API 不支援 System.Drawing Xamarin.Mac.NET 4.5 或行動裝置的架構。_|
|System.Json.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services)堆疊中顯示[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的型別： <ul><li>系統</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx); 的一部分[System.Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data)支援。|
|System.Web.Services|1.1|[基本的 Web 服務](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services)從.NET 3.5 的設定檔，移除的伺服器功能。|
|System.Xml.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>可攜式類別庫

Mac 的繫結，除了可以取用 Xamarin.tvOS [.NET 可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)。

## <a name="related-links"></a>相關連結

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
