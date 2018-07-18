---
title: Xamarin.Forms 的項目
description: 這篇文章說明如何使用 Xamarin.Forms 項目類別，以單行文字] 或 [應用程式中的密碼輸入接受。
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 95afdfde878759d4a598e200d16fe6fb1fa2005e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998225"
---
# <a name="xamarinforms-entry"></a>Xamarin.Forms 的項目

_單行文字或輸入的密碼_

Xamarin.Forms`Entry`用於單行文字輸入。 `Entry`，例如`Editor`檢視中，支援多個鍵盤類型。 此外，`Entry`可用來當做密碼欄位。

## <a name="display-customization"></a>顯示的自訂

### <a name="setting-and-reading-text"></a>設定和讀取文字

`Entry`，例如其他文字呈現的檢視，公開`Text`屬性。 這個屬性可用來設定和讀取所呈現的文字`Entry`。 下列範例示範如何設定`Text`在 XAML 中的屬性：

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

### <a name="keyboards"></a>鍵盤

當使用者互動時，會顯示鍵盤`Entry`可以透過程式設計方式設定`Keyboard`屬性。

鍵盤類型的選項如下：

- **預設值**&ndash;預設鍵盤
- **對談**&ndash;用於傳簡訊到數位 emoji 適合的位置
- **電子郵件**&ndash;輸入電子郵件地址時，使用
- **數值**&ndash;輸入數字時，使用
- **電話**&ndash;輸入電話號碼時，使用
- **Url** &ndash;為用來輸入檔案路徑和 web 位址

沒有[範例中的每個鍵盤](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/)配方 區段中。

### <a name="enabling-and-disabling-spell-checking"></a>啟用和停用拼字檢查

[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性會控制是否拼字檢查已啟用。 根據預設，此屬性設為`true`。 當使用者輸入文字，則會指出拼字錯誤。

不過，某些文字項目的情況下，輸入使用者名稱，例如拼字檢查提供的負數的體驗，因此應該停用藉由設定[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設`false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 當[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)屬性設定為`false`，並在不使用自訂的鍵盤，將會停用原生的拼字檢查工具。 不過，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)具有已設定，以停用拼字檢查，例如[ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat)，則`IsSpellCheckEnabled`屬性會被忽略。 因此，無法使用屬性來啟用拼字檢查`Keyboard`，明確地停用。

### <a name="placeholders"></a>預留位置

`Entry` 可以設定為顯示預留位置文字，它不會儲存使用者輸入時。 在實務上，這通常被認為是在表單中釐清適用於指定欄位的內容。 預留位置文字色彩無法自訂，因此不論相同`TextColor`設定。 如果您的設計呼叫自訂的預留位置色彩時，您必須切換回[自訂轉譯器]()。 將建立下列`Entry`使用"Username"做為 XAML 中的預留位置：

```xaml
<Entry Placeholder="Username" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "項目預留位置範例")

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

- [TextChanged](xref:Xamarin.Forms.Entry.TextChanged) &ndash;文字變更的項目中時引發。 變更之前和之後，請提供的文字。
- [已完成](xref:Xamarin.Forms.Entry.Completed)&ndash;使用者已結束輸入鍵盤上按 return 鍵時引發。

### <a name="completed"></a>已完成

`Completed`事件用來回應完成的項目互動。 `Completed` 當使用者在鍵盤上輸入 return 鍵結束與欄位的輸入，就會引發。 事件處理常式是泛用事件處理常式，採取寄件者和`EventArgs`:

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
