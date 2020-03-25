---
title: XAML 中的原生檢視
description: 從 iOS、Android 和通用 Windows 平臺的原生視圖可以直接從 Xamarin. 表單 XAML 檔案參考。 屬性和事件處理常式可以在原生視圖上設定，而且可以與 Xamarin. 表單檢視進行互動。 本文示範如何從 Xamarin XAML 檔案使用原生視圖。
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2019
ms.openlocfilehash: 6d3954f44cd769ed02535eb260b9952e81e67c98
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247622"
---
# <a name="native-views-in-xaml"></a>XAML 中的原生檢視

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_從 iOS、Android 和通用 Windows 平臺的原生視圖可以直接從 Xamarin. 表單 XAML 檔案參考。屬性和事件處理常式可以在原生視圖上設定，而且可以與 Xamarin. 表單檢視進行互動。本文示範如何從 Xamarin XAML 檔案使用原生視圖。_

本文章討論下列主題：

- [使用原生視圖](#consuming)–從 XAML 使用原生視圖的程式。
- [使用原生](#native_bindings)系結–原生視圖屬性的資料系結。
- [將引數傳遞至原生](#passing_arguments)視圖–將引數傳遞至原生視圖的函式，以及呼叫原生視圖 factory 方法。
- [從程式碼參考原生視圖](#native_view_code)–從程式碼後置檔案中，抓取 XAML 檔案中宣告的原生視圖實例。
- 子類別化[原生視圖](#subclassing)–子類別化原生視圖以定義 XAML 易懂的 API。  

<a name="overview" />

## <a name="overview"></a>概觀

若要將原生視圖內嵌至 Xamarin. form XAML 檔案：

1. 在 XAML 檔案中，為包含原生視圖的命名空間加入 `xmlns` 的命名空間宣告。
1. 在 XAML 檔案中建立原生視圖的實例。

> [!IMPORTANT]
> 針對任何使用原生視圖的 XAML 頁面，必須停用編譯的 XAML。 這可以藉由使用 `[XamlCompilation(XamlCompilationOptions.Skip)]` 屬性來裝飾 XAML 頁面的程式碼後置類別來完成。 如需 XAML 編譯的詳細資訊，請參閱[Xamarin 中的 Xaml 編譯](~/xamarin-forms/xaml/xamlc.md)。

若要從程式碼後置檔案參考原生視圖，您必須使用共用的資產專案（SAP），並使用條件式編譯指示詞包裝平臺特定的程式碼。 如需詳細資訊，請參閱[從程式碼參考原生視圖](#native_view_code)。

<a name="consuming" />

## <a name="consuming-native-views"></a>使用原生視圖

下列程式碼範例示範如何將每個平臺的原生視圖使用於 Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

此外 `targetPlatform`，也必須指定原生視圖命名空間的 `clr-namespace` 和 `assembly`。 這應該設定為 `iOS`、`Android`、`UWP`、`Windows` （相當於 `UWP`）、`macOS`、`GTK`、`Tizen`或 `WPF`。 在執行時間，XAML 剖析器會忽略任何 `targetPlatform` 不符合應用程式執行所在平臺的 XML 命名空間前置詞。

每個命名空間宣告都可以用來參考指定之命名空間中的任何類別或結構。 例如，`ios` 命名空間宣告可以用來參考 iOS `UIKit` 命名空間中的任何類別或結構。 原生視圖的屬性可以透過 XAML 設定，但屬性和物件類型必須相符。 例如，`UILabel.TextColor` 屬性會設定為使用 `x:Static` 標記延伸和 `ios` 命名空間 `UIColor.Red`。

您也可以使用 `Class.BindableProperty="value"` 語法，在原生視圖上設定可系結的屬性和附加的可系結屬性。 每個原生視圖都會包裝在平臺特定的 `NativeViewWrapper` 實例中，而此實例是衍生自[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)類別。 在原生視圖上設定可系結屬性或附加的可系結屬性，會將屬性值傳送至包裝函式。 例如，您可以在原生視圖上設定 `View.HorizontalOptions="Center"` 來指定中央水準配置。

> [!NOTE]
> 請注意，樣式無法搭配原生視圖使用，因為樣式只能以 `BindableProperty` 物件支援的屬性為目標。

Android widget 的函式通常需要 Android `Context` 物件做為引數，而這可以透過 `MainActivity` 類別中的靜態屬性來使用。 因此，在 XAML 中建立 Android widget 時，`Context` 物件通常必須使用具有 `x:Static` 標記延伸的 `x:Arguments` 屬性，傳遞至 widget 的函式。 如需詳細資訊，請參閱[將引數傳遞至原生視圖](#passing_arguments)。

> [!NOTE]
> 請注意，在 .NET Standard 程式庫專案或共用的資產專案（SAP）中，不可能使用 `x:Name` 來命名原生視圖。 這麼做會產生原生類型的變數，這將會造成編譯錯誤。 不過，原生視圖可以包裝在 `ContentView` 實例中，並在程式碼後置檔案中取出，前提是已使用 SAP。 如需詳細資訊，請參閱[從程式碼參考原生視圖](#native_view_code)。

<a name="native_bindings" />

## <a name="native-bindings"></a>原生系結

資料系結是用來同步處理 UI 與其資料來源，並簡化 Xamarin Forms 應用程式如何顯示和互動其資料。 假設來源物件會執行 `INotifyPropertyChanged` 介面，則*來源*物件中的變更會由系結架構自動推送至*目標*物件，而*目標*物件中的變更則可選擇性地推送至*來源*物件。

原生視圖的屬性也可以使用資料系結。 下列程式碼範例示範使用原生視圖屬性的資料系結：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

此頁面包含的[`Entry`](xref:Xamarin.Forms.Entry) ，其[`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)屬性系結至 `NativeSwitchPageViewModel.IsSwitchOn` 屬性。 頁面的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)會設定為程式碼後置檔案中 `NativeSwitchPageViewModel` 類別的新實例，並使用 ViewModel 類別來執行 `INotifyPropertyChanged` 介面。

此頁面也包含每個平臺的原生參數。 每個原生參數都會使用[`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)系結來更新 `NativeSwitchPageViewModel.IsSwitchOn` 屬性的值。 因此，關閉開關時，會停用 `Entry`，而當參數開啟時，就會啟用 `Entry`。 下列螢幕擷取畫面顯示每個平臺上的這項功能：

![](xaml-images/native-switch-disabled.png "Native Switch Disabled")
![](xaml-images/native-switch-enabled.png "Native Switch Enabled")

如果原生屬性會執行 `INotifyPropertyChanged`，或支援 iOS 上的索引鍵/值觀察（KVO），則會自動支援雙向系結，或是 UWP 上的 `DependencyProperty`。 不過，許多原生視圖並不支援屬性變更通知。 針對這些視圖，您可以將[`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName)屬性值指定為系結運算式的一部分。 這個屬性應該設定為原生視圖中的事件名稱，以在目標屬性變更時發出信號。 然後，當原生參數的值變更時，會通知 `Binding` 類別使用者已變更參數值，而且 `NativeSwitchPageViewModel.IsSwitchOn` 屬性值已更新。

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>將引數傳遞至原生視圖

您可以使用具有 `x:Static` 標記延伸的 `x:Arguments` 屬性，將函式引數傳遞至原生視圖。 此外，您可以使用 `x:FactoryMethod` 屬性來指定方法的名稱，以及使用 `x:Arguments` 屬性，藉以呼叫原生視圖 factory 方法（`public static` 方法，其會傳回與定義方法之類別或結構相同類型的物件或值）。

下列程式碼範例示範這兩種技術：

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

[`UIFont.FromName`](xref:UIKit.UIFont.FromName*) factory 方法可用來將[`UILabel.Font`](xref:UIKit.UILabel.Font)屬性設定為 iOS 上的新[`UIFont`](xref:UIKit.UIFont) 。 `UIFont` 的名稱和大小是由 `x:Arguments` 屬性的子系的方法引數所指定。

[`Typeface.Create`](xref:Android.Graphics.Typeface.Create*) factory 方法可用來將[`TextView.Typeface`](xref:Android.Widget.TextView.Typeface)屬性設定為 Android 上的新[`Typeface`](xref:Android.Graphics.Typeface) 。 `Typeface` 系列名稱和樣式是由 `x:Arguments` 屬性的子系的方法引數所指定。

[`FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily)的函式是用來將[`TextBlock.FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily)屬性設定為通用 Windows 平臺（UWP）上的新 `FontFamily`。 `FontFamily` 名稱是由 `x:Arguments` 屬性之子系的方法引數所指定。

> [!NOTE]
> 引數必須符合此函數或 factory 方法所需的類型。

下列螢幕擷取畫面顯示指定 factory 方法和函式引數的結果，以在不同的原生視圖上設定字型：

![](xaml-images/passing-arguments.png "Setting Fonts on Native Views")

如需在 XAML 中傳遞引數的詳細資訊，請參閱[在 xaml 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)。

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>從程式碼參考原生視圖

雖然您無法使用 `x:Name` 屬性來命名原生視圖，但是您可以從共用存取專案中的程式碼後置檔案，抓取 XAML 檔案中宣告的原生視圖實例，前提是原生視圖是指定 `x:Name` 屬性值之[`ContentView`](xref:Xamarin.Forms.ContentView)的子系。 然後，在程式碼後置檔案中的條件式編譯指示詞內部，您應該：

1. 抓取[`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content)屬性值，並將它轉換成平臺特定的 `NativeViewWrapper` 類型。
1. 取出 `NativeViewWrapper.NativeElement` 屬性，並將它轉換成原生檢視類型。

然後可以在原生視圖上叫用原生 API，以執行所需的作業。 這種方法也能讓不同平臺的多個 XAML 原生視圖可以是相同[`ContentView`](xref:Xamarin.Forms.ContentView)的子系。 下列程式碼範例示範這項技術：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
              <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

在上述範例中，每個平臺的原生視圖都是[`ContentView`](xref:Xamarin.Forms.ContentView)控制項的子系，並使用 `x:Name` 屬性值來取出程式碼後置中的 `ContentView`：

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

存取[`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content)屬性，以將包裝的原生視圖當做平臺特定的 `NativeViewWrapper` 實例來取得。 接著會存取 `NativeViewWrapper.NativeElement` 屬性，以將原生視圖當做其原生類型來取得。 接著會叫用原生視圖的 API 來執行所需的作業。

IOS 和 Android 原生按鈕會共用相同的 `OnButtonTap` 事件處理常式，因為每個原生按鈕都會取用 `EventHandler` 的委派，以回應觸控事件。 不過，通用 Windows 平臺（UWP）會使用個別的 `RoutedEventHandler`，而這種情況下會耗用此範例中的 `OnButtonTap` 事件處理常式。 因此，按一下 [原生] 按鈕時，`OnButtonTap` 事件處理常式會執行，它會縮放並旋轉包含在名為 `contentViewTextParent`之[`ContentView`](xref:Xamarin.Forms.ContentView)內的原生控制項。 下列螢幕擷取畫面會示範在每個平臺上發生的情況：

![](xaml-images/contentview.png "ContentView Containing a Native Control")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>子類別化原生視圖

許多 iOS 和 Android 原生視圖並不適合在 XAML 中具現化，因為它們使用方法（而非屬性）來設定控制項。 這個問題的解決方法是在包裝函式中建立原生 views 的子類別，以定義更多 XAML 易懂的 API，以使用屬性來設定控制項，並使用與平臺無關的事件。 包裝的原生視圖可以放在共用的資產專案（SAP）中，並以條件式編譯指示詞括住，或放在平臺特定專案中，並從 XAML 中的 .NET Standard 程式庫專案參考。

下列程式碼範例示範使用子類別化原生視圖的 Xamarin Forms 頁面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

此頁面包含的[`Label`](xref:Xamarin.Forms.Label) ，會顯示使用者從原生控制項選擇的水果。 `Label` 系結至 `SubclassedNativeControlsPageViewModel.SelectedFruit` 屬性。 頁面的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)會設定為程式碼後置檔案中 `SubclassedNativeControlsPageViewModel` 類別的新實例，並使用 ViewModel 類別來執行 `INotifyPropertyChanged` 介面。

此頁面也包含每個平臺的原生選擇器視圖。 每個原生視圖都會將其 `ItemSource` 屬性系結至 `SubclassedNativeControlsPageViewModel.Fruits` 集合，藉以顯示水果的集合。 這可讓使用者挑選水果，如下列螢幕擷取畫面所示：

![](xaml-images/sub-classed.png "Subclassed Native Views")

在 iOS 和 Android 上，原生選擇器會使用方法來設定控制項。 因此，這些選擇器必須子類別化以公開屬性，使其成為 XAML 易記。 在通用 Windows 平臺（UWP）上，`ComboBox` 已經是 XAML 易懂，因此不需要子類別化。

### <a name="ios"></a>iOS

IOS 執行會將[`UIPickerView`](xref:UIKit.UIPickerView) view 子類別化，並公開可從 XAML 輕鬆取用的屬性和事件：

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

`MyUIPickerView` 類別會公開 `ItemsSource` 和 `SelectedItem` 屬性，以及 `SelectedItemChanged` 事件。 [`UIPickerView`](xref:UIKit.UIPickerView)需要基礎[`UIPickerViewModel`](xref:UIKit.UIPickerViewModel)資料模型，這是由 `MyUIPickerView` 屬性和事件所存取。 `UIPickerViewModel` 資料模型是由 `PickerModel` 類別所提供：

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

`PickerModel` 類別會透過 `Items` 屬性，提供 `MyUIPickerView` 類別的基礎儲存體。 每當 `MyUIPickerView` 中選取的專案變更時，就會執行[`Selected`](xref:UIKit.UIPickerViewModel.Selected*)方法，這會更新選取的索引並引發 `ItemChanged` 事件。 這可確保 `SelectedItem` 屬性一律會傳回使用者所挑選的最後一個專案。 此外，`PickerModel` 類別會覆寫用來設定 `MyUIPickerView` 實例的方法。

### <a name="android"></a>Android

Android 執行會將[`Spinner`](xref:Android.Widget.Spinner) view 子類別化，並公開可從 XAML 輕鬆取用的屬性和事件：

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

`MySpinner` 類別會公開 `ItemsSource` 和 `SelectedObject` 屬性，以及 `ItemSelected` 事件。 `MySpinner` 類別所顯示的專案是由與 view 關聯的[`Adapter`](xref:Android.Widget.Adapter)所提供，而當第一次設定 `ItemsSource` 屬性時，會將專案填入至 `Adapter`。 每當 `MySpinner` 類別中選取的專案變更時，`OnBindableSpinnerItemSelected` 事件處理常式就會更新 `SelectedObject` 屬性。

## <a name="summary"></a>摘要

本文示範如何從 Xamarin XAML 檔案使用原生視圖。 屬性和事件處理常式可以在原生視圖上設定，而且可以與 Xamarin. 表單檢視進行互動。

## <a name="related-links"></a>相關連結

- [NativeSwitch （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [原生格式](~/xamarin-forms/platform/native-forms.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
