---
title: Xamarin.Forms 進入
description: 本文說明如何使用 Xamarin.Forms Entry 類別，在應用程式中接受單行文字或密碼輸入。
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 625dd57d1f84b95cef1c6513ae832af805bf565a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375027"
---
# <a name="no-locxamarinforms-entry"></a>Xamarin.Forms 進入

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-text)

Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) 用於單行文字輸入。 `Entry`和 view 一樣，也 [`Editor`](xref:Xamarin.Forms.Editor) 支援多個鍵盤類型。 此外， `Entry` 也可以當做密碼欄位使用。

## <a name="set-and-read-text"></a>設定和讀取文字

`Entry`和其他文字呈現視圖一樣，也會公開 [`Text`](xref:Xamarin.Forms.InputView.Text) 屬性。 這個屬性可以用來設定和讀取所呈現的文字 `Entry` 。 下列範例將示範如何 `Text` 在 XAML 中設定屬性：

```xaml
<Entry Text="I am an Entry" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

若要讀取文字，請使用 `Text` c # 存取屬性：

```csharp
var text = MyEntry.Text;
```

## <a name="set-placeholder-text"></a>設定預留位置文字

[`Entry`](xref:Xamarin.Forms.Entry)可以設定為在未儲存使用者輸入時顯示預留位置文字。 這是藉由將 [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 屬性設定為，來完成 `string` ，而且通常用來指出適用于的內容類型 `Entry` 。 此外，您可以藉由將屬性設定為，來控制預留位置文字色彩 [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> 您 `Entry` 可以藉由設定其屬性來定義的寬度 `WidthRequest` 。 請勿根據 `Entry` 其屬性的值所定義的寬度而定 `Text` 。

## <a name="prevent-text-entry"></a>防止文字輸入

藉 [`Entry`](xref:Xamarin.Forms.Entry) 由將 `IsReadOnly` 預設值為的屬性（property）設定 `false` 為，可以防止使用者修改中的文字 `true` 。

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> `IsReadonly`屬性不會改變的視覺外觀 [`Entry`](xref:Xamarin.Forms.Entry) ，與也會將的 `IsEnabled` 視覺外觀變更為灰色的屬性不同 `Entry` 。

## <a name="transform-text"></a>轉換文字

[`Entry`](xref:Xamarin.Forms.Entry)可以藉 `Text` 由將 `TextTransform` 屬性設定為列舉值，來轉換其儲存在屬性中的文字大小寫 `TextTransform` 。 此列舉有四個值：

- `None` 表示不轉換文字。
- `Default` 指出將使用平臺的預設行為。 此為 `TextTransform` 屬性的預設值。
- `Lowercase` 指出文字將轉換成小寫。
- `Uppercase` 指出文字將轉換成大寫。

下列範例顯示如何將文字轉換成大寫：

```xaml
<Entry Text="This text will be displayed in uppercase."
       TextTransform="Uppercase" />
```

對等的 C# 程式碼為：

```csharp
Entry entry = new Entry
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="limit-input-length"></a>限制輸入長度

[`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength)屬性可以用來限制允許的輸入長度 [`Entry`](xref:Xamarin.Forms.Entry) 。 這個屬性應該設定為正整數：

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

如果 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 屬性值為0，表示不允許任何輸入，而且的值 `int.MaxValue` （是的預設值 [`Entry`](xref:Xamarin.Forms.Entry) ）表示可能輸入的字元數沒有有效的限制。

## <a name="character-spacing"></a>字元間距

您可以藉 [`Entry`](xref:Xamarin.Forms.Entry) 由將 `Entry.CharacterSpacing` 屬性設定為值，將字元間距套用至 `double` ：

```xaml
<Entry ...
       CharacterSpacing="10" />
```

對等的 C# 程式碼為：

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

結果是，所顯示文字中的字元會與 [`Entry`](xref:Xamarin.Forms.Entry) `CharacterSpacing` 裝置無關的單位分隔開來。

> [!NOTE]
> `CharacterSpacing`屬性值會套用至和屬性所顯示的文字 `Text` `Placeholder` 。

## <a name="password-fields"></a>密碼欄位

`Entry` 提供 `IsPassword` 屬性。 當 `IsPassword` 為時 `true` ，欄位的內容會顯示為黑色圓形：

在 XAML 中：

```xaml
<Entry IsPassword="true" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![專案 IsPassword 範例](entry-images/password.png)

預留位置可以搭配 `Entry` 設定為密碼欄位的實例使用：

在 XAML 中：

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![專案 IsPassword 和預留位置範例](entry-images/passwordplaceholder.png)

## <a name="set-the-cursor-position-and-text-selection-length"></a>設定游標位置和文字選取範圍長度

[`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition)屬性可用來傳回或設定下一個字元將插入至屬性中所儲存之字串的位置 [`Text`](xref:Xamarin.Forms.InputView.Text) ：

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

屬性的預設值 [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) 為0，表示將在開頭插入文字 `Entry` 。

此外， [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) 屬性也可以用來傳回或設定中的文字選取範圍長度 `Entry` ：

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

屬性的預設值 [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) 為0，表示沒有選取任何文字。

## <a name="display-a-clear-button"></a>顯示清除按鈕

`ClearButtonVisibility`屬性可用來控制是否 [`Entry`](xref:Xamarin.Forms.Entry) 顯示 [清除] 按鈕，讓使用者可以清除文字。 這個屬性應該設定為 `ClearButtonVisibility` 列舉成員：

- `Never` 表示永遠不會顯示清除按鈕。 這是 `Entry.ClearButtonVisibility` 屬性的預設值。
- `WhileEditing` 指出將會在中顯示 [清除] 按鈕 [`Entry`](xref:Xamarin.Forms.Entry) ，同時包含焦點和文字。

下列範例顯示如何在 XAML 中設定屬性：

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

對等的 C# 程式碼為：

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

下列螢幕擷取畫面顯示 [`Entry`](xref:Xamarin.Forms.Entry) 已啟用 [清除] 按鈕的：

![螢幕擷取畫面：在 iOS 和 Android 上具有 [清除] 按鈕的專案](entry-images/entry-clear-button.png)

## <a name="customize-the-keyboard"></a>自訂鍵盤

當使用者與互動時顯示的鍵盤 [`Entry`](xref:Xamarin.Forms.Entry) ，可以透過屬性以程式設計的方式設定，以程式設計方式 [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) 從類別設定為下列其中一個屬性 [`Keyboard`](xref:Xamarin.Forms.Keyboard) ：

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
<Entry Keyboard="Chat" />
```

對等的 C# 程式碼為：

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
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

### <a name="customize-the-return-key"></a>自訂傳回金鑰

您 [`Entry`](xref:Xamarin.Forms.Entry) 可以藉由將 [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) 屬性設定為列舉值，自訂在螢幕小鍵盤上傳回按鍵的外觀 [`ReturnType`](xref:Xamarin.Forms.ReturnType) ：

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) –表示不需要特定的傳回索引鍵，而且將會使用平臺預設值。
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) –表示「完成」傳回索引鍵。
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) –表示 "Go" 傳回索引鍵。
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) –表示「下一步」傳回索引鍵。
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) –表示 "Search" 傳回索引鍵。
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) –表示「傳送」傳回索引鍵。

下列 XAML 範例顯示如何設定傳回索引鍵：

```xaml
<Entry ReturnType="Send" />
```

對等的 C# 程式碼為：

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> 傳回索引鍵的確切外觀取決於平臺。 在 iOS 上，return 鍵是以文字為基礎的按鈕。 不過，在 Android 和通用 Windows 平臺上，return 鍵是以圖示為基礎的按鈕。

按下 return 鍵時， [`Completed`](xref:Xamarin.Forms.Entry.Completed) 就會引發事件，並 `ICommand` 執行由屬性指定的任何專案 [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) 。 此外，屬性所 `object` 指定的任何 [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) 會以參數形式傳遞至 `ICommand` 。 如需命令的詳細資訊，請參閱[命令介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="enable-and-disable-spell-checking"></a>啟用和停用拼寫檢查

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性控制是否已啟用拼寫檢查。 根據預設，屬性會設定為 `true` 。 當使用者輸入文字時，就會指出拼寫錯誤。

不過，對於某些文字輸入案例（例如輸入使用者名稱），拼寫檢查會提供負面的體驗，而且應該藉由將 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性設定為來停用 `false` ：

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 當 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性設定為 `false` ，且未使用自訂鍵盤時，將會停用原生拼寫檢查。 但是，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 設定為停用拼寫檢查（例如）， [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) 則 `IsSpellCheckEnabled` 會忽略屬性。 因此，不能使用屬性來啟用明確停用之的拼寫檢查 `Keyboard` 。

## <a name="enable-and-disable-text-prediction"></a>啟用和停用文字預測

[`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)屬性控制文字預測和自動文字更正是否已啟用。 根據預設，屬性會設定為 `true` 。 當使用者輸入文字時，會顯示文字預測。

不過，在某些文字輸入案例中（例如輸入使用者名稱、文字預測和自動文字更正）會提供負面的體驗，而且應該藉由將 [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) 屬性設定為來停用 `false` ：

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 當 [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) 屬性設定為 `false` ，且未使用自訂鍵盤時，會停用文字預測和自動文字修正。 但是，如果已 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 設定停用文字預測，則 `IsTextPredictionEnabled` 會忽略屬性。 因此，屬性無法用來啟用明確停用之的文字預測 `Keyboard` 。

## <a name="colors"></a>色彩

您可以透過下列可系結屬性，將專案設定為使用自訂的背景和文字色彩：

- **TextColor** &ndash; 設定文字的色彩。
- **BackgroundColor** &ndash; 設定文字背後顯示的色彩。

請特別小心，以確保每個平臺都能使用這些色彩。 由於每個平臺的文字和背景色彩都有不同的預設值，因此您通常需要設定兩者。

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

請注意，此預留位置不受指定的影響 `TextColor` 。

若要在 XAML 中設定背景色彩：

```xaml
<Entry BackgroundColor="#2c3e50" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![專案 BackgroundColor 範例](entry-images/textbackgroundcolor.png)

請小心確保您所選擇的背景和文字色彩可在每個平臺上使用，且不會隱匿任何預留位置文字。

## <a name="events-and-interactivity"></a>活動和互動性

專案會公開兩個事件：

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)&ndash;當專案中的文字變更時引發。 提供變更之前和之後的文字。
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)&ndash;當使用者按下鍵盤上的 return 鍵結束輸入時引發。

> [!NOTE]
> 從中 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 繼承的類別 [`Entry`](xref:Xamarin.Forms.Entry) 也具有 [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) 和 [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) 事件。

### <a name="completed"></a>已完成

`Completed`事件是用來回應與專案互動的完成。 `Completed` 當使用者按下 (鍵盤上的 return 鍵，或按下 UWP) 上的 Tab 鍵，以欄位結束輸入時，就會引發。 事件的處理常式是一般事件處理常式，接受寄件者和 `EventArgs` ：

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

和 c #：

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

[`Completed`](xref:Xamarin.Forms.Entry.Completed)事件引發之後， `ICommand` 會執行由屬性所指定的，並將指定的傳遞給 [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) `object` [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) `ICommand` 。

### <a name="textchanged"></a>TextChanged

`TextChanged`事件是用來回應欄位內容中的變更。

`TextChanged` 每當變更時，就會引發 `Text` `Entry` 。 事件的處理常式會使用的實例 `TextChangedEventArgs` 。 `TextChangedEventArgs` 透過和屬性，提供對的舊值和新值的存取 `Entry` `Text` `OldTextValue` `NewTextValue` ：

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

您 `TextChanged` 可以在 XAML 中訂閱此事件：

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

和 c #：

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>相關連結

- [Text (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [項目 API](xref:Xamarin.Forms.Entry)