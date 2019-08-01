---
title: Xamarin 活頁簿中的表示法
description: 本檔描述 Xamarin Workbooks 表示管線, 這可針對傳回值的任何程式碼, 呈現豐富的結果。
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: b61452fc21d81f427249825decee4f119c50abf0
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511503"
---
# <a name="representations-in-xamarin-workbooks"></a>Xamarin 活頁簿中的表示法

## <a name="representations"></a>表示

在活頁簿或偵測器會話中, 執行並產生結果的程式碼 (例如, 傳回值的方法或運算式的結果), 會透過代理程式中的表示管線來處理。 所有物件 (除了整數之類的基本專案) 都會反映出來, 以產生互動式成員圖形, 並會進行處理, 以提供用戶端可以呈現更豐富的替代標記法。 由於延遲和互動式反映和遠端處理, 會安全地支援任何大小和深度的物件 (包括迴圈和無限可列舉值)。

Xamarin Workbooks 提供一些通用於所有代理程式和用戶端的類型, 允許豐富呈現結果。 `Color`是這類類型的其中一個範例, 例如, 在 iOS 上, 代理程式會負責將`CGColor`或`UIColor`物件轉換成`Xamarin.Interactive.Representations.Color`物件。

除了常見的標記法, 整合 SDK 還提供 Api 來序列化代理程式中的自訂表格示法, 以及用戶端中的呈現標記法。

## <a name="external-representations"></a>外部標記法

`Xamarin.Interactive.IAgent.RepresentationManager`提供註冊的`RepresentationProvider`能力, 其整合必須執行, 才能從任意物件轉換成不可知的格式來呈現。 這些不可知的`ISerializableObject`形式必須執行介面。

`ISerializableObject`執行介面會加入序列化方法, 以精確控制物件的序列化方式。 `Serialize`方法預期開發人員會確切指定要序列化的屬性, 以及最終的名稱。 查看 [ `Person` `KitchenSink` sample] [sample] 中的物件, 我們可以看到它的運作方式:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

如果我們想要從原始物件提供超集合或屬性子集, 我們可以使用`Serialize`來執行此動作。 例如, 我們可能會執行如下所示的內容, 以在上`Age` `Person`提供預先計算的屬性:

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
> 直接產生`ISerializableObject`物件的 api 不需要`RepresentationProvider`由處理。 如果您想要顯示的物件**不** `ISerializableObject`是, 您會想要處理將它`RepresentationProvider`包裝在中的。

### <a name="rendering-a-representation"></a>呈現標記法

轉譯器會在 JavaScript 中執行, 並可存取透過所表示`ISerializableObject`之物件的 JavaScript 版本。 JavaScript 複本也會有一個`$type`字串屬性, 指出 .net 型別名稱。

我們建議您在用戶端整合程式碼中使用 TypeScript, 這當然會編譯成 vanilla JavaScript。 不論是哪種方式, SDK 都會提供[typings][typings] , 可直接由 TypeScript 參考, 或只是在偏好撰寫 vanilla JavaScript 時手動參考。

呈現的主要整合點為`xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

在這裡`PersonRenderer` , `Renderer`會執行介面。 如需詳細資訊, 請參閱[typings][typings] 。

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
