---
title: 可用的組件
description: 本檔列出可在 Xamarin、Xamarin 和 Xamarin 中使用的元件。 它也會連結至 .NET Standard 程式庫和可移植類別庫的相關檔。
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: cb5c4a463a8368d6a82d89baba08145f161a95d6
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457935"
---
# <a name="available-assemblies"></a>可用的組件

Xamarin、Xamarin 和 Xamarin 全都隨附于數十個元件。 如同 Silverlight 是桌面 .NET 元件的擴充子集，Xamarin 平臺也是數個 Silverlight 和 desktop .NET 元件的擴充子集。

Xamarin 平臺與針對不同設定檔編譯的現有元件不相容。 您必須重新編譯您的原始程式碼，以產生以正確設定檔為目標的元件 (，就像您需要重新編譯原始程式碼，以個別) 的 Silverlight 和 .NET 3.5 為目標。

您可以使用下列三種模式來編譯 xamarin： Mac 應用程式：一個使用 Xamarin 的策劃行動設定檔，也就是可讓您將現有的完整桌面元件設為目標的 Xamarin .NET 4.5 架構，而不支援的應用程式則是使用系統 Mono 安裝中所找到的 .NET API。 如需詳細資訊，請參閱我們的 [目標 framework](~/mac/platform/target-framework.md) 檔。

## <a name="net-standard-libraries"></a>.NET Standard 程式庫

除了 iOS、Android 和 Mac 系結，Xamarin 專案也可以取用 .NET Standard 連結 [庫](~/cross-platform/app-fundamentals/net-standard.md)。

## <a name="portable-class-libraries"></a>可攜式類別庫

Xamarin 專案也可以使用 [.Net 可移植類別庫](~/cross-platform/app-fundamentals/pcl.md)，不過這項技術會被取代成 .NET Standard。

## <a name="supported-assemblies"></a>支援的元件

這些是 **參考管理員 > 元件 > Framework** 中提供的元件 (Visual Studio 2017 **) > (** Visual Studio for Mac) 和 Xamarin 平臺的相容性。

> [!div class="mx-tdCol2BreakAll"]
> |組件|API 相容性|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |l18N.dll|包含 CJK、MidEast、其他、罕見、西部|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |Microsoft.CSharp.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |Mono.CSharp.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |Mono.Data.Sqlite.dll|SQLite 的 ADO.NET 提供者;請參閱限制。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |Mono.Data.Tds.dll|TDS 通訊協定支援;適用于 [SqlClient](xref:System.Data.SqlClient) 中的 Support. [資料](xref:System.Data)。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |Mono. 動態. &#8203;Interpreter.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| | |
> |Mono.Security.dll|密碼編譯 Api。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |monotouch.dll|此元件包含 CocoaTouch API 的 c # 系結。 這僅適用于傳統 iOS 專案。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| | |
> |Monotouch.dll &#8203;Dialog-1.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| | |
> |Monotouch.dll &#8203;NUnitLite.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| | |
> |mscorlib.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |OpenTK-1.0.dll|OpenGL/OpenAL 物件導向的 Api，已擴充為可提供 iPhone 裝置支援。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))，再加上下列命名空間的類型：<br />System.Collections.Specialized<br />System. &#8203;ComponentModel<br />ComponentModel 設計<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />系統 .Net。快取<br />System.Net.Mail<br />系統 .Net Mime<br />System.Net. &#8203;System.net.networkinformation.networkinformationexception<br />System.Net.Security<br />System.Net.Sockets<br />System.runtime.interopservices.outattribute &#8203;<br />System.Runtime.Versioning<br />&#8203;AccessControl<br />System.Security.Authentication<br />&#8203;密碼編譯<br />System.Security.Permissions<br />System.Threading<br />系統計時器|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System. &#8203;ComponentModel &#8203;Composition.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System. &#8203;ComponentModel &#8203;DataAnnotations.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System.Core.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System.Data.dll|[.Net 3.5](/previous-versions/ms229335(v=vs.100)) ， [已移除部分功能](~/ios/data-cloud/system.data.md)。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |&#8203;的服務。 &#8203;Client.dll|完整的 oData 用戶端。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System.IO &#8203;壓縮| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System.IO &#8203;壓縮。 &#8203;檔案系統| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System.Json.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System.Net &#8203;Http.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System. &#8203;Numerics.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |&#8203;Serialization.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System. &#8203;ServiceModel.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))中有 WCF 堆疊|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |&#8203;System.servicemodel. &#8203;Internals.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |&#8203;System.servicemodel. &#8203;Web.dll|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))，再加上下列命名空間的類型： <br />系統<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System. &#8203;Transactions.dll|[.Net 3.5](/previous-versions/ms229335(v=vs.100)); [System. Data](~/ios/data-cloud/system.data.md) support 的一部分。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |&#8203;Services.dll|.NET 3.5 設定檔中的基本 Web 服務，已移除伺服器功能。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System. &#8203;Windows.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System. &#8203;Xml.dll|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System.Xml &#8203;Linq.dll|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System.Xml.Serialization.dll| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |Xamarin.iOS.dll|此元件包含 CocoaTouch API 的 c # 系結。 這僅適用于整合的 iOS 專案。|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| | |
> |Java.Interop.dll| | |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| |
> |Mono.Android.dll| | |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| |
> |Mono. Android. &#8203;Export.dll| | |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| |
> |Mono.Posix.dll| | |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| |
> |System. &#8203;EnterpriseServices.dll| | |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| |
> |&#8203;NUnitLite.dll 的| | |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| |
> |CompilerServices &#8203;SymbolWriter.dll|適用于編譯器寫入器。| | |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |Xamarin.Mac.dll| | | |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|
> |System. &#8203;Drawing.dll|在 Xamarin、.NET 4.5 或 Mobile framework 的 Unified API 中，不支援「系統繪圖」。 System. 繪圖支援可以使用 [sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics) 程式庫新增至 IOS 和 macOS|![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")| |![支援 Xamarin](~/media/shared/yes.png "支援 Xamarin")|