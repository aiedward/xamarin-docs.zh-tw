---
title: Getting Started with Xamarin Workbooks SDK
description: 本文件說明如何開始使用 Xamarin 活頁簿 SDK，可用來開發 Xamarin 活頁簿的整合。
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 5800e98acbff147735ae4a6125979a4b47be2367
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382715"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Getting Started with Xamarin Workbooks SDK

本文件提供的快速指南，開始使用開發 Xamarin 活頁簿的整合。 此工作的大部分會使用穩定的 Xamarin 活頁簿，但**活頁簿 1.3 中才支援載入透過 NuGet 套件的整合**，在撰寫本文時的 alpha 色板。

## <a name="general-overview"></a>一般概觀

Xamarin Workbooks 整合是小型的程式庫，可使用[ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK 整合與 Xamarin Workbooks 和 Inspector 代理程式，以提供增強的體驗。

有 3 個主要的步驟，以開始使用開發整合 — 我們這裡會加以說明。

## <a name="creating-the-integration-project"></a>建立整合專案

整合程式庫是最適合開發多平台程式庫。 因為您想要在所有可用的代理程式、 過去和未來上提供最佳的整合，您會想要選擇一組廣泛支援程式庫。 我們建議使用 「 可攜式程式庫 」 範本，針對最廣泛的支援：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![可攜式程式庫範本 Visual Studio for Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![可攜式程式庫範本中的 Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

在 Visual Studio 中，您會想要確定您為您的可攜式程式庫中選取 下列目標平台：

[![可攜式程式庫的平台的 Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

一旦您建立的程式庫專案，將參考加入我們`Xamarin.Workbooks.Integration`NuGet 程式庫透過 NuGet 套件管理員。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![NuGet Visual Studio for Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

您會想要刪除空白會為您專案的一部分建立的類別，您將不再需要它的。 完成這些步驟之後，您即可開始建置您的整合。

## <a name="building-an-integration"></a>建置整合

我們將建置簡單的整合。 我們真的很喜歡色彩綠色，因此我們將新增綠色，為每個物件的表示法。 首先，建立新的類別，稱為`SampleIntegration`，並使它實作成為我們[ `IAgentIntegration` ] [ integration-type]介面：

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

我們想要做為新增[表示](~/tools/workbooks/sdk/representations.md)的每個物件都是綠色的色彩。 我們這樣使用表示提供者。 提供者繼承自[ `RepresentationProvider` ] [ reppr]類別，如我們，我們只需要覆寫[ `ProvideRepresentations` ] [ prrep]:

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

我們會傳回[ `Color` ] [ color]、 預先建置表示型別，在我們的 SDK。
您會注意到傳回的型別`IEnumerable<object>`&mdash;一個表示提供者可能會傳回許多物件的表示法 ！ 因此請務必不要任何猜測哪些物件會傳遞至您的每個物件，稱為所有表示提供者。

最後一個步驟是實際向代理程式註冊我們的提供者，然後告訴活頁簿哪裡可以找到我們的整合型別。 若要註冊提供者，新增下列程式碼`IntegrateWith`方法中的`SampleIntegration`我們稍早建立的類別：

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

設定整合型別是透過整個組件的屬性。 您可以將它放在您 AssemblyInfo.cs、 或您的整合類型，以便與相同的類別：

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

在開發期間，您可能會發現它更方便地使用[`AddProvider`多載][ addprovider]上`RepresentationManager`，可讓您註冊簡單的回呼，以提供的活頁簿內的表示法然後將該程式碼插入您`RepresentationProvider`一旦您完成實作。 轉譯的範例[ `OxyPlot` ] [ oxyplot] `PlotModel`可能看起來像這樣：

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> 這些 Api 可讓您快速啟動並執行，但我們不建議只使用這些傳送整個整合&mdash;它們提供非常幾乎無法控制您的型別用戶端的處理方式。

表示法，註冊，您的整合已可送交 ！

## <a name="shipping-your-integration"></a>傳送您的整合

寄送您的整合，您必須將它新增至 NuGet 套件。
您可以將它與您現有的程式庫的 NuGet，或如果您要建立新的封裝，您可以使用此範本的.nuspec 檔案做為起點。
您必須填妥您的整合的相關區段。 最重要的部分是所有為您的整合檔案必須位於`xamarin.interactive`目錄的封裝根目錄。 這可讓我們輕鬆找出所有相關的檔案，為您的整合，不論您使用現有的封裝，或建立新的帳戶。

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

一旦您已建立.nuspec 檔案，您可以封裝您的 NuGet 就像這樣：

```csharp
nuget pack MyIntegration.nuspec
```

然後將它以發行[NuGet][nugetorg]。 一旦有，就能夠從任何活頁簿中參考它，並觀看實作示範。 在以下的螢幕擷取畫面，我們已封裝的範例整合，我們在這份文件中建置和安裝 NuGet 套件的活頁簿中：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![使用整合的活頁簿](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![使用整合的活頁簿](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

請注意，您沒有看到任何`#r`指示詞，或任何項目來初始化整合 — 活頁簿已經處理所有的一切，在幕後 ！

## <a name="next-steps"></a>後續步驟

查看其他文件如需有關移動的項目組成的 SDK，以及我們[範例整合](~/tools/workbooks/samples/index.md)的其他項目，您可以從您的整合，例如提供自訂執行中的 JavaScript活頁簿的用戶端。

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
