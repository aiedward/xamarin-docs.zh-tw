---
title: 在 Xamarin.Forms 中 XAML 編譯
description: 這篇文章說明如何 XAML 可以選擇性地編譯成中繼語言 (IL)，使用 Xamarin.Forms XAML 編譯器 (XAMLC) 直接。
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: 9567f3ad8d748a94a03cd1c86254072d4ba3bbdc
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563520"
---
# <a name="xaml-compilation-in-xamarinforms"></a>在 Xamarin.Forms 中 XAML 編譯

_XAML 可選擇性地編譯成中繼語言 (IL)，使用 XAML 編譯器 (XAMLC) 直接。_

XAML 編譯會提供許多優點：

- 它會執行 XAML 的編譯時間檢查，以通知使用者是否有任何錯誤。
- 它能免去 XAML 元素一部分的載入和具現化時間。
- 它能透過不再包含 .xaml 檔案來協助減少最終組件的檔案大小。

若要確保回溯相容性的預設會停用 XAML 編譯。 可以在組件和類別層級中啟用加[ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)屬性。

下列程式碼範例示範如何啟用 XAML 編譯組件層級：

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

在此範例中，編譯時期檢查的組件內所包含的所有 XAML 將會執行，但在編譯時期，而不被執行階段所報告的 XAML 發生錯誤。 因此，`assembly`前置詞[ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)屬性指定的屬性會套用至整個組件。

> [!NOTE]
> [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)屬性並[ `XamlCompilationOptions` ](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)列舉位於`Xamarin.Forms.Xaml`命名空間，必須將它們匯入。

下列程式碼範例示範如何在類別層級啟用 XAML 編譯：

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

在此範例中，在編譯時間檢查針對 XAML 的`HomePage`類別將會執行，而且在編譯程序中報告錯誤。

> [!NOTE]
> 可以啟用已編譯的繫結，以改善在 Xamarin.Forms 應用程式中的資料繫結效能。 如需詳細資訊，請參閱 <<c0> [ 編譯的繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

## <a name="related-links"></a>相關連結

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
