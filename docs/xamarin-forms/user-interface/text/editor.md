---
title: Xamarin.Forms 編輯器
description: 這篇文章說明如何使用 Xamarin.Forms 編輯器控制項接受應用程式中的多行文字輸入。
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/13/2018
ms.openlocfilehash: 2ec9ba6e39673b5a60911f9a9ae70474dbe2443b
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203107"
---
# <a name="xamarinforms-editor"></a>Xamarin.Forms 編輯器

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
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – 預設鍵盤。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – 用於輸入電子郵件地址時。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) – 輸入數字時使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) – 使用輸入文字，不含任何時[ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags)指定。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) – 使用輸入電話號碼時。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) – 輸入文字時使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) – 用於輸入檔案路徑 & 網址。

這可在 XAML，如下所示：

```xaml
<Editor Keyboard="Chat" />
```

對等的 C# 程式碼是：

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

每個鍵盤的範例可在我們[配方](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/)存放庫。

[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)類別也有[ `Create` ](xref:Xamarin.Forms.Keyboard.Create*)可用來指定大小寫、 拼字檢查和建議的行為，以自訂鍵盤的 factory 方法。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 列舉值，指定為引數的方法，以自訂`Keyboard`傳回。 `KeyboardFlags`列舉型別包含下列值：

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – 沒有功能新增至鍵盤。
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) – 指出將會自動大寫的每個輸入句子的第一個單字的第一個字母。
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) – 表示該拼字檢查將會執行上輸入的文字。
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – 表示該文字自動完成，就會收到上輸入的文字。
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) – 指出將會自動大寫的每個單字的第一個字母。
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) – 指出將會自動大寫的每個字元。
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) – 指出將會發生任何自動的大小寫。
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) – 表示拼字檢查、 字組自動完成和句子大小寫，會發生在輸入的文字。

下列 XAML 程式碼範例示範如何自訂預設值[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)提供字組自動完成，並充分利用每個輸入的字元：

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

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性會控制是否拼字檢查已啟用。 根據預設，此屬性設為`true`。 當使用者輸入文字，則會指出拼字錯誤。

不過，某些文字項目的情況下，輸入使用者名稱，例如拼字檢查提供的負數的體驗，因此應該停用藉由設定[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設`false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 當[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設定為`false`，並在不使用自訂的鍵盤，將會停用原生的拼字檢查工具。 不過，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)具有已設定，以停用拼字檢查，例如[ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat)，則`IsSpellCheckEnabled`屬性會被忽略。 因此，無法使用屬性來啟用拼字檢查`Keyboard`，明確地停用。

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

### <a name="completed"></a>已完成

`Completed`事件用來做出回應的互動完成`Editor`。 `Completed` 當使用者在鍵盤上輸入 return 鍵結束與欄位的輸入，就會引發。 事件處理常式是泛用事件處理常式，採取寄件者和`EventArgs`:

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

- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [編輯器 API](xref:Xamarin.Forms.Editor)
