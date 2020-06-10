---
title： " Xamarin.Forms Editor" 描述： "本文說明如何使用 Xamarin.Forms 編輯器控制項接受應用程式中的多行文字輸入。
assetid： 7074DB3A-30D2-4A6B-9A89-B029EEF20B07 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：09/26/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-editor"></a>Xamarin.Forms 編輯器

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_多行文字輸入_

[`Editor`](xref:Xamarin.Forms.Editor)控制項是用來接受多行輸入。 本文將說明：

- **[自訂](#customization)** &ndash;鍵盤和色彩選項。
- **[互動](#interactivity)** &ndash; 性可以聽取以提供互動性的事件。

## <a name="customization"></a>自訂

### <a name="setting-and-reading-text"></a>設定和讀取文字

[`Editor`](xref:Xamarin.Forms.Editor)和其他文字呈現的視圖一樣，會公開 `Text` 屬性。 這個屬性可以用來設定和讀取所呈現的文字 `Editor` 。 下列範例示範如何 `Text` 在 XAML 中設定屬性：

```xaml
<Editor Text="I am an Editor" />
```

在 C# 中：

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

若要讀取文字，請存取 `Text` c # 中的屬性：

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>設定預留位置文字

[`Editor`](xref:Xamarin.Forms.Editor)可以設定為在未儲存使用者輸入時顯示預留位置文字。 將屬性設定為，即可完成這 [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) `string` 項作業，通常用來表示適用于的內容類型 `Editor` 。 此外，您可以將屬性設定為，以控制預留位置文字色彩 [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>防止文字輸入

藉 [`Editor`](xref:Xamarin.Forms.Editor) 由將 `IsReadOnly` 屬性（具有的預設值 `false` ）設定為，可防止使用者修改中的文字 `true` ：

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> `IsReadonly`屬性不會改變的視覺外觀，不同于也會將的 [`Editor`](xref:Xamarin.Forms.Editor) `IsEnabled` 視覺外觀變更 `Editor` 為灰色的屬性。

### <a name="limiting-input-length"></a>限制輸入長度

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)屬性可以用來限制允許的輸入長度 [`Editor`](xref:Xamarin.Forms.Editor) 。 此屬性應該設定為正整數：

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)屬性值為0表示不允許輸入，而且的值為的 `int.MaxValue` 預設值 [`Editor`](xref:Xamarin.Forms.Editor) ，表示可以輸入的字元數沒有有效的限制，而不會有任何影響。

### <a name="character-spacing"></a>字元間距

[`Editor`](xref:Xamarin.Forms.Editor)將 `Editor.CharacterSpacing` 屬性設定為值，即可將字元間距套用至 `double` ：

```xaml
<Editor ...
        CharacterSpacing="10" />
```

對等的 C# 程式碼為：

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

結果是所顯示文字中的字元 [`Editor`](xref:Xamarin.Forms.Editor) 會與 `CharacterSpacing` 裝置獨立單位分開。

> [!NOTE]
> `CharacterSpacing`屬性值會套用至和屬性所顯示的文字 `Text` `Placeholder` 。

### <a name="auto-sizing-an-editor"></a>自動調整編輯器大小

藉 [`Editor`](xref:Xamarin.Forms.Editor) 由將 [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) 屬性設定為 [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) （列舉的值），可以自動調整其內容的大小 [`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) 。 這個列舉有兩個值：

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled)表示停用自動調整大小，而是預設值。
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)表示已啟用自動調整大小。

這可以在程式碼中完成，如下所示：

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

啟用自動調整大小時， [`Editor`](xref:Xamarin.Forms.Editor) 當使用者以文字填滿時，的高度會增加，而當使用者刪除文字時，高度也會減少。

> [!NOTE]
> [`Editor`](xref:Xamarin.Forms.Editor)如果已設定屬性，將不會自動調整 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 。

### <a name="customizing-the-keyboard"></a>自訂鍵盤

當使用者與互動時所呈現的鍵盤 [`Editor`](xref:Xamarin.Forms.Editor) ，可以透過屬性，以程式設計方式設定 [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 為類別的下列其中一個屬性 [`Keyboard`](xref:Xamarin.Forms.Keyboard) ：

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)–用於傳送和表情適用的地方。
- [`Default`](xref:Xamarin.Forms.Keyboard.Default)–預設鍵盤。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)–在輸入電子郵件地址時使用。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)–在輸入數位時使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain)–在輸入文字時使用，不含任何 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 指定的。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)–在輸入電話號碼時使用。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)–在輸入文字時使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)–用來輸入 & 網址的檔案路徑。

執行下列工作即可用 XAML 來達成這點：

```xaml
<Editor Keyboard="Chat" />
```

對等的 C# 程式碼為：

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

您可以在我們的[食譜](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry)存放庫中找到每個鍵盤的範例。

[`Keyboard`](xref:Xamarin.Forms.Keyboard)類別也具有 [`Create`](xref:Xamarin.Forms.Keyboard.Create*) factory 方法，可透過指定大小寫、拼字檢查和建議行為，用來自訂鍵盤。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)列舉值會指定為方法的引數，並傳回自訂的 `Keyboard` 。 `KeyboardFlags` 列舉包含下列值：

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None)–沒有任何功能新增至鍵盤。
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)-指出每個輸入之句子第一個單字的第一個字母會自動大寫。
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)–表示將在輸入的文字上執行拼字檢查。
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)–表示輸入的文字將會提供文字自動完成。
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord)–指出每個單字的第一個字母會自動大寫。
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter)–指出每個字元都將自動大寫。
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone)–表示不會自動進行任何大小寫。
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)–表示輸入的文字上會出現拼字檢查、word 完成和句子大小寫。

下列 XAML 程式碼範例示範如何自訂預設值， [`Keyboard`](xref:Xamarin.Forms.Keyboard) 以提供文字完成，並將每個輸入的字元設為大寫：

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

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性會控制是否啟用拼寫檢查。 根據預設，屬性會設定為 `true` 。 當使用者輸入文字時，會顯示拼寫錯誤。

不過，針對某些文字輸入案例（例如輸入使用者名稱），拼寫檢查會提供負面的體驗，因此應該藉由將 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性設定為來停用 `false` ：

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 當 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性設定為 `false` ，且未使用自訂鍵盤時，原生拼寫檢查將會停用。 不過，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 設定停用拼寫檢查（例如 [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) ），則 `IsSpellCheckEnabled` 會忽略屬性。 因此，屬性不能用來為明確停用的啟用拼寫檢查 `Keyboard` 。

### <a name="enabling-and-disabling-text-prediction"></a>啟用和停用文字預測

`IsTextPredictionEnabled`屬性控制是否啟用文字預測和自動文字更正。 根據預設，屬性會設定為 `true` 。 當使用者輸入文字時，會顯示文字預測。

不過，對於某些文字輸入案例，例如輸入使用者名稱、文字預測和自動文字更正會提供負面的體驗，而且應該藉由將 `IsTextPredictionEnabled` 屬性設定為來停用 `false` ：

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 當 `IsTextPredictionEnabled` 屬性設定為 `false` ，且未使用自訂鍵盤時，會停用文字預測和自動文字更正。 不過，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 設定停用文字預測，則 `IsTextPredictionEnabled` 會忽略屬性。 因此，屬性不能用來啟用明確停用之的文字預測 `Keyboard` 。

### <a name="colors"></a>色彩

`Editor`可以透過屬性設定為使用自訂背景色彩 `BackgroundColor` 。 必須特別小心，以確保每個平臺都能使用色彩。 因為每個平臺都有不同的文字色彩預設值，所以您可能需要為每個平臺設定自訂背景色彩。 如需優化每個平臺 UI 的詳細資訊，請參閱[使用平臺](~/xamarin-forms/platform/device.md)調整。

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

## <a name="interactivity"></a>互動

`Editor`公開兩個事件：

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash;在編輯器中的文字變更時引發。 提供變更前後的文字。
- [已完成](xref:Xamarin.Forms.Editor.Completed) &ndash;當使用者按下鍵盤上的 return 鍵結束輸入時引發。

> [!NOTE]
> [`VisualElement`](xref:Xamarin.Forms.VisualElement) [`Entry`](xref:Xamarin.Forms.Entry) 繼承自的類別也具有 [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) 和 [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) 事件。

### <a name="completed"></a>Completed

`Completed`事件是用來回應與的互動完成 `Editor` 。 `Completed`當使用者在鍵盤上輸入 return 鍵（或按 UWP 上的 Tab 鍵）來結束具有欄位的輸入時，會引發。 事件的處理常式是一般事件處理常式，接受寄件者和 `EventArgs` ：

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

`TextChanged`每當變更時，就會引發 `Text` `Editor` 。 事件的處理常式會接受的實例 `TextChangedEventArgs` 。 `TextChangedEventArgs`透過和屬性，提供的新舊值的存取權 `Editor` `Text` `OldTextValue` `NewTextValue` ：

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
