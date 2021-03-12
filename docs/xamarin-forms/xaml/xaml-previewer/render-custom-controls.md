---
title: 在 XAML 預覽中轉譯自訂控制項
description: 本文說明如何在 XAML 預覽程式中顯示您的自訂控制項。
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0e58626c193fb8e4c1bcf071bc44b64af4009851
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602940"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>在 XAML 預覽中轉譯自訂控制項

_自訂控制項有時候在 XAML 預覽中無法如預期般運作。使用本文中的指導方針來瞭解預覽自訂控制項的限制。_

> [!WARNING]
> XAML 預覽程式在 Visual Studio 2019 16.8 版和 Visual Studio for Mac 8.8 版中已淘汰，並由 Visual Studio 2019 版本16.9 和 Visual Studio for Mac 版本8.9 中的 XAML 熱重載功能所取代。
> 深入瞭解 [檔中的](~/xamarin-forms/xaml/hot-reload.md)XAML 熱重載。
## <a name="basic-preview-mode"></a>基本預覽模式

即使您尚未建立您的專案，XAML 預覽程式也會轉譯您的頁面。 在您建立之前，任何依賴程式碼後端的控制項都會顯示其基底 Xamarin.Forms 類型。 當您的專案建立時，XAML 預覽程式將會嘗試顯示已啟用設計階段轉譯的自訂控制項。 如果轉譯失敗，則會顯示基底 Xamarin.Forms 類型。

## <a name="enable-design-time-rendering-for-custom-controls"></a>啟用自訂控制項的設計階段轉譯

如果您自行建立自訂控制項，或使用協力廠商程式庫中的控制項，預覽程式可能會不正確地顯示它們。 無論您是從文件庫撰寫控制項還是將其匯入，自訂控制項都必須選擇設計階段轉譯，才能出現在預覽中。 使用您所建立的控制項，將新增 [`[DesignTimeVisible(true)]`](xref:System.ComponentModel.DesignTimeVisibleAttribute) 至控制項的類別，以便在預覽程式中顯示它：

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

使用 [James Montemagno 的 ImageCirclePlugin's 基類](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs) 作為範例。

## <a name="skiasharp-controls"></a>SkiaSharp 控制項

目前，只有當您在 iOS 上預覽時，才支援 SkiaSharp 控制項。 它們不會在 Android preview 上轉譯。

## <a name="troubleshooting"></a>疑難排解

### <a name="check-your-xamarinforms-version"></a>檢查您的 Xamarin.Forms 版本
請確定您至少已 Xamarin.Forms 安裝3.6。 您可以 Xamarin.Forms 在 NuGet 上更新您的版本。

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>即使有 `[DesignTimeVisible(true)]` ，我的自訂控制項也不會正確轉譯。
高度依賴程式碼後端或後端資料的自訂控制項，在 XAML 預覽程式中不一定會有作用。 您可以嘗試：

* 移動控制項，使其不會在[設計模式啟用](index.md#detect-design-mode)時初始化
* 設定 [設計階段資料](design-time-data.md) 以顯示來自後端的假資料

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>XAML 預覽器會顯示「自訂控制項未正確轉譯」錯誤
請嘗試清除並重建您的專案，或關閉並重新開啟 XAML 檔案。
