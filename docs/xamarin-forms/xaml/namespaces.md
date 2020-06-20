---
title: 中的 XAML 命名空間Xamarin.Forms
description: XAML 會針對命名空間宣告使用 xmlns XML 屬性。 本文介紹 XAML 命名空間語法，並示範如何宣告 XAML 命名空間來存取型別。
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7f35342134767ccdadfab086bfa14f6b610b325d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84130373"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>中的 XAML 命名空間Xamarin.Forms

_XAML 會針對命名空間宣告使用 xmlns XML 屬性。本文介紹 XAML 命名空間語法，並示範如何宣告 XAML 命名空間來存取型別。_

## <a name="overview"></a>概觀

XAML 檔案的根項目中一律會有兩個 XAML 命名空間宣告。 第一個會定義預設命名空間，如下列 XAML 程式碼範例所示：

```xaml
xmlns="http://xamarin.com/schemas/2014/forms"
```

預設命名空間會指定 XAML 檔案中不含前置詞的元素會參考 Xamarin.Forms 類別，例如 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。

第二個命名空間宣告會使用 `x` 前置詞，如下列 XAML 程式碼範例所示：

```xaml
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML 會使用前置詞來宣告非預設的命名空間，並在參考命名空間內的類型時使用前置詞。 `x`命名空間宣告會指定在 xaml 中使用前置詞定義的專案，用於 xaml 內建的 `x` 元素和屬性（特別是 2009 XAML 規格）。

下表概述支援的 `x` 命名空間屬性 Xamarin.Forms ：

|建構|描述|
|--- |--- |
|`x:Arguments`|指定非預設的函式或 factory 方法物件宣告的函式引數。|
|`x:Class`|指定 XAML 中定義之類別的命名空間和類別名稱。 類別名稱必須符合程式碼後置檔案的類別名稱。 請注意，此結構只能出現在 XAML 檔案的根項目中。|
|`x:DataType`|指定 XAML 專案和其子系將系結至之物件的型別。|
|`x:FactoryMethod`|指定可以用來初始化物件的 factory 方法。|
|`x:FieldModifier`|為已命名的 XAML 元素指定產生之欄位的存取層級。|
|`x:Key`|為中的每個資源指定唯一的使用者定義索引鍵 `ResourceDictionary` 。 索引鍵的值是用來抓取 XAML 資源，而且通常會當做標記延伸的引數使用 `StaticResource` 。|
|`x:Name`|指定 XAML 元素的執行時間物件名稱。 設定 `x:Name` 類似于在程式碼中宣告變數。|
|`x:TypeArguments`|指定泛型型別之函式的泛型型別引數。|

如需屬性的詳細資訊 `x:DataType` ，請參閱[編譯](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)的系結。 如需屬性的詳細資訊 `x:FieldModifier` ，請參閱[欄位](~/xamarin-forms/xaml/field-modifiers.md)修飾詞。 如需和屬性的詳細資訊 `x:Arguments` `x:FactoryMethod` ，請參閱[在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)。 如需屬性的詳細資訊 `x:TypeArguments` ，請參閱[使用 Xamarin.Forms XAML 中的泛型](generics.md)。

> [!NOTE]
> 除了上面所列的命名空間屬性之外， Xamarin.Forms 還包含可以透過 `x` 命名空間前置詞使用的標記延伸。 如需詳細資訊，請參閱[使用 XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md)。

在 XAML 中，命名空間宣告繼承自父元素到子專案。 因此，在 XAML 檔案的根項目中定義命名空間時，該檔案內的所有元素都會繼承命名空間宣告。

## <a name="declaring-namespaces-for-types"></a>宣告類型的命名空間

您可以在 XAML 中參考型別，方法是宣告具有前置詞的 XAML 命名空間，並使用命名空間宣告來指定 Common Language Runtime （CLR）命名空間名稱，並選擇性地輸入元件名稱。 在命名空間宣告內定義下列關鍵字的值，即可達成此目的：

- **clr 命名空間：** 或**使用：** –在元件中宣告的 clr 命名空間，其中包含要公開為 XAML 元素的類型。 此為必要關鍵字。
- **assembly =** –包含所參考之 CLR 命名空間的元件。 這個值是元件的名稱，不含副檔名。 元件的路徑應該建立為專案檔中的參考，其中包含將會參考元件的 XAML 檔案。 如果**clr 命名空間**值與參考類型的應用程式程式碼位於相同的元件內，則可以省略這個關鍵字。

請注意，用來分隔 `clr-namespace` 或 `using` 標記與其值的字元是冒號，而用來分隔標記與其值的字元 `assembly` 是等號。 要在兩個標記之間使用的字元是分號。

下列程式碼範例顯示 XAML 命名空間宣告：

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

或者，這可以撰寫為：

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

`local`前置詞是用來指出命名空間中的類型在應用程式的本機上的慣例。 或者，如果類型是在不同的元件中，則元件名稱也應該在命名空間宣告中定義，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

然後在從匯入的命名空間宣告類型的實例時，指定命名空間前置詞，如下列 XAML 程式碼範例所示：

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

如需定義自訂命名空間架構的詳細資訊，請參閱[XAML 自訂命名空間](custom-namespace-schemas.md)架構。

## <a name="summary"></a>摘要

本文介紹了 XAML 命名空間語法，並示範如何宣告 XAML 命名空間來存取型別。 XAML 會使用 `xmlns` XML 屬性進行命名空間宣告，而且可以藉由宣告具有前置詞的 xaml 命名空間，在 xaml 中參考類型。

## <a name="related-links"></a>相關連結

- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
- [XAML 中的泛型與Xamarin.Forms](generics.md)
