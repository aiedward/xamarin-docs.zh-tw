---
title: XAML 中的原生檢視
description: 從 iOS、Android 和通用 Windows 平臺的原生視圖可以直接從 XAML 檔案參考 Xamarin.Forms 。 屬性和事件處理常式可以在原生視圖上設定，並且可以與 Xamarin.Forms 視圖互動。 本文示範如何使用 XAML 檔案中的原生視圖 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 78bd93e2f6556480ae7d2903771d7d6303dda148
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558372"
---
# <a name="native-views-in-xaml"></a>XAML 中的原生檢視

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_從 iOS、Android 和通用 Windows 平臺的原生視圖可以直接從 XAML 檔案參考 Xamarin.Forms 。屬性和事件處理常式可以在原生視圖上設定，並且可以與 Xamarin.Forms 視圖互動。本文示範如何使用 XAML 檔案中的原生視圖 Xamarin.Forms 。_

若要將原生視圖內嵌至 Xamarin.Forms XAML 檔案：

1. `xmlns`針對包含原生視圖的命名空間，在 XAML 檔案中加入命名空間宣告。
1. 在 XAML 檔案中建立原生視圖的實例。

> [!IMPORTANT]
> 使用原生視圖的任何 XAML 頁面都必須停用編譯的 XAML。 您可以使用屬性裝飾 XAML 頁面的程式碼後端類別來完成這項作業 `[XamlCompilation(XamlCompilationOptions.Skip)]` 。 如需 XAML 編譯的詳細資訊，請參閱[中 Xamarin.Forms 的 xaml 編譯](~/xamarin-forms/xaml/xamlc.md)。

若要從程式碼後端檔案參考原生視圖，您必須使用 (SAP) 的共用資產專案，並使用條件式編譯指示詞包裝平臺特定程式碼。 如需詳細資訊，請參閱 [從程式碼參考原生視圖](#refer-to-native-views-from-code)。

## <a name="consume-native-views"></a>使用原生視圖

下列程式碼範例將示範如何使用每個平臺的原生視圖 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

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

以及指定 `clr-namespace` `assembly` 原生視圖命名空間的和， `targetPlatform` 也必須指定。 這應該設定為 `iOS` 、 `Android` 、 `UWP` 、 (， `Windows` 相當於 `UWP`) 、、 `macOS` `GTK` 、 `Tizen` 或 `WPF` 。 在執行時間，XAML 剖析器將會忽略任何 `targetPlatform` 不符合應用程式執行所在平臺的 XML 命名空間前置詞。

每個命名空間宣告都可以用來從指定的命名空間參考任何類別或結構。 例如， `ios` 命名空間宣告可以用來參考 iOS 命名空間中的任何類別或結構 `UIKit` 。 原生視圖的屬性可透過 XAML 設定，但屬性和物件類型必須相符。 例如， `UILabel.TextColor` 屬性會設定為 `UIColor.Red` 使用 `x:Static` 標記延伸和 `ios` 命名空間。

您也可以使用語法，在原生視圖上設定可系結屬性和附加的可系結屬性 `Class.BindableProperty="value"` 。 每個原生視圖都會包裝在 `NativeViewWrapper` 從類別衍生的平臺特定實例中 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) 。 在原生視圖上設定可系結屬性或附加的可系結屬性，會將屬性值傳送至包裝函式。 例如，您可以 `View.HorizontalOptions="Center"` 在原生視圖上設定，藉以指定中央水準版面配置。

> [!NOTE]
> 請注意，樣式無法與原生視圖一起使用，因為樣式只能以物件所支援的屬性為目標 `BindableProperty` 。

Android widget 的函式通常需要 Android `Context` 物件做為引數，而這可以透過類別中的靜態屬性來提供 `MainActivity` 。 因此，當您在 XAML 中建立 Android widget 時， `Context` 通常必須使用 `x:Arguments` 具有標記延伸的屬性，將物件傳遞至 widget 的函式 `x:Static` 。 如需詳細資訊，請參閱 [將引數傳遞給原生視圖](#pass-arguments-to-native-views)。

> [!NOTE]
> 請注意， `x:Name` 在 .NET Standard 程式庫專案或 (SAP) 的共用資產專案中，無法將原生視圖命名。 這麼做會產生原生類型的變數，這會造成編譯錯誤。 不過，原生視圖可以包裝在 `ContentView` 實例中，並在程式碼後端檔案中取出（前提是要使用 SAP）。 如需詳細資訊，請參閱 [從程式碼參考原生視圖](#refer-to-native-views-from-code)。

## <a name="native-bindings"></a>原生系結

資料系結是用來同步處理 UI 與其資料來源，並簡化 Xamarin.Forms 應用程式顯示和與其資料互動的方式。 假設來源物件會執行 `INotifyPropertyChanged` 介面，則 *來源* 物件中的變更會由系結架構自動推送至 *目標* 物件，而 *目標* 物件中的變更則可以選擇性地推送至 *來源* 物件。

原生視圖的屬性也可以使用資料系結。 下列程式碼範例示範如何使用原生視圖的屬性來系結資料：

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

頁面包含 [`Entry`](xref:Xamarin.Forms.Entry) 其屬性系結 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) 至屬性的 `NativeSwitchPageViewModel.IsSwitchOn` 。 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)頁面的會設定為 `NativeSwitchPageViewModel` 程式碼後端檔案中類別的新實例，並使用 ViewModel 類別來執行 `INotifyPropertyChanged` 介面。

此頁面也包含每個平臺的原生參數。 每個原生參數都使用系結 [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) 來更新屬性的值 `NativeSwitchPageViewModel.IsSwitchOn` 。 因此，當參數關閉時， `Entry` 會停用，當切換開啟時， `Entry` 即會啟用。 下列螢幕擷取畫面顯示每個平臺上的這項功能：

![原生切換已停用 ](xaml-images/native-switch-disabled.png)
 ![ 原生切換](xaml-images/native-switch-enabled.png)

自動支援雙向系結，前提是原生屬性會在 `INotifyPropertyChanged` iOS 上執行或支援 (KVO) 的索引鍵/值觀察，或是 `DependencyProperty` UWP 上的。 但是，許多原生視圖都不支援屬性變更通知。 針對這些視圖，您可以指定 [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) 屬性值做為系結運算式的一部分。 這個屬性應該設為原生視圖中的事件名稱，以在目標屬性變更時發出信號。 然後，當原生參數的值變更時， `Binding` 會通知類別使用者已變更參數值，而且 `NativeSwitchPageViewModel.IsSwitchOn` 屬性值已更新。

## <a name="pass-arguments-to-native-views"></a>將引數傳遞至原生視圖

您可以使用 `x:Arguments` 具有標記延伸的屬性，將函式引數傳遞至原生視圖 `x:Static` 。 此外，原生視圖 factory 方法 (`public static` 方法，這些方法會傳回與定義方法的類別或結構相同類型的物件或值) 可以藉由使用屬性指定方法的名稱 `x:FactoryMethod` ，以及使用屬性的引數來呼叫 `x:Arguments` 。

下列程式碼範例將示範這兩種技術：

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

[`UIFont.FromName`](xref:UIKit.UIFont.FromName*)Factory 方法是用來將屬性設 [`UILabel.Font`](xref:UIKit.UILabel.Font) 為 [`UIFont`](xref:UIKit.UIFont) iOS 上的新。 `UIFont`名稱和大小是由屬性子系的方法引數所指定 `x:Arguments` 。

[`Typeface.Create`](xref:Android.Graphics.Typeface.Create*)Factory 方法是用來將屬性設 [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) 為 Android 上的新屬性 [`Typeface`](xref:Android.Graphics.Typeface) 。 `Typeface`系列名稱和樣式是由屬性子系的方法引數所指定 `x:Arguments` 。

您 [`FontFamily`](/uwp/api/Windows.UI.Xaml.Media.FontFamily) 可以使用此函式，在 [`TextBlock.FontFamily`](/uwp/api/Windows.UI.Xaml.Controls.TextBlock) `FontFamily` 通用 Windows 平臺 (UWP) 上將屬性設定為新的。 此 `FontFamily` 名稱是由屬於屬性子系的方法引數所指定 `x:Arguments` 。

> [!NOTE]
> 引數必須符合函式或 factory 方法所需的類型。

下列螢幕擷取畫面顯示指定 factory 方法和函式引數，以在不同原生視圖上設定字型的結果：

![在原生視圖上設定字型](xaml-images/passing-arguments.png)

如需在 XAML 中傳遞引數的詳細資訊，請參閱 [在 xaml 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)。

## <a name="refer-to-native-views-from-code"></a>從程式碼參考原生視圖

雖然您無法將原生視圖命名為屬性，但您可以 `x:Name` 從共用存取專案中的程式碼後端檔案取出 XAML 檔案中宣告的原生視圖實例，前提是原生視圖為 [`ContentView`](xref:Xamarin.Forms.ContentView) 指定屬性值之的子系 `x:Name` 。 然後，在程式碼後端檔案的條件式編譯指示詞內，您應該：

1. 抓取 [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) 屬性值，並將它轉換為平臺特定的 `NativeViewWrapper` 類型。
1. 取得 `NativeViewWrapper.NativeElement` 屬性，並將它轉換成原生檢視類型。

然後可以在原生視圖上叫用原生 API，以執行所需的作業。 這種方法也會提供不同平臺的多個 XAML 原生視圖可以是相同的子系的優點 [`ContentView`](xref:Xamarin.Forms.ContentView) 。 下列程式碼範例將示範這項技術：

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

在上述範例中，每個平臺的原生視圖都是控制項的子系 [`ContentView`](xref:Xamarin.Forms.ContentView) ，而且 `x:Name` 屬性值會用來在 `ContentView` 程式碼後端中取出：

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

[`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content)存取屬性可將包裝的原生視圖取出為平臺特定的 `NativeViewWrapper` 實例。 `NativeViewWrapper.NativeElement`接著會存取屬性，以將原生視圖取出為其原生類型。 然後叫用原生視圖的 API 來執行所需的作業。

IOS 和 Android 原生按鈕共用相同的 `OnButtonTap` 事件處理常式，因為每個原生按鈕都會使用 `EventHandler` 委派來回應觸控事件。 不過，通用 Windows 平臺 (UWP) 會使用不同的 `RoutedEventHandler` ，而這會在 `OnButtonTap` 此範例中使用事件處理常式。 因此，當您按一下 [原生] 按鈕時， `OnButtonTap` 事件處理常式就會執行，這會調整並旋轉包含在命名內的原生控制項 [`ContentView`](xref:Xamarin.Forms.ContentView) `contentViewTextParent` 。 下列螢幕擷取畫面示範在每個平臺上發生這種情況：

![包含原生控制項的 ContentView](xaml-images/contentview.png)

## <a name="subclass-native-views"></a>子類別化原生視圖

許多 iOS 和 Android 原生視圖都不適合在 XAML 中具現化，因為它們會使用方法（而非屬性）來設定控制項。 此問題的解決方法是在包裝函式中，將原生視圖子類別化，以定義更適合 XAML 的 API，此 API 會使用屬性來設定控制項，並使用平臺獨立事件。 然後，您可以將包裝的原生視圖放在共用資產專案中， (SAP) 並以條件式編譯指示詞括住，或放在平臺特定專案中，然後在 .NET Standard 程式庫專案的 XAML 中參考。

下列程式碼範例示範使用子類別化 Xamarin.Forms 原生視圖的頁面：

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

頁面包含 [`Label`](xref:Xamarin.Forms.Label) ，可顯示使用者從原生控制項選擇的水果。 系結 `Label` 至 `SubclassedNativeControlsPageViewModel.SelectedFruit` 屬性。 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)頁面的會設定為 `SubclassedNativeControlsPageViewModel` 程式碼後端檔案中類別的新實例，並使用 ViewModel 類別來執行 `INotifyPropertyChanged` 介面。

此頁面也包含每個平臺的原生選擇器視圖。 每個原生視圖都會將其屬性系結至集合，以顯示水果的集合 `ItemSource` `SubclassedNativeControlsPageViewModel.Fruits` 。 這可讓使用者挑選水果，如下列螢幕擷取畫面所示：

![子類別化原生視圖](xaml-images/sub-classed.png)

在 iOS 和 Android 上，原生選擇器會使用方法來設定控制項。 因此，這些選擇器必須子類別化才能公開屬性，使其可供 XAML 理解。 在通用 Windows 平臺 (UWP) 上，已 `ComboBox` 具備 XAML 的易用性，所以不需要子類別化。

### <a name="ios"></a>iOS

IOS 執行會將視圖子類別化 [`UIPickerView`](xref:UIKit.UIPickerView) ，並公開可從 XAML 輕鬆取用的屬性和事件：

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

`MyUIPickerView`類別會公開 `ItemsSource` 和 `SelectedItem` 屬性，以及 `SelectedItemChanged` 事件。 [`UIPickerView`](xref:UIKit.UIPickerView)需要基礎 [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) 資料模型（由 `MyUIPickerView` 屬性和事件存取）。 `UIPickerViewModel`資料模型是由 `PickerModel` 類別提供：

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

類別會透過 `PickerModel` 屬性提供類別的基礎儲存體 `MyUIPickerView` `Items` 。 每當變更中的選取專案時， `MyUIPickerView` [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) 就會執行方法，以更新選取的索引並引發 `ItemChanged` 事件。 這可確保 `SelectedItem` 屬性一律會傳回使用者所挑選的最後一個專案。 此外，此 `PickerModel` 類別會覆寫用來設定實例的方法 `MyUIPickerView` 。

### <a name="android"></a>Android

Android 執行會將視圖子類別化 [`Spinner`](xref:Android.Widget.Spinner) ，並公開可從 XAML 輕鬆取用的屬性和事件：

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

`MySpinner`類別會公開 `ItemsSource` 和 `SelectedObject` 屬性，以及 `ItemSelected` 事件。 類別所顯示的專案 `MySpinner` 是由 [`Adapter`](xref:Android.Widget.Adapter) 與視圖相關聯的所提供，而專案則會 `Adapter` 在 `ItemsSource` 第一次設定屬性時填入。 每當類別中選取的專案 `MySpinner` 變更時， `OnBindableSpinnerItemSelected` 事件處理常式就會更新 `SelectedObject` 屬性。

## <a name="related-links"></a>相關連結

- [NativeSwitch (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (範例) ](/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [原生表單](~/xamarin-forms/platform/native-forms.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)