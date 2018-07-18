---
title: 自訂轉譯器簡介
description: 本文章簡介如何自訂轉譯器，並概述的程序建立自訂轉譯器。
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: 180a196e0b95854815c8a74ef1d2df63407dd04f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998000"
---
# <a name="introduction-to-custom-renderers"></a>自訂轉譯器簡介

_自訂轉譯器會提供自訂的外觀和行為的 Xamarin.Forms 控制項的強大方法。他們可以使用小型的樣式變更或複雜的平台特定版面配置和行為的自訂。本文章簡介如何自訂轉譯器，並概述的程序建立自訂轉譯器。_

Xamarin.Forms[頁面、 版面配置和控制項](~/xamarin-forms/user-interface/controls/index.md)呈現一個常見的 API，可說明跨平台行動裝置的使用者介面。 每個頁面、 版面配置和控制項不同的方式呈現每個平台上，使用`Renderer`類別，接著再建立原生控制項 （對應至 Xamarin.Forms 表示法），排列在畫面上，並將在指定的行為。共用的程式碼。

開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 指定類型的自訂轉譯器可以新增至自訂控制項在一處同時允許其他平台; 上的預設行為的一個應用程式專案或不同的自訂轉譯器可以新增至每個應用程式專案，以在 iOS、 Android 和通用 Windows 平台 (UWP) 上建立不同的外觀與風格。 不過，實作自訂轉譯器類別來執行簡單的控制項自訂通常是重量回應。 效果簡化這個程序，和通常用於小型的樣式變更。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="examining-why-custom-renderers-are-necessary"></a>正在檢查為什麼自訂轉譯器為必要

Xamarin.Forms 控制項的外觀會變更，而不需使用自訂轉譯器，是兩步驟程序涉及建立透過子類別化，並接著使用 自訂控制項，以取代原始控制項的自訂控制項。 下列程式碼範例顯示子類別化的範例`Entry`控制項：

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

`MyEntry`控制項是`Entry`控制 where`BackgroundColor`設為灰色，而且可以在藉由宣告其位置的命名空間和控制項項目上使用的命名空間前置詞在 Xaml 中參考。 下列程式碼範例示範如何`MyEntry`可供自訂控制項`ContentPage`:

```xaml
<ContentPage
    ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

`local`命名空間前置詞可以是任何項目。 不過，`namespace`和`assembly`值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，前置詞用來參考自訂控制項。

> [!NOTE]
> 定義`xmlns`比簡單多了在.NET Standard 程式庫專案中共用的專案。 .NET Standard 程式庫會編譯成組件上，因此很容易判斷何種`assembly=CustomRenderer`值應該是。 （包含 XAML） 的所有共用的資產時使用共用專案，編譯成每個參考的專案，這表示，如果 iOS、 Android 和 UWP 專案有自己*組件名稱*就無法寫入`xmlns`宣告因為值必須是每個應用程式不同。 共用專案的 XAML 中的自訂控制項需要使用相同的組件名稱來設定每個應用程式專案。

`MyEntry`自訂控制項然後轉譯在每個平台，以灰色背景，如下列螢幕擷取畫面所示：

![](introduction-images/screenshots.png "每個平台的 MyEntry 自訂控制項")

已經完成變更每個平台上的控制項的背景色彩單純透過子類別化控制項。 不過，這項技術僅限於因為無法充分利用平台專屬的增強功能和自訂它可以達到的目的。 在需要時，就必須實作自訂轉譯器。

## <a name="creating-a-custom-renderer-class"></a>建立自訂轉譯器類別

建立自訂轉譯器類別的程序如下所示：

1. 建立將原生控制項呈現的轉譯器類別的子類別。
1. 覆寫將原生控制項呈現的方法，撰寫自訂控制項的邏輯。 通常，`OnElementChanged`方法用來呈現原生控制項，在建立對應的 Xamarin.Forms 控制項時呼叫。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 控制項的自訂轉譯器類別。 這個屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 大部分的 Xamarin.Forms 元素，則是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，則會使用預設的轉譯器控制項的基底類別。 不過，自訂轉譯器所需的每個平台專案中時轉譯[檢視](xref:Xamarin.Forms.View)或是[ViewCell](xref:Xamarin.Forms.ViewCell)項目。

在這一系列的主題將提供示範和說明此程序不同的 Xamarin.Forms 元素。

## <a name="troubleshooting"></a>疑難排解

如果自訂控制項包含在.NET Standard 程式庫專案已加入至方案 （也就是不.NET Standard 程式庫 Visual Studio for Mac/Visual Studio Xamarin.Forms 應用程式專案範本所建立），例外狀況可能會發生在 iOS 中時嘗試存取自訂的控制項。 如果發生此問題，但可以藉由建立自訂控制項的參考解析`AppDelegate`類別：

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

這會強制編譯器辨識`ClassInPCL`解決它的型別。 或者，`Preserve`屬性可以新增至`AppDelegate`類別來達到相同的結果：

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

這會建立參考`ClassInPCL`型別，表示它必須在執行階段。 如需詳細資訊，請參閱 <<c0> [ 保留的程式碼](~/ios/deploy-test/linker.md)。

## <a name="summary"></a>總結

本文章提供自訂的轉譯器的簡介，並概述了建立自訂轉譯器的程序。 自訂轉譯器會提供自訂的外觀和行為的 Xamarin.Forms 控制項的強大方法。 他們可以使用小型的樣式變更或複雜的平台特定版面配置和行為的自訂。


## <a name="related-links"></a>相關連結

- [Effects](~/xamarin-forms/app-fundamentals/effects/index.md)
