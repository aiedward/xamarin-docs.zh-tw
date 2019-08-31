---
title: 進階的整合主題
description: 本檔說明與 Xamarin Workbooks 整合相關的 advanced 主題。 它會討論 xamarin 活頁簿中的整合 NuGet 套件和 API 公開。
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 07dd0e64b90bb0aa11f0a7050e3b86f3203ce7de
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199972"
---
# <a name="advanced-integration-topics"></a>進階的整合主題

整合元件應該參考[ `Xamarin.Workbooks.Integrations` NuGet][nuget]。 如需開始使用 NuGet 套件的詳細資訊, 請參閱我們的[快速入門檔](~/tools/workbooks/sdk/index.md)。

此外, 也支援用戶端整合, 其起始方式是將具有相同名稱的 JavaScript 或 CSS 檔案放在相同的目錄中。 例如, 如果代理程式整合元件 (參考 NuGet) 名`SampleExternalIntegration.dll`為, 則`SampleExternalIntegration.js`和`SampleExternalIntegration.css`也會整合到用戶端 (如果存在)。 用戶端整合是選擇性的。

外部整合本身可以封裝為 NuGet, 並直接在裝載代理程式的應用程式內提供和參考, 或只是放`.workbook`在想要取用它的檔案。

參照套件時, NuGet 套件中的外部整合 (代理程式和用戶端) 將會自動載入, 根據快速入門檔, 而活頁簿隨附的整合元件則需要參考它, 如下所示:

```csharp
#r "SampleExternalIntegration.dll"
```

以這種方式參考整合時, 用戶端不會立即&mdash;載入它, 因此您必須從整合呼叫一些程式碼, 才能讓它載入。 未來將會解決此錯誤。

`Xamarin.Interactive` PCL 提供一些重要的整合 api。 每個整合至少必須提供整合進入點:

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

此時, 一旦參考整合元件之後, 用戶端就會隱含地載入 JavaScript 和 CSS 整合檔案。

## <a name="apis"></a>API

與活頁簿或即時檢查會話所參考的任何元件相同, 會話都可以存取其任何公用 Api。 因此, 請務必要有安全且合理的 API 介面供使用者探索。

整合元件實際上是應用程式或 SDK 與會話之間的橋樑。 它可以在活頁簿或即時檢查會話的內容中提供特別意義的新 Api, 或不提供公用 Api, 而只是執行「幕後」工作, 像是產生物件[標記法](~/tools/workbooks/sdk/representations.md)。

> [!NOTE]
> 必須是公用, 但不能透過 IntelliSense 呈現的 api, 可以使用一般`[EditorBrowsable (EditorBrowsableState.Never)]`屬性來標記。

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
