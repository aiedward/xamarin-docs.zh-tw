---
title: Xamarin.Forms 的項目
description: 這篇文章說明如何使用 Xamarin.Forms 項目類別，以單行文字] 或 [應用程式中的密碼輸入接受。
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
ms.openlocfilehash: 303cca48defdadd69449edbd6c4c3f5e4410bbbb
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831959"
---
# <a name="xamarinforms-entry"></a>Xamarin.Forms 的項目

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_單行文字或輸入的密碼_

Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry)用於單行文字輸入。 `Entry`，例如[ `Editor` ](xref:Xamarin.Forms.Editor)檢視中，支援多個鍵盤類型。 此外，`Entry`可用來當做密碼欄位。

## <a name="display-customization"></a>顯示的自訂

### <a name="setting-and-reading-text"></a>設定和讀取文字

`Entry`，例如其他文字呈現的檢視，公開[ `Text` ](xref:Xamarin.Forms.Entry.Text)屬性。 這個屬性可用來設定和讀取所呈現的文字`Entry`。 下列範例示範如何設定`Text`在 XAML 中的屬性：

```xaml
<Entry Text="I am an Entry" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

若要讀取的文字，存取`Text`C# 中的屬性：

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>設定預留位置文字

[ `Entry` ](xref:Xamarin.Forms.Entry)可以設定為顯示預留位置文字，它不會儲存使用者輸入時。 這可以藉由設定[ `Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder)屬性設`string`，和通常用來表示的內容適用於類型`Entry`。 此外，控制的預留位置文字的色彩，藉由設定[ `PlaceholderColor` ](xref:Xamarin.Forms.Entry.PlaceholderColor)屬性設[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> 寬度`Entry`可以藉由設定定義其`WidthRequest`屬性。 不依賴的寬度`Entry`的值所定義根據其`Text`屬性。

### <a name="limiting-input-length"></a>限制的輸入的長度

[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)屬性可以用來限制所允許的輸入的長度[ `Entry` ](xref:Xamarin.Forms.Entry)。 這個屬性應該設定為正整數：

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

A [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)屬性值為 0 表示將會允許任何輸入項目，並針對`int.MaxValue`，這是預設值[ `Entry` ](xref:Xamarin.Forms.Entry)，指出已沒有可以輸入的字元數的有效限制。

### <a name="password-fields"></a>密碼欄位

`Entry` 提供`IsPassword`屬性。 當`IsPassword`是`true`，欄位的內容會顯示為黑色圓圈：

在 XAML 中：

```xaml
<Entry IsPassword="true" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "項目 IsPassword 範例")

預留位置可能使用的執行個體`Entry`做為密碼欄位設定：

在 XAML 中：

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "項目 IsPassword 和版面配置區範例")

### <a name="setting-the-cursor-position-and-text-selection-length"></a>設定資料指標位置和文字選取範圍長度

[ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition)屬性可以用來傳回或設定下一個字元會插入儲存在字串中的位置[ `Text` ](xref:Xamarin.Forms.Entry.Text)屬性：

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

預設值[ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition)屬性為 0，表示文字會插入開頭`Entry`。

颾魤 ㄛ [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength)屬性可以用來傳回或設定文字選取範圍中的長度`Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

預設值[ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength)屬性為 0，表示未選取任何文字。

### <a name="customizing-the-keyboard"></a>自訂鍵盤

當使用者互動時，會顯示鍵盤[ `Entry` ](xref:Xamarin.Forms.Entry)可以透過程式設計方式設定[ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard)屬性，其中一個的下列屬性[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)類別：

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
<Entry Keyboard="Chat" />
```

對等的 C# 程式碼是：

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

每個鍵盤的範例可在我們[配方](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry)存放庫。

[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)類別也有[ `Create` ](xref:Xamarin.Forms.Keyboard.Create*)可用來指定大小寫、 拼字檢查和建議的行為，以自訂鍵盤的 factory 方法。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 列舉值會指定為引數的方法，以自訂`Keyboard`傳回。 `KeyboardFlags`列舉型別包含下列值：

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

對等的 C# 程式碼是：

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

#### <a name="customizing-the-return-key"></a>自訂 Return 鍵

在 螢幕小鍵盤，這是傳回的索引鍵的外觀顯示時[ `Entry` ](xref:Xamarin.Forms.Entry)具有焦點，可以藉由設定自訂[ `ReturnType` ](xref:Xamarin.Forms.Entry.ReturnType)屬性設為值的[ `ReturnType` ](xref:Xamarin.Forms.ReturnType)列舉型別：

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) -表示沒有特定的傳回索引鍵為必要，就會使用該平台預設值。
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) -指出 「 已完成 」 的 return 鍵。
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) – 表示"Go"return 鍵。
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) – 表示"Next"return 鍵。
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) -指出 「 搜尋 」 傳回索引鍵。
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) -指出 「 傳送 」 傳回索引鍵。

下列 XAML 範例示範如何設定傳回的索引鍵：

```xaml
<Entry ReturnType="Send" />
```

對等的 C# 程式碼是：

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> 傳回索引鍵的確切外觀是取決於平台。 在 iOS 上，傳回的索引鍵會是以文字為基礎的按鈕。 不過，在 Android 和通用 Windows 平台上，傳回的索引鍵是圖示型的按鈕。

按下 return 鍵時， [ `Completed` ](xref:Xamarin.Forms.Entry.Completed)引發事件，以及任何`ICommand`所指定[ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand)執行屬性。 此外，任何`object`所指定[ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter)屬性會傳遞至`ICommand`做為參數。 如需有關命令的詳細資訊，請參閱 <<c0> [ 的命令介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

### <a name="enabling-and-disabling-spell-checking"></a>啟用和停用拼字檢查

[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性會控制是否拼字檢查 已啟用。 根據預設，此屬性設為`true`。 當使用者輸入文字，則會指出拼字錯誤。

不過，某些文字項目的情況下，輸入使用者名稱，例如拼字檢查可提供負體驗和藉由設定，應該停用[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設`false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 當[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設定為`false`，並在不使用自訂的鍵盤，將會停用原生的拼字檢查工具。 不過，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)具有已設定，以停用拼字檢查，例如[ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat)，則`IsSpellCheckEnabled`屬性會被忽略。 因此，無法使用屬性來啟用拼字檢查`Keyboard`，明確地停用。

### <a name="enabling-and-disabling-text-prediction"></a>啟用和停用文字預測

[ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)屬性會控制是否文字預測和自動啟用文字更正。 根據預設，此屬性設為`true`。 當使用者輸入文字，則會看到文字預測。

不過，某些文字項目的情況下，例如輸入使用者名稱、 文字預測和文字自動更正提供負的體驗和藉由設定，應該停用[ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)屬性設`false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 當[ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)屬性設定為`false`，，而不是自訂的鍵盤用文字預測和自動文字更正已停用。 不過，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)已設定該停用文字預測`IsTextPredictionEnabled`屬性會被忽略。 因此，無法使用屬性來啟用文字預測`Keyboard`，明確地停用。

### <a name="colors"></a>色彩

若要使用的自訂背景和文字色彩，透過下列繫結的屬性，可以設定項目：

- **TextColor** &ndash;設定文字的色彩。
- **BackgroundColor** &ndash;設定顯示的文字後面的色彩。

特別是為了確保會在每個平台上的可用色彩。 由於每個平台有不同的預設值的文字和背景色彩，因此您常需要同時設定，如果您設定一個。

下列程式碼可用於設定項目的文字色彩：

在 XAML 中：

```xaml
<Entry TextColor="Green" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![](entry-images/textcolor.png "項目 TextColor 範例")

請注意，不是預留位置受到指定`TextColor`。

若要在 XAML 中設定的背景色彩：

```xaml
<Entry BackgroundColor="#2c3e50" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "項目的 BackgroundColor 範例")

務必確定您選擇的背景和文字色彩在每個平台上使用，而且不會遮住任何預留位置文字。

## <a name="events-and-interactivity"></a>事件與互動性

項目會公開兩個事件：

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) &ndash; 項目中的文字變更時引發。 變更之前和之後，請提供的文字。
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; 當使用者在鍵盤上按 return 鍵結束輸入時引發。

### <a name="completed"></a>已完成

`Completed`事件用來回應完成的項目互動。 `Completed` 當使用者在鍵盤上按 return 鍵結束與欄位的輸入，就會引發。 事件處理常式是泛用事件處理常式，採取寄件者和`EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

已完成的事件可供訂閱在 XAML 中：

```xaml
<Entry Completed="Entry_Completed" />
```

和 C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

在後[ `Completed` ](xref:Xamarin.Forms.Entry.Completed)事件引發時，任何`ICommand`所指定[ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand)執行屬性時，與`object`所指定[ `ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter)屬性傳遞至`ICommand`。

### <a name="textchanged"></a>TextChanged

`TextChanged`事件用來對欄位的內容中的變更做出回應。

`TextChanged` 每當引發`Text`的`Entry`變更。 事件處理常式可接受的執行個體`TextChangedEventArgs`。 `TextChangedEventArgs` 提供的舊和新值的存取權`Entry``Text`透過`OldTextValue`和`NewTextValue`屬性：

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

`TextChanged`可以在 XAML 中以訂閱事件：

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

和 C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```


## <a name="related-links"></a>相關連結

- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [項目 API](xref:Xamarin.Forms.Entry)
