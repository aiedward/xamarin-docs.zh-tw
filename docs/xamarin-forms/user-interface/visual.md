---
title: Xamarin.Forms 的視覺效果
description: 本文介紹 Xamarin.Forms 視覺效果，在 iOS 和 Android 相同，或大致相同，呈現檢視。
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2018
ms.openlocfilehash: df2351e35817a6468fed236752eea8e5ad11024f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061882"
---
# <a name="xamarinforms-visual"></a>Xamarin.Forms 的視覺效果

![預覽](~/media/shared/preview.png)

_本文介紹 Xamarin.Forms 視覺效果，在 iOS 和 Android 相同，或大致相同，呈現檢視。_

許多開發人員想要在 iOS 和 Android 上完全相同，或基本相同，建立 Xamarin.Forms 應用程式看起來。 Xamarin.Forms 4.0 pre1 包含一種機制，包括實作的視覺外觀，與選擇透過外觀的應用程式的其他轉譯器`Visual`屬性：

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>    
```

實作的視覺外觀的轉譯器則用來轉譯器檢視，而不是預設轉譯器中。 在轉譯器選取項目時，`Visual`屬性檢視的檢查，且包含在轉譯器選取項目程序。 此外，如果`Visual`在執行階段變更的屬性、 指定的轉譯器會重新建立，以及任何子系。

> [!IMPORTANT]
> `Visual`屬性定義於`VisualElement`類別，以檢視繼承`Visual`從其父項的屬性值。 因此，設定`Visual`屬性上的`ContentPage`可確保任何支援的檢視頁面中，將會使用該視覺外觀。 颾魤 ㄛ`Visual`可以覆寫檢視上的屬性。

Xamarin.Forms 4.0 pre1 包含實驗性的視覺外觀，即是 material 轉譯器轉譯器為依據材質的設計。 材質的轉譯器為目前包含在 iOS 和 Android 上的下列檢視：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)

在功能上，材質的轉譯器為沒有不同的預設轉譯器。 不過，目前在實驗，只用於藉由將下列這一行的程式碼，以您`AppDelegate`類別在 iOS 上，或您`MainActivity`類別在 Android 上，然後再呼叫`Forms.Init`:

```csharp
Forms.SetFlags("Visual_Experimental");
```

此外，在 iOS 上，您的平台專案必須具有[Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents/)安裝的 NuGet 套件。 在 Android 上，視覺效果只適用於 API 29，您的平台專案必須使用 v28 支援文件庫，並設定其繼承自資料元件佈景主題，或繼續繼承 AppCompat 佈景主題，同時將一些新的佈景主題屬性新增至主題的佈景主題。 如需詳細資訊，請參閱 <<c0> [ 開始使用資料元件適用於 Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

下列螢幕擷取畫面會顯示包含四個檢視，如需哪些資料轉譯器存在，但使用的預設轉譯器轉譯使用者介面：

[![預設轉譯器](visual-images/default-renderers.png "檢視 使用預設的轉譯器")](visual-images/default-renderers-large.png#lightbox)

下列螢幕擷取畫面顯示相同的使用者介面使用的材料的轉譯器轉譯：

[![Material 轉譯器](visual-images/material-renderers.png "檢視使用資料轉譯器")](visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> Material 轉譯器轉譯的控制項保持原生控制項，與因此還是會有平台，例如字型、 shadows、 色彩和提高權限的區域之間的使用者介面不同。

## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
