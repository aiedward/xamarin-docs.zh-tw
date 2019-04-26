---
title: 在 Xamarin.Forms 中的 XAML 命名空間
description: XAML 會用於命名空間宣告 xmlns XML 屬性。 本文介紹 XAML 命名空間語法，並示範如何宣告 XAML 命名空間，以存取的型別。
ms.prod: xamarin
ms.assetid: C03B5553-B199-4A19-9F0F-E5BCE1DB268F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
ms.openlocfilehash: d307c128826775e6d4f7129c79e17522e7e05d6a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61176517"
---
# <a name="xaml-namespaces-in-xamarinforms"></a>在 Xamarin.Forms 中的 XAML 命名空間

_XAML 會用於命名空間宣告 xmlns XML 屬性。本文介紹 XAML 命名空間語法，並示範如何宣告 XAML 命名空間，以存取的型別。_

## <a name="overview"></a>總覽

有兩個都在 XAML 檔案的根項目內的 XAML 命名空間宣告。 第一個定義預設命名空間，如下列 XAML 程式碼範例所示：

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
```

預設命名空間指定沒有前置詞在 XAML 檔中定義的項目參考 Xamarin.Forms 類別，例如[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)。

第二個命名空間宣告會使用`x`前置詞，如下列 XAML 程式碼範例所示：

```csharp
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

XAML 會使用前置詞來宣告非預設的命名空間，以參考在命名空間中的類型時所使用的前置詞。 `x`命名空間宣告會指定項目定義中使用的前置詞的 XAML`x`用於項目和是內建於 XAML （特別是 2009 XAML 規格） 的屬性。

下表列出`x`Xamarin.Forms 所支援的命名空間屬性：

|建構|描述|
|--- |--- |
|`x:Arguments`|指定非預設建構函式或 factory 方法物件宣告的建構函式引數。|
|`x:Class`|指定在 XAML 中定義的類別的命名空間和類別名稱。 類別名稱必須符合類別名稱的程式碼後置檔案。 請注意，此建構只會出現在 XAML 檔案的根項目。|
|`x:DataType`|指定之物件的 XAML 項目和它的子系，將繫結至型別。|
|`x:FactoryMethod`|指定可用來初始化物件的 factory 方法。|
|`x:FieldModifier`|指定 產生具名 XAML 項目欄位的存取層級。|
|`x:Key`|指定的唯一使用者定義索引鍵中每個資源`ResourceDictionary`。 索引鍵的值用來擷取 XAML 資源，和通常做為引數`StaticResource`標記延伸。|
|`x:Name`|指定的 XAML 項目執行階段物件名稱。 設定`x:Name`類似於宣告中的程式碼的變數。|
|`x:TypeArguments`|指定泛型型別的建構函式的泛型型別引數。|

如需詳細資訊`x:DataType`屬性，請參閱[編譯的繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。 如需詳細資訊`x:FieldModifier`屬性，請參閱[欄位修飾詞](~/xamarin-forms/xaml/field-modifiers.md)。 如需詳細資訊`x:Arguments`， `x:FactoryMethod`，並`x:TypeArguments`屬性，請參閱[在 XAML 中傳遞的引數](~/xamarin-forms/xaml/passing-arguments.md)。

> [!NOTE]
> 除了上面所列的命名空間屬性，Xamarin.Forms 也包含可以透過取用的標記延伸`x`命名空間前置詞。 如需詳細資訊，請參閱 <<c0> [ 耗用 XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md)。

在 XAML 中，命名空間宣告繼承自父項目子系項目中。 因此，當定義命名空間中的 XAML 檔案的根項目，該檔案中的所有項目會繼承命名空間宣告。

## <a name="declaring-namespaces-for-types"></a>宣告類型的命名空間

藉由宣告 XAML 命名空間前置詞，與指定的 Common Language Runtime (CLR) 命名空間名稱，並選擇性地組件名稱的命名空間宣告，可以在 XAML 中參考類型。 這被藉由定義下列關鍵字在命名空間宣告內的值：

- **clr 命名空間：** 或是**使用：** -包含要公開為 XAML 項目類型的組件內的 CLR 命名空間宣告。 需要此關鍵字。
- **組件 =** – 包含參考的 CLR 命名空間的組件。 這個值是不含副檔名的組件名稱。 做為參考專案檔，其中包含將參考組件的 XAML 檔案中，您應該建立組件的路徑。 如果可以省略此關鍵字**clr 命名空間**值是在相同的組件參考類型的應用程式程式碼。

請注意，用來分隔字元`clr-namespace`或是`using`語彙基元與其值是冒號，而字元區隔`assembly`語彙基元與其值是等號。 使用兩個語彙基元之間的字元是分號。

下列程式碼範例示範 XAML 命名空間宣告：

```xaml
<ContentPage ... xmlns:local="clr-namespace:HelloWorld" ...>
  ...
</ContentPage>
```

或者，這可以寫成：

```xaml
<ContentPage ... xmlns:local="using:HelloWorld" ...>
  ...
</ContentPage>
```

`local`前置詞是用來表示命名空間內的型別是應用程式的本機慣例。 或者，如果類型不同的組件中，組件名稱應該也定義在命名空間宣告中，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage ... xmlns:behaviors="clr-namespace:Behaviors;assembly=BehaviorsLibrary" ...>
  ...
</ContentPage>
```

當宣告類型，以匯入的命名空間的執行個體，為下列 XAML 程式碼範例所示，然後指定命名空間前置詞：

```xaml
<ListView ...>
  <ListView.Behaviors>
    <behaviors:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

定義自訂命名空間的結構描述的詳細資訊，請參閱[XAML 自訂命名空間的結構描述](custom-namespace-schemas.md)。

## <a name="summary"></a>總結

本文章會導入的 XAML 命名空間語法，並示範如何宣告 XAML 命名空間，以存取的型別。 XAML 使用`xmlns`可以在 XAML 中參考的命名空間宣告和類型的 XML 屬性，藉由宣告具有前置詞的 XAML 命名空間。

## <a name="related-links"></a>相關連結

- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
