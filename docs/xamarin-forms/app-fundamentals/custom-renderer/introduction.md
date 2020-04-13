---
title: 自訂轉譯器簡介
description: 本文簡介如何自訂轉譯器，並概述建立自訂轉譯器的程序。
ms.prod: xamarin
ms.assetid: 264314BE-1C5C-4727-A14E-F6F98151CDBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/19/2016
ms.openlocfilehash: ad2868a82f662f45066a6111a1dd3bd2aacad671
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771873"
---
# <a name="introduction-to-custom-renderers"></a>自訂轉譯器簡介

_自定義呈現器為自定義 Xamarin.Forms 控制件的外觀和行為提供了一種強大的方法。它們可用於小型樣式更改或複雜的特定於平臺的佈局和行為自定義。本文介紹了自定義呈現器,並概述了創建自定義呈現器的過程。_

Xamarin.Forms [頁面、配置和控制項](~/xamarin-forms/user-interface/controls/index.md)提供通用 API，描述跨平台行動裝置的使用者介面。 系統會在每個平台上使用 `Renderer` 類別，以不同的方式轉譯每個頁面、配置和控制項，進而建立原生控制項 (對應至 Xamarin.Forms 表示方式)、將其排列在畫面上，然後新增在共用程式碼中指定的行為。

開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 您可將指定類型的自訂轉譯器新增至某個應用程式專案，在某處自訂控制項，同時允許其他平台的預設行為；或者，將不同的自訂轉譯器新增至每個應用程式專案，在 iOS、Android 和通用 Windows 平台 (UWP) 上建立不同的外觀與風格。 不過，實作自訂轉譯器類別來執行簡單的控制項自訂通常是繁重的回應方式。 效果會簡化這個程序，且通常用於小型的樣式變更。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="examining-why-custom-renderers-are-necessary"></a>檢查自訂轉譯器為何必要的原因

變更 Xamarin.Forms 控制項的外觀，但不使用自訂轉譯器，是兩步驟的程序，包括透過子類別化建立自訂控制項，然後以此自訂控制項取代原始控制項。 下列程式碼範例會示範 `Entry` 控制項子類別化的範例：

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

![](introduction-images/screenshots.png "MyEntry Custom Control on each Platform")

只要透過子類別化控制項，就能變更每個平台上的控制項背景色彩。 不過，這項技術僅限於其所能達到的程度，因為無法充分利用平台特定的增強功能和自訂。 有必要時，必須實作自訂轉譯器。

## <a name="creating-a-custom-renderer-class"></a>建立自訂轉譯器類別

建立自訂轉譯器類別的程序如下：

1. 建立轉譯原生控制項之轉譯器類別的子類別。
1. 覆寫轉譯原生控制項的方法，並撰寫自訂控制項的邏輯。 `OnElementChanged` 方法常用於轉譯原生控制項，會在建立對應的 Xamarin.Forms 控制項時呼叫。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，指定用它轉譯 Xamarin.Forms 控制項。 這個屬性會用來向 Xamarin.Forms 註冊自訂轉譯器。

> [!NOTE]
> 對大部分的 Xamarin.Forms 項目而言，可以選擇是否在每個平台專案中提供自訂轉譯器。 如果自訂轉譯器尚未註冊，則會使用控制項基底類別的預設轉譯器。 不過，轉譯 [View](xref:Xamarin.Forms.View) 或 [ViewCell](xref:Xamarin.Forms.ViewCell) 項目時，每個平台專案都必須要有自訂轉譯器。

本系列的主題都會提供不同 Xamarin.Forms 項目的此程序示範和說明。

## <a name="troubleshooting"></a>疑難排解

如果自訂控制項包含在已新增至解決方案的 .NET Standard 程式庫專案中 (即非 Visual Studio for Mac/Visual Studio Xamarin.Forms 應用程式專案範本建立的 .NET Standard 程式庫)，在 iOS 中，嘗試存取自訂控制項時可能會發生例外狀況。 如果發生此問題，從 `AppDelegate` 類別建立自訂控制項參考可予以解決：

```csharp
var temp = new ClassInPCL(); // in AppDelegate, but temp not used anywhere
```

這會強制編譯器解析類型以辨識 `ClassInPCL` 類型。 或者，您可將 `Preserve` 屬性新增至 `AppDelegate` 類別來達到相同的結果：

```csharp
[assembly: Preserve (typeof (ClassInPCL))]
```

這會建立 `ClassInPCL` 類型的參考，指出它在執行階段為必要。 如需詳細資訊，請參閱[保留程式碼](~/ios/deploy-test/linker.md)。

## <a name="summary"></a>總結

本文已簡介自訂轉譯器，並概述建立自訂轉譯器的程序。 自訂轉譯器提供自訂 Xamarin.Forms 控制項外觀和行為的有力方法。 自訂轉譯器可用於小型樣式變更或複雜的平台特定版面配置，以及行為自訂。

## <a name="related-links"></a>相關連結

- [影響](~/xamarin-forms/app-fundamentals/effects/index.md)
