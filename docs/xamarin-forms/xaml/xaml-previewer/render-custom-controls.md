---
title: 呈現 XAML 預覽程式中的自訂控制項
description: 本文說明如何在 XAML 預覽程式中顯示您的自訂控制項。
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 977c29312e0be8b92f216c224414f9bd03f8562d
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58858968"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>呈現 XAML 預覽程式中的自訂控制項

_自訂控制項有時不正常運作中 XAML 預覽程式。這篇文章中使用的指南，以了解預覽您的自訂控制項的限制。_

## <a name="basic-preview-mode"></a>基本的 [預覽] 模式

即使您還沒有建立您的專案，XAML 預覽程式會呈現您的頁面。 直到您建置時，程式碼後置所依賴的任何控制項就會顯示其基底的 Xamarin.Forms 型別。 建置您的專案時，XAML 預覽程式會嘗試使用已啟用的設計階段呈現中顯示自訂控制項。 如果呈現失敗，它會顯示基底的 Xamarin.Forms 型別。

## <a name="enable-design-time-rendering-for-custom-controls"></a>啟用自訂控制項的設計階段呈現

如果您讓自己的自訂控制項，或從協力廠商程式庫中使用控制項時，預覽程式可能無法正確顯示。 設計階段轉譯為是否您撰寫控制項，或從程式庫將它匯入，會出現在預覽程式，必須選擇自訂控制項。 您已建立的控制項，加上[ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute)至您的控制項類別，以顯示在預覽程式中：

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

使用[James Montemagno ImageCirclePlugin 基底類別](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs)做為範例。


## <a name="skiasharp-controls"></a>SkiaSharp 控制項

目前，SkiaSharp 控制項時，才支援您目前在 iOS 上預覽。 它們不會呈現在 Android 的預覽。

## <a name="troubleshooting"></a>疑難排解

### <a name="check-your-xamarinforms-version"></a>檢查您的 Xamarin.Forms 版本
請確定您至少已安裝的 Xamarin.Forms 3.6。 您可以更新 Xamarin.Forms 版本在 NuGet 上。

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>即使`[DesignTimeVisible(true)]`，我自訂的控制項不會正確呈現。
依賴程式碼後置或後端資料的自訂控制項一律沒有作用中 XAML 預覽程式。 您可以嘗試：
* 移動控制項，因此它不會初始化如果[設計模式已啟用](index.md#detect-design-mode)
* 設定好[設計階段資料](design-time-data.md)顯示假的資料，從後端

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>XAML 預覽程式會顯示錯誤 「 自訂控制項不正確地轉譯 」
請嘗試清除並重建您的專案，或關閉並重新開啟 XAML 檔案。
