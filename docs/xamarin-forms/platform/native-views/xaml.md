---
title: 在 XAML 中的原生檢視
description: 從 iOS、 Android 和通用 Windows 平台的原生檢視可以直接參考從 Xamarin.Forms XAML 檔案。 原生的檢視，可以設定屬性和事件處理常式，它們可以與 Xamarin.Forms 檢視互動。 這篇文章會示範如何使用 Xamarin.Forms XAML 檔案中的原生檢視。
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: 3c4fa085c9fdf17cdc256d9710c23911bb60d584
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770637"
---
# <a name="native-views-in-xaml"></a>在 XAML 中的原生檢視

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_從 iOS、 Android 和通用 Windows 平台的原生檢視可以直接參考從 Xamarin.Forms XAML 檔案。原生的檢視，可以設定屬性和事件處理常式，它們可以與 Xamarin.Forms 檢視互動。這篇文章會示範如何使用 Xamarin.Forms XAML 檔案中的原生檢視。_

這篇文章討論下列主題：

- [使用原生檢視](#consuming)– 使用 XAML 的原生檢視的程序。
- [使用原生的繫結](#native_bindings)– 資料繫結與原生檢視的屬性。
- [將引數傳遞至原生檢視](#passing_arguments)– 將引數傳遞至原生檢視建構函式，並呼叫原生檢視 factory 方法。
- [從程式碼參考原生檢視](#native_view_code)– 擷取原生檢視執行個體的 XAML 檔案，從其程式碼後置檔案中宣告。
- [子類別化原生檢視](#subclassing)– 子類別化來定義 XAML 容易使用的 API 的原生檢視。  

<a name="overview" />

## <a name="overview"></a>總覽

若要內嵌到 Xamarin.Forms XAML 檔案的原生檢視：

1. 新增`xmlns`包含原生檢視的命名空間的 XAML 檔案中的命名空間宣告。
1. 在 XAML 檔案中建立原生檢視的執行個體。

> [!IMPORTANT]
> 針對任何使用原生視圖的 XAML 頁面，必須停用編譯的 XAML。 這可以藉由使用`[XamlCompilation(XamlCompilationOptions.Skip)]`屬性來裝飾 XAML 頁面的程式碼後置類別來完成。 如需 XAML 編譯的詳細資訊，請參閱[Xamarin 中的 Xaml 編譯](~/xamarin-forms/xaml/xamlc.md)。

若要從程式碼後置檔案參考原生的檢視，您必須使用共用資產專案 (SAP)，並將條件式編譯指示詞與平台特定程式碼包裝。 如需詳細資訊，請參閱[從程式碼參考原生檢視](#native_view_code)。

<a name="consuming" />

## <a name="consuming-native-views"></a>使用原生檢視

下列程式碼範例會示範如何使用 Xamarin.Forms 每個平台的原生檢視[ `ContentPage` ](xref:Xamarin.Forms.ContentPage):

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

以及指定`clr-namespace`並`assembly`原生檢視命名空間`targetPlatform`也必須指定。 這應該設定為其中一個值[ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform)列舉型別，而且通常會設定為`iOS`， `Android`，或`Windows`。 在執行階段，XAML 剖析器將會忽略任何 XML 命名空間前置詞具有`targetPlatform`不符合應用程式執行所在的平台。

每個命名空間宣告可用來從指定的命名空間參考任何類別或結構。 例如，`ios`命名空間宣告可用來參考任何類別或結構，從 iOS`UIKit`命名空間。 原生檢視的屬性可以設定透過 XAML，但必須符合的屬性和物件類型。 例如，`UILabel.TextColor`屬性設定為`UIColor.Red`使用`x:Static`標記延伸和`ios`命名空間。

可繫結屬性和附加的可繫結屬性也可以設定在原生檢視上使用`Class.BindableProperty="value"`語法。 每個原生的檢視會包裝在特定平台`NativeViewWrapper`執行個體，它是衍生自[ `Xamarin.Forms.View` ](xref:Xamarin.Forms.View)類別。 設定 原生的檢視中的 繫結的屬性或附加的可繫結屬性包裝函式傳輸屬性值。 例如，指定置中的水平版面配置，藉由設定`View.HorizontalOptions="Center"`上原生的檢視。

> [!NOTE]
> 請注意樣式無法搭配原生的檢視，因為樣式可以只為目標所支援的屬性`BindableProperty`物件。

Android 的小工具的建構函式通常需要 Android`Context`物件引數，而這可透過靜態屬性`MainActivity`類別。 因此，在 XAML 中，建立 Android 的小工具時`Context`物件通常必須傳遞至小工具的建構函式使用`x:Arguments`屬性搭配`x:Static`標記延伸。 如需詳細資訊，請參閱 <<c0> [ 傳遞的引數至原生檢視](#passing_arguments)。

> [!NOTE]
> 請注意命名的原生檢視`x:Name`不能在.NET Standard 程式庫專案或共用資產專案 (SAP)。 這樣會產生原生類型，這會導致編譯錯誤的變數。 不過，包裝原生檢視在`ContentView`執行個體，並擷取在程式碼後置檔案中，前提是正在使用的 SAP。 如需詳細資訊，請參閱 <<c0> [ 從程式碼參考原生檢視](#native_view_code)。

<a name="native_bindings" />

## <a name="native-bindings"></a>原生的繫結

資料繫結用於其資料來源，與同步處理使用者介面，並簡化 Xamarin.Forms 應用程式的顯示方式，以及與資料互動。 前提是來源物件會實作`INotifyPropertyChanged`介面，變更*來源*物件都會自動發送給*目標*物件的繫結架構和變更*目標*物件可以選擇性地推送至*來源*物件。

原生檢視的屬性也可以使用資料繫結。 下列程式碼範例示範使用的原生檢視屬性的資料繫結：

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

此頁面包含[ `Entry` ](xref:Xamarin.Forms.Entry)其[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)屬性繫結至`NativeSwitchPageViewModel.IsSwitchOn`屬性。 [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的頁面設定的新執行個體`NativeSwitchPageViewModel`類別在程式碼後置檔案中，使用 ViewModel 類別實作`INotifyPropertyChanged`介面。

此頁面也包含每個平台的原生交換器。 使用每個原生交換器[ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)更新的值的繫結`NativeSwitchPageViewModel.IsSwitchOn`屬性。 因此，當參數為 off，`Entry`已停用，以及當參數為 on，`Entry`已啟用。 下列螢幕擷取畫面會顯示這項功能，每個平台：

![](xaml-images/native-switch-disabled.png "原生交換器已停用")
![](xaml-images/native-switch-enabled.png "原生交換器已啟用")

前提是原生屬性實作，會自動支援雙向繫結`INotifyPropertyChanged`，在 iOS 上，支援索引鍵-值觀察 (KVO) 或`DependencyProperty`UWP 上。 不過，許多原生檢視不支援屬性變更通知。 對於這些檢視中，您可以指定[ `UpdateSourceEventName` ](xref:Xamarin.Forms.Binding.UpdateSourceEventName)屬性值，做為繫結運算式的一部分。 此屬性應該設定為原生通知的目標屬性已變更的檢視中的事件名稱。 然後，當原生參數的值變更時，`Binding`類別會收到通知，使用者已變更參數值，而`NativeSwitchPageViewModel.IsSwitchOn`更新屬性值。

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>引數傳遞給原生檢視

可以傳遞給原生檢視使用的建構函式引數`x:Arguments`屬性搭配`x:Static`標記延伸。 此外，原生檢視 factory 方法 (`public static`方法會傳回物件或值型別的相同類別或結構，其定義的方法) 可以藉由指定方法的呼叫使用`x:FactoryMethod`屬性和其引數使用`x:Arguments`屬性。

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

[ `UIFont.FromName` ](xref:UIKit.UIFont.FromName*) Factory 方法用於設定[ `UILabel.Font` ](xref:UIKit.UILabel.Font)屬性，以新[ `UIFont` ](xref:UIKit.UIFont)在 iOS 上。 `UIFont`子系的方法引數所指定的名稱和大小`x:Arguments`屬性。

[ `Typeface.Create` ](xref:Android.Graphics.Typeface.Create*) Factory 方法用於設定[ `TextView.Typeface` ](xref:Android.Widget.TextView.Typeface)屬性，以新[ `Typeface` ](xref:Android.Graphics.Typeface)在 Android 上。 `Typeface`子系的方法引數所指定系列名稱和樣式`x:Arguments`屬性。

[ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily)建構函式用來設定[ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily)屬性，以新`FontFamily`通用 Windows 平台 (UWP) 上。 `FontFamily`子系的方法引數所指定名稱`x:Arguments`屬性。

> [!NOTE]
> 引數必須符合建構函式或 factory 方法所需的類型。

下列螢幕擷取畫面會顯示指定不同的原生檢視上設定的字型 factory 方法和建構函式引數的結果：

![](xaml-images/passing-arguments.png "設定字型的原生檢視")

如需在 XAML 中傳遞引數的詳細資訊，請參閱[XAML 中傳遞的引數](~/xamarin-forms/xaml/passing-arguments.md)。

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>從程式碼參考原生檢視

雖然未命名的原生檢視`x:Name`屬性，就可以擷取自其程式碼後置檔案，在共用存取專案中，XAML 檔案中所宣告的原生檢視執行個體，前提是原生的檢視是子系[ `ContentView` ](xref:Xamarin.Forms.ContentView)指定`x:Name`屬性值。 然後，程式碼後置檔案中的條件式編譯指示詞內您應該：

1. 擷取[ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content)屬性值，並將它轉換成特定平台`NativeViewWrapper`型別。
1. 擷取`NativeViewWrapper.NativeElement`屬性並將其轉換成原生檢視型別。

原生 API 則叫用原生的檢視，來執行所需的作業。 這個方法也提供權益，多個 XAML 原生檢視針對不同平台可以是相同的子系[ `ContentView` ](xref:Xamarin.Forms.ContentView)。 下列程式碼範例示範這項技術：

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

上述範例中，每個平台的原生檢視都的子系[ `ContentView` ](xref:Xamarin.Forms.ContentView)控制項，與`x:Name`屬性值，用來擷取`ContentView`中程式碼後置：

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

[ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content)存取屬性來擷取特定平台為包裝原生檢視`NativeViewWrapper`執行個體。 `NativeViewWrapper.NativeElement`擷取原生的檢視做為其原生類型，然後存取屬性。 原生檢視的 API 則會叫用來執行所需的作業。

IOS 和 Android 的原生按鈕會共用相同`OnButtonTap`事件處理常式，因為每個原生的按鈕都會耗用`EventHandler`委派以回應觸控事件。 不過，通用 Windows 平台 (UWP) 會使用個別`RoutedEventHandler`，而這又會消耗`OnButtonTap`在此範例中的事件處理常式。 因此，當原生按一下按鈕時，`OnButtonTap`事件處理常式執行的縮放和旋轉內所包含的原生控制項[ `ContentView` ](xref:Xamarin.Forms.ContentView)名為`contentViewTextParent`。 下列螢幕擷取畫面會示範如何發生這種情況在每個平台上：

![](xaml-images/contentview.png "ContentView 包含原生控制項")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>子類別化原生檢視

許多的 iOS 和 Android 的原生檢視並不適合在 XAML 中執行個體化，因為它們使用的方法，而不是屬性，來設定控制項。 此問題的解決方案是以子類別中定義更適合 XAML 的 API，會使用屬性來設定控制項，並使用平台無關的事件的包裝函式的原生檢視。 包裝原生檢視可以是放在共用資產專案 」 (SAP) 和加上條件式編譯指示詞，或放在平台專屬專案並從 XAML 參考.NET Standard 程式庫專案中。

下列程式碼範例示範如何使用 Xamarin.Forms 頁面子類別化原生檢視：

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

此頁面包含[ `Label` ](xref:Xamarin.Forms.Label)顯示水果由使用者選擇從原生控制項。 `Label`繫結至`SubclassedNativeControlsPageViewModel.SelectedFruit`屬性。 [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的頁面設定的新執行個體`SubclassedNativeControlsPageViewModel`類別在程式碼後置檔案中，使用 ViewModel 類別實作`INotifyPropertyChanged`介面。

此頁面也包含每個平台的原生的選擇器 檢視。 每個原生的檢視會顯示水果的集合繫結及其`ItemSource`屬性設`SubclassedNativeControlsPageViewModel.Fruits`集合。 這可讓使用者從中挑選的成果，如下列螢幕擷取畫面所示：

![](xaml-images/sub-classed.png "子類別化的原生視圖")

在 iOS 和 Android 原生的選擇器會使用方法，來設定控制項。 因此，這些選擇器必須公開屬性，使其適合 XAML 的子類別。 在通用 Windows 平台 (UWP)，`ComboBox`已經是適合 XAML 的因此不需要子類別化。

### <a name="ios"></a>iOS

IOS 實作子類別[ `UIPickerView` ](xref:UIKit.UIPickerView) 檢視中，並公開屬性，屬性可以從 XAML 輕鬆地取用的事件：

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

`MyUIPickerView`類別會公開`ItemsSource`並`SelectedItem`屬性，並有`SelectedItemChanged`事件。 A [ `UIPickerView` ](xref:UIKit.UIPickerView)需要有基礎[ `UIPickerViewModel` ](xref:UIKit.UIPickerViewModel)資料模型，即可存取`MyUIPickerView`屬性和事件。 `UIPickerViewModel`所提供的資料模型`PickerModel`類別：

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

`PickerModel`類別提供的基礎儲存體`MyUIPickerView`類別，透過`Items`屬性。 每當在選取的項目`MyUIPickerView`變更[ `Selected` ](xref:UIKit.UIPickerViewModel.Selected*)執行方法時，這會更新選取的索引和引發`ItemChanged`事件。 這可確保`SelectedItem`屬性一律會傳回使用者所挑選的最後一個項目。 颾魤 ㄛ`PickerModel`類別會覆寫方法用來安裝`MyUIPickerView`執行個體。

### <a name="android"></a>Android

Android 的實作子類別[ `Spinner` ](xref:Android.Widget.Spinner) 檢視中，並公開屬性，屬性可以從 XAML 輕鬆地取用的事件：

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

`MySpinner`類別會公開`ItemsSource`並`SelectedObject`屬性，並有`ItemSelected`事件。 所顯示的項目`MySpinner`類別所提供的[ `Adapter` ](xref:Android.Widget.Adapter)相關聯的檢視，以及項目會填入`Adapter`當`ItemsSource`屬性第一次設定。 每當在選取的項目`MySpinner`類別的變更，`OnBindableSpinnerItemSelected`事件處理常式更新`SelectedObject`屬性。

## <a name="summary"></a>總結

這篇文章會示範如何使用 Xamarin.Forms XAML 檔案中的原生檢視。 原生的檢視，可以設定屬性和事件處理常式，它們可以與 Xamarin.Forms 檢視互動。

## <a name="related-links"></a>相關連結

- [NativeSwitch （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [原生格式](~/xamarin-forms/platform/native-forms.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
