---
title: Xamarin.Forms 編輯器
description: 本文說明如何使用 Xamarin.Forms 編輯器控制項接受應用程式中的多行文字輸入。
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0a0517f0eac552f18a88d84bafba2e7e1e1f2631
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "93367552"
---
# <a name="xamarinforms-editor"></a>Xamarin.Forms 編輯器

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-text)

[`Editor`](xref:Xamarin.Forms.Editor)控制項可用來接受多行輸入。

## <a name="set-and-read-text"></a>設定和讀取文字

[`Editor`](xref:Xamarin.Forms.Editor)和其他文字呈現視圖一樣，也會公開 `Text` 屬性。 這個屬性可以用來設定和讀取所呈現的文字 `Editor` 。 下列範例將示範如何 `Text` 在 XAML 中設定屬性：

```xaml
<Editor Text="I am an Editor" />
```

在 C# 中：

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

若要讀取文字，請使用 `Text` c # 存取屬性：

```csharp
var text = MyEditor.Text;
```

## <a name="set-placeholder-text"></a>設定預留位置文字

[`Editor`](xref:Xamarin.Forms.Editor)可以設定為在未儲存使用者輸入時顯示預留位置文字。 這是藉由將 [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 屬性設定為，來完成 `string` ，而且通常用來指出適用于的內容類型 `Editor` 。 此外，您可以藉由將屬性設定為，來控制預留位置文字色彩 [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

## <a name="prevent-text-entry"></a>防止文字輸入

藉 [`Editor`](xref:Xamarin.Forms.Editor) 由將 `IsReadOnly` 預設值為的屬性（property）設定 `false` 為，可以防止使用者修改中的文字 `true` 。

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> `IsReadonly`屬性不會改變的視覺外觀 [`Editor`](xref:Xamarin.Forms.Editor) ，與也會將的 `IsEnabled` 視覺外觀變更為灰色的屬性不同 `Editor` 。

## <a name="transform-text"></a>轉換文字

[`Editor`](xref:Xamarin.Forms.Editor)可以藉 `Text` 由將 `TextTransform` 屬性設定為列舉值，來轉換其儲存在屬性中的文字大小寫 `TextTransform` 。 此列舉有四個值：

- `None` 表示不轉換文字。
- `Default` 指出將使用平臺的預設行為。 此為 `TextTransform` 屬性的預設值。
- `Lowercase` 指出文字將轉換成小寫。
- `Uppercase` 指出文字將轉換成大寫。

下列範例顯示如何將文字轉換成大寫：

```xaml
<Editor Text="This text will be displayed in uppercase."
        TextTransform="Uppercase" />
```

對等的 C# 程式碼為：

```csharp
Editor editor = new Editor
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="limit-input-length"></a>限制輸入長度

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)屬性可以用來限制允許的輸入長度 [`Editor`](xref:Xamarin.Forms.Editor) 。 這個屬性應該設定為正整數：

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

如果 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 屬性值為0，表示不允許任何輸入，而且的值 `int.MaxValue` （是的預設值 [`Editor`](xref:Xamarin.Forms.Editor) ）表示可能輸入的字元數沒有有效的限制。

## <a name="character-spacing"></a>字元間距

您可以藉 [`Editor`](xref:Xamarin.Forms.Editor) 由將 `Editor.CharacterSpacing` 屬性設定為值，將字元間距套用至 `double` ：

```xaml
<Editor ...
        CharacterSpacing="10" />
```

對等的 C# 程式碼為：

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

結果是，所顯示文字中的字元會與 [`Editor`](xref:Xamarin.Forms.Editor) `CharacterSpacing` 裝置無關的單位分隔開來。

> [!NOTE]
> `CharacterSpacing`屬性值會套用至和屬性所顯示的文字 `Text` `Placeholder` 。

## <a name="auto-size-an-editor"></a>自動調整編輯器的大小

您 [`Editor`](xref:Xamarin.Forms.Editor) 可以藉由將 [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) 屬性設定為 [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) （列舉的值），對其內容進行自動調整大小 [`EditorAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) 。 此列舉有兩個值：

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) 指出自動調整大小已停用，而且是預設值。
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) 表示已啟用自動調整大小。

這可在程式碼中完成，如下所示：

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

啟用自動調整大小時， [`Editor`](xref:Xamarin.Forms.Editor) 當使用者以文字填滿時，的高度將會增加，而且當使用者刪除文字時，高度將會降低。

> [!NOTE]
> [`Editor`](xref:Xamarin.Forms.Editor)如果已設定屬性，將不會自動調整 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 。

## <a name="customize-the-keyboard"></a>自訂鍵盤

當使用者與互動時顯示的鍵盤 [`Editor`](xref:Xamarin.Forms.Editor) ，可以透過屬性以程式設計的方式設定，以程式設計方式 [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 從類別設定為下列其中一個屬性 [`Keyboard`](xref:Xamarin.Forms.Keyboard) ：

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) –用於傳送和表情的位置。
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) –預設鍵盤。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) –輸入電子郵件地址時使用。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) –輸入數位時使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) –輸入文字時使用，不含任何 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 指定的。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) –輸入電話號碼時使用。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) –輸入文字時使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) –用來 & 網址輸入檔案路徑。

執行下列工作即可用 XAML 來達成這點：

```xaml
<Editor Keyboard="Chat" />
```

對等的 C# 程式碼為：

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

您可以在我們的 [食譜](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) 存放庫中找到每個鍵盤的範例。

此 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 類別也有一個 [`Create`](xref:Xamarin.Forms.Keyboard.Create*) factory 方法，可透過指定大小寫、拼字檢查和建議行為來自訂鍵盤。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 列舉值會指定為方法的引數，並會傳回自訂的 `Keyboard` 。 `KeyboardFlags` 列舉包含下列值：

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) –沒有任何功能會新增至鍵盤。
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) –表示每個輸入之句子第一個單字的第一個字母會自動大寫。
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) –表示將在輸入的文字上執行拼字檢查。
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) –表示輸入的文字上將會提供單字自動完成。
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) –表示每個單字的第一個字母都會自動大寫。
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) -表示每個字元會自動變成大寫。
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) –表示不會進行自動大小寫。
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) –表示輸入的文字上會出現拼字檢查、word 完成和句子大小寫。

下列 XAML 程式碼範例示範如何自訂預設值 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 以提供文字完成，並讓每個輸入的字元變成大寫：

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

對等的 C# 程式碼為：

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

## <a name="enable-and-disable-spell-checking"></a>啟用和停用拼寫檢查

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性控制是否已啟用拼寫檢查。 根據預設，屬性會設定為 `true` 。 當使用者輸入文字時，就會指出拼寫錯誤。

不過，對於某些文字輸入案例（例如輸入使用者名稱），拼寫檢查會提供負面的體驗，因此應該將 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性設定為來停用 `false` ：

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 當 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性設定為 `false` ，且未使用自訂鍵盤時，將會停用原生拼寫檢查。 但是，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 設定為停用拼寫檢查（例如）， [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) 則 `IsSpellCheckEnabled` 會忽略屬性。 因此，不能使用屬性來啟用明確停用之的拼寫檢查 `Keyboard` 。

## <a name="enable-and-disable-text-prediction"></a>啟用和停用文字預測

`IsTextPredictionEnabled`屬性控制文字預測和自動文字更正是否已啟用。 根據預設，屬性會設定為 `true` 。 當使用者輸入文字時，會顯示文字預測。

不過，在某些文字輸入案例中（例如輸入使用者名稱、文字預測和自動文字更正）會提供負面的體驗，而且應該藉由將 `IsTextPredictionEnabled` 屬性設定為來停用 `false` ：

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 當 `IsTextPredictionEnabled` 屬性設定為 `false` ，且未使用自訂鍵盤時，會停用文字預測和自動文字修正。 但是，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 設定停用文字預測，則 `IsTextPredictionEnabled` 會忽略屬性。 因此，屬性無法用來啟用明確停用之的文字預測 `Keyboard` 。

## <a name="colors"></a>色彩

`Editor` 可以設定為透過屬性使用自訂背景色彩 `BackgroundColor` 。 請特別小心，以確保每個平臺都能使用這些色彩。 由於每個平臺的文字色彩都有不同的預設值，因此您可能需要為每個平臺設定自訂背景色彩。 如需針對每個平臺優化 UI 的詳細資訊，請參閱 [使用平臺](~/xamarin-forms/platform/device.md) 調整。

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

![使用 BackgroundColor 的編輯器範例](editor-images/textbackgroundcolor.png)

確定您所選擇的背景和文字色彩可在每個平臺上使用，且不會隱匿任何預留位置文字。

## <a name="events-and-interactivity"></a>活動和互動性

`Editor` 會公開兩個事件：

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; 當編輯器中的文字變更時引發。 提供變更之前和之後的文字。
- [已完成](xref:Xamarin.Forms.Editor.Completed) &ndash; 當使用者按下鍵盤上的 return 鍵結束輸入時引發。

> [!NOTE]
> 從中 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 繼承的類別 [`Entry`](xref:Xamarin.Forms.Entry) 也具有 [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) 和 [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) 事件。

### <a name="completed"></a>已完成

`Completed`事件是用來回應與互動的完成 `Editor` 。 `Completed` 當使用者在鍵盤上輸入 return 鍵 (或在 UWP) 上按下 Tab 鍵時，就會引發。 事件的處理常式是一般事件處理常式，接受寄件者和 `EventArgs` ：

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

已完成的事件可以在程式碼和 XAML 中訂閱：

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

`TextChanged`事件是用來回應欄位內容中的變更。

`TextChanged` 每當變更時，就會引發 `Text` `Editor` 。 事件的處理常式會使用的實例 `TextChangedEventArgs` 。 `TextChangedEventArgs` 透過和屬性，提供對的舊值和新值的存取 `Editor` `Text` `OldTextValue` `NewTextValue` ：

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

已完成的事件可以在程式碼和 XAML 中訂閱：

在程式碼中：

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

- [Text (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [編輯器 API](xref:Xamarin.Forms.Editor)