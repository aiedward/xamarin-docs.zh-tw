---
title: 裝置方向
description: 本文說明如何配置以直向 Xamarin.Forms 和橫向方向呈現良好的應用程式。
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: de5ebf086af7fa8d7c6360bf3a1f48f57b834e44
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938433"
---
# <a name="device-orientation"></a>裝置方向

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

請務必考慮您的應用程式的使用方式，以及如何併入橫向方向以改善使用者體驗。 個別的版面配置可以設計成容納多個方向，並充分利用可用的空間。 在應用層級，可以停用或啟用旋轉。

## <a name="controlling-orientation"></a>控制方向

使用時 Xamarin.Forms ，控制裝置方向的支援方法是使用每個個別專案的設定。

### <a name="ios"></a>iOS

在 iOS 上，裝置方向會針對使用**plist**檔案的應用程式進行設定。 使用本檔頂端的 IDE 選項來選取您想要查看的指示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，開啟 iOS 專案，然後開啟 [ **plist**]。 從 [iPhone 部署資訊] 索引標籤開始，檔案將會開啟至 [設定] 面板：

![Visual Studio 中的 iPhone 部署資訊](device-orientation-images/orientation-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，開啟 iOS 專案，然後開啟 [ **plist**]。 在 [**應用程式**] 索引標籤下，區段將可設定方向：

![Visual Studio for Mac 中的 iPhone 部署資訊](device-orientation-images/orientation-vsmac.png)

如果您想要使用 [索引鍵-值編輯器] 介面來編輯值，請選取畫面底部的 [**來源**>] 索引標籤：

![Visual Studio for Mac 中支援的裝置方向](device-orientation-images/orientation-source-vsmac.png)

-----

### <a name="android"></a>Android

若要控制 Android 上的方向，請開啟**MainActivity.cs** ，並使用裝飾類別的屬性來設定方向 `MainActivity` ：

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

- **橫向** &ndash;不論感應器資料為何，強制應用程式方向為橫向。
- **縱向** &ndash;強制應用程式方向為直向，而不論感應器資料為何。
- **使用者** &ndash;會使用使用者慣用的方向來呈現應用程式。
- **落後** &ndash;使應用程式的方向與其背後的[活動](xref:Android.App.Activity)方向相同。
- **感應器** &ndash;導致應用程式的方向由感應器決定，即使使用者已停用自動輪替也一樣。
- **SensorLandscape** &ndash;讓應用程式在使用感應器資料時，使用橫向方向來變更畫面所面向的方向（如此一來，螢幕看起來就不會變）。
- **SensorPortrait** &ndash;讓應用程式在使用感應器資料時，使用直向方向來變更螢幕的方向（如此一來，螢幕看起來就不會變）。
- **ReverseLandscape** &ndash;讓應用程式使用橫向方向，朝向與平常相反的方向，因此顯示「倒置」。
- **ReversePortrait** &ndash;讓應用程式使用直向方向，朝向與平常相反的方向，因此顯示「倒置」。
- **FullSensor** &ndash;讓應用程式依賴感應器資料來選取正確的方向（可能是4）。
- **FullUser** &ndash;讓應用程式使用使用者的方向喜好設定。 如果已啟用自動旋轉，則可以使用所有4個方向。
- **UserLandscape** &ndash;[ _ \[ 不 \] 支援_] 會導致應用程式使用橫向方向，除非使用者已啟用自動旋轉，在此情況下，它會使用感應器來判斷方向。 此選項會中斷編譯。
- **UserPortrait** &ndash;[ _ \[ 不 \] 支援_] 會導致應用程式使用直向方向，除非使用者已啟用自動旋轉，在此情況下，它會使用感應器來判斷方向。 此選項會中斷編譯。
- 已**鎖定** &ndash;[ _ \[ 不 \] 支援_] 會導致應用程式在啟動時使用螢幕方向，而不會回應裝置實體方向的變更。 此選項會中斷編譯。

請注意，原生 Android Api 可讓您控制方向的管理方式，包括明確地比對使用者表示之喜好設定的選項。

### <a name="universal-windows-platform"></a>通用 Windows 平臺

在通用 Windows 平臺（UWP）上，會在**package.appxmanifest.xml**檔案中設定支援的方向。 開啟資訊清單將會顯示 [設定] 面板，其中可選取支援的方向。

## <a name="reacting-to-changes-in-orientation"></a>回應方向變更

Xamarin.Forms不提供任何原生事件來通知您的應用程式在共用程式碼中的方向變更。 不過， [Xamarin.Essentials](~/essentials/index.md) 包含一個 `DeviceDisplay` 提供方向變更通知的 [] 類別。

若要在沒有的情況下偵測方向，請 Xamarin.Essentials 監視的 `SizeChanged` 事件 `Page` ，這會在變更的寬度或高度時引發 `Page` 。 當的寬度 `Page` 大於高度時，裝置會處於橫向模式。 如需詳細資訊，請參閱[根據螢幕方向顯示影像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)。

或者，也可以覆寫上的 [`OnSizeAllocated`](xref:Xamarin.Forms.Page.OnSizeAllocated*) 方法，並在 `Page` 該處插入任何版面配置變更邏輯。 `OnSizeAllocated`每當配置新的大小時，就會呼叫方法 `Page` ，這會在裝置旋轉時發生。 請注意，的基底實 `OnSizeAllocated` 作用會執行重要的版面配置函式，因此請務必在覆寫中呼叫基底實作為：

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

若無法採取該步驟，將會導致無法正常運作的頁面。

請注意，在 `OnSizeAllocated` 旋轉裝置時，可能會多次呼叫方法。 每次變更版面配置會浪費資源，而且可能會導致閃爍。 請考慮使用頁面中的執行個體變數來追蹤方向是橫向或縱向，而且只有在發生變更時才重新繪製：

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

一旦偵測到裝置方向變更之後，您可能會想要在使用者介面中新增或移除其他視圖，以回應可用空間的變更。 例如，請考慮每個平臺上的內建計算機：

![垂直計算機應用程式](device-orientation-images/calculator-portrait.png)

和橫向：

![橫向的計算機應用程式](device-orientation-images/calculator-landscape.png)

請注意，應用程式會藉由在橫向新增更多功能來利用可用的空間。

## <a name="responsive-layout"></a>回應式版面配置

您可以使用內建的版面配置來設計介面，以便在裝置旋轉時正常轉換。 設計在回應方向變更時，會繼續吸引人的介面時，請考慮下列一般規則：

- 請**注意比例** &ndash;方向上的變更可能會在某些假設與比率有關時造成問題。 例如，在直向螢幕垂直空間的1/3 中有足夠空間的視圖，可能無法放入橫向的垂直空間1/3。
- **請小心使用絕對值** &ndash;直向直向的絕對（圖元）值在橫向可能沒有意義。 當需要絕對值時，請使用嵌套配置來隔離其影響。 例如， `TableView` `ItemTemplate` 當專案範本具有保證的統一高度時，在中使用絕對值是合理的。

上述規則也適用于實作為多種螢幕大小的介面時，通常會被視為最佳做法。 本指南的其餘部分將使用中的每個主要版面配置，說明回應式版面配置的特定範例 Xamarin.Forms 。

> [!NOTE]
> 為了清楚起見，下列各節將示範如何一次只使用一種類型來執行回應式版面配置 `Layout` 。 在實務上， `Layout` 使用 `Layout` 每個元件的更簡單或最直覺，混合來達到所需的版面配置通常會比較簡單。

### <a name="stacklayout"></a>StackLayout

請考慮下列以直向顯示的應用程式：

![縱向的相片應用程式](device-orientation-images/photo-stack-portrait.png)

和橫向：

![橫向的相片應用程式](device-orientation-images/photo-stack-landscape.png)

這會使用下列 XAML 來完成：

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

有些 c # 是用來根據裝置的方向來變更的方向 `outerStack` ：

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

- `outerStack`會調整為根據方向，將影像和控制項呈現為水準或垂直堆疊，以充分利用可用的空間。

### <a name="absolutelayout"></a>AbsoluteLayout

請考慮下列以直向顯示的應用程式：

![縱向的相片應用程式](device-orientation-images/photo-abs-portrait.png)

和橫向：

![橫向的相片應用程式](device-orientation-images/photo-abs-landscape.png)

這會使用下列 XAML 來完成：

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

- 由於頁面的配置方式，因此不需要程式碼就能引進回應能力。
- `ScrollView`是用來允許標籤顯示，即使螢幕的高度小於按鈕和影像的固定高度總和也一樣。

### <a name="relativelayout"></a>RelativeLayout

請考慮下列以直向顯示的應用程式：

![縱向的相片應用程式](device-orientation-images/photo-rel-portrait.png)

和橫向：

![橫向的相片應用程式](device-orientation-images/photo-rel-landscape.png)

這會使用下列 XAML 來完成：

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

- 由於頁面的配置方式，因此不需要程式碼就能引進回應能力。
- `ScrollView`是用來允許標籤顯示，即使螢幕的高度小於按鈕和影像的固定高度總和也一樣。

### <a name="grid"></a>方格

請考慮下列以直向顯示的應用程式：

![縱向的相片應用程式](device-orientation-images/photo-grid-portrait.png)

和橫向：

![橫向的相片應用程式](device-orientation-images/photo-grid-landscape.png)

這會使用下列 XAML 來完成：

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

以及下列程式性程式碼來處理旋轉變更：

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

- 由於頁面的配置方式，有一個方法可以變更控制項的格線位置。

## <a name="related-links"></a>相關連結

- [版面配置（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 範例（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [回應式版面配置（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [根據螢幕方向顯示影像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
