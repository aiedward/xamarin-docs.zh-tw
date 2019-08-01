---
title: Xamarin.Forms 編輯器
description: 這篇文章說明如何使用 Xamarin.Forms 編輯器控制項接受應用程式中的多行文字輸入。
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: 1f9cd0acd201f124f4a4577fec23dce52aee627d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657513"
---
# <a name="xamarinforms-editor"></a>Xamarin.Forms 編輯器

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_多行文字輸入_

[ `Editor` ](xref:Xamarin.Forms.Editor)控制項用來接受多行的輸入。 本文涵蓋：

- **[自訂](#customization)** &ndash;鍵盤和色彩選項。
- **[互動](#interactivity)** &ndash;可以接聽的以提供互動功能的事件。

## <a name="customization"></a>自訂

### <a name="setting-and-reading-text"></a>設定和讀取文字

[ `Editor` ](xref:Xamarin.Forms.Editor)，例如其他文字呈現的檢視，公開`Text`屬性。 這個屬性可用來設定和讀取所呈現的文字`Editor`。 下列範例示範如何設定`Text`在 XAML 中的屬性：

```xaml
<Editor Text="I am an Editor" />
```

在 C# 中：

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

若要讀取的文字，存取`Text`C# 中的屬性：

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>設定預留位置文字

[ `Editor` ](xref:Xamarin.Forms.Editor)可以設定為顯示預留位置文字，它不會儲存使用者輸入時。 這可以藉由設定[ `Placeholder` ](xref:Xamarin.Forms.Editor.Placeholder)屬性設`string`，和通常用來表示的內容適用於類型`Editor`。 此外，控制的預留位置文字的色彩，藉由設定[ `PlaceholderColor` ](xref:Xamarin.Forms.Editor.PlaceholderColor)屬性設[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>防止文字輸入

藉[`Editor`](xref:Xamarin.Forms.Editor)由`false`將屬性 (具有的預設值) 設定為`true`, 可防止使用者修改中的文字: `IsReadOnly`

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> 屬性不會改變的視覺外觀[`Editor`](xref:Xamarin.Forms.Editor), 不同`IsEnabled`于也會將的視覺外觀`Editor`變更為灰色的屬性。 `IsReadonly`

### <a name="limiting-input-length"></a>限制的輸入的長度

[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)屬性可以用來限制所允許的輸入的長度[ `Editor` ](xref:Xamarin.Forms.Editor)。 這個屬性應該設定為正整數：

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

A [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)屬性值為 0 表示將會允許任何輸入項目，並針對`int.MaxValue`，這是預設值[ `Editor` ](xref:Xamarin.Forms.Editor)，指出已沒有可以輸入的字元數的有效限制。

### <a name="auto-sizing-an-editor"></a>自動調整大小的編輯器

[ `Editor` ](xref:Xamarin.Forms.Editor)可以對藉由設定其內容自動調整大小[ `Editor.AutoSize` ](xref:Xamarin.Forms.Editor.AutoSize)屬性設[ `TextChanges` ](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)，這是值為[ `EditoAutoSizeOption` ](xref:Xamarin.Forms.EditorAutoSizeOption)列舉型別。 這個列舉型別有兩個值：

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) 指出，自動調整大小已停用，而且是預設值。
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) 表示已啟用 自動調整大小。

這可在程式碼，如下所示：

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

當啟用自動調整大小、 高度[ `Editor` ](xref:Xamarin.Forms.Editor)使用者會將它填入文字，並為使用者刪除文字，將會減少高度時，會增加。

> [!NOTE]
> [ `Editor` ](xref:Xamarin.Forms.Editor)會未自動調整大小 if [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性已設定。

### <a name="customizing-the-keyboard"></a>自訂鍵盤

當使用者互動時，會顯示鍵盤[ `Editor` ](xref:Xamarin.Forms.Editor)可以透過程式設計方式設定[ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard)屬性，其中一個的下列屬性[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)類別：

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) – 用於 傳簡訊到和 emoji 所在有用的地方。
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) - 預設鍵盤。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) - 輸入電子郵件地址時使用。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) - 輸入數字時使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) - 輸入文字時使用，不指定任何 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) - 輸入電話號碼時使用。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) - 輸入文字時使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) - 用於輸入檔案路徑與網址。

這可在 XAML，如下所示：

```xaml
<Editor Keyboard="Chat" />
```

對等的 C# 程式碼是：

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

每個鍵盤的範例可在我們[配方](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry)存放庫。

[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)類別也有[ `Create` ](xref:Xamarin.Forms.Keyboard.Create*)可用來指定大小寫、 拼字檢查和建議的行為，以自訂鍵盤的 factory 方法。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 列舉值會被指定為方法的引數，並傳回自訂的 `Keyboard`。 `KeyboardFlags` 列舉包含下列值：

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) - 未新增任何功能至鍵盤。
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) - 表示每個輸入句子中第一個字的首字母會自動變成大寫。
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) - 表示將在輸入的文字上執行拼字檢查。
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) - 表示將在輸入的文字上提供文字自動完成。
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) - 表示每個字的第一個字母會自動變成大寫。
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) - 表示每個字元會自動變成大寫。
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) - 表示不會執行自動大小寫。
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) - 表示將在輸入的文字上將執行拼字檢查、文字自動完成和句子大小寫。

下列 XAML 程式碼範例示範如何自訂預設 [`Keyboard`](xref:Xamarin.Forms.Keyboard)，以提供文字自動完成，並將每個輸入的字元變成大寫：

```xaml
<Editor>
    <Editor.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Editor.Keyboard>
</Editor>
```

對等的 C# 程式碼是：

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>啟用和停用拼字檢查

          [
          `IsSpellCheckEnabled`
          ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性會控制是否拼字檢查 已啟用。 根據預設，此屬性設為`true`。 當使用者輸入文字，則會指出拼字錯誤。

不過，某些文字項目的情況下，輸入使用者名稱，例如拼字檢查提供的負數的體驗，因此應該停用藉由設定[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設`false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 當[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設定為`false`，並在不使用自訂的鍵盤，將會停用原生的拼字檢查工具。 不過，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)具有已設定，以停用拼字檢查，例如[ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat)，則`IsSpellCheckEnabled`屬性會被忽略。 因此，無法使用屬性來啟用拼字檢查`Keyboard`，明確地停用。

### <a name="enabling-and-disabling-text-prediction"></a>啟用和停用文字預測

`IsTextPredictionEnabled`屬性控制是否啟用文字預測和自動文字更正。 根據預設，此屬性設為`true`。 當使用者輸入文字，則會看到文字預測。

不過, 對於某些文字輸入案例, 例如輸入使用者名稱、文字預測和自動文字更正會提供負面的體驗, 而且應該藉由將`IsTextPredictionEnabled`屬性設定為來`false`停用:

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 當屬性設定為`false`, 且未使用自訂鍵盤時, 會停用文字預測和自動文字更正。 `IsTextPredictionEnabled` 不過，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)已設定該停用文字預測`IsTextPredictionEnabled`屬性會被忽略。 因此，無法使用屬性來啟用文字預測`Keyboard`，明確地停用。

### <a name="colors"></a>色彩

`Editor` 可以設定為使用自訂的背景色彩透過`BackgroundColor`屬性。 特別是為了確保會在每個平台上的可用色彩。 因為每個平台都有不同的預設值的文字色彩，您可能需要設定每個平台自訂的背景色彩。 請參閱[使用平台調整](~/xamarin-forms/platform/device.md)如需有關最佳化每個平台的 UI。

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "使用 BackgroundColor 範例編輯器")

請確定您選擇的背景和文字色彩在每個平台上使用，而且不會遮住任何預留位置文字。

## <a name="interactivity"></a>互動

`Editor` 會公開兩個事件：

- [TextChanged](xref:Xamarin.Forms.Editor.TextChanged) &ndash;在編輯器中的文字變更時引發。 變更之前和之後，請提供的文字。
- [已完成](xref:Xamarin.Forms.Editor.Completed)&ndash;使用者已結束輸入鍵盤上按 return 鍵時引發。

> [!NOTE]
> 繼承自的[`Entry`](xref:Xamarin.Forms.Entry) [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)類別也具有和[`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)事件。 [`VisualElement`](xref:Xamarin.Forms.VisualElement)

### <a name="completed"></a>已完成

`Completed`事件用來做出回應的互動完成`Editor`。 `Completed`當使用者在鍵盤上輸入 return 鍵 (或按 UWP 上的 Tab 鍵) 來結束具有欄位的輸入時, 會引發。 事件處理常式是泛用事件處理常式，採取寄件者和`EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

已完成的事件可以訂閱此發行集的程式碼和 XAML 中：

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

`TextChanged`事件用來對欄位的內容中的變更做出回應。

`TextChanged` 每當引發`Text`的`Editor`變更。 事件處理常式可接受的執行個體`TextChangedEventArgs`。 `TextChangedEventArgs` 提供的舊和新值的存取權`Editor``Text`透過`OldTextValue`和`NewTextValue`屬性：

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

已完成的事件可以訂閱此發行集的程式碼和 XAML 中：

程式碼：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```


## <a name="related-links"></a>相關連結

- [文字 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [編輯器 API](xref:Xamarin.Forms.Editor)
