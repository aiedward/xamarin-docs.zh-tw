---
title: 可用的組件
description: Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 中可用的組件
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: asb3993
ms.author: amburns
ms.date: 03/13/2018
ms.openlocfilehash: ef1dde061ffce4082922cc2eb2af1ae9b045aedd
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="available-assemblies"></a>可用的組件

_Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 中可用的組件_

Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 所有隨附在十幾組件。 就像是 Silverlight 桌面的.NET 組件中擴充的子集，Xamarin 平台也是數個 Silverlight 和桌面的.NET 組件擴充的子集。

Xamarin 平台不是與現有的組件編譯為不同的設定檔相容的 ABI。 您必須重新編譯您的原始程式碼產生組件 （就如同您需要重新編譯原始程式碼，以個別目標 Silverlight 和.NET 3.5），以正確的設定檔為目標。

Xamarin.Mac 應用程式可以在三種模式中編譯： 使用 Xamarin 的策劃行動設定檔、 Xamarin.Mac.NET 4.5 Framework 可讓您針對現有的完整桌面組件，而且會使用.NET 應用程式開發介面不支援的一個中找到系統單聲道安裝。 如需詳細資訊，請參閱我們[目標架構](~/mac/platform/target-framework.md)文件。


## <a name="net-standard-libraries"></a>.NET 標準程式庫

除了 iOS、 Android 和 Mac 繫結時，Xamarin 專案可能會耗用[.NET 標準程式庫](~/cross-platform/app-fundamentals/net-standard.md)。

## <a name="portable-class-libraries"></a>可攜式類別庫
 
也可以在使用 Xamarin 專案[.NET 可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)，不過這項技術被取代為.NET 標準。

## <a name="supported-assemblies"></a>支援的組件

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|應用程式開發介面相容性|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|西包含 CJK，MidEast 其他、 罕見，|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|SQLite; 的 ADO.NET 提供者請參閱 < 限制。|✓|✓|✓|
> |Mono.Data.Tds.dll|TDS 通訊協定支援;用於[System.Data.SqlClient](https://developer.xamarin.com/api/namespace/System.Data.SqlClient/)內支援[System.Data](https://developer.xamarin.com/api/namespace/System.Data/)。|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|密碼編譯 Api。|✓|✓|✓|
> |monotouch.dll|這個組件包含 CocoaTouch 應用程式開發介面的 C# 繫結。 這只是傳統的 iOS 專案中可用。|✓| | |
> |MonoTouch.&#8203;Dialog-1.dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|OpenGL/OpenAL 物件導向應用程式開發介面，擴充，以提供 iPhone 裝置支援。|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的類型：<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx) ，與[移除某些功能](~/ios/data-cloud/system.data.md)。|✓|✓|✓|
> |System.Data.&#8203;Services.&#8203;Client.dll|完整的 oData 用戶端。|✓|✓|✓|
> |System.IO.&#8203;Compression| |✓|✓|✓|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |✓|✓|✓|
> |System.Json.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net.&#8203;Http.dll| |✓|✓|✓|
> |System.&#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.dll|為存在於 WCF 堆疊[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的類型： <br />系統<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); 屬於[System.Data](~/ios/data-cloud/system.data.md)支援。|✓|✓|✓|
> |System.Web.&#8203;Services.dll|從.NET 3.5 設定檔，以移除的伺服器功能的基本 Web 服務。|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|這個組件包含 CocoaTouch 應用程式開發介面的 C# 繫結。 這只用於整合的 iOS 專案。|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|編譯器寫入器。| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|System.Drawing API-僅限使用傳統的 API。統一的 API 不支援 System.Drawing Xamarin.Mac.NET 4.5 或行動裝置的架構。System.Drawing 支援可以加入 iOS 和 OS X 使用[sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics)程式庫|✓| |✓|
