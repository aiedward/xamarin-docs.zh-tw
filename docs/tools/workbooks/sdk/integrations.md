---
title: 進階的 Integration 主題
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 8ab0bb71d4c79895eca94899c3f277b466fe0eb1
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="external-integrations"></a>外部的整合

整合組件應該參考[ `Xamarin.Workbooks.Integrations` NuGet][nuget]。 請查看我們[快速入門文件](~/tools/workbooks/sdk/index.md)如需開始使用 NuGet 封裝。

用戶端整合也支援，並藉由將代理程式的整合組件與同名的 JavaScript 或 CSS 檔案放在相同的目錄。 例如，如果名為代理程式 」 整合組件 （即在參考的 NuGet） `SampleExternalIntegration.dll`，然後`SampleExternalIntegration.js`和`SampleExternalIntegration.css`會整合到的用戶端中，如果有的話。 用戶端整合是選擇性的。

外部整合本身可以封裝為 NuGet、 提供並直接在裝載代理程式，或只是放置在一起的應用程式參照`.workbook`想要使用它的檔案。

封裝參考時，根據的快速入門文件，來參考它，因此需要與活頁簿一起出貨的整合組件時，就會自動載入 NuGet 封裝中的外部整合 （代理程式和用戶端）：

```csharp
#r "SampleExternalIntegration.dll"
```

在參考時整合這種方式，它將不會載入用戶端立即&mdash;必須先從的整合，讓它載入呼叫某些程式碼。 我們會解決此錯誤在未來。

`Xamarin.Interactive` PCL 提供幾個重要的整合應用程式開發介面。 每個整合至少必須提供整合的進入點：

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

此時，一旦整合組件參考時，用戶端會以隱含方式會載入 JavaScript 和 CSS 整合檔案。

## <a name="apis"></a>API

與任何組件參考的活頁簿或即時檢查工作階段，任何其公用 Api 是可存取工作階段。 因此就一定要有安全又實用的 API 介面，讓使用者瀏覽。

整合組件實際上就是應用程式或感興趣的 SDK 與工作階段之間的橋樑。 它可以提供有意義特別是在內容的活頁簿或即時檢查工作階段，或提供任何公用 Api 和直接執行 「 幕後 」 工作，例如產生物件的新 Api[表示](~/tools/workbooks/sdk/representations.md)。

> [!NOTE]
> 這必須是公用的但不是應該顯示 IntelliSense 透過應用程式開發介面都可以使用一般標記`[EditorBrowsable (EditorBrowsableState.Never)]`屬性。

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
