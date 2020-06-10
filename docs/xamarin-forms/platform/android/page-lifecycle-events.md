---
標題：「Android 上的頁面週期事件」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，分別停用應用程式暫停和繼續時的消失和顯示頁面事件。
assetid： F6E3759C-D347-407A-91A2-CF9B3B7D4CBD ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：07/10/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="page-lifecycle-events-on-android"></a>Android 上的頁面週期事件

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定是用來 [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 分別針對使用 AppCompat 的應用程式，停用應用程式暫停和繼續時的和頁面事件。 此外，它還可以控制是否要在恢復時顯示幕幕小鍵盤（如果是在暫停時顯示），前提是螢幕鍵盤的作業模式設定為 [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) 。

> [!NOTE]
> 請注意，這些事件預設為啟用，以保留依賴事件之應用程式的現有行為。 停用這些事件會使 AppCompat 事件週期符合預先 AppCompat 的事件週期。

您可以將 [`Application.SendDisappearingEventOnPause`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty) 、 [`Application.SendAppearingEventOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty) 和 [`Application.ShouldPreserveKeyboardOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) 附加屬性設定為值，以在 XAML 中使用這個平臺特定的 `boolean` ：

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

或者，您也可以使用 Fluent API，從 c # 取用它：

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

`Application.Current.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 [ `Application.SendDisappearingEventOnPause` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. SendDisappearingEventOnPause （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。Application}，system.string）方法， [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) 當應用程式進入背景時，會使用命名空間中的來啟用或停用引發頁面事件。 [ `Application.SendAppearingEventOnResume` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. SendAppearingEventOnResume （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。Application}，system.string）方法可用來在 [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 應用程式從背景繼續時，啟用或停用引發頁面事件。 [ `Application.ShouldPreserveKeyboardOnResume` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. ShouldPreserveKeyboardOnResume （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。Application}，system.string）方法可用來控制是否要在恢復時顯示幕幕小鍵盤（如果是在暫停時顯示），前提是螢幕鍵盤的操作模式設定為 [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) 。

結果是， [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) 和 [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 頁面事件不會分別在應用程式暫停和繼續時引發，而且如果在應用程式暫停時顯示幕幕小鍵盤，則在應用程式繼續時也會顯示它：

[![](page-lifecycle-events-images/keyboard-on-resume.png "Lifecycle Events Platform-Specific")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "Lifecycle Events Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
