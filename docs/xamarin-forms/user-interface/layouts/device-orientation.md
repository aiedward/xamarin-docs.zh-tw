---
title: 裝置方向
description: 這篇文章說明如何在縱向或橫向方向中更棒的版面配置 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: 9245a17423d97887d2032856b10427685b25c29b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244347"
---
# <a name="device-orientation"></a>裝置方向

請務必考慮您的應用程式的使用方式以及如何合併，來改善使用者經驗。 個別的配置可以設計來容納多個方向，最好使用可用的空間。 應用程式層級，可以停用或啟用旋轉。

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>控制方向

使用 Xamarin.Forms 時，控制裝置方向的支援的方法時使用的設定，針對每個個別的專案。

### <a name="ios"></a>iOS

在 iOS 上，設定應用程式使用裝置方向**Info.plist**檔案。 如果應用程式所包含的是它做為目標，這個檔案將包含方向設定為 iPhone 和 iPod，以及適用於 iPad 的設定。 以下是您的 IDE 特有的指示。 使用這份文件頂端的 IDE 選項，選取您想要查看哪些指示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 中開啟的 iOS 專案，並開啟**Info.plist**。 檔案會開啟到組態面板中，從 iPhone 部署資訊 索引標籤：

![iPhone Visual Studio 中的部署資訊](device-orientation-images/orientation-vs-iphone.png)

若要設定 iPad 方向，請選取**iPad 部署資訊**位於左上方的面板，然後選取從可用的方向 索引標籤：

![在 Visual Studio 中支援的裝置方向](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在適用於 Mac 的 Visual Studio 中開啟的 iOS 專案，並開啟**Info.plist**。 在下**應用程式**索引標籤上，各節將可設定的方向：

![iPhone 適用於 Mac 的 Visual Studio 中的部署資訊](device-orientation-images/orientation-xam-ui.png)

如果您想要編輯使用索引鍵-值編輯器介面，選取的值**來源**> 在畫面底部的索引標籤：

![在 Visual Studio for Mac 支援裝置方向](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

若要控制在 Android 上的方向，請開啟**Weatherapp**和設定使用屬性裝飾的方向`MainActivity`類別：

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android 支援數個選項的指定方向：

- **橫向**&ndash;強制應用程式方向是橫向，不論感應器資料。
- **縱向**&ndash;強制為直向，不論感應器資料的應用程式方向。
- **使用者**&ndash;導致應用程式可以使用使用者的慣用的方向顯示。
- **後置**&ndash;會導致應用程式的方向的方向相同[活動](https://developer.xamarin.com/api/type/Android.App.Activity/)背後。
- **感應器**&ndash;會導致以感應器，來判斷應用程式的方向，即使使用者已停用自動旋轉。
- **SensorLandscape** &ndash;會導致應用程式使用同時使用來變更 （以便螢幕不被視為顛倒） 遇到螢幕方向的感應器資料。
- **SensorPortrait** &ndash;會導致應用程式使用直式方向時使用變更 （以便螢幕不被視為顛倒） 遇到螢幕方向的感應器資料。
- **ReverseLandscape** &ndash;會導致應用程式使用面對相反的方向，從平常久時，才會出現 「 顛倒。 」
- **ReversePortrait** &ndash;會導致應用程式使用直式方向，面對相反的方向，從平常久時，才會出現 「 顛倒。 」
- **FullSensor** &ndash;導致應用程式依賴感應器資料，以選取正確的方向 （從可能的 4)。
- **FullUser** &ndash;會導致應用程式使用使用者的方向偏好設定。 如果啟用自動旋轉，則可以使用所有 4 個方向。
- **UserLandscape** &ndash; _\[不支援\]_ 會導致應用程式使用，除非使用者具有自動旋轉啟用，在此情況下，它會使用若要判斷方向的感應器。 此選項將會中斷編譯。
- **UserPortrait** &ndash; _\[不支援\]_ 除非使用者具有自動旋轉啟用，它會使用在此情況下，會導致應用程式使用直式方向若要判斷方向的感應器。 此選項將會中斷編譯。
- **鎖定** &ndash; _\[不支援\]_ 會導致應用程式使用螢幕方向，不論其所處啟動，而不會變更裝置回應的實體方向。 此選項將會中斷編譯。

請注意，原生 Android Api 提供許多控制管理方向的方式，包括明確有所出入的使用者的選項來表示喜好設定。

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP)，在中設定支援的方向**Package.appxmanifest**檔案。 開啟資訊清單會顯示組態面板，其中可以選取支援的方向。

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>對回應方向中的變更

Xamarin.Forms 不提供任何原生事件通知的共用程式碼中的方向變更您的應用程式。 不過，`SizeChanged`事件`Page`時引發寬度或高度`Page`變更。 當的寬度`Page`大於高度，裝置會以橫向模式。 如需詳細資訊，請參閱[顯示根影像](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/)。

> [!NOTE]
> 沒有現有的、 可用的 NuGet 封裝共用的程式碼中接收通知的方向變更。 請參閱[GitHub 儲存機制](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation)如需詳細資訊。

另外，它是可以覆寫[ `OnSizeAllocated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnSizeAllocated(System.Double,System.Double)/)方法`Page`，插入所有版面配置變更那里邏輯。 `OnSizeAllocated`方法呼叫時`Page`會配置新的大小，這種的 whenver 裝置旋轉。 請注意的基底實作`OnSizeAllocated`執行重要的配置函式，所以請務必呼叫基底實作，在覆寫：

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

若要執行該步驟失敗會導致非正常運作的頁面。

請注意，`OnSizeAllocated`方法可能會呼叫多次旋轉裝置時。 每次變更您的配置是一種浪費的資源，而且可能會造成閃爍。 請考慮使用您在網頁內的執行個體變數來追蹤是否方向為橫向或縱向，並只重繪變更時：

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

一旦偵測到變更裝置方向中的，您可能想要新增或移除額外的檢視，從您的使用者介面中可用空間的變更做出回應。 例如，請考慮在直向每個平台的內建計算器：

![](device-orientation-images/calculator-portrait.png "在直向 [小算盤] 應用程式")

然後橫印：

![](device-orientation-images/calculator-landscape.png "在橫向 [小算盤] 應用程式")

請注意，應用程式善用可用空間在橫向中加入更多的功能。

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>回應式配置

它可使用內建的版面配置，以便依正常程序轉換旋轉裝置時的設計介面。 設計介面，仍將繼續保有吸引人時回應變更方向時請考慮下列一般規則：

- **請注意比例**&ndash;方向中的變更可能會造成問題，做出特定假設大致相同比例進行時。 例如，會有足夠的空間 1/3 的直螢幕的垂直空間中的檢視可能不符合 1/3 橫向的垂直空間。
- **請謹慎使用絕對值**&ndash;直印在合理的絕對 （像素） 值不合理橫印中。 需要絕對值時，使用巢狀的配置，以找出其影響。 比方說，它會合理地使用中的絕對值`TableView``ItemTemplate`當項目範本已保證統一的高度。

通常實作介面的多個螢幕大小和都視為最佳作法，也適用於上述規則。 本指南的其餘部分將說明特定的回應使用 Xamarin.Forms 中的每個主要的版面配置的版面配置範例。

> [!NOTE]
> 為了清楚起見，下列各節將示範如何實作使用一種回應版面配置`Layout`一次。 在實務上，它通常是較簡單混合`Layout`來達成所需的版面配置，使用簡單或很直覺式 s`Layout`每個元件。

### <a name="stacklayout"></a>StackLayout

請考慮下列應用程式，以直向顯示：

![](device-orientation-images/photo-stack-portrait.png "在直向相片應用程式")

然後橫印：

![](device-orientation-images/photo-stack-landscape.png "在橫向的相片應用程式")

以下列 XAML 達成：

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

某些 C# 用來變更方向`outerStack`根據裝置的方向：

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

- `outerStack` 會調整成水平或垂直方向，以充分利用可用的空間依據堆疊中呈現映像和控制項。


### <a name="absolutelayout"></a>AbsoluteLayout

請考慮下列應用程式，以直向顯示：

![](device-orientation-images/photo-abs-portrait.png "在直向相片應用程式")

然後橫印：

![](device-orientation-images/photo-abs-landscape.png "在橫向的相片應用程式")

以下列 XAML 達成：

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

- 頁面配置的方式，因為沒有導入回應性的程序程式碼需要。
- `ScrollView`用來允許皆可看到即使螢幕的高度是固定的按鈕和影像高度的總和小於標籤。


### <a name="relativelayout"></a>RelativeLayout

請考慮下列應用程式，以直向顯示：

![](device-orientation-images/photo-rel-portrait.png "在直向相片應用程式")

然後橫印：

![](device-orientation-images/photo-rel-landscape.png "在橫向的相片應用程式")

以下列 XAML 達成：

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

- 頁面配置的方式，因為沒有導入回應性的程序程式碼需要。
- `ScrollView`用來允許皆可看到即使螢幕的高度是固定的按鈕和影像高度的總和小於標籤。

### <a name="grid"></a>Grid

請考慮下列應用程式，以直向顯示：

![](device-orientation-images/photo-grid-portrait.png "在直向相片應用程式")

然後橫印：

![](device-orientation-images/photo-grid-landscape.png "在橫向的相片應用程式")

以下列 XAML 達成：

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

以及下列程序性程式碼來處理旋轉的變更：

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

- 頁面配置的方式，因為有一個方法是變更方格控制項的位置。


## <a name="related-links"></a>相關連結

- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
- [回應式配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)
- [顯示根據影像](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/)
