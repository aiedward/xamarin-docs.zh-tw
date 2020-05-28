---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bb77e9fafe39bf76a7d4290dba0bc658cd15094f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140032"
---
# <a name="entry-input-method-editor-options-on-android"></a>Android 上的專案輸入法方法編輯器選項

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定會為的螢幕小鍵盤設定輸入法編輯器（IME）選項 [`Entry`](xref:Xamarin.Forms.Entry) 。 這包括設定軟鍵盤右下角的 [使用者動作] 按鈕，以及與的互動 `Entry` 。 它會在 XAML 中使用，方法是將 [`Entry.ImeOptions`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) 附加屬性設為列舉的值 [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

`Entry.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 [ `Entry.SetImeOptions` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetImeOptions （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。Entry}、 Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. ImeFlags））方法（在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空間中）是用來設定的軟鍵盤的 [輸入方法動作] 選項 [`Entry`](xref:Xamarin.Forms.Entry) ，其 [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) 列舉提供下列值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default)-表示不需要特定的動作索引鍵，而且基礎控制項會自行產生（如果可以的話）。 這會是 `Next` 或 `Done` 。
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None)–表示不會提供任何動作金鑰。
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go)–表示動作索引鍵將會執行 "go" 作業，讓使用者進入他們所輸入文字的目標。
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search)–表示動作按鍵會執行「搜尋」作業，讓使用者能夠搜尋其所輸入的文字。
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send)–表示動作索引鍵將會執行「傳送」作業，並將文字傳遞至其目標。
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next)–表示動作索引鍵將會執行「下一步」作業，讓使用者進入下一個將接受文字的欄位。
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done)–表示動作按鍵會執行「完成」作業，關閉螢幕小鍵盤。
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous)–表示動作索引鍵將執行「上一步」作業，將使用者帶到接受文字的前一個欄位。
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction)–用來選取動作選項的遮罩。
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning)–表示 spellchecker 不會從使用者學習，也不會根據使用者先前所輸入的內容來建議更正。
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen)–表示 UI 不應為全螢幕。
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi)–表示不會針對已解壓縮的文字顯示任何 UI。
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction)–表示不會針對自訂動作顯示任何 UI。

結果是指定的 [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) 值會套用至的軟鍵盤 [`Entry`](xref:Xamarin.Forms.Entry) ，以設定輸入法選項：

[![專案輸入方法編輯器平臺特定](entry-ime-options-images/entry-imeoptions.png "專案輸入方法編輯器平臺特定")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "專案輸入方法編輯器平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
