---
title: 自訂轉譯器簡介
description: 這篇文章提供自訂的轉譯器的簡介，並且摘要說明建立自訂轉譯器的程序。
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: fa22be081433bdd0c59a0d921511d3f3d83d4448
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241760"
---
# <a name="introduction-to-custom-renderers"></a>自訂轉譯器簡介

_自訂轉譯器會提供強大的方法用於自訂的外觀和 Xamarin.Forms 控制項的行為。它們可以用於小型的樣式變更或複雜的平台專屬版面配置和自訂行為。這篇文章提供自訂的轉譯器的簡介，並且摘要說明建立自訂轉譯器的程序。_

Xamarin.Forms[頁面、 版面配置和控制項](~/xamarin-forms/user-interface/controls/index.md)呈現一般 API 來描述跨平台行動裝置的使用者介面。 每個頁面、 版面配置和控制項以不同的方式上呈現每個平台，使用`Renderer`類別接著會建立原生控制項 （對應至 Xamarin.Forms 表示法），其排列以在畫面上，並將在指定的行為。共用的程式碼。

開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 每種類型的自訂轉譯器可以加入一個應用程式專案中加入自訂控制項，在一個地方，同時允許在其他平台; 上的預設行為或不同的自訂轉譯器可以加入 iOS、 Android 和通用 Windows 平台 (UWP) 上建立不同的外觀與風格的每個應用程式專案。 不過，實作自訂轉譯器類別來執行簡單的控制項自訂通常是重量回應。 影響簡化這個程序，而且通常用於小型的樣式變更。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="examining-why-custom-renderers-are-necessary"></a>正在檢查為什麼自訂轉譯器會視需要

Xamarin.Forms 控制項的外觀會變更，而不需使用自訂轉譯器，為兩個步驟程序，包括建立透過子類別化，然後使用自訂控制項來取代原始控制項的自訂控制項。 下列程式碼範例顯示子類別化的範例`Entry`控制項：

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

`MyEntry`控制項是`Entry`控制 where`BackgroundColor`設為灰色，而且可以在 Xaml 中宣告的命名空間，做為其位置，並使用命名空間前置詞上的控制項項目參考。 下列程式碼範例示範如何`MyEntry`可由自訂控制項`ContentPage`:

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
> 定義`xmlns`.NET 標準程式庫專案中共用專案比來得簡單。 標準.NET 程式庫已編譯的組件，所以可以輕鬆地判斷何種`assembly=CustomRenderer`值應該是。 當使用共用專案時，所有共用的資產 （包括 XAML） 都會編譯成每個參考的專案，這表示，如果 iOS、 Android 和 UWP 專案具有自己*組件名稱*就無法寫入`xmlns`宣告因為此值必須為每個應用程式不同。 共用專案的 XAML 中的自訂控制項將需要使用相同的組件名稱來設定每個應用程式專案。

`MyEntry`中的下列螢幕擷取畫面所示，每個平台上，以灰色背景，然後呈現自訂控制項：

![](introduction-images/screenshots.png "每個平台上的 MyEntry 自訂控制項")

單純透過子類別化控制項時，即已完成變更每個平台上的控制項的背景色彩。 不過，這項技術受到因為無法充分利用平台專屬的增強功能和自訂它可以達到的目的。 需要時，就必須實作自訂轉譯器。

## <a name="creating-a-custom-renderer-class"></a>建立自訂轉譯器類別

建立自訂轉譯器類別的程序如下所示：

1. 建立呈現原生控制項的轉譯器類別的子類別。
1. 覆寫方法呈現原生控制項和撰寫自訂控制項的邏輯。 通常，`OnElementChanged`方法用來呈現原生控制項，當建立對應的 Xamarin.Forms 控制項時呼叫。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 控制項的自訂轉譯器類別。 此屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 大部分的 Xamarin.Forms 項目，則是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，將使用預設的產生器控制項的基底類別。 不過，自訂轉譯器所需每個平台專案中時呈現[檢視](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)或[ViewCell](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)項目。

在這一系列的主題將提供示範與說明此程序的不同 Xamarin.Forms 項目。

## <a name="troubleshooting"></a>疑難排解

如果自訂控制項包含在已加入至方案 （也就是不.NET 標準程式庫由 Visual Studio for Mac/Visual Studio Xamarin.Forms 應用程式專案範本建立），例外狀況的.NET 標準程式庫專案可能會發生在 iOS 中時嘗試存取的自訂控制項。 如果發生這個問題可以藉由建立自訂控制項的參考已解決`AppDelegate`類別：

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

這會強制編譯器可以辨認`ClassInPCL`解析它的類型。 或者，`Preserve`屬性可以加入至`AppDelegate`類別來達成相同結果：

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

這會建立參考`ClassInPCL`類型，表示它具有需要在執行階段。 如需詳細資訊，請參閱[保留程式碼](~/ios/deploy-test/linker.md)。

## <a name="summary"></a>總結

本文提供的自訂轉譯器的簡介，並建立自訂轉譯器的程序概述了。 自訂轉譯器會提供強大的方法用於自訂的外觀和 Xamarin.Forms 控制項的行為。 它們可以用於小型的樣式變更或複雜的平台專屬版面配置和自訂行為。


## <a name="related-links"></a>相關連結

- [Effects](~/xamarin-forms/app-fundamentals/effects/index.md)
