---
title: 自訂 Entry
description: Xamarin.Forms Entry 控制項允許單行文字的編輯。 本文示範如何建立 Entry 控制項的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/26/2018
ms.openlocfilehash: 775ef2a38cf423536072358299ac4f30db974d9a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056992"
---
# <a name="customizing-an-entry"></a>自訂 Entry

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)

_Xamarin.Forms Entry 控制項允許單行文字的編輯。本文示範如何建立 Entry 控制項的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。_

每個 Xamarin.Forms 控制項都隨附每個平台的轉譯器，這些平台可建立原生控制項的執行個體。 當 Xamarin.Forms 應用程式轉譯 [`Entry`](xref:Xamarin.Forms.Entry) 控制項時，在 iOS 中會先具現化 `EntryRenderer` 類別，再由該類別具現化原生 `UITextField` 控制項。 在 Android 平台上，`EntryRenderer` 類別會具現化 `EditText` 控制項。 在通用 Windows 平台 (UWP) 上，`EntryRenderer` 類別會具現化 `TextBox` 控制項。 如需 Xamarin.Forms 控制項對應的轉譯器和原生控制項類別詳細資訊，請參閱[轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明 [`Entry`](xref:Xamarin.Forms.Entry) 控制項和實作它之對應原生控制項間的關聯性：

![](entry-images/entry-classes.png "Entry 控制項與實作原生控制項之間的關聯性")

您可在每個平台上建立 [`Entry`](xref:Xamarin.Forms.Entry) 控制項的自訂轉譯器，利用轉譯處理序實作平台特定的自訂。 執行這項作業的流程如下：

1. [建立](#Creating_the_Custom_Entry_Control) Xamarin.Forms 自訂控制項。
1. [使用](#Consuming_the_Custom_Control) Xamarin.Forms 的自訂控制項。
1. 在每個平台上[建立](#Creating_the_Custom_Renderer_on_each_Platform)控制項的自訂轉譯器。

每個項目現在會依序進行討論，以實作在每個平台上有不同背景色彩的 [`Entry`](xref:Xamarin.Forms.Entry) 控制項。

> [!IMPORTANT]
> 本文說明如何建立簡易的自訂轉譯器。 但是，建立自訂轉譯器並非實作在每個平台上有不同背景色彩 `Entry` 的必要流程。 透過使用 [`Device`](xref:Xamarin.Forms.Device) 類別，或是 `OnPlatform` 標記延伸來提供平台特定值，皆可以更輕易的完成此動作。 如需詳細資訊，請參閱[提供平台特定值](~/xamarin-forms/platform/device.md#providing-platform-specific-values)和 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>建立自訂 Entry 控制項

您可以子類別化 `Entry` 控制項，建立自訂 [`Entry`](xref:Xamarin.Forms.Entry) 控制項，如下列程式碼範例所示：

```csharp
public class MyEntry : Entry
{
}
```

`MyEntry` 控制項會在 .NET Standard 程式庫專案中建立，其單純只是一個 [`Entry`](xref:Xamarin.Forms.Entry) 控制項。 控制項的自訂作業會在自訂轉譯器中完成，因此不需要在 `MyEntry` 控制項中進行其它實作。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自訂控制項

您可以宣告控制項的位置命名空間並使用控制項項目上的命名空間前置詞，在 .NET Standard 程式庫專案的 XAML 中參考 `MyEntry` 控制項。 下列程式碼範例示範 XAML 頁面如何使用 `MyEntry` 控制項：

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

`local` 命名空間前置詞可以任意命名。 不過，`clr-namespace` 和 `assembly` 值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，即會使用前置詞來參考自訂控制項。

下列程式碼範例示範 C# 頁面如何使用 `MyEntry` 控制項：

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

此程式碼會具現化新的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件，在頁面上顯示垂直和水平置中的 [`Label`](xref:Xamarin.Forms.Label) 和 `MyEntry` 控制項。

自訂轉譯器現在可以新增至每個應用程式專案，來自訂控制項在每個平台上的外觀。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每個平台上建立自訂轉譯器

建立自訂轉譯器類別的流程如下：

1. 建立轉譯原生控制項 `EntryRenderer` 類別的子類別。
1. 覆寫轉譯原生控制項的 `OnElementChanged` 方法，並撰寫自訂控制項的邏輯。 此方法會在建立對應 Xamarin.Forms 控制項時呼叫。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，指定用它轉譯 Xamarin.Forms 控制項。 這個屬性用來向 Xamarin.Forms 註冊自訂轉譯器。

> [!NOTE]
> 您可以選擇在每個平台專案中提供自訂轉譯器。 如果自訂轉譯器尚未註冊，則會使用控制項基底類別的預設轉譯器。

下圖說明應用程式範例中每個專案的責任，以及它們之間的關聯性：

![](entry-images/solution-structure.png "MyEntry 自訂轉譯器專案責任")

`MyEntry` 控制項是由平台特定 `MyEntryRenderer` 類別轉譯，其全部衍生自各平台的 `EntryRenderer` 類別。 這會導致每個 `MyEntry` 控制項都使用平台特定背景色彩轉譯，如下列螢幕擷取畫面所示：

![](entry-images/screenshots.png "每個平台上的 MyEntry 控制項")

`EntryRenderer` 類別會公開 `OnElementChanged` 方法，在建立 Xamarin.Forms 控制項來轉譯對應原生控制項時呼叫。 此方法會接受 `ElementChangedEventArgs` 參數，其中包含 `OldElement` 和 `NewElement` 屬性。 這些屬性分別代表轉譯器「過去」所附加的 Xamarin.Forms 項目，以及「現在」所附加的 Xamarin.Forms 項目。 在應用程式範例中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `MyEntry` 控制項的參考。

在 `MyEntryRenderer` 類別中，`OnElementChanged` 方法的覆寫版本是執行原生控制項自訂的位置。 平台上所使用的原生控制項具型別參考可透過 `Control` 屬性存取。 此外，所要轉譯 Xamarin.Forms 控制項的參考可透過 `Element` 屬性取得，雖然在此應用程式範例中並未使用該屬性。

每個自訂轉譯器類別都裝飾了向 Xamarin.Forms 註冊轉譯器的 `ExportRenderer` 屬性。 此屬性接受兩個參數：正在轉譯的 Xamarin.Forms 控制項類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

下列各節會討論每個平台特定 `MyEntryRenderer` 自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例示範適用於 iOS 平台的自訂轉譯器：

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

呼叫基底類別的 `OnElementChanged` 方法會具現化 iOS `UITextField` 控制項，且控制項的參考會指派給轉譯器的 `Control` 屬性。 背景色彩則會使用 `UIColor.FromRGB` 方法設為淺紫色。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

下列程式碼範例示範適用於 Android 平台的自訂轉譯器：

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

呼叫基底類別的 `OnElementChanged` 方法會具現化 Android `EditText` 控制項，且控制項的參考會指派給轉譯器的 `Control` 屬性。 背景色彩則會使用 `Control.SetBackgroundColor` 方法設為淺綠色。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上建立自訂轉譯器

下列程式碼範例示範適用於 UWP 的自訂轉譯器：

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

呼叫基底類別的 `OnElementChanged` 方法會具現化 `TextBox` 控制項，且控制項的參考會指派給轉譯器的 `Control` 屬性。 背景色彩則會透過建立 `SolidColorBrush` 執行個體設為青色。

## <a name="summary"></a>總結

本文示範了如何建立 [`Entry`](xref:Xamarin.Forms.Entry) 控制項的自訂控制項轉譯器，讓開發人員以自己的平台特定轉譯來覆寫預設原生轉譯。 自訂轉譯器提供自訂 Xamarin.Forms 控制項外觀的有效方法。 它們可用於小型樣式變更或複雜的平台特定配置及行為自訂。


## <a name="related-links"></a>相關連結

- [CustomRendererEntry (Samples)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
