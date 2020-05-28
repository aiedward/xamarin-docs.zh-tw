---
title: 中的 XAML 編譯Xamarin.Forms
description: 本文說明如何使用 Xamarin.Forms xaml 編譯器（XAMLC），選擇性地將 XAML 直接編譯成中繼語言（IL）。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eebbb3040175118320639bcb4482ec77b5c16ac7
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137289"
---
# <a name="xaml-compilation-in-xamarinforms"></a>中的 XAML 編譯Xamarin.Forms

_XAML 可選擇性地使用 XAML 編譯器 (XAMLC) 直接編譯成中繼語言 (IL)。_

XAML 編譯提供一些優點：

- 它會執行 XAML 的編譯時間檢查，以通知使用者是否有任何錯誤。
- 它能免去 XAML 元素一部分的載入和具現化時間。
- 它能透過不再包含 .xaml 檔案來協助減少最終組件的檔案大小。

XAML 編譯預設為停用，以確保回溯相容性。 您可以藉由加入屬性，在元件和類別層級上啟用此功能 [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) 。

下列程式碼範例將示範如何在元件層級啟用 XAML 編譯：

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

在此範例中，將會執行包含在元件內之所有 XAML 的編譯階段檢查，並在編譯時期（而非執行時間）報告 XAML 錯誤。 因此， `assembly` 屬性的前置詞 [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) 會指定將屬性套用至整個元件。

> [!NOTE]
> [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)屬性和 [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions) 列舉位於 `Xamarin.Forms.Xaml` 命名空間中，必須匯入才能使用它們。

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

在此範例中，將會執行類別之 XAML 的編譯階段檢查， `HomePage` 以及在編譯過程中報告的錯誤。

> [!NOTE]
> 可以啟用編譯的系結，以改善應用程式中的資料系結效能 Xamarin.Forms 。 如需詳細資訊，請參閱[編譯繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

## <a name="related-links"></a>相關連結

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
