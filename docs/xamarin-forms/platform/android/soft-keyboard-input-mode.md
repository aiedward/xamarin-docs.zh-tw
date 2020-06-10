---
標題：「Android 上的軟鍵盤輸入模式」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的來設定軟鍵盤輸入區域的操作模式。
assetid： AFCDC92F-F61E-42F6-904B-50B5C4949970 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：07/10/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="soft-keyboard-input-mode-on-android"></a>Android 上的螢幕小鍵盤輸入模式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定是用來設定軟鍵盤輸入區域的作業模式，並將 [`Application.WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) 附加屬性設為列舉的值，以在 XAML 中使用 [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) ：

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

`Application.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 [ `Application.UseWindowSoftInputModeAdjust` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. UseWindowSoftInputModeAdjust （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。應用程式}、 Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. WindowSoftInputModeAdjust））方法（在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空間中）是用來設定軟鍵盤輸入區域作業模式， [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) 列舉提供兩個值： [`Pan`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) 和 [`Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) 。 `Pan`值使用 [ [`AdjustPan`](xref:Android.Views.SoftInput.AdjustPan) 調整] 選項，當輸入控制項有焦點時，不會調整視窗大小。 相反地，視窗的內容會移動流覽，如此一來，最新的焦點就不會被軟鍵盤遮蔽。 `Resize`值使用 [ [`AdjustResize`](xref:Android.Views.SoftInput.AdjustResize) 調整] 選項，這會在輸入控制項有焦點時調整視窗大小，以騰出空間給螢幕小鍵盤。

結果是當輸入控制項有焦點時，可以設定螢幕小鍵盤輸入區域的作業模式：

[![](soft-keyboard-input-mode-images/pan-resize.png "Soft Keyboard Operating Mode Platform-Specific")](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
