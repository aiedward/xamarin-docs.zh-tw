---
title: 自訂項目
description: Xamarin.Forms 項目控制項可讓單行編輯的文字。 本文示範如何建立自訂轉譯器項目控制項，讓開發人員覆寫預設原生呈現使用他們自己平台專屬的自訂。
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: c120add5a301e440911bd9794da77732e7787cc0
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2018
---
# <a name="customizing-an-entry"></a>自訂項目

_Xamarin.Forms 項目控制項可讓單行編輯的文字。本文示範如何建立自訂轉譯器項目控制項，讓開發人員覆寫預設原生呈現使用他們自己平台專屬的自訂。_

Xamarin.Forms 中的每個控制項都有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) Xamarin.Forms 應用程式，在 iOS 中所要呈現控制項`EntryRenderer`類別具現化，進而會具現化的原生`UITextField`控制項。 Android 平台上，`EntryRenderer`類別具現化`EditText`控制項。 在通用 Windows 平台 (UWP)，`EntryRenderer`類別具現化`TextBox`控制項。 如需有關轉譯器，而且 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱[轉譯器的基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項和對應的原生控制項實作它：

![](entry-images/entry-classes.png "項目控制項和實作的原生控制項之間的關聯性")

轉譯程序可以採取利用平台專屬的自訂實作所建立的自訂轉譯器[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)每個平台上的控制項。 執行此程序如下所示：

1. [建立](#Creating_the_Custom_Entry_Control)Xamarin.Forms 自訂控制項。
1. [取用](#Consuming_the_Custom_Control)Xamarin.Forms 中的，將自訂控制項。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)自訂轉譯器，針對每個平台上的控制項。

每個項目將現在依次討論實作[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)有不同的背景色彩，每個平台的控制項。

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>建立自訂的項目控制項

自訂[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項可以由子類別化`Entry`控制，如下列程式碼範例所示：

```csharp
public class MyEntry : Entry
{
}
```

`MyEntry`控制項會建立可攜式類別庫 (PCL) 專案中，而且只要[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項。 自訂控制項的執行時，自訂轉譯器，因此沒有額外的實作需要用於`MyEntry`控制項。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自訂控制項

`MyEntry`控制項可以在 XAML 中參考 PCL 專案中，宣告的命名空間，做為其位置，並使用命名空間前置詞上的控制項項目。 下列程式碼範例示範如何`MyEntry`控制項可供 XAML 頁面：

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

`local`命名空間前置詞可以命名為任何項目。 不過，`clr-namespace`和`assembly`值必須符合自訂控制項的詳細資料。 一旦宣告命名空間前置詞用來參考自訂控制項。

下列程式碼範例示範如何`MyEntry`控制項可供 C# 頁面：

```csharp
public class MainPage : ContentPage
{
  public MainPage ()
  {
    Content = new StackLayout {
      Children = {
        new Label {
          Text = "Hello, Custom Renderer !",
        },
        new MyEntry {
          Text = "In Shared Code",
        }
      },
      VerticalOptions = LayoutOptions.CenterAndExpand,
      HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
  }
}
```

此程式碼會具現化新[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)物件將會顯示[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)和`MyEntry`控制項置於兩者垂直和水平頁面。

自訂轉譯器現在加入自訂控制項的外觀，每個平台上的每個應用程式專案。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立的自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`EntryRenderer`呈現原生控制項的類別。
1. 覆寫`OnElementChanged`呈現自訂控制項的原生控制及寫入邏輯方法。 建立對應的 Xamarin.Forms 控制項時，會呼叫這個方法。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 控制項的自訂轉譯器類別。 此屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 它是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，將使用預設的產生器控制項的基底類別。

下圖說明範例應用程式，以及它們之間的關聯性中的每一個專案的責任：

![](entry-images/solution-structure.png "MyEntry 自訂轉譯器專案責任")

`MyEntry`要呈現控制項的平台專屬`MyEntryRenderer`類別全都衍生自`EntryRenderer`每個平台的類別。 這會導致每個`MyEntry`控制所呈現的平台專屬的背景色彩，如下列螢幕擷取畫面所示：

![](entry-images/screenshots.png "每個平台上的 MyEntry 控制項")

`EntryRenderer`類別會公開`OnElementChanged`Xamarin.Forms 控制項是用來呈現對應的原生控制項時所呼叫的方法。 這個方法會採用`ElementChangedEventArgs`參數包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加和 Xamarin.Forms 的項目，轉譯器*是*附加至分別。 範例應用程式中`OldElement`屬性會是`null`和`NewElement`屬性會包含參考`MyEntry`控制項。

覆寫的版本`OnElementChanged`方法中的`MyEntryRenderer`類別會用於執行原生控制項自訂。 平台上使用原生控制項的型別的參考可以透過存取`Control`屬性。 此外，要呈現 Xamarin.Forms 控制項的參考可以透過取得`Element`屬性，雖然它不會使用範例應用程式中。

每個自訂轉譯器類別以裝飾`ExportRenderer`xamarin.forms 註冊轉譯器的屬性。 屬性會採用兩個參數 – Xamarin.Forms 控制項所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列各節將討論每個平台專屬的實作`MyEntryRenderer`自訂轉譯器類別。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例會顯示 iOS 平台的自訂轉譯器：

```csharp
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer (typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.iOS
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged (e);

            if (Control != null) {
                // do whatever you want to the UITextField here!
                Control.BackgroundColor = UIColor.FromRGB (204, 153, 255);
                Control.BorderStyle = UITextBorderStyle.Line;
            }
        }
    }
}
```

基底類別呼叫`OnElementChanged`方法具現化 iOS`UITextField`控制項，與指派至這個產生器控制項的參考`Control`屬性。 背景色彩會接著設為使用淡紫色`UIColor.FromRGB`方法。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立的自訂轉譯器

下列程式碼範例會顯示 Android 平台的自訂轉譯器：

```csharp
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.Android
{
    class MyEntryRenderer : EntryRenderer
    {
        public MyEntryRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.SetBackgroundColor(global::Android.Graphics.Color.LightGreen);
            }
        }
    }
}
```

基底類別呼叫`OnElementChanged`方法具現化 Android`EditText`控制項，與指派至這個產生器控制項的參考`Control`屬性。 背景色彩會接著設為淺綠色以及`Control.SetBackgroundColor`方法。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上建立的自訂轉譯器

下列程式碼範例示範 UWP 的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.UWP
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.Background = new SolidColorBrush(Colors.Cyan);
            }
        }
    }
}
```

基底類別呼叫`OnElementChanged`方法具現化`TextBox`控制項，與指派至這個產生器控制項的參考`Control`屬性。 背景色彩會接著設為青色藉由建立`SolidColorBrush`執行個體。

## <a name="summary"></a>總結

本文示範如何建立 Xamarin.Forms 自訂控制項轉譯器[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項，讓開發人員覆寫預設使用他們自己的特定平台呈現原生呈現。 自訂轉譯器會提供自訂 Xamarin.Forms 控制項的外觀的強大方法。 它們可以用於小型的樣式變更或複雜的平台專屬版面配置和自訂行為。


## <a name="related-links"></a>相關連結

- [CustomRendererEntry （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
