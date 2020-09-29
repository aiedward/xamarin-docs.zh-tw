---
title: Xamarin for tvOS 所支援的元件
description: 為了協助您瞭解可供 tvOS 應用程式使用的功能，本檔提供 Xamarin 針對 tvOS 開發所支援的元件清單。
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 138b65d51c7069f04433f5b4aee6f8d2b743ccdb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435021"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Xamarin for tvOS 所支援的元件

## <a name="supported-assemblies"></a>支援的元件

這是 xamarin 針對您的 tvOS 應用程式所支援的元件清單。以下列出這些清單的詳細清單。  一些值得注意的遺漏包括 `System.EnterpriseServices` ASP.NET 堆疊和 Windows。

|組件|已新增|API 相容性|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|適用于編譯器寫入器。|
|Mono.Data.Sqlite.dll|1.2|SQLite 的 ADO.NET 提供者;請參閱 [限制](~/ios/data-cloud/system.data.md)。|
|Mono.Data.Tds.dll|1.2|TDS 通訊協定支援;適用于 [SqlClient](xref:System.Data.SqlClient) 中的 Support. [資料](~/ios/data-cloud/system.data.md)。|
|Mono.Security.dll|1.0|密碼編譯 Api。|
|monotouch.dll|1.0|此元件包含 [COCOATOUCH API 的 c #](/dotnet/api/?view=xamarinios-10.8)系結。|
|mscorlib.dll|1.0|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|OpenTK.dll|1.0|OpenGL/OpenAL 物件導向的 Api，已 [擴充為可提供 iPhone 裝置支援](xref:OpenGLES)。|
|System.dll|1.0|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))，再加上下列命名空間的類型： <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>ComponentModel 設計</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>系統 .Net。快取</li> <li>System.Net.Mail</li> <li>系統 .Net Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>系統計時器</li></ul>|
|System.Core.dll|1.0|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.Data.dll|1.2|[.Net 3.5](/previous-versions/ms229335(v=vs.100))， [已移除部分功能](~/ios/data-cloud/system.data.md)。|
|System.Data.Service.Client.dll|3.x|完整的 oData 用戶端。|
|System.Drawing|1.0|System. 只繪製 API-Classic API。<br />_在 Xamarin .NET 4.5 或行動架構的 Unified API 中，不支援「系統繪圖」。_|
|System.Json.dll|1.1|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.Runtime.Serialization.dll|?|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.ServiceModel.dll|1.1|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))中有[WCF](../../../cross-platform/data-cloud/web-services/index.md)堆疊|
|System.ServiceModel.Web.dll|?|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))，再加上下列命名空間的類型： <ul><li>系統</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.Net 3.5](/previous-versions/ms229335(v=vs.100)); [System. Data](../../data-cloud/system.data.md) support 的一部分。|
|System.Web.Services|1.1|.NET 3.5 設定檔中的[基本 Web 服務](../../../cross-platform/data-cloud/web-services/index.md)，已移除伺服器功能。|
|System.Xml.dll|1.0|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|
|System.Xml.Linq.dll|1.0|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|

<a name="Summary"></a>

## <a name="portable-class-libraries"></a>可攜式類別庫

除了 Mac 系結，tvOS 還可以使用 [.Net 可移植類別庫](~/cross-platform/app-fundamentals/pcl.md)。

## <a name="related-links"></a>相關連結

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)