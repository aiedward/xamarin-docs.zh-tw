---
title: 在 XAML 預覽器中轉譯自訂控制項
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
ms.openlocfilehash: 4f20a0586aee998c10372c60c96577321e697aad
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137263"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>在 XAML 預覽器中轉譯自訂控制項

_自訂控制項有時不會如預期般在 XAML 預覽器中運作。請使用本文中的指導方針來瞭解預覽自訂控制項的限制。_

## <a name="basic-preview-mode"></a>基本預覽模式

即使您尚未建立專案，XAML 預覽程式也會轉譯您的頁面。 在您建立之前，任何依賴程式碼後置的控制項都會顯示其基底 Xamarin.Forms 類型。 當您建立專案時，XAML 預覽程式會嘗試顯示已啟用設計階段轉譯的自訂控制項。 如果轉譯失敗，則會顯示基底 Xamarin.Forms 類型。

## <a name="enable-design-time-rendering-for-custom-controls"></a>啟用自訂控制項的設計階段轉譯

如果您自行建立自訂控制項，或使用協力廠商程式庫中的控制項，預覽程式可能會不正確地顯示它們。 不論您是從程式庫撰寫控制項或將它匯入，自訂控制項都必須選擇將時間轉譯設計成出現在預覽器中。 使用您已建立的控制項，將新增 [`[DesignTimeVisible(true)]`](xref:System.ComponentModel.DesignTimeVisibleAttribute) 至控制項的類別，以在預覽程式中顯示它：

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

使用[James Montemagno 的 ImageCirclePlugin's 基類](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs)做為範例。

## <a name="skiasharp-controls"></a>SkiaSharp 控制項

目前，只有當您在 iOS 上預覽時，才支援 SkiaSharp 控制項。 它們不會在 Android preview 上呈現。

## <a name="troubleshooting"></a>疑難排解

### <a name="check-your-xamarinforms-version"></a>檢查您的 Xamarin.Forms 版本
請確定您至少已 Xamarin.Forms 安裝3.6。 您可以 Xamarin.Forms 在 NuGet 上更新您的版本。

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>即使 `[DesignTimeVisible(true)]` 是，我的自訂控制項也不會正確轉譯。
嚴格依賴程式碼後置或後端資料的自訂控制項，不一定會在 XAML 預覽程式中運作。 您可以嘗試：

* 移動控制項，使其在[設計模式已啟用](index.md#detect-design-mode)時不會初始化
* 設定[設計階段資料](design-time-data.md)，以顯示後端的假資料

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>XAML 預覽器會顯示錯誤「自訂控制項無法正確轉譯」
嘗試清理和重建您的專案，或關閉並重新開啟 XAML 檔案。
