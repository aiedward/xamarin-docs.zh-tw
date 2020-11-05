---
title: 'Xamarin.Forms C # 標記'
description: 'C # 標記是一組可簡化的 helper 方法和類別，可簡化以 c # 建立宣告式 Xamarin.Forms 使用者介面的程式。'
ms.prod: xamarin
ms.assetid: D41B9DCD-5C34-4C2F-B177-FC082AB2E9E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e874543b356ffe5dd821c6c2f20e487d79919449
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367916"
---
# <a name="no-locxamarinforms-c-markup"></a>Xamarin.Forms C # 標記

![發行前 API](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)

C # 標記是一組可簡化的 helper 方法和類別，可簡化以 c # 建立宣告式 Xamarin.Forms 使用者介面的程式。 C # 標記提供的流暢 API 可在 `Xamarin.Forms.Markup` 命名空間中使用。

和 XAML 一樣，c # 標記也可讓 UI 標記和 UI 邏輯之間有清楚的分隔。 這可以藉由將 UI 標記和 UI 邏輯分隔成不同的部分類別檔案來達成。 例如，針對登入頁面，UI 標記會位於名為 *LoginPage.cs* 的檔案中，而 ui 邏輯則會在名為 *LoginPage.logic.cs* 的檔案中。

C # 標記可從 Xamarin.Forms 4.6 取得。 不過，它目前是實驗性，而且只能透過將下列程式碼新增至 *App.cs* 檔案來使用：

```csharp
Device.SetFlags(new string[]{ "Markup_Experimental" });
```

> [!NOTE]
> C # 標記可在支援的所有平臺上使用 Xamarin.Forms 。

## <a name="basic-example"></a>基本範例

下列範例示範如何 [`Grid`](xref:Xamarin.Forms.Grid) [`Label`](xref:Xamarin.Forms.Label) [`Entry`](xref:Xamarin.Forms.Entry) 在 c # 中將頁面內容設定為包含和的新：

```csharp
Grid grid = new Grid();

Label label = new Label { Text = "Code: " };
grid.Children.Add(label, 0, 1);

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
grid.Children.Add(entry, 0, 2);
Grid.SetColumnSpan(entry, 2);
entry.SetBinding(Entry.TextProperty, new Binding("RegistrationCode"));

Content = grid;
```

這個範例會建立 [`Grid`](xref:Xamarin.Forms.Grid) 具有子系 [`Label`](xref:Xamarin.Forms.Label) 和物件的物件 [`Entry`](xref:Xamarin.Forms.Entry) 。 會 `Label` 顯示文字，而資料會系結 `Entry` 至 `RegistrationCode` viewmodel 的屬性。 每個子視圖都會設定為顯示在中的特定資料 `Grid` 列，而會 `Entry` 跨越中的所有資料行 `Grid` 。 此外， `Entry` 也會設定的高度，以及其鍵盤、色彩、文字的字型大小，以及它的大小 `Margin` 。 最後， `Page.Content` 屬性會設定為 `Grid` 物件。

C # 標記可讓您使用其流暢的 API 重新撰寫此程式碼：

```csharp
using Xamarin.Forms.Markup;
using static Xamarin.Forms.Markup.GridRowsColumns;

Content = new Grid
{
  Children =
  {
    new Label { Text = "Code:" }
               .Row (BodyRow.CodeHeader) .Column (BodyCol.Header),

    new Entry { Placeholder = "Enter number", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
               .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
               .Bind (nameof(vm.RegistrationCode))
  }
}};
```

這個範例與上一個範例相同，但 c # 標記流暢 API 可簡化以 c # 建立 UI 的程式。

> [!NOTE]
> C # 標記包含設定特定視圖屬性的擴充方法。 這些擴充方法並非用來取代所有的屬性 setter。 相反地，它們是設計用來改善程式碼的可讀性，並且可以與屬性 setter 結合使用。 當屬性存在時，建議一律使用擴充方法，但您可以選擇您偏好的餘額。

## <a name="data-binding"></a>資料繫結

C # 標記包含 `Bind` 擴充方法以及多載，可在 view 可系結屬性和指定的屬性之間建立資料系結。 `Bind`方法會針對所包含的大部分控制項，知道預設的可系結屬性 Xamarin.Forms 。 因此，使用這個方法時，通常不需要指定目標屬性。 不過，您也可以註冊其他控制項的預設可系結屬性：

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.Register(HoverButton.CommandProperty, RadialGauge.ValueProperty);
```

`Bind`方法可以用來系結至任何可系結的屬性：

```csharp
using Xamarin.Forms.Markup;
// ...

new Label { Text = "No data available" }
           .Bind (Label.IsVisibleProperty, nameof(vm.Empty))
```

此外， `BindCommand` 擴充方法可以 `Command` `CommandParameter` 在單一方法呼叫中系結至控制項的預設值和屬性：

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap me" }
              .BindCommand (nameof(vm.TapCommand))
```

根據預設，會系結至系結 `CommandParameter` 內容。 您也可以指定和系結的系結路徑和來源 `Command` `CommandParameter` ：

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap Me" }
              .BindCommand (nameof(vm.TapCommand), vm, nameof(Item.Id))
```

在此範例中，系結內容是 `Item` 實例，因此您不需要指定系結的來源 `Id` `CommandParameter` 。

如果您只需要系結至 `Command` ，就可以傳遞 `null` 給 `parameterPath` 方法的引數 `BindCommand` 。 或者，使用 `Bind` 方法。

您也可以註冊其他控制項的預設值 `Command` 和 `CommandParameter` 屬性：

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.RegisterCommand(
    (CustomViewA.CommandProperty, CustomViewA.CommandParameterProperty),
    (CustomViewB.CommandProperty, CustomViewB.CommandParameterProperty)
);
```

您可以使用和參數，將內嵌轉換器程式碼傳遞至 `Bind` 方法 `convert` `convertBack` ：

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

此外，轉換器程式碼和實例可以與類別重複使用 `FuncConverter` ：

```csharp
using Xamarin.Forms.Markup;
//...

FuncConverter<int, Thickness> treeMarginConverter = new FuncConverter<int, Thickness>(depth => new Thickness(depth * 20, 0, 0, 0));
new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth), converter: treeMarginConverter),
```

`FuncConverter`類別也支援 `CultureInfo` 物件：

```csharp
using Xamarin.Forms.Markup;
//...

cultureAwareConverter = new FuncConverter<DateTimeOffset, string, int>(
    (date, daysToAdd, culture) => date.AddDays(daysToAdd).ToString(culture)
);
```

您也可以將資料系結至以 `Span` 屬性指定的物件 `FormattedText` ：

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

`Command` 和 `CommandParameter` 屬性可以是 `GestureElement` `View` 使用 `BindClickGesture` 、和 `BindSwipeGesture` `BindTapGesture` 擴充方法系結至和類型的資料：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .BindTapGesture (nameof(vm.TapCommand))
```

這個範例會建立指定之型別的手勢辨識器，並將它新增至 [`Label`](xref:Xamarin.Forms.Label) 。 `Bind*Gesture`擴充方法提供的參數與 `BindCommand` 擴充方法相同。 不過，根據預設，並不會系結 `Bind*Gesture` `CommandParameter` `BindCommand` 。

若要初始化具有參數的手勢辨識器，請使用 `ClickGesture` 、、 `PanGesture` `PinchGesture` 、 `SwipeGesture` 和 `TapGesture` 擴充方法：

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .TapGesture (g => g.Bind(nameof(vm.DoubleTapCommand)).NumberOfTapsRequired = 2)
```

因為手勢辨識器是 `BindableObject` ，所以您可以在 `Bind` 初始化時使用和 `BindCommand` 擴充方法。 您也可以使用擴充方法來初始化自訂手勢辨識器類型 `Gesture<TGestureElement, TGestureRecognizer>` 。

## <a name="layout"></a>Layout

C # 標記包含一系列的版面配置延伸方法，可支援在版面配置中定位視圖，以及在視圖中提供內容：

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

針對設計成支援由左至右 (LTR) 或由右至左 (RTL) 流程方向的 c # 標記，上面所列的擴充方法提供最直覺的名稱集合： `Left` 、 `Right` `Top` 和 `Bottom` 。

若要讓正確的左邊和右邊擴充方法集合可供使用，並且在進程中明確地設計標記的流程方向，請包含下列兩個指示詞的其中一個 `using` ： `using Xamarin.Forms.Markup.LeftToRight;` 或 `using Xamarin.Forms.Markup.RightToLeft;` 。

針對設計成支援由左至右和從右至左流動方向的 c # 標記，建議使用下表中的擴充方法，而不是上述其中一個命名空間：

| 類型 | 擴充方法 |
|---|---|
| `Label` | `TextStart`, `TextEnd` |
| `LayoutOptions` | `Start`, `End` <br/> `StartExpand`, `EndExpand` |

### <a name="layout-line-convention"></a>版面配置線條慣例

建議的慣例是以下列順序將視圖的所有版面配置延伸方法放在同一行：

1. 包含此視圖的資料列和資料行。
1. 資料列和資料行中的對齊方式。
1. 視圖周圍的邊界。
1. 視圖大小。
1. 在視圖內填補。
1. 填補中的內容對齊。

下列程式碼顯示此慣例的範例：

```csharp
new Label { }
           .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal () // Layout line
```

遵循此慣例，可讓您快速讀取 c # 標記，並建立視圖內容在 UI 中所在位置的精神圖。

## <a name="grid-rows-and-columns"></a>格線資料列和資料行

列舉可以用來定義 [`Grid`](xref:Xamarin.Forms.Grid) 資料列和資料行，而不是使用數位。 這提供了在新增或移除資料列或資料行時，不需要重新編號的優點。

> [!IMPORTANT]
> 使用列舉來定義資料 [`Grid`](xref:Xamarin.Forms.Grid) 列和資料行需要下列指示詞 `using` ： `using static Xamarin.Forms.Markup.GridRowsColumns;`

下列程式碼顯示如何使用列舉定義和取用資料 [`Grid`](xref:Xamarin.Forms.Grid) 列和資料行的範例：

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

此外，您可以簡潔地定義不具列舉的資料列和資料行：

```csharp
new Grid
{
    RowDefinitions = Rows.Define (Auto, Star, 20),
    ColumnDefinitions = Columns.Define (Auto, Star, 20, 40)
    // ...
}
```

## <a name="fonts"></a>字型

下列清單中的控制項可以呼叫 `FontSize` 、 `Bold` 、 `Italic` 和 `Font` 擴充方法，以設定控制項所顯示文字的外觀：

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

您可以使用擴充方法，將效果附加至控制項 `Effect` ：

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

此外，您也可以使用 `Assign` 擴充方法，從 ui 邏輯檔案) 的 ui 標記之外存取控制項 (：

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Assign (out MyListView)
```

## <a name="styles"></a>樣式

下列範例示範如何使用 c # 標記建立隱含和明確的樣式：

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

藉由將隱含樣式載入至應用程式資源字典，即可使用這些隱含樣式：

```csharp
public App()
{
    Resources = Styles.Implicit;
    // ...
}
```

您可以使用擴充方法來使用明確的樣式 `Style` 。

```csharp
using static CSharpForMarkupExample.Styles;
// ...

new Button { Text = "Tap Me" } .Style (FilledButton),
```

> [!NOTE]
> 除了擴充方法之外，還有、、 `Style` `ApplyToDerivedTypes` `BasedOn` `Add` 和 `CanCascade` 擴充方法。

或者，您也可以建立自己的樣式擴充方法：

```csharp
public static TButton Filled<TButton>(this TButton button) where TButton : Button
{
    button.Buttons(); // Equivalent to Style .BasedOn (Buttons)
    button.TextColor = Color.White;
    button.BackgroundColor = Color.Red;
    return button;
}
```

`Filled`然後可以使用擴充方法，如下所示：

```csharp
new Button { Text = "Tap Me" } .Filled ()
```

## <a name="platform-specifics"></a>平台特定功能

`Invoke`擴充方法可以用來套用平臺特定。 不過，若要避免發生不明確的錯誤，請不要 `using` 直接包含命名空間的指示詞 `Xamarin.Forms.PlatformConfiguration.*Specific` 。 相反地，請建立命名空間別名，並透過別名使用平臺特定：

```csharp
using Xamarin.Forms.Markup;
using PciOS = Xamarin.Forms.PlatformConfiguration.iOSSpecific;
// ...

new ListView { } .Invoke (l => PciOS.ListView.SetGroupHeaderStyle(l, PciOS.GroupHeaderStyle.Grouped))
```

此外，如果您經常使用特定的平臺，您可以在自己的擴充類別中為它們建立流暢的擴充方法：

```csharp
public static T iOSGroupHeaderStyle<T>(this T listView, PciOS.GroupHeaderStyle style) where T : Forms.ListView
{
  PciOS.ListView.SetGroupHeaderStyle(listView, style);
  return listView;
}
```

然後可以使用擴充方法，如下所示：

```csharp
new ListView { } .iOSGroupHeaderStyle(PciOS.GroupHeaderStyle.Grouped)
```

如需有關平臺細節的詳細資訊，請參閱 [Android 平臺功能](~/xamarin-forms/platform/android/index.md)、 [iOS 平臺](~/xamarin-forms/platform/ios/index.md)功能和 [Windows 平臺功能](~/xamarin-forms/platform/windows/index.md)。

## <a name="recommended-convention"></a>建議的慣例

建議的屬性和 helper 方法的順序和群組如下：

- **目的** ：任何屬性或 helper 方法，其值識別控制項的用途 (例如， `Text` `Placeholder` `Assign`) 。
- **其他** ：在同一行或多行上不是版面配置或系結的所有屬性或 helper 方法。
- **版面** 配置：配置是依序排列：資料列和資料行、配置選項、邊界、大小、填補和內容對齊。
- 系 **結：資料** 系結是在方法鏈的結尾執行，每一行有一個系結的屬性。 如果 *預設* 的可系結屬性已系結，它應該位於方法鏈的結尾。

下列程式碼顯示下列慣例的範例：

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

一致地套用此慣例可讓您快速掃描 c # 標記，並建立 UI 版面配置的精神影像。

## <a name="related-links"></a>相關連結

- [CSharpForMarkupDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)
- [Android 平臺功能](~/xamarin-forms/platform/android/index.md)
- [iOS 平臺功能](~/xamarin-forms/platform/ios/index.md)
- [Windows 平臺功能](~/xamarin-forms/platform/windows/index.md)