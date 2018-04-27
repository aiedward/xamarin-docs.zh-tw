---
title: 編輯器
description: 多行文字輸入
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: dc83defcb3eb69cf53c205793ce77029c0947c2f
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2018
---
# <a name="editor"></a>編輯器

_多行文字輸入_

`Editor`控制項用來接受多行的輸入。 本文將說明如何：

- **[自訂](#Customization)** &ndash;鍵盤和色彩選項。
- **[互動功能](#Interactivity)** &ndash;提供互動功能可以接聽的事件。

## <a name="customization"></a>自訂

### <a name="setting-and-reading-text"></a>設定及讀取文字

編輯器，例如其他文字呈現的檢視，公開`Text`屬性。 `Text` 可用來設定並閱讀所呈現的文字`Editor`。 下列範例示範如何在 XAML 中的設定文字：

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

### <a name="keyboards"></a>鍵盤

當使用者互動時顯示鍵盤`Editor`可以透過程式設計方式設定[ ``Keyboard`` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/)屬性。

鍵盤類型的選項如下：

- **預設**&ndash;預設鍵盤
- **交談**&ndash;用於文字行動 （& s） 上的芳鄰 emoji 的非常有用
- **電子郵件**&ndash;輸入電子郵件地址時，使用
- **數值**&ndash;輸入數字時，使用
- **電話**&ndash;輸入電話號碼時，使用
- **Url** &ndash;為用來輸入檔案路徑 （& s） 之 web 位址

沒有[範例的每個鍵盤](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/)我們配方一節。

### <a name="colors"></a>色彩

`Editor` 可以設定為使用自訂背景色彩，透過`BackgroundColor`屬性。 若要確保色彩將會使用每個平台上必須特別注意。 因為每個平台都有不同的文字色彩的預設值，您可能需要設定每個平台自訂的背景色彩。 請參閱[使用平台 Tweaks](~/xamarin-forms/platform/device.md)最佳化每個平台 UI 相關的詳細資訊。

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

請確定您選擇的背景和文字色彩可以在每個平台上使用，而且不會遮住任何預留位置文字。

## <a name="interactivity"></a>互動

`Editor` 會公開兩個事件：

- [TextChanged](http://developer.xamarin.com/api/event/Xamarin.Forms.Editor.TextChanged/) &ndash;在編輯器中的文字變更時引發。 變更之前和之後，請提供的文字。
- [完成](http://developer.xamarin.com/api/event/Xamarin.Forms.Editor.Completed/)&ndash;使用者輸入鍵盤上按 return 鍵結束時引發。

### <a name="completed"></a>已完成

`Completed` 」 事件可用來做出回應的互動完成`Editor`。 `Completed` 使用者輸入鍵盤上的 return 鍵結束與欄位的輸入時引發。 事件處理常式會採取寄件者的一般事件處理常式和`EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

已完成的事件可以在程式碼和 XAML 訂閱：

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

`TextChanged` 」 事件可用來對欄位的內容中的變更做出回應。

`TextChanged` 每當引發`Text`的`Editor`變更。 事件處理常式可接受的執行個體`TextChangedEventArgs`。 `TextChangedEventArgs` 提供舊的和新值的存取權`Editor``Text`透過`OldTextValue`和`NewTextValue`屬性：

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

已完成的事件可以在程式碼和 XAML 訂閱：

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
- [編輯器應用程式開發介面](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)
