---
title: 透過 Xamarin tvos 的組件支援
description: 為了協助釐清 tvOS 應用程式可用的功能，本文件提供支援 Xamarin tvOS 開發的組件的清單。
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 217ec5ea81b304555bcaf19e53c8132628628627
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788819"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>透過 Xamarin tvos 的組件支援

## <a name="supported-assemblies"></a>支援的組件

這是支援 Xamarin Xamarin.tvOS 應用程式的組件清單。下面將列出這些詳細的清單。  包含一些值得注意的遺漏`System.EnterpriseServices`，Windows.Forms 與 ASP.NET 堆疊。

|Assembly|已加入|應用程式開發介面相容性|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|編譯器寫入器。|
|Mono.Data.Sqlite.dll|1.2|SQLite; 的 ADO.NET 提供者請參閱[限制](~/ios/data-cloud/system.data.md)。|
|Mono.Data.Tds.dll|1.2|TDS 通訊協定支援;用於[System.Data.SqlClient](https://developer.xamarin.com/api/namespace/System.Data.SqlClient/)內支援[System.Data](~/ios/data-cloud/system.data.md)。|
|Mono.Security.dll|1.0|密碼編譯 Api。|
|monotouch.dll|1.0|這個組件包含[CocoaTouch 應用程式開發介面的 C# 繫結](https://developer.xamarin.com/api/root/ios-unified/)。|
|mscorlib.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|OpenGL/OpenAL 物件導向應用程式開發介面，[延伸提供的 iPhone 裝置支援](https://developer.xamarin.com/api/namespace/OpenGLES/)。|
|System.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的類型： <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)，[移除某些功能](~/ios/data-cloud/system.data.md)。|
|System.Data.Service.Client.dll|3.x|完整的 oData 用戶端。|
|System.Drawing|1.0|System.Drawing API-僅限使用傳統的 API。<br />_統一的 API 不支援 System.Drawing Xamarin.Mac.NET 4.5 或行動裝置的架構。_|
|System.Json.dll|1.1|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services)為出現在堆疊[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的類型： <ul><li>系統</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); 屬於[System.Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data)支援。|
|System.Web.Services|1.1|[基本 Web 服務](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services)從.NET 3.5 設定檔，以移除的伺服器功能。|
|System.Xml.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>可攜式類別庫

除了 Mac 的繫結，會消耗 Xamarin.tvOS [.NET 可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)。

## <a name="related-links"></a>相關連結

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
