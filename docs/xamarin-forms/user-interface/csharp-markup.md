---
title: 'Xamarin. 表單 c # 標記'
description: 'C # 標記是一組可加入宣告的流暢 helper 方法和類別，以簡化建立宣告式 Xamarin 的程式。在 c # 中使用表單使用者介面。'
ms.prod: xamarin
ms.assetid: D41B9DCD-5C34-4C2F-B177-FC082AB2E9E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/25/2020
ms.openlocfilehash: fa758b1240570f90ebf8a723401176f6be9dd6ac
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532608"
---
# <a name="xamarinforms-c-markup"></a>Xamarin. 表單 c # 標記

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)

C # 標記是一組可加入宣告的流暢 helper 方法和類別，以簡化建立宣告式 Xamarin 的程式。在 c # 中使用表單使用者介面。 C # 標記提供的 Fluent API 可在`Xamarin.Forms.Markup`命名空間中取得。

就像使用 XAML 一樣，c # 標記可讓 UI 標記和 UI 邏輯之間有清楚的分隔。 將 UI 標記和 UI 邏輯分隔成不同的部分類別檔案，即可達成此目的。 例如，登入頁面上的 UI 標記會位於名為*LoginPage.cs*的檔案中，而 ui 邏輯則會位於名為*LoginPage.logic.cs*的檔案中。

C # 標記可從 Xamarin. 表單4.6 中取得。 不過，它目前是實驗性，而且只能透過將下列程式程式碼新增至*App.cs*檔案來使用：

```csharp
Device.SetFlags(new string[]{ "Markup_Experimental" });
```

> [!NOTE]
> C # 標記適用于 Xamarin 所支援的所有平臺。

## <a name="basic-example"></a>基本範例

下列範例示範如何在 c [`Entry`](xref:Xamarin.Forms.Entry) # 中建立物件：

```csharp
Entry entry = new Entry
{
    Placeholder = "Enter number",
    Keyboard = Keyboard.Numeric,
    BackgroundColor = Color.AliceBlue,
    TextColor = Color.Black,
    FontSize = 15,
    HeightRequest = 44,
    Margin = fieldMargin
};
entry.SetBinding(Entry.TextProperty, new Binding("RegistrationCode", BindingMode.TwoWay));
grid.Children.Add(entry, 0, 2);
Grid.SetColumnSpan(entry, 2);
```

這個範例會使用[`Entry`](xref:Xamarin.Forms.Entry) `TwoWay`系結，建立資料系`RegistrationCode`結至 viewmodel 之屬性的物件。 它會設定為出現在的特定資料列中[`Grid`](xref:Xamarin.Forms.Grid)，並跨越中的所有資料行`Grid`。 此外， `Entry`也會設定的高度，以及其文字的字型大小和`Margin`。

C # 標記可讓此程式碼使用其 Fluent API 重新撰寫：

```csharp
using Xamarin.Forms.Markup;
using static Xamarin.Forms.Markup.GridRowsColumns;

Entry entry = new Entry { Placeholder = "Enter number", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
                         .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
                         .Bind (nameof(vm.RegistrationCode), BindingMode.TwoWay);
```

這個範例與前一個範例相同，但 c # 標記 Fluent API 可簡化在 c # 中建立 UI 的程式。

> [!NOTE]
> C # 標記包含設定特定視圖屬性的擴充方法。 這些擴充方法並不是用來取代所有的屬性 setter。 相反地，它們是設計來改善程式碼可讀性，並可與屬性 setter 搭配使用。 建議一律使用擴充方法，當屬性存在時，您可以選擇慣用的餘額。

## <a name="data-binding"></a>資料繫結

C # 標記包含`Bind`擴充方法以及多載，可在 view 可系結屬性和指定的屬性之間建立資料系結。 `Bind`方法知道 Xamarin 所包含的大多數控制項的預設可系結屬性。 因此，使用這個方法時，通常不需要指定目標屬性。 不過，您也可以註冊其他控制項的預設可系結屬性：

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.Register(HoverButton.CommandProperty, RadialGauge.ValueProperty);
```

方法`Bind`可以用來系結至任何可系結的屬性：

```csharp
using Xamarin.Forms.Markup;
// ...

new Label { Text = "No data available" }
           .Bind (Label.IsVisibleProperty, nameof(vm.Empty))
```

此外， `BindCommand`擴充方法可以在單一方法呼叫中系結至`Command`控制項`CommandParameter`的預設和屬性：

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap me" }
              .BindCommand (nameof(vm.TapCommand))
```

根據預設， `CommandParameter`會系結至系結內容。 您也可以指定`Command`和`CommandParameter`系結的系結路徑和來源：

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap Me" }
              .BindCommand (nameof(vm.TapCommand), vm, nameof(Item.Id))
```

在此範例中，系結內容是`Item`一個實例，因此您不需要指定系結的來源`Id` `CommandParameter` 。

如果您只需要系結至`Command`，則可以傳遞`null`至`BindCommand`方法`parameterPath`的引數。 或者，請使用`Bind`方法。

您也可以註冊其他控制項`Command`的`CommandParameter`預設和屬性：

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.RegisterCommand(
    (CustomViewA.CommandProperty, CustomViewA.CommandParameterProperty),
    (CustomViewB.CommandProperty, CustomViewB.CommandParameterProperty)
);
```

您可以使用`convert`和`convertBack`參數，將內嵌`Bind`轉換器程式碼傳遞至方法：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth),
                  convert: (int depth) => new Thickness(depth * 20, 0, 0, 0))
```

也支援型別安全轉換器參數：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { }
           .Bind (nameof(viewModel.Text),
                  convert: (string text, int repeat) => string.Concat(Enumerable.Repeat(text, repeat)))
```

此外，轉換器程式碼和實例可以與`FuncConverter`類別一起重複使用：

```csharp
using Xamarin.Forms.Markup;
//...

FuncConverter<int, Thickness> treeMarginConverter = new FuncConverter<int, Thickness>(depth => new Thickness(depth * 20, 0, 0, 0));
new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth), converter: treeMarginConverter),
```

`FuncConverter`類別也支援`CultureInfo`物件：

```csharp
using Xamarin.Forms.Markup;
//...

cultureAwareConverter = new FuncConverter<DateTimeOffset, string, int>(
    (date, daysToAdd, culture) => date.AddDays(daysToAdd).ToString(culture)
);
```

您也可以將資料系結至`Span`使用`FormattedText`屬性所指定的物件：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { } .FormattedText (
    new Span { Text = "Built with " },
    new Span { TextColor = Color.Blue, TextDecorations = TextDecorations.Underline }
              .BindTapGesture (nameof(vm.ContinueToCSharpForMarkupCommand))
              .Bind (nameof(vm.Title))
)
```

### <a name="gesture-recognizers"></a>手勢辨識器

`Command`和`CommandParameter`屬性可以使用`GestureElement` 、和`BindTapGesture`擴充方法`View` ， `BindSwipeGesture`系結`BindClickGesture`至和類型的資料：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .BindTapGesture (nameof(vm.TapCommand))
```

這個範例會建立指定類型的手勢辨識器，並將其新增至[`Label`](xref:Xamarin.Forms.Label)。 `Bind*Gesture`擴充方法提供與`BindCommand`擴充方法相同的參數。 不過，根據預設`Bind*Gesture` ，並不`CommandParameter`會系`BindCommand`結，而是。

若要初始化具有參數的手勢辨識器， `ClickGesture`請`PanGesture`使用`PinchGesture`、 `SwipeGesture`、、 `TapGesture`和擴充方法：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .TapGesture (g => g.Bind(nameof(vm.DoubleTapCommand)).NumberOfTapsRequired = 2)
```

因為手勢辨識器是`BindableObject`，所以您可以在初始化`Bind`時`BindCommand`使用和擴充方法。 您也可以使用`Gesture<TGestureElement, TGestureRecognizer>`擴充方法來初始化自訂手勢辨識器類型。

## <a name="layout"></a>配置

C # 標記包含一系列的版面配置擴充方法，可支援版面配置中的定位視圖，以及 views 中的內容：

| 類型 | 擴充方法 |
|---|---|
| `FlexLayout` | `AlignSelf`, `Basis`, `Grow`, `Menu`, `Order`, `Shrink` |
| `Grid` | `Row`, `Column`, `RowSpan`, `ColumnSpan` |
| `Label` | `TextLeft`, `TextCenterHorizontal`, `TextRight` <br/> `TextTop`, `TextCenterVertical`, `TextBottom` <br/> `TextCenter` |
| `Layout` | `Padding`, `Paddings` |
| `LayoutOptions` | `Left`, `CenterHorizontal`, `FillHorizontal`, `Right` <br/> `LeftExpand`, `CenterExpandHorizontal`, `FillExpandHorizontal`, `RightExpand` <br /> `Top`, `Bottom`, `CenterVertical`, `FillVertical` <br /> `TopExpand`, `BottomExpand`, `CenterExpandVertical`, `FillExpandVertical` <br /> `Center`, `Fill`, `CenterExpand`, `FillExpand` |
| `View` | `Margin`, `Margins` |
| `VisualElement` | `Height`, `Width`, `MinHeight`, `MinWidth`, `Size`, `MinSize` |

### <a name="left-to-right-and-right-to-left-support"></a>由左至右和由右至左支援

針對支援由左至右（LTR）或由右至左（RTL）流程方向設計的 c # 標記，以上所列的擴充方法提供最具直覺性的名稱集合`Left`：、 `Right` `Top`和。 `Bottom`

若要提供正確的左和右擴充方法集合，並在程式中明確地決定標記的設計流程方向，請包含下列兩個`using`指示詞的其中一個： `using Xamarin.Forms.Markup.LeftToRight;`、或`using Xamarin.Forms.Markup.RightToLeft;`。

針對設計為支援由左至右和由右至左流動方向的 c # 標記，建議使用下表中的擴充方法，而不是上述任何一個命名空間：

| 類型 | 擴充方法 |
|---|---|
| `Label` | `TextStart`, `TextEnd` |
| `LayoutOptions` | `Start`, `End` <br/> `StartExpand`, `EndExpand` |

### <a name="layout-line-convention"></a>版面配置線條慣例

建議的慣例是以下列順序將視圖的所有版面配置延伸方法放在同一行：

1. 包含此視圖的資料列和資料行。
1. 資料列和資料行內的對齊。
1. 視圖周圍的邊界。
1. 視圖大小。
1. 在視圖內填補。
1. 填補內的內容對齊。

下列程式碼顯示此慣例的範例：

```csharp
new Label { }
           .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal () // Layout line
```

遵循慣例，可讓您快速閱讀 c # 標記，並建立視圖內容在 UI 中的思維圖。

## <a name="grid-rows-and-columns"></a>格線資料列和資料行

列舉可以用來定義[`Grid`](xref:Xamarin.Forms.Grid)資料列和資料行，而不是使用數位。 這可提供在加入或移除資料列或資料行時，不需要重新編號的優點。

> [!IMPORTANT]
> 使用[`Grid`](xref:Xamarin.Forms.Grid)列舉定義資料列和資料行需要`using`下列指示詞：`using static Xamarin.Forms.Markup.GridRowsColumns;`

下列程式碼顯示如何使用列舉定義和取用資料列[`Grid`](xref:Xamarin.Forms.Grid)和資料行的範例：

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms.Markup.LeftToRight;
using static Xamarin.Forms.Markup.GridRowsColumns;
// ...

enum BodyRow
{
    Prompt,
    CodeHeader,
    CodeEntry,
    Button
}

enum BodyCol
{
    FieldLabel,
    FieldValidation
}

View Build() => new Grid
{
    RowDefinitions = Rows.Define(
        (BodyRow.Prompt    , 170 ),
        (BodyRow.CodeHeader, 75  ),
        (BodyRow.CodeEntry , Auto),
        (BodyRow.Button    , Auto)
    ),

    ColumnDefinitions = Columns.Define(
        (BodyCol.FieldLabel     , 160 ),
        (BodyCol.FieldValidation, Star)
    ),

    Children =
    {
        new Label { LineBreakMode = LineBreakMode.WordWrap } .Font (15) .Bold ()
                   .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal ()
                   .Bind (nameof(vm.RegistrationPrompt)),

        new Label { Text = "Registration code" } .Bold ()
                   .Row (BodyRow.CodeHeader) .Column(BodyCol.FieldLabel) .Bottom () .Margin (fieldNameMargin),

        new Label { } .Italic ()
                   .Row (BodyRow.CodeHeader) .Column (BodyCol.FieldValidation) .Right () .Bottom () .Margin (fieldNameMargin)
                   .Bind (nameof(vm.RegistrationCodeValidationMessage)),

        new Entry { Placeholder = "E.g. 123456", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
                   .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
                   .Bind (nameof(vm.RegistrationCode), BindingMode.TwoWay),

        new Button { Text = "Verify" } .Style (FilledButton)
                    .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (PageMarginSize)
                    .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode))
                    .Bind (nameof(vm.VerifyRegistrationCodeCommand)),
    }
};
```

此外，您可以簡潔地定義不含列舉的資料列和資料行：

```csharp
new Grid
{
    RowDefinitions = Rows.Define (Auto, Star, 20),
    ColumnDefinitions = Columns.Define (Auto, Star, 20, 40)
    // ...
}
```

## <a name="fonts"></a>字型

下列清單`FontSize`中的控制項可以呼叫、、 `Bold` `Italic`和`Font`擴充方法，以設定控制項所顯示之文字的外觀：

- `Button`
- `DatePicker`
- `Editor`
- `Entry`
- `Label`
- `Picker`
- `SearchBar`
- `Span`
- `TimePicker`

## <a name="effects"></a>效果

您可以使用`Effect`擴充方法，將效果附加至控制項：

```csharp
using Xamarin.Forms.Markup;
// ...

new Button { Text = "Tap Me" }
            .Effects (new ButtonMixedCaps())
```

## <a name="logic-integration"></a>邏輯整合

`Invoke`擴充方法可以用來在 c # 標記中執行內嵌程式碼：

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Invoke (l => l.ItemTapped += OnListViewItemTapped)
```

此外，您可以使用`Assign`擴充方法，從 ui 標記外部存取控制項（在 ui 邏輯檔案中）：

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Assign (out MyListView)
```

## <a name="styles"></a>樣式

下列範例顯示如何使用 c # 標記來建立隱含和明確的樣式：

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms;

namespace CSharpForMarkupDemos
{
    public static class Styles
    {
        static Style<Button> buttons, filledButton;
        static Style<Label> labels;
        static Style<Span> link;

        #region Implicit styles

        public static ResourceDictionary Implicit => new ResourceDictionary { Buttons, Labels };

        public static Style<Button> Buttons => buttons ?? (buttons = new Style<Button>(
            (Button.HeightRequestProperty, 44),
            (Button.FontSizeProperty, 13),
            (Button.HorizontalOptionsProperty, LayoutOptions.Center),
            (Button.VerticalOptionsProperty, LayoutOptions.Center)
        ));

        public static Style<Label> Labels => labels ?? (labels = new Style<Label>(
            (Label.FontSizeProperty, 13),
            (Label.TextColorProperty, Color.Black)
        ));

        #endregion Implicit styles

        #region Explicit styles

        public static Style<Button> FilledButton => filledButton ?? (filledButton = new Style<Button>(
            (Button.TextColorProperty, Color.White),
            (Button.BackgroundColorProperty, Color.FromHex("#1976D2")),
            (Button.CornerRadiusProperty, 5)
        )).BasedOn(Buttons);

        public static Style<Span> Link => link ?? (link = new Style<Span>(
            (Span.TextColorProperty, Color.Blue),
            (Span.TextDecorationsProperty, TextDecorations.Underline)
        ));

        #endregion Explicit styles
    }
}
```

藉由將隱含樣式載入應用程式資源字典，即可加以取用：

```csharp
public App()
{
    Resources = Styles.Implicit;
    // ...
}
```

明確樣式可以與`Style`擴充方法一起使用。

```csharp
using static CSharpForMarkupExample.Styles;
// ...

new Button { Text = "Tap Me" } .Style (FilledButton),
```

> [!NOTE]
> 除了`Style` `ApplyToDerivedTypes`擴充方法之外，也有、 `BasedOn` `Add`、和`CanCascade`擴充方法。

或者，您也可以建立自己的樣式延伸方法：

```csharp
public static TButton Filled<TButton>(this TButton button) where TButton : Button
{
    button.Buttons(); // Equivalent to Style .BasedOn (Buttons)
    button.TextColor = Color.White;
    button.BackgroundColor = Color.Red;
    return button;
}
```

接著`Filled` ，您可以使用擴充方法，如下所示：

```csharp
new Button { Text = "Tap Me" } .Filled ()
```

## <a name="platform-specifics"></a>平台特定功能

`Invoke`擴充方法可以用來套用平臺特定。 不過，若要避免不明確的`using` `Xamarin.Forms.PlatformConfiguration.*Specific`錯誤，請不要直接包含命名空間的指示詞。 相反地，請建立命名空間別名，並透過別名使用平臺特定：

```csharp
using Xamarin.Forms.Markup;
using PciOS = Xamarin.Forms.PlatformConfiguration.iOSSpecific;
// ...

new ListView { } .Invoke (l => PciOS.ListView.SetGroupHeaderStyle(l, PciOS.GroupHeaderStyle.Grouped))
```

此外，如果您經常使用特定的平臺細節，您可以在自己的擴充類別中為它們建立流暢的擴充方法：

```csharp
public static T iOSGroupHeaderStyle<T>(this T listView, PciOS.GroupHeaderStyle style) where T : Forms.ListView
{
  PciOS.ListView.SetGroupHeaderStyle(listView, style);
  return listView;
}
```

接著，您可以使用擴充方法，如下所示：

```csharp
new ListView { } .iOSGroupHeaderStyle(PciOS.GroupHeaderStyle.Grouped)
```

如需平臺細節的詳細資訊，請參閱[Android 平臺功能](~/xamarin-forms/platform/android/index.md)、 [iOS 平臺功能](~/xamarin-forms/platform/ios/index.md)和[Windows 平臺功能](~/xamarin-forms/platform/windows/index.md)。

## <a name="recommended-convention"></a>建議慣例

建議的順序和屬性和 helper 方法的群組如下：

- **目的**：任何屬性或 helper 方法，其值可識別控制項的目的（例如`Text`， `Placeholder`、、`Assign`）。
- **其他**：不是版面配置或系結的所有屬性或 helper 方法，位於同一行或多行。
- **版面**配置：版面配置會向外排序：資料列和資料行、版面配置選項、邊界、大小、填補和內容對齊。
- **Bind**：資料系結會在方法鏈的結尾執行，每一行有一個系結屬性。 如果 [*預設*可系結] 屬性已系結，則應該位於方法鏈的結尾。

下列程式碼顯示遵循此慣例的範例：

```csharp
new Button { Text = "Verify" /* purpose */ } .Style (FilledButton) // other
            .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (10) // layout
            .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode)) // bind
            .Bind (nameof(vm.VerifyRegistrationCodeCommand)), // bind default

new Label { }
           .Assign (out animatedMessageLabel) // purpose
           .Invoke (label => label.SizeChanged += MessageLabel_SizeChanged) // other
           .Row (BodyRow.Message) .ColumnSpan (All<BodyCol>()) // layout
           .Bind (nameof(vm.Message)), // bind default
```

一致地套用此慣例可讓您快速掃描 c # 標記，並建立 UI 版面配置的心理影像。

## <a name="related-links"></a>相關連結

- [CSharpForMarkupDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)
- [Android 平臺功能](~/xamarin-forms/platform/android/index.md)
- [iOS 平臺功能](~/xamarin-forms/platform/ios/index.md)
- [Windows 平臺功能](~/xamarin-forms/platform/windows/index.md)
