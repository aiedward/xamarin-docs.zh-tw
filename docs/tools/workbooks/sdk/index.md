---
title: 使用 Xamarin Workbooks SDK 的消費者入門
description: 本檔說明如何開始使用 Xamarin Workbooks SDK，其可用於開發 Xamarin Workbooks 的整合。
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 8e3dc65f9f615ff893f3526d53d99da25045c794
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283958"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>使用 Xamarin Workbooks SDK 的消費者入門

本檔提供開始開發 Xamarin Workbooks 整合的快速入門手冊。 這大部分都適用于穩定的 Xamarin Workbooks，但只有在撰寫時，Alpha 色板的活頁**簿1.3 中才支援透過 NuGet 封裝載入**整合。

## <a name="general-overview"></a>一般總覽

Xamarin Workbooks 整合是小型程式庫，會使用[ `Xamarin.Workbooks.Integrations` NuGet][nuget] SDK 來與 Xamarin Workbooks 和偵測器代理程式整合，以提供增強的體驗。

開始開發整合有3個主要步驟，我們將在此概述。

## <a name="creating-the-integration-project"></a>建立整合專案

整合程式庫最好是以多平臺程式庫的方式開發。 因為您想要在過去和未來的所有可用代理程式上提供最佳整合，所以您會想要選擇一組廣泛支援的程式庫。 我們建議使用「便攜媒體櫃」範本提供最廣泛的支援：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![便攜媒體櫃範本 Visual Studio for Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![便攜媒體櫃範本 Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

在 Visual Studio 中，您會想要確定為便攜媒體櫃選取下列目標平臺：

[![可移植的程式庫平臺 Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

建立程式庫專案之後，請透過 nuget 套件管理員， `Xamarin.Workbooks.Integration`將參考新增至我們的 nuget 程式庫。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![NuGet Visual Studio for Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

您會想要在專案中刪除為您建立的空白類別，但您不需要這麼做。 完成這些步驟之後，您就可以開始建立您的整合。

## <a name="building-an-integration"></a>建立整合

我們將建立簡單的整合。 我們很喜歡綠色的色彩，所以我們會將綠色色彩新增為每個物件的標記法。 首先，建立名`SampleIntegration`為的新類別，並讓它`IAgentIntegration`執行介面：

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

我們要做的是，為每個綠色色彩的物件加上一個[標記法](~/tools/workbooks/sdk/representations.md)。 我們會使用標記法提供者來執行此動作。 提供者繼承自`RepresentationProvider`類別-就我們而言，我們只需要覆`ProvideRepresentations`寫：

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

我們在 SDK 中`Color`傳回一個預先建立的表示型別。
您會注意到這裡的傳回型別是`IEnumerable<object>` &mdash;一個表示提供者可能會傳回物件的許多標記法！ 所有的表示提供者都會針對每個物件呼叫，因此請務必不要對要傳遞給您的物件做出任何假設。

最後一個步驟是實際向代理程式註冊我們的提供者，並告訴活頁簿何處尋找我們的整合類型。 若要註冊提供者，請將此程式`IntegrateWith`代碼新增至`SampleIntegration`我們稍早建立的類別中的方法：

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

設定整合類型是透過整個元件的屬性來完成。 您可以將此設為您的 AssemblyInfo.cs，或在與整合類型相同的類別中，以方便您：

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

在開發期間，您可能會發現在上`AddProvider` `RepresentationManager`使用多載會比較方便，這可讓您註冊簡單的回呼來提供活頁簿內的標記法，然後`RepresentationProvider`將該程式碼移至您的執行一次您已經完成。 [`OxyPlot`][oxyplot] 呈現`PlotModel`的範例可能如下所示：

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> 這些 api 可讓您快速啟動並執行，但我們不建議您只使用&mdash;它們來傳送完整的整合，而只會提供用戶端處理類型的方式非常少的控制。

註冊標記法之後，您的整合就已準備好寄送！

## <a name="shipping-your-integration"></a>運送您的整合

若要交付您的整合，您必須將它新增至 NuGet 套件。
您可以使用現有的程式庫 NuGet 來寄送它，或者，如果您要建立新的套件，您可以使用這個 nuspec 檔案作為起點。
您必須填寫與整合相關的章節。 最重要的部分是，整合的所有檔案都必須位於封裝根目錄的`xamarin.interactive`目錄中。 這可讓我們輕鬆地找到整合的所有相關檔案，不論您是使用現有的套件或建立新的封裝。

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

建立 nuspec 檔案之後，您可以如下所示來封裝 NuGet：

```csharp
nuget pack MyIntegration.nuspec
```

然後將它發佈至[NuGet][nugetorg]。 一旦出現之後，您就可以從任何活頁簿參考它，並查看其運作方式。 在下面的螢幕擷取畫面中，我們已封裝我們在本檔中建立的範例整合，並在活頁簿中安裝 NuGet 套件：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![具有整合的活頁簿](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![具有整合的活頁簿](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

請注意，您不會`#r`看到任何指示詞或任何專案來初始化整合-活頁簿已在幕後處理所有的動作！

## <a name="next-steps"></a>後續步驟

如需有關構成 SDK 之移動部分的詳細資訊，請參閱我們的其他檔，以及我們的[範例](~/tools/workbooks/samples/index.md)整合，以瞭解您可以從整合執行的其他專案，例如提供在活頁簿用戶端中執行的自訂 JavaScript。

[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[oxyplot]: http://www.oxyplot.org/
