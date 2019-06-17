---
title: 在 Android 上的項目輸入法編輯器選項
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Android 平台特定設定的輸入的法編輯器選項的螢幕小鍵盤的項目。
ms.prod: xamarin
ms.assetid: 7909C738-04B2-4476-9A3B-A6D79BC3B9B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4da446cf342065ce7766f8df0c71008ab47c31c4
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926826"
---
# <a name="entry-input-method-editor-options-on-android"></a>在 Android 上的項目輸入法編輯器選項

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

此 Android 平台特定設定的輸入的法編輯器 (IME) 選項的螢幕小鍵盤[ `Entry` ](xref:Xamarin.Forms.Entry)。 這包括設定使用者的 [動作] 按鈕，在螢幕小鍵盤和與互動的角`Entry`。 它由在 XAML 中設定[ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty)附加屬性的值[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)列舉型別：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

`Entry.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來設定的螢幕小鍵盤的輸入的法動作選項[ `Entry` ](xref:Xamarin.Forms.Entry)，具有[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)列舉提供下列值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) -表示沒有特定的動作索引鍵為必要項，而且其基礎的控制項將會產生其本身如果可以。 這會是`Next`或`Done`。
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) – 表示，沒有動作索引鍵都可以。
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) – 表示 [動作] 鍵將會執行 [執行] 作業，它們將文字的目標使用者輸入。
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) – 表示 [動作] 鍵執行 「 搜尋 」 作業，將使用者結果的搜尋文字輸入。
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) – 表示 [動作] 鍵將會執行 「 傳送 」 作業，傳遞到其目標的文字。
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) – 表示 [動作] 鍵，會執行"next"的作業，讓使用者可以將接受文字的下一個欄位。
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) – 表示 [動作] 鍵，會執行為 「 已完成 」 的作業，關閉螢幕小鍵盤。
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) – 表示 [動作] 鍵，會執行 「 之前 」 的作業，並將使用者帶到會接受文字的前一個欄位。
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – 要選取 [動作] 選項的遮罩。
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) – 表示拼字檢查功能將既不了解使用者，也建議修正根據使用者具有先前輸入的內容。
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) – 表示 UI 應該不會全螢幕。
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) – 指出已擷取的文字將會顯示任何 UI。
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) -表示沒有 UI，將顯示的自訂動作。

結果是，指定[ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)值套用至的螢幕小鍵盤[ `Entry` ](xref:Xamarin.Forms.Entry)，可設定的輸入的法編輯器的選項：

[![項目輸入方法編輯器平台專屬](entry-ime-options-images/entry-imeoptions.png "項目輸入方法編輯器平台專屬")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "項目輸入方法編輯器平台專屬")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
