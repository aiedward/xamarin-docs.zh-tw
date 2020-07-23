---
title: 自訂轉譯器簡介
description: 本文簡介如何自訂轉譯器，並概述建立自訂轉譯器的程序。
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0eb768f5d6cabbb41b07a5aad33269fc0f493237
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939525"
---
# <a name="introduction-to-custom-renderers"></a>自訂轉譯器簡介

_自訂轉譯器提供了一種強大的方法，可自訂控制項的外觀和行為 Xamarin.Forms 。它們可用於小型樣式變更或複雜的平臺特定版面配置和行為自訂。本文提供自訂轉譯器的簡介，並概述建立自訂轉譯器的程式。_

Xamarin.Forms[頁面、版面配置和控制項](~/xamarin-forms/user-interface/controls/index.md)呈現通用 API 來描述跨平臺行動使用者介面。 每個頁面、版面配置和控制項在每個平臺上都以不同的方式轉譯，方法是使用 `Renderer` 類別來建立原生控制項（對應于 Xamarin.Forms 標記法）、將它排列在畫面上，然後加入在共用程式碼中指定的行為。

開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 您可將指定類型的自訂轉譯器新增至某個應用程式專案，在某處自訂控制項，同時允許其他平台的預設行為；或者，將不同的自訂轉譯器新增至每個應用程式專案，在 iOS、Android 和通用 Windows 平台 (UWP) 上建立不同的外觀與風格。 不過，實作自訂轉譯器類別來執行簡單的控制項自訂通常是繁重的回應方式。 效果會簡化這個程序，且通常用於小型的樣式變更。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="examining-why-custom-renderers-are-necessary"></a>檢查自訂轉譯器為何必要的原因

變更控制項的外觀 Xamarin.Forms ，而不使用自訂轉譯器，是一個包含兩個步驟的程式，其中牽涉到透過子類別化來建立自訂控制項，然後使用自訂控制項來取代原始控制項。 下列程式碼範例會示範 `Entry` 控制項子類別化的範例：

```csharp
public class MyEntry : Entry
{
  public MyEntry ()
  {
    BackgroundColor = Color.Gray;
  }
}
```

`MyEntry` 控制項是 `BackgroundColor` 設為灰色的 `Entry` 控制項，您可以宣告它的位置命名空間，並在控制項項目上使用命名空間前置詞，在 XAML 中參考此控制項。 下列程式碼範例示範 `ContentPage` 如何使用 `MyEntry` 自訂控制項：

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

`local` 命名空間前置詞可以使用任何內容。 不過，`namespace` 和 `assembly` 值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，即會使用前置詞來參考自訂控制項。

> [!NOTE]
> 在 .NET Standard 程式庫專案中定義 `xmlns` 比使用共用專案更簡單。 .NET Standard 程式庫會編譯成組件，因此很容易判斷 `assembly=CustomRenderer` 值應為何。 使用共用專案時，所有共用的資產 (包括 XAML) 都會編譯成每個參考專案；這表示，如果 iOS、Android 和 UWP 專案有自己的「組件名稱」**，就無法寫入 `xmlns` 宣告，因為每個應用程式必須有不同的值。 共用專案的 XAML 自訂控制項需要使用相同的組件名稱來設定每個應用程式專案。

然後，使用灰色背景在每個平台中轉譯 `MyEntry` 自訂控制項，如下列螢幕擷取畫面所示：

![每個平台上的 MyEntry 自訂控制項](introduction-images/screenshots.png)

只要透過子類別化控制項，就能變更每個平台上的控制項背景色彩。 不過，這項技術僅限於其所能達到的程度，因為無法充分利用平台特定的增強功能和自訂。 有必要時，必須實作自訂轉譯器。

## <a name="creating-a-custom-renderer-class"></a>建立自訂轉譯器類別

建立自訂轉譯器類別的程序如下：

1. 建立轉譯原生控制項之轉譯器類別的子類別。
1. 覆寫轉譯原生控制項的方法，並撰寫自訂控制項的邏輯。 通常， `OnElementChanged` 方法是用來呈現原生控制項，這會在建立對應控制項時呼叫 Xamarin.Forms 。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，以指定將用來呈現 Xamarin.Forms 控制項。 這個屬性是用來向註冊自訂轉譯器 Xamarin.Forms 。

> [!NOTE]
> 對於大部分的 Xamarin.Forms 元素而言，在每個平臺專案中提供自訂轉譯器是選擇性的。 如果自訂轉譯器尚未註冊，則會使用控制項基底類別的預設轉譯器。 不過，轉譯 [View](xref:Xamarin.Forms.View) 或 [ViewCell](xref:Xamarin.Forms.ViewCell) 項目時，每個平台專案都必須要有自訂轉譯器。

本系列中的主題將針對不同元素提供此程式的示範和說明 Xamarin.Forms 。

## <a name="troubleshooting"></a>疑難排解

如果自訂控制項包含在已新增至方案的 .NET Standard 程式庫專案中（亦即，不是 Visual Studio for Mac/Visual Studio 應用程式專案範本所建立的 .NET Standard 程式庫 Xamarin.Forms ），則在嘗試存取自訂控制項時，iOS 可能會發生例外狀況。 如果發生此問題，從 `AppDelegate` 類別建立自訂控制項參考可予以解決：

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

這會強制編譯器解析類型以辨識 `ClassInPCL` 類型。 或者，您可將 `Preserve` 屬性新增至 `AppDelegate` 類別來達到相同的結果：

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

這會建立 `ClassInPCL` 類型的參考，指出它在執行階段為必要。 如需詳細資訊，請參閱[保留程式碼](~/ios/deploy-test/linker.md)。

## <a name="summary"></a>總結

本文已簡介自訂轉譯器，並概述建立自訂轉譯器的程序。 自訂轉譯器提供了一種強大的方法，可自訂控制項的外觀和行為 Xamarin.Forms 。 自訂轉譯器可用於小型樣式變更或複雜的平台特定版面配置，以及行為自訂。

## <a name="related-links"></a>相關連結

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
