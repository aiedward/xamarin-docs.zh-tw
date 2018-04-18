---
title: Xamarin 活頁簿中的表示法
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: a5593ac902bfd2478cd8587aeef7e4a3926627dd
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="representations-in-xamarin-workbooks"></a>Xamarin 活頁簿中的表示法

## <a name="representations"></a>表示法

在活頁簿或偵測器工作階段中執行，而且產生的結果 （例如方法傳回的值或運算式的結果） 的程式碼是透過管線來處理表示代理程式中。 除了基本型別，例如整數、 以外的所有物件會反映以產生圖形互動的成員，並且會透過提供用戶端可能會造成更豐富的替代表示程序。 （包括循環和無限的可列舉值） 安全地支援的任何大小和深度物件因延遲和互動式反映和遠端執行功能。

Xamarin 活頁簿會提供所有代理程式和用戶端，它提供豐富的轉譯結果的一些常見的類型。 [`Color`][xir-color] 是一種這類類型，例如在 iOS 上，代理程式負責將`CGColor`或`UIColor`物件至`Xamarin.Interactive.Representations.Color`物件。

共通的表示，除了整合 SDK 會提供應用程式開發介面來序列化自訂的表示法，在代理程式和轉譯的用戶端中的表示法。

## <a name="external-representations"></a>外部表示

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] 可讓您註冊[ `RepresentationProvider` ] [ repp]，其整合必須實作以將任意的物件轉換為無從驗證的形式來呈現。 必須實作這些無從驗證的表單[ `ISerializableObject` ] [ serobj]介面。

實作`ISerializableObject`介面加入序列化方法，以精確控制如何序列化物件。 `Serialize`方法必須要有哪些屬性要序列化，以及最終的名稱將會為開發人員將完全指定。 查看`Person`物件存放至我們 [`KitchenSink`範例] [範例]，我們可以看到此運作方式：

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

如果我們想要提供超集或從原始物件的屬性子集，我們可以透過達成`Serialize`。 例如，我們可能會執行類似下面的提供預先計算`Age`屬性`Person`:

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
> 產生的 Api`ISerializableObject`物件直接不需要由`RepresentationProvider`。 如果您想要顯示的物件**不** `ISerializableObject`，您會想要處理包裝在您`RepresentationProvider`。

### <a name="rendering-a-representation"></a>轉譯表示法

轉譯器會實作在 JavaScript 中，而且可以透過下列方式表示物件的 JavaScript 版本存取`ISerializableObject`。 也會有 JavaScript 複製`$type`字串表示.NET 型別名稱的屬性。

我們建議使用適用於用戶端整合程式碼，當然會編譯成 JavaScript 的香草 TypeScript。 無論如何，SDK 會提供[類型][ typings]其可以直接參考 TypeScript 或只參考以手動方式如果撰寫香草最好使用 JavaScript。

轉譯的主要整合點是`xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

在這裡，`PersonRenderer`實作`Renderer`介面。 請參閱[類型][ typings]如需詳細資訊。

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
