---
title: Xamarin.Forms RelativeLayout
description: 這篇文章說明如何使用 Xamarin.Forms RelativeLayout 類別來建立可調整以符合任何畫面大小的 Ui。
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 4bd4524f4bf84327571609c8fb43dec164c9db56
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061222"
---
# <a name="xamarinforms-relativelayout"></a>Xamarin.Forms RelativeLayout

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)

`RelativeLayout` 用來位置和大小檢視相對於檢視版面配置] 或 [同層級的內容。 不同於`AbsoluteLayout`，`RelativeLayout`沒有移動錨點的概念，且沒有定位項目相對於底部或版面配置的右邊緣的功能。 `RelativeLayout` 支援它自己的範圍之外的定位項目。

[![](relative-layout-images/layouts-sml.png "Xamarin.Forms 版面配置")](relative-layout-images/layouts.png#lightbox "Xamarin.Forms 版面配置")

## <a name="purpose"></a>用途

`RelativeLayout` 可用來檢視在相對於整體的版面配置，或至其他檢視 畫面上的位置。

![](relative-layout-images/flag.png "RelativeLayout 探勘")

## <a name="usage"></a>使用量

### <a name="understanding-constraints"></a>了解條件約束

定位和調整大小內檢視`RelativeLayout`是使用條件約束。 條件約束運算式可以包含下列資訊：

- **型別**&ndash;條件約束是否相對於父代或另一個檢視。
- **屬性**&ndash;將做為基礎的條件約束的屬性。
- **係數**&ndash;来套用至屬性值的因數。
- **常數**&ndash;来做為值的位移的值。
- **ElementName** &ndash;條件約束是相對於檢視的名稱。

在 XAML 中，以表示條件約束`ConstraintExpression`s。 參考下列範例：

```xaml
<BoxView Color="Green" WidthRequest="50" HeightRequest="50"
    RelativeLayout.XConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Width,
                             Factor=0.5,
                             Constant=-100}"
    RelativeLayout.YConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Height,
                             Factor=0.5,
                             Constant=-100}" />
```

在C#，條件約束表示不太一樣，使用 在檢視上的 函式，而不是運算式。 條件約束指定做為引數的配置`Add`方法：

```csharp
layout.Children.Add(box, Constraint.RelativeToParent((parent) =>
    {
      return (.5 * parent.Width) - 100;
    }),
    Constraint.RelativeToParent((parent) =>
    {
        return (.5 * parent.Height) - 100;
    }),
    Constraint.Constant(50), Constraint.Constant(50));
```

請注意上述的版面配置的下列層面：

- `x`和`y`條件約束指定其自己的條件約束。
- 在C#，做為函式定義相對的條件約束。 概念，例如`Factor`不存在，但可以手動實作。
- 方塊的`x`座標會定義為寬度的一半的父項、-100。
- 方塊的`y`座標會定義為高度的一半的父項、-100。

> [!NOTE]
> 由於條件約束的定義的方式，就能夠進行更複雜的版面配置中C#不可以使用 XAML 來指定。

這兩個上述範例中的定義做為條件約束`RelativeToParent`&ndash;亦即，其值是相對於父項目。 它也可定義相對於另一個檢視的條件約束。 這可讓 （向開發人員） 更具直覺性的版面配置，並且可以讓您的版面配置程式碼的目的更明顯。

請考慮的其中一個項目必須是小於另一個 20 個像素的版面配置。 如果這兩個項目會定義具有常數值，可能會有較其`Y`條件約束定義為常數，它是 20 像素大於`Y`條件約束的較高的項目。 該方法會簡短如果較高的項目位於使用的比例，使不已知的像素大小。 在此情況下，限制根據另一個項目位置的項目是更強固的：

```xaml
<RelativeLayout>
    <BoxView Color="Red" x:Name="redBox"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent,
            Property=Height,Factor=.15,Constant=0}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=1,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.8,Constant=0}" />
    <BoxView Color="Blue"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=Y,Factor=1,Constant=20}"
        RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=X,Factor=1,Constant=20}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=.5,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.5,Constant=0}" />
</RelativeLayout>
```

若要完成相同的配置中C#:

```csharp
layout.Children.Add (redBox, Constraint.RelativeToParent ((parent) => {
        return parent.X;
    }), Constraint.RelativeToParent ((parent) => {
        return parent.Y * .15;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .8;
    }));
layout.Children.Add (blueBox, Constraint.RelativeToView (redBox, (Parent, sibling) => {
        return sibling.X + 20;
    }), Constraint.RelativeToView (blueBox, (parent, sibling) => {
        return sibling.Y + 20;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width * .5;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .5;
    }));
```

這會產生下列輸出，藍色方塊的位置決定_相對_紅色方塊的位置：

![](relative-layout-images/red-blue-box.png "具有紅色和藍色 BoxViews RelativeLayout")

### <a name="sizing"></a>調整大小

檢視由配置`RelativeLayout`有兩個選項來指定其大小：

- `HeightRequest & WidthRequest`
- `RelativeLayout.WidthConstraint` & `RelativeLayout.HeightConstraint`

`HeightRequest` 和`WidthRequest`指定預定的高度和寬度的檢視，但可能會覆寫配置所需。 `WidthConstraint` 和`HeightConstraint`支援設定的高度和寬度為相對於的版面配置或另一個檢視的屬性值，或以常值。

## <a name="exploring-a-complex-layout"></a>瀏覽複雜的配置
每個配置有優點和缺點，用於建立特定的配置。 在此系列的版面配置的文章中，已建立範例應用程式以使用三個不同的版面配置實作相同的頁面配置。

請考慮下列 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.RelativeLayoutPage"
BackgroundColor="Maroon"
Title="RelativeLayout">
    <ContentPage.Content>
    <ScrollView>
      <RelativeLayout>
        <BoxView Color="Gray" HeightRequest="100"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Button BorderRadius="35" x:Name="imageCircleBack"
            BackgroundColor="Maroon" HeightRequest="70" WidthRequest="70" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.5, Constant = -35}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=70}" />
        <Button BorderRadius="30" BackgroundColor="Red" HeightRequest="60"
            WidthRequest="60" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=imageCircleBack, Property=X, Factor=1,Constant=5}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=75}" />
        <Label Text="User Name" FontAttributes="Bold" FontSize="26"
            HorizontalTextAlignment="Center" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=140}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Entry Text="Bio + Hashtags" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=180}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <RelativeLayout BackgroundColor="White" RelativeLayout.YConstraint="
            {ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=220}" HeightRequest="60" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" >
            <BoxView BackgroundColor="Black" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=5}" />
            <BoxView BackgroundColor="Maroon" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5, Constant=}" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=60}" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5, Constant=55}" />
        </RelativeLayout>
        <RelativeLayout Padding="5,0,0,0">
          <Label FontSize="14" Text="Age:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=305}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=280}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Interests:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=345}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=320}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
            LineBreakMode="WordWrap"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=395}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
            BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=370}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
      </RelativeLayout>
    </ScrollView>
  </ContentPage.Content>
</ContentPage>
```

上述程式碼會產生下列配置：

![](relative-layout-images/relative.png "複雜 RelativeLayout")

請注意， `RelativeLayouts`s 為巢狀，因為在某些情況下建立巢狀版面配置可能很容易呈現在相同的版面配置內的所有項目。 也請注意，某些元素`RelativeToView`，因為，可讓您更輕鬆且更直覺的版面配置時檢視之間的關聯性可讓您引導定位。


## <a name="related-links"></a>相關連結

- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
