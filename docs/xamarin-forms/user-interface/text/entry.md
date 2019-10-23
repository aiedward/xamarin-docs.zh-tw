---
title: Xamarin. 表單專案
description: 本文說明如何使用 Xamarin 專案類別，接受應用程式中的單行文字或密碼輸入。
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: 106d22cd7f20f8368bc728aa45c4d7f19d09c44c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697070"
---
# <a name="xamarinforms-entry"></a>Xamarin. 表單專案

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_單行文字或密碼輸入_

[Xamarin] [`Entry`](xref:Xamarin.Forms.Entry)用於單行文字輸入。 @No__t_0 （如[`Editor`](xref:Xamarin.Forms.Editor)視圖）支援多種鍵盤類型。 此外，也可以使用 `Entry` 做為密碼欄位。

## <a name="display-customization"></a>顯示自訂

### <a name="setting-and-reading-text"></a>設定和讀取文字

@No__t_0，如同其他文字呈現的視圖，會公開[`Text`](xref:Xamarin.Forms.Entry.Text)屬性。 這個屬性可以用來設定和讀取 `Entry` 所呈現的文字。 下列範例示範如何在 XAML 中設定 `Text` 屬性：

```xaml
<Entry Text="I am an Entry" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

若要讀取文字，請存取中C#的 `Text` 屬性：

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>設定預留位置文字

[@No__t_1](xref:Xamarin.Forms.Entry)可以設定為在未儲存使用者輸入時顯示預留位置文字。 這是藉由將 [ [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) ] 屬性設定為 [`string`] 來完成，而且通常用來指出適用于 `Entry` 的內容類型。 此外，您可以藉由將 [ [`PlaceholderColor`](xref:Xamarin.Forms.Entry.PlaceholderColor) ] 屬性設定為 [ [`Color`](xref:Xamarin.Forms.Color)] 來控制預留位置文字色彩：

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> @No__t_0 的寬度可以藉由設定其 `WidthRequest` 屬性來定義。 請勿依據其 `Text` 屬性的值所定義之 `Entry` 的寬度而定。

### <a name="preventing-text-entry"></a>防止文字輸入

藉由將 `IsReadOnly` 屬性（其預設值為 `false`）設定為 `true`，可以防止使用者修改[`Entry`](xref:Xamarin.Forms.Entry)中的文字：

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> @No__t_0 屬性不會改變[`Entry`](xref:Xamarin.Forms.Entry)的視覺外觀，不同于也會將 `Entry` 的視覺外觀變更為灰色的 `IsEnabled` 屬性。

### <a name="limiting-input-length"></a>限制輸入長度

[@No__t_1](xref:Xamarin.Forms.InputView.MaxLength)屬性可以用來限制[`Entry`](xref:Xamarin.Forms.Entry)所允許的輸入長度。 此屬性應該設定為正整數：

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

[@No__t_1](xref:Xamarin.Forms.InputView.MaxLength)屬性值0表示不允許輸入，而且 `int.MaxValue` 的值（這是[`Entry`](xref:Xamarin.Forms.Entry)的預設值），表示可以輸入的字元數沒有有效的限制。

### <a name="character-spacing"></a>字元間距

將 [`Entry.CharacterSpacing`] 屬性設定為 `double` 值，即可將字元間距套用至[`Entry`](xref:Xamarin.Forms.Entry) ：

```xaml
<Entry ...
       CharacterSpacing="10" />
```

對等的 C# 程式碼為：

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

結果是[`Entry`](xref:Xamarin.Forms.Entry)所顯示文字中的字元間距，`CharacterSpacing` 與裝置無關的單位分開。

> [!NOTE]
> [@No__t_0] 屬性值會套用至 [`Text`] 和 [`Placeholder`] 屬性所顯示的文字。

### <a name="password-fields"></a>密碼欄位

`Entry` 提供 `IsPassword` 屬性。 當 `IsPassword` `true` 時，欄位的內容會以黑色圓圈呈現：

在 XAML 中：

```xaml
<Entry IsPassword="true" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![專案 IsPassword 範例](entry-images/password.png)

預留位置可以與設定為密碼欄位的 `Entry` 實例搭配使用：

在 XAML 中：

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![專案 IsPassword 和預留位置範例](entry-images/passwordplaceholder.png)

### <a name="setting-the-cursor-position-and-text-selection-length"></a>設定游標位置和文字選取範圍長度

[@No__t_1](xref:Xamarin.Forms.Entry.CursorPosition)屬性可以用來傳回或設定下一個字元將插入至儲存在[`Text`](xref:Xamarin.Forms.Entry.Text)屬性中之字串的位置：

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

[@No__t_1](xref:Xamarin.Forms.Entry.CursorPosition)屬性的預設值為0，表示將會在 `Entry` 開頭插入文字。

此外， [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength)屬性可以用來傳回或設定 `Entry` 內文字選取的長度：

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

[@No__t_1](xref:Xamarin.Forms.Entry.SelectionLength)屬性的預設值為0，表示未選取任何文字。

### <a name="displaying-a-clear-button"></a>顯示 [清除] 按鈕

@No__t_0 屬性可以用來控制[`Entry`](xref:Xamarin.Forms.Entry)是否顯示 [清除] 按鈕，讓使用者可以清除文字。 這個屬性應該設定為 `ClearButtonVisibility` 列舉成員：

- `Never` 表示永遠不會顯示 [清除] 按鈕。 這是 `Entry.ClearButtonVisibility` 屬性的預設值。
- `WhileEditing` 表示[`Entry`](xref:Xamarin.Forms.Entry)中會顯示 [清除] 按鈕，而其具有焦點和文字。

下列範例顯示如何在 XAML 中設定屬性：

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

對等的 C# 程式碼為：

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

下列螢幕擷取畫面顯示已啟用 [清除] 按鈕的[`Entry`](xref:Xamarin.Forms.Entry) ：

![在 iOS 和 Android 上具有 [清除] 按鈕之專案的螢幕擷取畫面](entry-images/entry-clear-button.png)

### <a name="customizing-the-keyboard"></a>自訂鍵盤

使用者與[`Entry`](xref:Xamarin.Forms.Entry)互動時所呈現的鍵盤，可以透過[`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)屬性，以程式設計方式設定為[`Keyboard`](xref:Xamarin.Forms.Keyboard)類別的下列其中一個屬性：

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
<Entry Keyboard="Chat" />
```

對等的 C# 程式碼為：

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
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
<Entry Placeholder="Enter text here">
    <Entry.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Entry.Keyboard>
</Entry>
```

對等的 C# 程式碼為：

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

#### <a name="customizing-the-return-key"></a>自訂傳回金鑰

當[`Entry`](xref:Xamarin.Forms.Entry)有焦點時，螢幕上顯示的傳回按鍵外觀，可以藉由將[`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType)屬性設定為[`ReturnType`](xref:Xamarin.Forms.ReturnType)列舉的值來自訂：

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) –表示不需要特定的傳回索引鍵，而且將會使用平臺預設值。
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) –表示「完成」傳回索引鍵。
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) –表示 "Go" 傳回碼。
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) –表示「下一個」傳回鍵。
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) –表示「搜尋」傳回碼。
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) –表示 "Send" 傳回金鑰。

下列 XAML 範例顯示如何設定傳回索引鍵：

```xaml
<Entry ReturnType="Send" />
```

對等的 C# 程式碼為：

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> 傳回金鑰的確切外觀取決於平臺。 在 iOS 上，return 鍵是以文字為基礎的按鈕。 不過，在 Android 和通用 Windows 平臺上，return 鍵是以圖示為基礎的按鈕。

按下 return 鍵時，會引發[`Completed`](xref:Xamarin.Forms.Entry.Completed)事件，並執行[`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand)屬性指定的任何 `ICommand`。 此外， [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter)屬性指定的任何 `object` 都會以參數的形式傳遞至 `ICommand`。 如需命令的詳細資訊，請參閱[命令介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

### <a name="enabling-and-disabling-spell-checking"></a>啟用和停用拼寫檢查

[@No__t_1](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性可控制是否啟用拼寫檢查。 根據預設，屬性會設定為 `true`。 當使用者輸入文字時，會顯示拼寫錯誤。

不過，針對某些文字輸入案例（例如輸入使用者名稱），拼寫檢查會提供負面的體驗，而且應該藉由將[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設定為 `false` 來停用：

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 當 [ [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) ] 屬性設定為 [`false`]，且未使用自訂鍵盤時，將會停用原生拼寫檢查。 不過，如果設定了停用拼寫檢查的[`Keyboard`](xref:Xamarin.Forms.Keyboard) （例如[`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat)），則會忽略 `IsSpellCheckEnabled` 屬性。 因此，屬性不能用來針對明確停用的 `Keyboard` 啟用拼寫檢查。

### <a name="enabling-and-disabling-text-prediction"></a>啟用和停用文字預測

[@No__t_1](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)屬性可控制是否啟用文字預測和自動文字更正。 根據預設，屬性會設定為 `true`。 當使用者輸入文字時，會顯示文字預測。

不過，對於某些文字輸入案例，例如輸入使用者名稱、文字預測和自動文字更正會提供負面的體驗，而且應該藉由將[`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)屬性設定為 `false` 來停用：

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 當 [ [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) ] 屬性設定為 [`false`]，且未使用自訂鍵盤時，會停用文字預測和自動文字更正。 不過，如果已設定[`Keyboard`](xref:Xamarin.Forms.Keyboard)停用文字預測，則會忽略 `IsTextPredictionEnabled` 屬性。 因此，屬性不能用來針對明確停用的 `Keyboard` 啟用文字預測。

### <a name="colors"></a>色彩

專案可以透過下列可系結屬性設定為使用自訂的背景和文字色彩：

- **TextColor** &ndash; 設定文字的色彩。
- **BackgroundColor** &ndash; 設定文字後面顯示的色彩。

必須特別小心，以確保每個平臺都能使用色彩。 由於每個平臺都有不同的文字和背景色彩預設值，因此您通常需要設定這兩者。

使用下列程式碼來設定專案的文字色彩：

在 XAML 中：

```xaml
<Entry TextColor="Green" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![專案 TextColor 範例](entry-images/textcolor.png)

請注意，預留位置不會受到指定 `TextColor` 的影響。

若要設定 XAML 中的背景色彩：

```xaml
<Entry BackgroundColor="#2c3e50" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![專案 BackgroundColor 範例](entry-images/textbackgroundcolor.png)

請小心確定您所選擇的背景和文字色彩可以在每個平臺上使用，而且不會遮蔽任何預留位置文字。

## <a name="events-and-interactivity"></a>事件與互動性

專案會公開兩個事件：

- 當專案中的文字變更時， [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) &ndash; 引發。 提供變更前後的文字。
- 當使用者按下鍵盤上的 return 鍵結束輸入時， [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; 引發。

> [!NOTE]
> [@No__t_3](xref:Xamarin.Forms.Entry)繼承的[`VisualElement`](xref:Xamarin.Forms.VisualElement)類別，也會有[`Focused`](xref:Xamarin.Forms.VisualElement.Focused)和[`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)事件。

### <a name="completed"></a>已完成

@No__t_0 事件是用來回應與專案互動的完成。 當使用者在鍵盤上按下 enter 鍵（或按 UWP 上的 Tab 鍵）時，就會引發 `Completed`。 事件的處理常式是一般事件處理常式，會取得傳送者並 `EventArgs`：

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

已完成的事件可以在 XAML 中訂閱：

```xaml
<Entry Completed="Entry_Completed" />
```

和C#：

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

在[`Completed`](xref:Xamarin.Forms.Entry.Completed)事件引發之後， [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand)屬性指定的任何 `ICommand` 都會執行，並將[`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter)屬性所指定的 `object` 傳遞至 `ICommand`。

### <a name="textchanged"></a>TextChanged

@No__t_0 事件是用來回應欄位內容中的變更。

每當 `Entry` 的 `Text` 變更時，就會引發 `TextChanged`。 事件的處理常式會取得 `TextChangedEventArgs` 的實例。 `TextChangedEventArgs` 透過 `OldTextValue` 和 `NewTextValue` 屬性，提供 `Entry` `Text` 的新舊值的存取權：

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

@No__t_0 事件可以在 XAML 中訂閱：

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

和C#：

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>相關連結

- [文字（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [項目 API](xref:Xamarin.Forms.Entry)
