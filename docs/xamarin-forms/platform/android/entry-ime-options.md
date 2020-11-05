---
title: Android 上的專案輸入方法編輯器選項
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，為輸入的軟鍵盤設定輸入法的選項。
ms.prod: xamarin
ms.assetid: 7909C738-04B2-4476-9A3B-A6D79BC3B9B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 79ceca6f028ec5d0399251e178d82d1b2fff81ef
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373337"
---
# <a name="entry-input-method-editor-options-on-android"></a>Android 上的專案輸入方法編輯器選項

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定會將輸入方法編輯器 (IME) 選項提供給的軟鍵盤 [`Entry`](xref:Xamarin.Forms.Entry) 。 這包括設定軟鍵盤右下角的 [使用者動作] 按鈕，以及與的互動 `Entry` 。 它是在 XAML 中使用，方法是將 [`Entry.ImeOptions`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) 附加屬性設定為 [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) 列舉值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

`Entry.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 [ `Entry.SetImeOptions` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetImeOptions (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。Entry}、 Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. ImeFlags) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空間中）是用來為的軟鍵盤設定 [輸入方法] 動作選項 [`Entry`](xref:Xamarin.Forms.Entry) ，且 [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) 列舉提供下列值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) –表示不需要任何特定的動作索引鍵，而且基礎控制項可能會產生其本身。 這將是 `Next` 或 `Done` 。
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) –表示不會提供任何動作金鑰。
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) –指出動作索引鍵將執行 "go" 作業，並將使用者帶到其所輸入文字的目標。
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) –表示動作金鑰會執行「搜尋」作業，讓使用者能夠搜尋他們所輸入的文字。
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) –指出動作索引鍵將會執行「傳送」作業，並將文字傳遞至其目標。
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) –指出動作索引鍵將會執行「下一步」作業，讓使用者前往下一個將接受文字的欄位。
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) –指出動作索引鍵將會執行「完成」作業，並關閉軟鍵盤。
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) –指出動作索引鍵將會執行「先前」作業，並將使用者帶到將接受文字的前一個欄位。
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) –用來選取動作選項的遮罩。
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) –表示 spellchecker 將不會向使用者學習，也不會根據使用者先前輸入的內容提出更正建議。
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) –表示 UI 不應進入全螢幕。
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) –表示不會顯示已解壓縮文字的 UI。
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) –表示自訂動作不會顯示任何 UI。

結果會將指定的 [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) 值套用至的軟鍵盤 [`Entry`](xref:Xamarin.Forms.Entry) ，以設定輸入法的選項：

[![專案輸入方法編輯器平臺特定](entry-ime-options-images/entry-imeoptions.png "專案輸入方法編輯器平臺特定")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "專案輸入方法編輯器平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)