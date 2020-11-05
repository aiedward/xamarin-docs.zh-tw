---
title: Android 上的頁面生命週期事件
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，以停用應用程式暫停和繼續時的消失和出現的頁面事件。
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: af3954df9db94a56e1097b2de0451d6486916fa2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374520"
---
# <a name="page-lifecycle-events-on-android"></a>Android 上的頁面生命週期事件

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定用來 [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 針對使用 AppCompat 的應用程式，分別停用應用程式暫停和繼續的和頁面事件。 此外，它還可以控制是否在恢復時顯示軟鍵盤（如果在 [暫停] 時顯示），只要將軟鍵盤的操作模式設為 [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) 。

> [!NOTE]
> 請注意，預設會啟用這些事件，以針對依賴事件的應用程式保留現有的行為。 停用這些事件會使 AppCompat 事件迴圈符合預先 AppCompat 的事件週期。

將 [`Application.SendDisappearingEventOnPause`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty) 、 [`Application.SendAppearingEventOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty) 和 [`Application.ShouldPreserveKeyboardOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) 附加屬性設定為值，即可在 XAML 中使用這個平臺特定 `boolean` ：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

`Application.Current.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 [ `Application.SendDisappearingEventOnPause` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. SendDisappearingEventOnPause (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。Application}、system.string) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) 命名空間中）是用來啟用或停用在 [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) 應用程式進入背景時引發頁面事件的功能。 [ `Application.SendAppearingEventOnResume` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. SendAppearingEventOnResume (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。Application}、system.string) # A3 方法可用來啟用或停用在 [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 應用程式從背景繼續時引發頁面事件。 [ `Application.ShouldPreserveKeyboardOnResume` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. ShouldPreserveKeyboardOnResume (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。Application}、system.string) # A3 方法可用來控制是否要在繼續時顯示軟鍵盤（如果在 [暫停] 時顯示），前提是已將軟鍵盤的作業模式設定為 [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) 。

結果是， [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) 和 [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 頁面事件不會在應用程式暫停和繼續時引發，而且如果螢幕小鍵盤是在應用程式暫停時顯示，則也會在應用程式繼續時顯示：

[![生命週期事件平臺特定](page-lifecycle-events-images/keyboard-on-resume.png)](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "生命週期事件 Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)