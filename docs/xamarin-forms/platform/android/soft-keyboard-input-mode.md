---
title: Android 上的軟鍵盤輸入模式
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，以設定軟鍵盤輸入區域的操作模式。
ms.prod: xamarin
ms.assetid: AFCDC92F-F61E-42F6-904B-50B5C4949970
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e041c766d0a4beefb8b4c74b9024c7554c818511
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368631"
---
# <a name="soft-keyboard-input-mode-on-android"></a>Android 上的軟鍵盤輸入模式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定用來設定軟鍵盤輸入區域的操作模式，並藉由將 [`Application.WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) 附加屬性設定為列舉的值，在 XAML 中使用它 [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) ：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

`Application.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 [ `Application.UseWindowSoftInputModeAdjust` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. UseWindowSoftInputModeAdjust (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。應用程式}、 Xamarin.Forms 。命名空間中的 PlatformConfiguration. AndroidSpecific. WindowSoftInputModeAdjust) # A3 方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 可用來設定軟鍵盤輸入區域作業模式，並 [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) 提供兩個值的列舉： [`Pan`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) 和 [`Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) 。 `Pan`值使用 [`AdjustPan`](xref:Android.Views.SoftInput.AdjustPan) 調整選項，當輸入控制項具有焦點時，不會調整視窗的大小。 相反地，系統會移動流覽視窗的內容，讓目前的焦點不會被軟鍵盤遮蔽。 `Resize`值會使用 [`AdjustResize`](xref:Android.Views.SoftInput.AdjustResize) 調整選項，這會在輸入控制項具有焦點時調整視窗大小，以騰出空間給軟鍵盤。

結果是，當輸入控制項具有焦點時，可以設定「軟鍵盤輸入區域」作業模式：

[![軟鍵盤操作模式平臺特定](soft-keyboard-input-mode-images/pan-resize.png)](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "軟鍵盤操作模式 Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)