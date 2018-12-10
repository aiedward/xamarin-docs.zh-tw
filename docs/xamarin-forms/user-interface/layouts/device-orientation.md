---
title: 裝置方向
description: 這篇文章說明如何在直向和橫向方向中更美觀的版面配置 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: 762d4752eb3882398f212e634c66305f74d5a459
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052659"
---
# <a name="device-orientation"></a>裝置方向

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)

請務必考慮應用程式使用的方式，以及如何合併橫向，來改善使用者經驗。 個別的版面配置可以設計來容納多個方向，最好使用的可用空間。 應用程式層級，可以停用或啟用旋轉。

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>控制方向

使用 Xamarin.Forms 時，控制裝置方向支援的方法時要用於每個個別的專案中的設定。

### <a name="ios"></a>iOS

在 iOS 上，設定應用程式使用裝置方向**Info.plist**檔案。 如果應用程式所包含的是它做為目標，此檔案會包含方向設定 iPhone 和 iPod，以及適用於 iPad 的設定。 以下是您的 IDE 特有的指示。 使用這份文件頂端的 IDE 選項，來選取您想要看到的指示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中開啟 iOS 專案，然後開啟**Info.plist**。 檔案將會開啟至 [設定] 面板中，開頭為 [iPhone 部署資訊] 索引標籤：

![iPhone 部署 Visual Studio 中的資訊](device-orientation-images/orientation-vs-iphone.png)

若要設定 [iPad 方向，請選取**iPad 部署資訊**位於左上方的窗格中，然後選取從可用的方向] 索引標籤：

![在 Visual Studio 中支援的裝置方向](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，開啟 iOS 專案，然後開啟**Info.plist**。 底下**應用程式** 索引標籤，區段可設定的方向：

![iPhone 部署資訊，在 Visual Studio for Mac](device-orientation-images/orientation-xam-ui.png)

如果您想要編輯使用索引鍵 / 值編輯器的介面，而選取的值**來源**> 在畫面底部的索引標籤：

![在 Visual Studio for Mac 支援裝置方向](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

若要控制在 Android 上的方向，開啟**MainActivity.cs**並設定使用屬性裝飾的方向`MainActivity`類別：

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android 支援數個選項來指定方向：

- **橫向**&ndash;強制應用程式方向為橫向，無論感應器資料。
- **直向**&ndash;強制應用程式方向是直向，不論感應器資料。
- **使用者**&ndash;導致應用程式能使用使用者的慣用的方向。
- **後置**&ndash;會導致應用程式的方向的方向相同[活動](https://developer.xamarin.com/api/type/Android.App.Activity/)背後。
- **感應器**&ndash;導致應用程式的方向感應器，來判斷，即使使用者已停用自動旋轉。
- **SensorLandscape** &ndash;會導致應用程式使用橫向時使用感應器資料變更 （以便讓畫面未顯示為 面朝下），面向螢幕的方向。
- **SensorPortrait** &ndash;會導致應用程式使用直式方向，同時使用感應器資料變更 （以便讓畫面未顯示為 面朝下），面向螢幕的方向。
- **ReverseLandscape** &ndash;會導致應用程式使用橫向，面向相反的方向，從一般，才會出現 「 上下顚倒。 」
- **ReversePortrait** &ndash;會導致應用程式使用直式方向，面向相反的方向，從一般，才會出現 「 上下顚倒。 」
- **FullSensor** &ndash;導致應用程式依賴感應器資料，以選取正確的方向 （從可能的 4)。
- **FullUser** &ndash;會導致應用程式使用使用者的方向偏好設定。 如果已啟用 自動旋轉，則可以使用所有 4 個方向。
- **UserLandscape** &ndash; _\[不支援\]_ 導致使用橫向，應用程式，除非使用者具有自動旋轉啟用，在此情況下，它會使用若要判斷方向的感應器。 此選項會中斷編譯。
- **UserPortrait** &ndash; _\[不支援\]_ 會導致應用程式使用直式方向，除非使用者具有自動旋轉啟用，在此情況下，它會使用若要判斷方向的感應器。 此選項會中斷編譯。
- **鎖住** &ndash; _\[不支援\]_ 會導致應用程式使用螢幕方向，無論它是在啟動時，未回應裝置中的變更的實體方向。 此選項會中斷編譯。

請注意，原生的 Android Api 提供了眾多控制管理方向的方式，包括明確相互抵觸的使用者的選項來表示喜好設定。

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP)，在中設定支援的方向**Package.appxmanifest**檔案。 開啟資訊清單會顯示設定面板，其中可選取支援的方向。

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>回應方向的變更

Xamarin.Forms 不提供任何原生事件來通知您的應用程式的方向共用程式碼中的變更。 不過，`SizeChanged`事件的`Page`時，會引發寬度或高度`Page`變更。 當寬度`Page`大於高度，裝置會以橫向模式。 如需詳細資訊，請參閱 <<c0> [ 顯示基的映像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)。

> [!NOTE]
> 沒有現有的免費適用於 NuGet 套件共用程式碼中接收通知的方向的變更。 請參閱[GitHub 存放庫](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation)如需詳細資訊。

或者，您可覆寫[ `OnSizeAllocated` ](xref:Xamarin.Forms.Page.OnSizeAllocated*)方法`Page`，插入的任何版面配置變更邏輯。 `OnSizeAllocated`就會呼叫方法時`Page`配置新的大小，每當裝置旋轉。 請注意的基底實作`OnSizeAllocated`執行重要的配置函式，因此請務必呼叫基底實作覆寫中：

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

若要執行這個步驟會導致未作用的頁面。

請注意，`OnSizeAllocated`方法可能會多次呼叫旋轉裝置時。 每次變更您的配置是一種浪費的資源，並可能會導致閃爍。 請考慮使用您的頁面中的執行個體變數來追蹤是否方向為橫向或縱向，並只重繪時有所變更：

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

一旦偵測到裝置方向變更，您可能想要新增或移除額外的檢視，從您的使用者介面中可用空間的變更做出回應。 例如，請考慮在直向每個平台上內建的計算機：

![](device-orientation-images/calculator-portrait.png "計算機應用程式，以直向")

和橫向：

![](device-orientation-images/calculator-landscape.png "架構中的計算機應用程式")

請注意，應用程式利用可用空間的架構中新增更多的功能。

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>回應式配置

您可使用內建的版面配置，如此一來，當在旋轉裝置時依正常程序轉換的設計介面。 設計介面，仍然是吸引人，當回應方向變更時，請考慮下列一般規則：

- **請注意比例**&ndash;方向中的變更可能會造成問題，做出特定假設進行關於比例時。 例如，1/3 的垂直間距，以橫向放不下會有足夠的空間中的垂直空間，以直向螢幕的 1/3 的檢視。
- **請謹慎使用絕對值** &ndash; （像素） 絕對值，以直向有意義可能毫無意義架構中。 絕對值，必要時，請使用巢狀的配置，以找出其影響。 例如，可合理地使用中的絕對值`TableView``ItemTemplate`當項目範本有保證的統一高度。

通常實作介面的多個螢幕大小和會被視為最佳做法時，也適用於上述規則。 本指南的其餘部分將說明在 Xamarin.Forms 中使用每個主要的版面配置的回應式配置的特定範例。

> [!NOTE]
> 為了清楚起見，下列各節會示範如何實作使用只是一種類型的回應式版面配置`Layout`一次。 在實務上，它通常是混合的工作變得更容易`Layout`來達成所需的版面配置，使用更簡單、 直覺`Layout`每個元件。

### <a name="stacklayout"></a>StackLayout

請考慮下列的應用程式，以直向顯示：

![](device-orientation-images/photo-stack-portrait.png "相片應用程式，以直向")

和橫向：

![](device-orientation-images/photo-stack-landscape.png "在橫向的相片應用程式")

是以下列 XAML 中完成：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

C# 用來變更方向`outerStack`根據裝置的方向：

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

請注意下列事項：

- `outerStack` 調整，以作為根據方向，以充分利用可用空間的水平或垂直堆疊顯示的映像和控制項。


### <a name="absolutelayout"></a>AbsoluteLayout

請考慮下列的應用程式，以直向顯示：

![](device-orientation-images/photo-abs-portrait.png "相片應用程式，以直向")

和橫向：

![](device-orientation-images/photo-abs-landscape.png "在橫向的相片應用程式")

是以下列 XAML 中完成：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImage="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

請注意下列事項：

- 頁面配置的方式，因為沒有必要程序性程式碼造成回應性。
- `ScrollView`用來允許標籤會顯示即使螢幕的高度是固定的按鈕和影像的高度的總和小於。


### <a name="relativelayout"></a>RelativeLayout

請考慮下列的應用程式，以直向顯示：

![](device-orientation-images/photo-rel-portrait.png "相片應用程式，以直向")

和橫向：

![](device-orientation-images/photo-rel-landscape.png "在橫向的相片應用程式")

是以下列 XAML 中完成：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImage="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

請注意下列事項：

- 頁面配置的方式，因為沒有必要程序性程式碼造成回應性。
- `ScrollView`用來允許標籤會顯示即使螢幕的高度是固定的按鈕和影像的高度的總和小於。

### <a name="grid"></a>Grid

請考慮下列的應用程式，以直向顯示：

![](device-orientation-images/photo-grid-portrait.png "相片應用程式，以直向")

和橫向：

![](device-orientation-images/photo-grid-landscape.png "在橫向的相片應用程式")

是以下列 XAML 中完成：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

以及下列程序性程式碼來處理循環的變更：

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

請注意下列事項：

- 頁面配置的方式，因為沒有方法來變更控制項的格線位置。


## <a name="related-links"></a>相關連結

- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
- [回應式配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)
- [顯示基的映像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
