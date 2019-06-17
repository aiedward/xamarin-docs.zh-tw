---
title: 進階的整合主題
description: 本文件說明與 Xamarin Workbooks 整合相關的進階的主題。 它討論的 Xamarin.Workbook.Integrations NuGet 套件和 API 在 Xamarin 活頁簿中的曝光度。
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 56ee709b78b8587c2717dc9d25a6357041812d23
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382241"
---
# <a name="advanced-integration-topics"></a>進階的整合主題

整合組件應該參考[ `Xamarin.Workbooks.Integrations` NuGet][nuget]。 請查看我們[快速入門文件](~/tools/workbooks/sdk/index.md)如需開始使用 NuGet 套件的詳細資訊。

用戶端的整合也支援，並藉由將整合代理程式的組件同名的 JavaScript 或 CSS 檔案放在相同的目錄。 例如，如果名為代理程式 」 整合組件 （即在參考 NuGet） `SampleExternalIntegration.dll`，然後`SampleExternalIntegration.js`和`SampleExternalIntegration.css`將會在用戶端整合，如果有的話。 用戶端的整合是選擇性的。

外部整合本身可以是封裝為 NuGet、 提供和裝載代理程式，或只是放置在一起的應用程式內的直接參考`.workbook`想要使用它的檔案。

封裝參考時，根據的快速入門文件，來參考它，因此需要與活頁簿一起出貨的整合組件時，就會自動載入 NuGet 套件中的外部整合 （代理程式和用戶端）：

```csharp
#r "SampleExternalIntegration.dll"
```

當參考整合如此一來，它將不會載入用戶端立即&mdash;您要從整合，以將它載入呼叫某些程式碼。 我們將會解決這個錯誤未來。

`Xamarin.Interactive` PCL 提供幾個重要整合 Api。 每個整合至少必須提供整合的進入點：

```csharp
using Xamarin.Interactive;

[assembly: AgentIntegration (typeof (AgentIntegration))]

class AgentIntegration : IAgentIntegration
{
    const string TAG = nameof (AgentIntegration);

    public void IntegrateWith (IAgent agent)
    {
        // hook into IAgent APIs
    }
}
```

到目前為止之後的整合組件參考時，, 用戶端將會隱含地載入 JavaScript 和 CSS 整合檔案。

## <a name="apis"></a>API

如有任何該組件是參考活頁簿或即時檢查工作階段，任何其公開的 Api 都可存取工作階段項目。 因此，它是一定要有安全且實用的 API 介面，讓使用者瀏覽。

整合的組件實際上是應用程式或感興趣的 SDK 與工作階段之間的橋樑。 它可以提供有意義的特別是在內容的活頁簿或即時檢查工作階段中，或提供沒有公用 Api，而且只需執行 「 幕後 」 工作，例如產生物件的新 Api[表示](~/tools/workbooks/sdk/representations.md)。

> [!NOTE]
> 應用程式開發介面必須是公用的但不是會顯示透過 IntelliSense 都可以使用一般標記`[EditorBrowsable (EditorBrowsableState.Never)]`屬性。

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
