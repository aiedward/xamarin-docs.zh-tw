---
title: 在 Xamarin.Forms 中 XAML 編譯
description: 這篇文章說明如何 XAML 可以選擇性地編譯成中繼語言 (IL)，使用 Xamarin.Forms XAML 編譯器 (XAMLC) 直接。
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/02/2018
ms.openlocfilehash: 4635857fc850b2985f988f8c20ff854e487f79ed
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403399"
---
# <a name="xaml-compilation-in-xamarinforms"></a>在 Xamarin.Forms 中 XAML 編譯

_XAML 可選擇性地編譯成中繼語言 (IL)，使用 XAML 編譯器 (XAMLC) 直接。_

XAMLC 提供許多優點：

- 它會執行 XAML 的編譯時間檢查，以通知使用者是否有任何錯誤。
- 它能免去 XAML 元素一部分的載入和具現化時間。
- 它能透過不再包含 .xaml 檔案來協助減少最終組件的檔案大小。

為確保回溯相容性，預設會停用 XAMLC。 可以在組件和類別層級中啟用新增`XamlCompilation`屬性。

下列程式碼範例示範如何讓 XAMLC 組件層級：

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

在此範例中，編譯時期檢查的組件內所包含的所有 XAML 將會執行，但在編譯時期，而不被執行階段所報告的 XAML 發生錯誤。 因此，`assembly`前置詞`XamlCompilation`屬性指定的屬性會套用至整個組件。

下列程式碼範例示範如何讓 XAMLC 類別層級：

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
> `XamlCompilation`屬性和`XamlCompilationOptions`列舉型別位於`Xamarin.Forms.Xaml`命名空間，必須將它們匯入。


## <a name="related-links"></a>相關連結

- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
