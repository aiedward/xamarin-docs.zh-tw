---
title: Xamarin. 表單編輯器
description: 本文說明如何使用 [Xamarin 編輯器] 控制項在應用程式中接受多行文字輸入。
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/26/2019
ms.openlocfilehash: 1ae176cfebdde31038c30895d1bf562ff3396eaa
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915337"
---
# <a name="xamarinforms-editor"></a>Xamarin. 表單編輯器

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_多行文字輸入_

[`Editor`](xref:Xamarin.Forms.Editor)控制項用來接受多行輸入。 本文將說明：

- **[自訂](#customization)** &ndash; 鍵盤和色彩選項。
- **[互動](#interactivity)** 性 &ndash; 可聽取以提供互動性的事件。

## <a name="customization"></a>自訂

### <a name="setting-and-reading-text"></a>設定和讀取文字

[`Editor`](xref:Xamarin.Forms.Editor)，如同其他文字呈現的視圖，會公開 `Text` 屬性。 這個屬性可以用來設定和讀取 `Editor`所呈現的文字。 下列範例示範如何在 XAML 中設定 `Text` 屬性：

```xaml
<Editor Text="I am an Editor" />
```

在 C# 中：

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

若要讀取文字，請存取中C#的 `Text` 屬性：

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>設定預留位置文字

[`Editor`](xref:Xamarin.Forms.Editor)可以設定為在未儲存使用者輸入時顯示預留位置文字。 這是藉由將 [ [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) ] 屬性設定為 [`string`] 來完成，而且通常用來指出適用于 `Editor`的內容類型。 此外，您可以藉由將 [ [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) ] 屬性設定為 [ [`Color`](xref:Xamarin.Forms.Color)] 來控制預留位置文字色彩：

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>防止文字輸入

藉由將 `IsReadOnly` 屬性（其預設值為 `false`）設定為 `true`，可以防止使用者修改[`Editor`](xref:Xamarin.Forms.Editor)中的文字：

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> `IsReadonly` 屬性不會改變[`Editor`](xref:Xamarin.Forms.Editor)的視覺外觀，不同于也會將 `Editor` 的視覺外觀變更為灰色的 `IsEnabled` 屬性。

### <a name="limiting-input-length"></a>限制輸入長度

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)屬性可以用來限制[`Editor`](xref:Xamarin.Forms.Editor)所允許的輸入長度。 此屬性應該設定為正整數：

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)屬性值0表示不允許輸入，而且 `int.MaxValue`的值（這是[`Editor`](xref:Xamarin.Forms.Editor)的預設值），表示可以輸入的字元數沒有有效的限制。

### <a name="character-spacing"></a>字元間距

將 [`Editor.CharacterSpacing`] 屬性設定為 `double` 值，即可將字元間距套用至[`Editor`](xref:Xamarin.Forms.Editor) ：

```xaml
<Editor ...
        CharacterSpacing="10" />
```

對等的 C# 程式碼為：

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

結果是[`Editor`](xref:Xamarin.Forms.Editor)所顯示文字中的字元間距，`CharacterSpacing` 與裝置無關的單位分開。

> [!NOTE]
> [`CharacterSpacing`] 屬性值會套用至 [`Text`] 和 [`Placeholder`] 屬性所顯示的文字。

### <a name="auto-sizing-an-editor"></a>自動調整編輯器大小

藉由將[`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize)屬性設定為[`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)（這是[`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption)列舉的值），就可以將[`Editor`](xref:Xamarin.Forms.Editor)自動調整為其內容。 這個列舉有兩個值：

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled)表示停用自動調整大小，而是預設值。
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)表示已啟用自動調整大小。

這可以在程式碼中完成，如下所示：

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

啟用自動調整大小時， [`Editor`](xref:Xamarin.Forms.Editor)的高度會在使用者填滿文字時增加，而且當使用者刪除文字時，高度也會減少。

> [!NOTE]
> 如果已設定[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性， [`Editor`](xref:Xamarin.Forms.Editor)將不會自動調整大小。

### <a name="customizing-the-keyboard"></a>自訂鍵盤

使用者與[`Editor`](xref:Xamarin.Forms.Editor)互動時所呈現的鍵盤，可以透過[`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)屬性，以程式設計方式設定為[`Keyboard`](xref:Xamarin.Forms.Keyboard)類別的下列其中一個屬性：

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) - 用於收發簡訊和 Emoji 有用的地方。
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) - 預設鍵盤。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) - 輸入電子郵件地址時使用。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) - 輸入數字時使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) - 輸入文字時使用，不指定任何 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) - 輸入電話號碼時使用。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) - 輸入文字時使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) - 用於輸入檔案路徑與網址。

執行下列工作即可用 XAML 來達成這點：

```xaml
<Editor Keyboard="Chat" />
```

對等的 C# 程式碼為：

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

您可以在我們的[食譜](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry)存放庫中找到每個鍵盤的範例。

[`Keyboard`](xref:Xamarin.Forms.Keyboard) 類別還具有 [`Create`](xref:Xamarin.Forms.Keyboard.Create*) Factory 方法，可透過指定大小寫、拼字檢查和建議的行為來自訂鍵盤。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 列舉值會被指定為方法的引數，並傳回自訂的 `Keyboard`。 `KeyboardFlags` 列舉包含下列值：

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

對等的 C# 程式碼為：

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>啟用和停用拼寫檢查

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性可控制是否啟用拼寫檢查。 根據預設，屬性會設定為 `true`。 當使用者輸入文字時，會顯示拼寫錯誤。

不過，針對某些文字輸入案例（例如輸入使用者名稱），拼寫檢查會提供負面的體驗，因此應該藉由將[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設定為 `false`來停用：

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 當 [ [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) ] 屬性設定為 [`false`]，且未使用自訂鍵盤時，將會停用原生拼寫檢查。 不過，如果設定了停用拼寫檢查的[`Keyboard`](xref:Xamarin.Forms.Keyboard) （例如[`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat)），則會忽略 `IsSpellCheckEnabled` 屬性。 因此，屬性不能用來針對明確停用的 `Keyboard` 啟用拼寫檢查。

### <a name="enabling-and-disabling-text-prediction"></a>啟用和停用文字預測

`IsTextPredictionEnabled` 屬性可控制是否啟用文字預測和自動文字更正。 根據預設，屬性會設定為 `true`。 當使用者輸入文字時，會顯示文字預測。

不過，對於某些文字輸入案例，例如輸入使用者名稱、文字預測和自動文字更正會提供負面的體驗，而且應該藉由將 `IsTextPredictionEnabled` 屬性設定為 `false`來停用：

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 當 [`IsTextPredictionEnabled`] 屬性設定為 [`false`]，且未使用自訂鍵盤時，會停用文字預測和自動文字更正。 不過，如果已設定[`Keyboard`](xref:Xamarin.Forms.Keyboard)停用文字預測，則會忽略 `IsTextPredictionEnabled` 屬性。 因此，屬性不能用來針對明確停用的 `Keyboard` 啟用文字預測。

### <a name="colors"></a>色彩

`Editor` 可以透過 `BackgroundColor` 屬性設定為使用自訂背景色彩。 必須特別小心，以確保每個平臺都能使用色彩。 因為每個平臺都有不同的文字色彩預設值，所以您可能需要為每個平臺設定自訂背景色彩。 如需優化每個平臺 UI 的詳細資訊，請參閱[使用平臺](~/xamarin-forms/platform/device.md)調整。

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

![](editor-images/textbackgroundcolor.png "Editor with BackgroundColor Example")

請確定您所選擇的背景和文字色彩可以在每個平臺上使用，而且不會遮蔽任何預留位置文字。

## <a name="interactivity"></a>互動性

`Editor` 會公開兩個事件：

- 當編輯器中的文字變更時，就會引發[TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash;。 提供變更前後的文字。
- 當使用者按下鍵盤上的 return 鍵結束輸入時，就會引發 &ndash;[完成](xref:Xamarin.Forms.Editor.Completed)。

> [!NOTE]
> [`Entry`](xref:Xamarin.Forms.Entry)繼承的[`VisualElement`](xref:Xamarin.Forms.VisualElement)類別，也會有[`Focused`](xref:Xamarin.Forms.VisualElement.Focused)和[`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)事件。

### <a name="completed"></a>Completed

`Completed` 事件是用來回應與 `Editor`的互動完成。 當使用者在鍵盤上輸入 return 鍵（或按 UWP 上的 Tab 鍵）來結束具有欄位的輸入時，就會引發 `Completed`。 事件的處理常式是一般事件處理常式，會取得傳送者並 `EventArgs`：

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

`TextChanged` 事件是用來回應欄位內容中的變更。

每當 `Editor` 的 `Text` 變更時，就會引發 `TextChanged`。 事件的處理常式會取得 `TextChangedEventArgs`的實例。 `TextChangedEventArgs` 透過 `OldTextValue` 和 `NewTextValue` 屬性，提供 `Editor` `Text` 的新舊值的存取權：

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

- [文字（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [編輯器 API](xref:Xamarin.Forms.Editor)
