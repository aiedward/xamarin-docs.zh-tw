---
title: "進入"
description: "單行文字或輸入的密碼"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 288cb4bd782c041e6dc91f1a40b7080e7859fad3
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="entry"></a>進入

_單行文字或輸入的密碼_

Xamarin.Forms`Entry`用於單行文字輸入。 `Entry`例如編輯器 檢視中，支援多個鍵盤類型。 此外，`Entry`可用來當作密碼欄位。

## <a name="display-customization"></a>顯示的自訂

### <a name="setting-and-reading-text"></a>設定及讀取文字

項目，例如其他文字呈現的檢視，會公開`Text`屬性。 `Text` 可用來設定並閱讀所呈現的文字`Entry`。 下列範例會示範在 XAML 中設定的文字：

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
> **請注意**： 寬度`Entry`可以藉由設定定義其`WidthRequest`屬性。 請不要依賴的寬度`Entry`被定義為基礎的值及其`Text`屬性。

### <a name="keyboards"></a>鍵盤

當使用者互動時顯示鍵盤`Entry`可以透過程式設計方式設定`Keyboard`屬性。

鍵盤類型的選項如下：

- **預設**&ndash;預設鍵盤
- **交談**&ndash;用於文字行動 （& s） 上的芳鄰 emoji 的非常有用
- **電子郵件**&ndash;輸入電子郵件地址時，使用
- **數值**&ndash;輸入數字時，使用
- **電話**&ndash;輸入電話號碼時，使用
- **Url** &ndash;為用來輸入檔案路徑和網址

沒有[範例的每個鍵盤](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/)我們配方一節。

### <a name="placeholders"></a>預留位置

`Entry` 可以將它不會儲存使用者輸入時顯示預留位置文字。 在實務上，這是通常示表單以釐清適用於給定欄位的內容。 預留位置文字色彩無法自訂，而且不論相同`TextColor`設定。 如果您的設計呼叫預留位置的自訂色彩，您必須改為使用[自訂轉譯器]()。 將會建立下列`Entry`"Username"做為預留位置，在 XAML 中使用：

```xaml
<Entry Placeholder="Username" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "項目預留位置範例")

### <a name="password-fields"></a>密碼欄位

`Entry` 提供`IsPassword`屬性。 當`IsPassword`是`true`，欄位的內容會呈現為黑色圓圈：

在 XAML 中：

```xaml
<Entry IsPassword="true" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "項目 IsPassword 範例")

執行個體可用於預留位置`Entry`做為密碼欄位設定：

在 XAML 中：

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "項目 IsPassword 和預留位置範例")


### <a name="colors"></a>色彩

項目可以設定為使用自訂背景和文字色彩，透過下列繫結屬性：

- **TextColor** &ndash;設定文字的色彩。
- **BackgroundColor** &ndash;設定顯示的文字後面的色彩。

若要確保色彩將會使用每個平台上必須特別注意。 因為每個平台都有不同的預設值的文字和背景色彩，您通常必須同時設定，如果您將其中一個。

使用下列程式碼以設定文字色彩的項目：

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

請注意，將預留位置是不受指定`TextColor`。

若要在 XAML 中設定的背景色彩：

```xaml
<Entry BackgroundColor="#2c3e50" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "項目 BackgroundColor 範例")

請小心確定在每個平台上，您可以使用您選擇的背景和文字色彩，而且不會遮住任何預留位置文字。

## <a name="events-and-interactivity"></a>事件和互動功能

項目會公開兩個事件：

- [TextChanged](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) &ndash;項目中的文字變更時引發。 變更之前和之後，請提供的文字。
- [完成](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/)&ndash;使用者輸入鍵盤上按 return 鍵結束時引發。

### <a name="completed"></a>已完成

`Completed` 」 事件可用來做出回應的互動，以及項目完成。 `Completed` 使用者輸入鍵盤上的 return 鍵結束與欄位的輸入時引發。 事件處理常式會採取寄件者的一般事件處理常式和`EventArgs`:

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

`TextChanged` 」 事件可用來對欄位的內容中的變更做出回應。

`TextChanged` 每當引發`Text`的`Entry`變更。 事件處理常式可接受的執行個體`TextChangedEventArgs`。 `TextChangedEventArgs` 提供舊的和新值的存取權`Entry``Text`透過`OldTextValue`和`NewTextValue`屬性：

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

`TextChanged`可以在 XAML 中來訂閱事件：

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
- [應用程式開發介面項目](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)
