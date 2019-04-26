---
title: Xamarin 活頁簿中的表示法
description: 本文件說明 Xamarin Workbooks 表示管線中，可讓任何傳回值的程式碼的豐富結果的呈現。
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: d9aafbe13e06875b6577a4d2308e419932fd1589
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382109"
---
# <a name="representations-in-xamarin-workbooks"></a>Xamarin 活頁簿中的表示法

## <a name="representations"></a>表示法

活頁簿或偵測器工作階段中，程式碼執行時，所產生的結果 （例如方法傳回值或運算式的結果） 會透過管線來處理表示在代理程式。 基本型別，例如整數、 除外的所有物件會反映產生互動式成員圖形，並且會瀏覽處理序能夠提供替代用戶端可以呈現更豐富的表示法。 （包括週期和無限的可列舉值） 因延遲和互動式的反映和遠端處理而安全地支援任何大小和深度的物件。

Xamarin 活頁簿會提供所有代理程式和用戶端以進行豐富的轉譯結果的幾個常見的類型。 [`Color`][xir-color] 是這種類型的其中一個範例，例如在 iOS 上，則代理程式便會負責將轉換`CGColor`或是`UIColor`物件到`Xamarin.Interactive.Representations.Color`物件。

除了常見的表示法，整合 SDK 會提供 Api，用來序列化自訂的表示法，在代理程式和轉譯的用戶端中的表示法。

## <a name="external-representations"></a>外部的表示法

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] 讓您能夠註冊[ `RepresentationProvider` ] [ repp]，整合必須實作從任意的物件轉換為無從驗證的表單來呈現。 必須實作這些無從驗證的 forms [ `ISerializableObject` ] [ serobj]介面。

實作`ISerializableObject`介面將精確地控制如何序列化物件的序列化方法。 `Serialize`方法預期開發人員會完全指定哪一個屬性，則為序列化，和最後一個名稱會是。 看看`Person`物件中我們 [`KitchenSink`範例] [範例]，我們可以查看其運作方式：

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

如果我們想要提供的超集或從原始物件的屬性子集，我們可以透過達成`Serialize`。 比方說，我們可能會執行類似下面的提供預先計算`Age`屬性上的`Person`:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; set; }
  public DateTime DateOfBirth { get; set; }

  // <snip>

  void ISerializableObject.Serialize (ObjectSerializer serializer)
  {
    serializer.Property (nameof (Name), Name);
    serializer.Property (nameof (DateOfBirth), DateOfBirth);

    // Let's pre-compute an Age property that's the person's age in years,
    // so we don't have to compute it in the renderer.
    var age = (DateTime.MinValue + (DateTime.Now - DateOfBirth)).Year - 1;
    serializer.Property ("Age", age)
  }
}
```

> [!NOTE]
> 產生的 Api`ISerializableObject`物件直接不需要由`RepresentationProvider`。 您想要顯示的物件是否**未** `ISerializableObject`，您會想要處理包裝在您`RepresentationProvider`。

### <a name="rendering-a-representation"></a>轉譯表示法

轉譯器會在 JavaScript 中實作，而可存取的物件，表示透過 JavaScript 版本`ISerializableObject`。 也會有 JavaScript 複製`$type`字串表示的.NET 型別名稱的屬性。

我們建議針對用戶端整合程式碼，當然會編譯成標準 JavaScript 般地使用 TypeScript。 無論如何，SDK 會提供[typings][typings]可以直接由 TypeScript 參考或直接參考手動如果撰寫香草，最好使用 JavaScript。

轉譯的主要的整合點是`xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

在這裡，`PersonRenderer`實作`Renderer`介面。 請參閱[typings][typings]如需詳細資訊。

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
