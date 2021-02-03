---
title: 中的 XAML 編譯 Xamarin.Forms
description: 本文說明如何選擇性地將 XAML 直接編譯成中繼語言 (IL) 與 Xamarin.Forms xaml 編譯器 (XAMLC) 。
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/03/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8d53f80372062f4830d92213110f01d005f92018
ms.sourcegitcommit: 4f274920d1fe906cda0bf83b8e928b3b50147d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509880"
---
# <a name="xaml-compilation-in-xamarinforms"></a>中的 XAML 編譯 Xamarin.Forms

_XAML 可選擇性地使用 XAML 編譯器 (XAMLC) 直接編譯成中繼語言 (IL)。_

XAML 編譯提供許多優點：

- 它會執行 XAML 的編譯時間檢查，以通知使用者是否有任何錯誤。
- 它能免去 XAML 元素一部分的載入和具現化時間。
- 它能透過不再包含 .xaml 檔案來協助減少最終組件的檔案大小。

架構中的 XAML 編譯預設為停用。 不過，它會在新專案的範本中啟用。 您可以藉 `XamlCompilationOptions.Skip` 由新增屬性，在元件和類別層級上明確地啟用或停用 () [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) 。

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

雖然可以將屬性放在任何地方，但最好的地方是將它放在 **AssemblyInfo.cs** 中。

在此範例中，將會執行包含在元件中的所有 XAML 的編譯時間檢查，而且在編譯時期（而非執行時間）回報 XAML 錯誤。 因此，屬性（attribute）的前置詞（attribute） `assembly` [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) 會指定套用至整個元件的屬性（attribute）。

> [!NOTE]
> [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)屬性和 [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions) 列舉位於 `Xamarin.Forms.Xaml` 命名空間中，必須匯入才能使用。

下列程式碼範例將示範如何在類別層級啟用 XAML 編譯：

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

在此範例中，將會執行類別的 XAML 的編譯時間檢查 `HomePage` ，並在編譯過程中回報錯誤。

> [!NOTE]
> 您可以啟用編譯的系結，以改善應用程式中的資料系結效能 Xamarin.Forms 。 如需詳細資訊，請參閱[編譯繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

## <a name="related-links"></a>相關連結

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
