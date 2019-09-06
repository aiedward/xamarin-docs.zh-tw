---
title: 可用的組件
description: 本檔列出可在 Xamarin、Xamarin 和 Xamarin 中使用的元件。 它也會連結到 .NET Standard 程式庫和可移植類別庫的相關檔。
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 3d3524a0f99d78fc356dc7c4bb3e3aca1940a718
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278441"
---
# <a name="available-assemblies"></a>可用的組件

Xamarin. iOS、Xamarin 和 Xamarin 全都隨附于數十個元件。 就像 Silverlight 是桌面 .NET 元件的擴充子集，Xamarin 平臺也是數個 Silverlight 和桌面 .NET 元件的擴充子集。

Xamarin 平臺與為不同設定檔編譯的現有元件不相容。 您必須重新編譯原始程式碼，以產生以正確設定檔為目標的元件（就如同您需要重新編譯原始程式碼以分別以 Silverlight 和 .NET 3.5 為目標）。

Xamarin. Mac 應用程式可透過三種模式來編譯：一個使用 Xamarin 的策劃行動設定檔、一個 Xamarin .NET 4.5 架構，它可讓您以現有的完整桌面元件為目標，而不支援使用在系統 Mono 中找到的 .NET API。過程. 如需詳細資訊，請參閱我們的[目標 framework](~/mac/platform/target-framework.md)檔。

## <a name="net-standard-libraries"></a>.NET Standard 程式庫

除了 iOS、Android 和 Mac 系結之外，Xamarin 專案也可以使用[.NET Standard 程式庫](~/cross-platform/app-fundamentals/net-standard.md)。

## <a name="portable-class-libraries"></a>可攜式類別庫

Xamarin 專案也可以使用[.Net 可移植的類別庫](~/cross-platform/app-fundamentals/pcl.md)，雖然這項技術已淘汰而改用 .NET Standard。

## <a name="supported-assemblies"></a>支援的元件

這些是參考管理員中提供的元件 **> 元件 > Framework** （Visual Studio 2017）和**編輯參考 > 封裝**（Visual Studio for Mac），以及其與 Xamarin 平臺的相容性。

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|API 相容性|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|包含 CJK、務必、Other、罕見、West|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|SQLite 的 ADO.NET 提供者;請參閱限制。|✓|✓|✓|
> |Mono.Data.Tds.dll|TDS 通訊協定支援;用於[SqlClient](xref:System.Data.SqlClient) [中的資料。](xref:System.Data)|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|密碼編譯 Api。|✓|✓|✓|
> |monotouch.dll|此元件包含 CocoaTouch C# API 的系結。 這只能在傳統 iOS 專案中使用。|✓| | |
> |MonoTouch.&#8203;Dialog-1.dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|OpenGL/OpenAL 物件導向 Api，已擴充以提供 iPhone 裝置支援。|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的類型：<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.Net 3.5](https://msdn.microsoft.com/library/ms229335.aspx) ，[部分功能已移除](~/ios/data-cloud/system.data.md)。|✓|✓|✓|
> |System.Data.&#8203;Services.&#8203;Client.dll|完整的 oData 用戶端。|✓|✓|✓|
> |System.IO.&#8203;Compression| |✓|✓|✓|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |✓|✓|✓|
> |System.Json.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net.&#8203;Http.dll| |✓|✓|✓|
> |System.&#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)中提供的 WCF 堆疊|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)，再加上下列命名空間中的類型： <br />系統<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.Net 3.5](https://msdn.microsoft.com/library/ms229335.aspx);[System. 資料](~/ios/data-cloud/system.data.md)支援的一部分。|✓|✓|✓|
> |System.Web.&#8203;Services.dll|來自 .NET 3.5 設定檔的基本 Web 服務，已移除伺服器功能。|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|此元件包含 CocoaTouch C# API 的系結。 這僅適用于整合的 iOS 專案。|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|適用于編譯器寫入器。| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|Unified API 中不支援 Xamarin、.NET 4.5 或行動架構的。 您可以使用[sysdrawing-coregraphics](https://github.com/mono/sysdrawing-coregraphics)程式庫，將繪圖支援新增至 IOS 和 macOS|✓| |✓|
