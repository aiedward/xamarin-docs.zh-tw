---
title: 自訂項目
description: Xamarin.Forms 的項目控制項可讓單行編輯的文字。 這篇文章會示範如何建立自訂轉譯器項目控制項，讓開發人員覆寫預設原生呈現自己的平台特定自訂。
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/26/2018
ms.openlocfilehash: 7fea736b0a04a69fd64100ae1d6bcd42c244359f
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459846"
---
# <a name="customizing-an-entry"></a>自訂項目

_Xamarin.Forms 的項目控制項可讓單行編輯的文字。這篇文章會示範如何建立自訂轉譯器項目控制項，讓開發人員覆寫預設原生呈現自己的平台特定自訂。_

每個 Xamarin.Forms 控制項已隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `Entry` ](xref:Xamarin.Forms.Entry) Xamarin.Forms 應用程式，在 iOS 中所要呈現控制項`EntryRenderer`類別具現化，它會具現化原生`UITextField`控制項。 Android 平台上，`EntryRenderer`類別會具現化`EditText`控制項。 在通用 Windows 平台 (UWP)，`EntryRenderer`類別會具現化`TextBox`控制項。 如需有關轉譯器和 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱 <<c0> [ 轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `Entry` ](xref:Xamarin.Forms.Entry)控制項和對應的原生控制項實作它：

![](entry-images/entry-classes.png "項目控制項和實作原生控制項之間的關聯性")

轉譯程序可以藉由建立自訂轉譯器的實作平台專屬的自訂採取善用[ `Entry` ](xref:Xamarin.Forms.Entry)每個平台上的控制項。 執行此動作的程序如下所示：

1. [建立](#Creating_the_Custom_Entry_Control)Xamarin.Forms 自訂控制項。
1. [取用](#Consuming_the_Custom_Control)Xamarin.Forms 自訂控制項。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)自訂轉譯器，針對每個平台上的控制項。

每個項目會現在依次討論實作[ `Entry` ](xref:Xamarin.Forms.Entry)每個平台有不同的背景色彩的控制項。

> [!IMPORTANT]
> 這篇文章說明如何建立簡單的自訂轉譯器。 不過，不需要建立自訂轉譯器來實作`Entry`每個平台上具有不同的背景色彩。 這可更輕鬆地透過使用[ `Device` ](xref:Xamarin.Forms.Device)類別，或`OnPlatform`標記延伸，以提供平台特定的值。 如需詳細資訊，請參閱 <<c0> [ 提供的平台特定的值](~/xamarin-forms/platform/device.md#providing-platform-specific-values)並[OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>建立自訂的項目控制項

自訂[ `Entry` ](xref:Xamarin.Forms.Entry)控制項可以由子類別化`Entry`控制項，如下列程式碼範例所示：

```csharp
public class MyEntry : Entry
{
}
```

`MyEntry`控制項會建立.NET Standard 程式庫專案中，而且只要[ `Entry` ](xref:Xamarin.Forms.Entry)控制項。 自訂控制項的執行時，自訂轉譯器，因此沒有其他的實作需要用於`MyEntry`控制項。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自訂控制項

`MyEntry`控制項可以在 XAML 中參考.NET Standard 程式庫專案中，藉由宣告其位置的命名空間和控制項項目上使用的命名空間前置詞。 下列程式碼範例示範如何`MyEntry`控制項可供 XAML 頁面：

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

此程式碼會具現化新[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)物件，將會顯示[ `Label` ](xref:Xamarin.Forms.Label)並`MyEntry`控制項置於兩者垂直和水平頁面。

自訂轉譯器現在可以新增至每個應用程式專案，以自訂每個平台上的控制項的外觀。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`EntryRenderer`呈現原生控制項的類別。
1. 覆寫`OnElementChanged`呈現的原生的控制及寫入邏輯來自訂控制項的方法。 建立對應的 Xamarin.Forms 控制項時，會呼叫這個方法。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 控制項的自訂轉譯器類別。 這個屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 它是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，則會使用預設的轉譯器控制項的基底類別。

下圖說明範例應用程式，以及其間的關聯性中的每個專案的責任：

![](entry-images/solution-structure.png "MyEntry 自訂轉譯器專案責任")

`MyEntry`平台特定的轉譯控制項`MyEntryRenderer`類別，這些全都衍生自`EntryRenderer`每個平台的類別。 這會導致每個`MyEntry`控制呈現與平台專屬的背景色彩，如下列螢幕擷取畫面所示：

![](entry-images/screenshots.png "每個平台的 MyEntry 控制項")

`EntryRenderer`類別會公開`OnElementChanged`方法中，當建立 Xamarin.Forms 控制項來呈現對應的原生控制項時呼叫。 這個方法會採用`ElementChangedEventArgs`參數，其中包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加至，和 Xamarin.Forms 的項目，轉譯器*是*附加分別。 在範例應用程式`OldElement`屬性會是`null`並`NewElement`屬性會包含參考`MyEntry`控制項。

覆寫的新版`OnElementChanged`方法中的`MyEntryRenderer`類別是執行原生控制項自訂的位置。 您可以透過平台上使用原生控制項的型別的參考`Control`屬性。 此外，所呈現的 Xamarin.Forms 控制項的參考可以透過取得`Element`屬性，雖然它不會使用在範例應用程式。

每個自訂轉譯器類別裝飾了`ExportRenderer`向 Xamarin.Forms 中的轉譯器的屬性。 屬性會採用兩個參數 – Xamarin.Forms 控制項所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列各節將討論的每個平台特定實作`MyEntryRenderer`自訂轉譯器類別。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例顯示適用於 iOS 平台的自訂轉譯器：

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

呼叫基底類別的`OnElementChanged`方法具現化 iOS`UITextField`控制項，與指派給這個產生器控制項的參考`Control`屬性。 背景色彩則設淺紫與`UIColor.FromRGB`方法。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

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

呼叫基底類別的`OnElementChanged`方法具現化 Android`EditText`控制項，與指派給這個產生器控制項的參考`Control`屬性。 背景色彩則設淺綠色以及`Control.SetBackgroundColor`方法。

### <a name="creating-the-custom-renderer-on-uwp"></a>建立 UWP 上的自訂轉譯器

下列程式碼範例顯示適用於 UWP 的自訂轉譯器：

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

呼叫基底類別的`OnElementChanged`方法具現化`TextBox`控制項，與指派給這個產生器控制項的參考`Control`屬性。 背景色彩然後藉由設定為青色`SolidColorBrush`執行個體。

## <a name="summary"></a>總結

這篇文章已示範如何建立 Xamarin.Forms 自訂控制項轉譯器[ `Entry` ](xref:Xamarin.Forms.Entry)控制項，讓開發人員覆寫預設原生呈現與他們自己的平台特定的轉譯。 自訂轉譯器會提供自訂的 Xamarin.Forms 控制項外觀的強大方法。 他們可以使用小型的樣式變更或複雜的平台特定版面配置和行為的自訂。


## <a name="related-links"></a>相關連結

- [CustomRendererEntry （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
