---
title: 建立 Xamarin.Forms 視覺效果轉譯器
description: Xamarin.Forms 的視覺效果可讓您選擇性地套用到 VisualElement 物件，而不需要子類別 Xamarin.Forms 控制項的轉譯器。
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 3c614bcd0044a0aa4a698c830d2f2af5aba6c65a
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557510"
---
# <a name="xamarinforms-visual"></a>Xamarin.Forms 的視覺效果

Xamarin.Forms 的視覺效果可讓轉譯器會選擇性地套用到`VisualElement`物件，而不需要子類別 Xamarin.Forms 控制項。 任何指定的轉譯器`VisualType`一部分`ExportRendererAttribute`將用來轉譯器檢視，而不是預設轉譯器。 在轉譯器選取項目時，`Visual`屬性檢視的檢查，且包含在轉譯器選取項目程序。

> [!IMPORTANT]
> 目前`Visual`呈現的控制項，但這會在未來版本中變更之後，無法變更。

Xamarin.Forms 包含材料設計為基礎的視覺外觀。 如需詳細資訊，請參閱 < [Xamarin.Forms 資料視覺化](material-visual.md)。

## <a name="create-a-visual"></a>建立視覺效果

在您跨平台程式庫中，建立衍生自類型`IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

## <a name="register-the-visual-type"></a>註冊的視覺效果類型

建立轉譯器所需的檢視，並註冊的視覺效果類型，做為平台的一部分`ExportRenderAttribute`:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ContentPage), typeof(CustomPageRenderer), new[] { typeof(CustomVisual) })]
namespace MyApp.Android
{
    public class CustomPageRenderer : PageRenderer
    {
        ...
    }
}
```

## <a name="consume-the-visual"></a>使用視覺效果

應用程式可以選擇使用自訂的視覺效果，透過`Visual`屬性：

```xaml
<ContentPage ...
             Visual="Custom">
    ...
</ContentPage>
```

對等的 C# 程式碼是：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = new CustomVisual();
```

## <a name="register-a-name-for-a-visual"></a>註冊名稱，以視覺效果

不同的視覺化程式庫之間可能有衝突，或可能是您只想要以不同的名稱來參考至視覺效果。 在此案例中，您可以使用註冊指定的視覺效果的不同名稱的組件屬性：

```csharp
[assembly: Visual("MyMaterialName", typeof(VisualMarker.MaterialVisual))]
```

透過已註冊的名稱可以使用自訂視覺效果：

```xaml
<ContentPage ...
             Visual="MyMaterialName">
    ...
</ContentPage>
````

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 資料視覺效果](material-visual.md)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
