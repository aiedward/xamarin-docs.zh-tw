---
title: Xamarin.Forms 中的 XAML 編譯
description: 本文說明如何 XAML 可以選擇性地編譯成中繼語言 (IL)，使用 Xamarin.Forms XAML 編譯器 (XAMLC) 直接。
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/21/2016
ms.openlocfilehash: 0128fecebe9f6ba8f55e965a8fa65787d03d9ded
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245741"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Xamarin.Forms 中的 XAML 編譯

_可以選擇性地編譯 XAML，直接在 XAML 編譯器 (XAMLC) 的中繼語言 (IL)。_

XAMLC 提供許多優點：

- 它會執行 XAML 的編譯時間檢查，以通知使用者是否有任何錯誤。
- 它能免去 XAML 元素一部分的載入和具現化時間。
- 它能透過不再包含 .xaml 檔案來協助減少最終組件的檔案大小。

為確保回溯相容性，預設會停用 XAMLC。 可以在組件和類別層級中啟用加`XamlCompilation`屬性。

下列程式碼範例示範如何啟用 XAMLC 組件層級：

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

此範例中，在編譯時期檢查的所有 XAML 內含`PhotoApp`命名空間將會執行，但在編譯時期，而不是執行階段所報告的 XAML 發生錯誤。
`assembly`前置詞`XamlCompilation`屬性會指定屬性會套用至整個組件。

下列程式碼範例示範如何啟用 XAMLC 類別層級：

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

在此範例中，在編譯時間檢查適用於 XAML 的`HomePage`類別將會執行，而錯誤報告為編譯處理程序的一部分。

> [!NOTE]
> `XamlCompilation`屬性和`XamlCompilationOptions`列舉位於`Xamarin.Forms.Xaml`命名空間，必須先將它們匯入。


## <a name="related-links"></a>相關連結

- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
