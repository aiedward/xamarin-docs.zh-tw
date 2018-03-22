---
title: "開始使用 Xamarin 活頁簿 SDK"
ms.topic: article
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 53bc3d1a897eb007cd4c2971b0e3aa3fa01e1238
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>開始使用 Xamarin 活頁簿 SDK

本文件提供的快速指南，開始使用開發 Xamarin 活頁簿的整合。 其中有許多能夠搭配穩定的 Xamarin 活頁簿，但是**載入整合透過 NuGet 封裝僅適用於在活頁簿 1.3**，在撰寫本文時的 alpha 色板。

# <a name="general-overview"></a>一般概觀

Xamarin 活頁簿整合是小型的程式庫，可使用[ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK 整合與 Xamarin 活頁簿和偵測器代理程式，以提供增強的體驗。

有 3 個主要步驟以開始使用開發整合，我們將以下概述它們。

## <a name="creating-the-integration-project"></a>建立整合專案

整合程式庫是最適合開發的多平台程式庫。 因為您想要對所有可用的代理程式、 過去和未來提供最佳的整合，您會想要選擇一組廣泛的支援程式庫。 我們建議使用 「 可攜式程式庫 」 範本進行廣泛的支援：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![可攜式程式庫範本 Visual Studio for Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![可攜式程式庫範本的 Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

在 Visual Studio 中，您會想要確定您的可攜式程式庫選取下列目標平台：

[![可攜式程式庫平台的 Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

一旦您建立的程式庫專案，將參考加入我們`Xamarin.Workbooks.Integration`NuGet 文件庫透過 NuGet 套件管理員。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![NuGet Visual Studio for Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

您會想要刪除空白會為您的專案中建立的類別，您將不再需要它這個。 完成這些步驟之後，您就可以開始建置您的整合。

## <a name="building-an-integration"></a>建置整合

我們將建置簡單的整合。 我們真的愛色彩綠色，因此我們會將綠色新增做對每個物件的表示法。 首先，建立新的類別稱為`SampleIntegration`，並讓它實作我們[ `IAgentIntegration` ] [ integration-type]介面：

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

我們想要做什麼是加入[表示](~/tools/workbooks/sdk/representations.md)是綠色每一個物件。 我們這樣使用表示提供者。 提供者繼承[ `RepresentationProvider` ] [ reppr]類別，如我們，我們只需要覆寫[ `ProvideRepresentations` ] [ prrep]:

```csharp
using Xamarin.Interactive.Representations;

class SampleRepresentationProvider : RepresentationProvider
{
    public override IEnumerable<object> ProvideRepresentations (object obj)
    {
        // This corresponds to Pantone 2250 XGC, our favorite color.
        yield return new Color (0.0, 0.702, 0.4314);
    }
}
```

我們正在傳回[ `Color` ] [ color]、 預先建置我們 SDK 中的表示法型別。
您會注意到傳回型別是`IEnumerable<object>`&mdash;一個表示提供者可能會傳回許多物件的表示法 ！ 所有表示提供者都稱為每個物件，所以不做任何假設有關正在將物件傳遞給您。

最後一個步驟是實際我們提供者向代理程式和告訴活頁簿的位置找不到我們的整合類型。 若要註冊提供者，將加入這個程式碼以`IntegrateWith`方法中的`SampleIntegration`我們稍早建立的類別：

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

設定整合型別是透過整個組件的屬性來完成。 您可以將它放在您的 AssemblyInfo.cs、 或為了方便您整合類型的類別相同：

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

在開發期間，您可能會發現更方便使用[`AddProvider`多載][ addprovider]上`RepresentationManager`可讓您註冊以提供在活頁簿，表示相互轉換的簡單回呼然後將該程式碼到您`RepresentationProvider`一旦您完成實作。 轉譯範例[ `OxyPlot` ] [ oxyplot] `PlotModel`可能看起來像這樣：

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> 這些 Api 可讓您快速啟動並執行，但我們不建議只使用這些傳送整個整合&mdash;它們提供較少的控制，透過您的型別用戶端的處理方式。

已註冊的表示法，您的整合，已可送交 ！

## <a name="shipping-your-integration"></a>傳送您的整合

若要寄送您的整合，您將需要將它加入至 NuGet 封裝。
隨附現有程式庫的 NuGet，或如果您要建立新的封裝，您可以使用這個範本.nuspec 檔案做為起點。
必須填寫您的整合相關區段。 最重要的部分是所有的整合您的檔案必須位於`xamarin.interactive`目錄根目錄的封裝。 這可讓我們能夠輕易地找到您的整合，無論您使用現有的封裝，或是建立一個新的所有相關檔案。

```xml
<?xml version="1.0"?>
<package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
    <metadata>
      <id>$YourNuGetPackage$</id>
      <version>$YourVersion$</version>
      <authors>$YourNameHere$</authors>
      <projectUrl>$YourProjectPage$</projectUrl>
      <description>A short description of your library.</description>
    </metadata>
    <files>
      <file src="Path\To\Your\Integration.dll" target="xamarin.interactive" />
    </files>
</package>
```

一旦您已建立.nuspec 檔案，您可以壓縮 NuGet 就像這樣：

```csharp
nuget pack MyIntegration.nuspec
```

然後發佈它以[NuGet][nugetorg]。 一旦有，您將能夠從任何活頁簿中參考它，並查看它的動作。 在以下的螢幕擷取畫面，我們已封裝我們內建的這份文件和活頁簿中安裝的 NuGet 套件的範例整合：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![活頁簿的整合](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![活頁簿的整合](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

請注意，您沒有看到任何`#r`指示詞或任何項目初始化整合 — 活頁簿已處理的所有作業，讓您在幕後 ！

## <a name="next-steps"></a>後續步驟

簽出其他文件如需有關移動片段組成 SDK，以及我們[範例整合](~/tools/workbooks/samples/index.md)的其他項目，您可以從您的整合，例如提供執行中的自訂 JavaScript活頁簿的用戶端。

[integration-type]: https://developer.xamarin.com/api/type/Xamarin.Interactive.IAgentIntegration/
[repman-api]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[xir]: https://developer.xamarin.com/api/namespace/Xamarin.Interactive.Representations/
[reppr]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[prrep]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.RepresentationProvider.ProvideRepresentations/p/System.Object/
[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[addprovider]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.IRepresentationManager.AddProvider/
[oxyplot]: http://www.oxyplot.org/
