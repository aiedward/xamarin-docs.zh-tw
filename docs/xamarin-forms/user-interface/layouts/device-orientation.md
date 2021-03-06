---
title: 裝置方向
description: 本文說明如何配置 Xamarin.Forms 應用程式，這些應用程式在直向和橫向方向看起來很棒。
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 955cb8371e6363f617738dcf2fe4a6eb27dcb032
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98608854"
---
# <a name="device-orientation"></a>裝置方向

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

請務必考慮您的應用程式的使用方式，以及如何合併橫向方向以改善使用者體驗。 可以設計個別的版面配置來容納多個方向，並充分利用可用的空間。 在應用層級，可以停用或啟用旋轉。

## <a name="controlling-orientation"></a>控制方向

使用時 Xamarin.Forms ，支援控制裝置方向的方法是使用每個個別專案的設定。

### <a name="ios"></a>iOS

在 iOS 上，裝置方向是針對使用 **plist** 檔案的應用程式設定。 您可以使用本檔頂端的 IDE 選項，來選取您想要查看的指示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中開啟 iOS 專案，並開啟 **plist**。 檔案會開啟至設定面板，從 [iPhone 部署資訊] 索引標籤開始：

![Visual Studio 中的 iPhone 部署資訊](device-orientation-images/orientation-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中開啟 iOS 專案，並開啟 **plist**。 在 [ **應用程式** ] 索引標籤底下，區段將可設定為 [方向]：

![Visual Studio for Mac 中的 iPhone 部署資訊](device-orientation-images/orientation-vsmac.png)

如果您想要使用索引鍵-值編輯器介面來編輯值，請選取畫面底部的 [ **來源**>] 索引標籤：

![Visual Studio for Mac 中支援的裝置方向](device-orientation-images/orientation-source-vsmac.png)

-----

### <a name="android"></a>Android

若要控制 Android 的方向，請開啟 **MainActivity.cs** ，並使用裝飾類別的屬性來設定方向 `MainActivity` ：

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin 支援數個指定方向的選項：

- **橫向** &ndash; 不論感應器資料為何，都強制將應用程式方向設為橫向。
- **縱向** &ndash; 不論感應器資料為何，都強制將應用程式方向設為直向。
- **使用者** &ndash; 讓應用程式使用使用者的慣用方向呈現。
- **後方** &ndash; 使應用程式的方向與 [活動](xref:Android.App.Activity) 背後的方向相同。
- **感應器** &ndash; 讓應用程式的方向由感應器決定，即使使用者已停用自動輪替也是一樣。
- **SensorLandscape** &ndash; 讓應用程式使用橫向方向，同時使用感應器資料來變更螢幕面向的方向 (，讓畫面不會被視為) 的倒置。
- **SensorPortrait** &ndash; 讓應用程式在使用感應器資料來變更螢幕面向 (的方向時使用直向，讓畫面不會被視為) 的倒置。
- **ReverseLandscape** &ndash; 讓應用程式使用橫向方向（面向于正常方向），因此會顯示為「倒置」。
- **ReversePortrait** &ndash; 讓應用程式使用直向方向，並朝平常面對相反的方向，如此就會顯示「倒置」。
- **FullSensor** &ndash; 使應用程式依賴感應器資料，以從可能的 4) 中選取正確的方向 (。
- **FullUser** &ndash; 讓應用程式使用使用者的方向喜好設定。 如果啟用自動旋轉，則可以使用全部4個方向。
- **UserLandscape** &ndash;[ _\[ 不 \] 支援_] 會導致應用程式使用橫向方向，除非使用者已啟用自動輪替，在這種情況下，它會使用感應器來判斷方向。 此選項會中斷編譯。
- **UserPortrait** &ndash;[ _\[ 不 \] 支援_] 會導致應用程式使用直向方向，除非使用者已啟用自動輪替，在此情況下，它會使用感應器來判斷方向。 此選項會中斷編譯。
- **鎖定** &ndash;_\[ 不支援 \]_ 會導致應用程式在啟動時使用螢幕方向，而不會回應裝置實體方向的變更。 此選項會中斷編譯。

請注意，原生 Android Api 可讓您控制方向的管理方式，包括明確與使用者的表示喜好設定有矛盾的選項。

### <a name="universal-windows-platform"></a>通用 Windows 平臺

在通用 Windows 平臺 (UWP) 上，支援的方向是在 **package.appxmanifest** 檔案中設定。 開啟資訊清單將會顯示可選取支援的方向的設定面板。

## <a name="reacting-to-changes-in-orientation"></a>回應方向變更

Xamarin.Forms 未提供任何原生事件，以通知您的應用程式在共用程式碼中的方向變更。 但是， [Xamarin.Essentials](~/essentials/index.md) 包含 `DeviceDisplay` 提供方向變更通知的 [] 類別。

若要偵測沒有的方向 Xamarin.Essentials ，請監視的 `SizeChanged` 事件 `Page` ，這會在變更的寬度或高度時引發 `Page` 。 當的寬度 `Page` 大於高度時，裝置會處於橫向模式。 如需詳細資訊，請參閱 [根據螢幕方向顯示影像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)。

或者，您可以覆寫上的 [`OnSizeAllocated`](xref:Xamarin.Forms.Page.OnSizeAllocated*) 方法，並在 `Page` 該處插入任何版面配置變更邏輯。 只要配置了 `OnSizeAllocated` 新的大小，就會呼叫方法 `Page` ，這會在裝置旋轉時進行。 請注意，的基底執行 `OnSizeAllocated` 會執行重要的版面配置函式，因此請務必在覆寫中呼叫基底實作為：

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

若無法執行該步驟，將會導致無法正常運作的頁面。

請注意，在 `OnSizeAllocated` 旋轉裝置時，可能會多次呼叫方法。 每次變更您的配置將會浪費資源，而且可能會導致閃爍。 請考慮使用頁面內的執行個體變數來追蹤方向是橫向或直向，而且只有在有變更時才重新繪製：

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

一旦偵測到裝置方向的變更，您可能會想要在使用者介面中新增或移除其他視圖，以回應可用空間的變更。 例如，請考慮縱向的每個平臺上內建的計算機：

![縱向的計算機應用程式](device-orientation-images/calculator-portrait.png)

和橫向：

![橫向的計算機應用程式](device-orientation-images/calculator-landscape.png)

請注意，應用程式會在橫向新增更多功能，以充分利用可用的空間。

## <a name="responsive-layout"></a>回應式版面配置

您可以使用內建的版面配置來設計介面，以便在裝置旋轉時正常轉換。 當您在回應方向變更時，設計能繼續吸引人的介面時，請考慮下列一般規則：

- **留意比例** &ndash; 在對比例進行某些假設時，方向變更可能會造成問題。 例如，在直向螢幕垂直空間垂直空間的1/3 中，有足夠空間的視圖可能無法容納在橫向的垂直空間中1/3。
- **請小心使用絕對值** &ndash; 在直向中有意義的絕對 (圖元) 值可能不會對橫向有意義。 當需要絕對值時，請使用嵌套配置來隔離其影響。 例如， `TableView` `ItemTemplate` 當專案範本具有保證的統一高度時，在中使用絕對值會很合理。

上述規則也適用于實作為多個螢幕大小的介面時，通常被視為最佳做法。 本指南的其餘部分將說明如何使用中的每個主要版面配置來回應版面配置的特定範例 Xamarin.Forms 。

> [!NOTE]
> 為了清楚起見，下列各節會示範如何使用一次只能使用一種類型來執行回應式版面配置 `Layout` 。 在實務上， `Layout` 使用 `Layout` 每個元件的簡單或最直覺性，混合以達成所需的版面配置通常比較簡單。

### <a name="stacklayout"></a>StackLayout

請考慮下列以直向顯示的應用程式：

![螢幕擷取畫面顯示直向 StackLayout 的相片應用程式。](device-orientation-images/photo-stack-portrait.png)

和橫向：

![螢幕擷取畫面顯示以橫向顯示的相片應用程式 StackLayout。](device-orientation-images/photo-stack-landscape.png)

這是使用下列 XAML 完成的：

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

某些 c # 可用來 `outerStack` 根據裝置的方向來變更方向：

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

請注意：

- `outerStack` 會調整成根據方向將影像和控制項呈現為水準或垂直堆疊，以充分利用可用的空間。

### <a name="absolutelayout"></a>AbsoluteLayout

請考慮下列以直向顯示的應用程式：

![螢幕擷取畫面顯示直向 AbsoluteLayout 的相片應用程式。](device-orientation-images/photo-abs-portrait.png)

和橫向：

![螢幕擷取畫面顯示以橫向顯示的相片應用程式 AbsoluteLayout。](device-orientation-images/photo-abs-landscape.png)

這是使用下列 XAML 完成的：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImageSource="deer.jpg">
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

請注意：

- 由於頁面的配置方式，不需要程式性程式碼就能引進回應性。
- `ScrollView`即使螢幕的高度小於按鈕的固定高度和影像的總和，還是會使用來允許顯示標籤。

### <a name="relativelayout"></a>RelativeLayout

請考慮下列以直向顯示的應用程式：

![螢幕擷取畫面顯示直向 RelativeLayout 的相片應用程式。](device-orientation-images/photo-rel-portrait.png)

和橫向：

![螢幕擷取畫面顯示以橫向顯示的相片應用程式 RelativeLayout。](device-orientation-images/photo-rel-landscape.png)

這是使用下列 XAML 完成的：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImageSource="deer.jpg">
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

請注意：

- 由於頁面的配置方式，不需要程式性程式碼就能引進回應性。
- `ScrollView`即使螢幕的高度小於按鈕的固定高度和影像的總和，還是會使用來允許顯示標籤。

### <a name="grid"></a>方格

請考慮下列以直向顯示的應用程式：

![螢幕擷取畫面：顯示直向的相片應用程式方格。](device-orientation-images/photo-grid-portrait.png)

和橫向：

![螢幕擷取畫面：顯示橫向的相片應用程式方格。](device-orientation-images/photo-grid-landscape.png)

這是使用下列 XAML 完成的：

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

以及下列程式碼來處理輪替變更：

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
            innerGrid.RowDefinitions.Clear();
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

請注意：

- 由於頁面的配置方式，有一種方法可以變更控制項的方格位置。

## <a name="related-links"></a>相關連結

- [版面配置 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 範例 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [回應式版面配置 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [根據螢幕方向顯示影像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)