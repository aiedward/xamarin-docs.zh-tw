---
title: 可用的組件
description: 本文列出可用於 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 的組件。 它也會連結至.NET Standard 程式庫和可攜式類別庫的相關文件。
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: asb3993
ms.author: amburns
ms.date: 03/13/2018
ms.openlocfilehash: 8882a7cd36eab5e7905585f5de4d6585dfb53439
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266544"
---
# <a name="available-assemblies"></a>可用的組件

Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 所有隨附在十幾個組件。 就像 Silverlight 是桌面的.NET 組件中擴充的子集，Xamarin 平台也是數個 Silverlight 和桌面的.NET 組件延伸的子集。

Xamarin 平台不是與現有的組件編譯為不同的設定檔相容的 ABI。 您必須重新編譯您的原始程式碼產生組件 （如同您需要重新編譯原始程式碼，以個別目標 Silverlight 和.NET 3.5），以正確的設定檔為目標。

Xamarin.Mac 應用程式可以在三種模式中編譯： 使用 Xamarin 的策劃行動設定檔、 Xamarin.Mac.NET 4.5 Framework 可讓您的目標現有完整桌面組件，以及使用.NET API，不支援的一個位於系統 Mono安裝。 如需詳細資訊，請參閱我們[目標 Framework](~/mac/platform/target-framework.md)文件。

## <a name="net-standard-libraries"></a>.NET 標準程式庫

除了 iOS、 Android 及 Mac 繫結，Xamarin 專案均可取[.NET Standard 程式庫](~/cross-platform/app-fundamentals/net-standard.md)。

## <a name="portable-class-libraries"></a>可攜式類別庫

也可以使用 Xamarin 專案[.NET 可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)，不過這項技術已被取代，而.NET Standard。

## <a name="supported-assemblies"></a>支援的組件

這些是在 可用的組件**參考管理員 > 組件 > Framework** (Visual Studio 2017) 和**編輯參考 > 封裝**(Visual Studio for Mac)，和其相容性使用 Xamarin 平台。

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|API 相容性|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|西部包括 CJK，MidEast，其他、 很少，|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|SQLite; 的 ADO.NET 提供者請參閱 < 限制。|✓|✓|✓|
> |Mono.Data.Tds.dll|TDS 通訊協定支援，用於[System.Data.SqlClient](xref:System.Data.SqlClient)內支援[System.Data](xref:System.Data)。|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|密碼編譯 Api。|✓|✓|✓|
> |monotouch.dll|這個組件包含產品 CocoaTouch api 的 C# 繫結。 這才可使用傳統的 iOS 專案中。|✓| | |
> |MonoTouch.&#8203;Dialog-1.dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|OpenGL/OpenAL 物件導向擴充，以提供 iPhone 裝置支援的 Api。|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的型別：<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx) ，以[移除某些功能](~/ios/data-cloud/system.data.md)。|✓|✓|✓|
> |System.Data.&#8203;Services.&#8203;Client.dll|完整的 oData 用戶端。|✓|✓|✓|
> |System.IO.&#8203;Compression| |✓|✓|✓|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |✓|✓|✓|
> |System.Json.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net.&#8203;Http.dll| |✓|✓|✓|
> |System.&#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.dll|WCF 中的堆疊[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的型別： <br />系統<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx); 的一部分[System.Data](~/ios/data-cloud/system.data.md)支援。|✓|✓|✓|
> |System.Web.&#8203;Services.dll|從.NET 3.5 的設定檔，移除的伺服器功能的基本 Web 服務。|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|這個組件包含產品 CocoaTouch api 的 C# 繫結。 這只用於整合 iOS 專案。|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|編譯器寫入器。| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|統一的 API 不支援 System.Drawing Xamarin.Mac、.NET 4.5 中，或行動裝置的架構。 可以新增 System.Drawing 支援 iOS 和 macOS 使用[sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics)文件庫|✓| |✓|
