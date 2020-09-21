---
title: Xamarin.Forms 雙螢幕
description: 本指南說明如何建立 Xamarin.Forms 雙螢幕裝置的應用程式。
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a414127c01d26db6bf7b462d6fc5a7f9ae44dddc
ms.sourcegitcommit: 69bd0fdc698c9b0c0d73217776d7084f32ae88ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832276"
---
# <a name="no-locxamarinforms-dual-screen"></a>Xamarin.Forms 雙螢幕

雙螢幕裝置（例如 Microsoft Surface）可為您的應用程式加速新的使用者體驗。 Xamarin.Forms 包括 `TwoPaneView` 和 `DualScreenInfo` 類別，讓您可以開發適用于雙螢幕裝置的應用程式。

## <a name="get-started"></a>開始使用

請遵循下列步驟，將雙重畫面功能新增至 Xamarin.Forms 應用程式：

1. 開啟方案的 [ **NuGet 封裝管理員** ] 對話方塊。
2. 在 [瀏覽]  索引標籤底下搜尋 `Xamarin.Forms.DualScreen`。
3. 將 `Xamarin.Forms.DualScreen` 套件安裝到您的解決方案。
4. 在事件中，將下列初始化方法呼叫新增至 Android 專案的 `MainActivity` 類別 `OnCreate` ：

    ```csharp
    Xamarin.Forms.DualScreen.DualScreenService.Init(this);
    ```

    應用程式必須要有此方法，才能偵測應用程式狀態的變更，例如跨越兩個畫面。

5. 更新 `Activity` Android 專案類別上的屬性 `MainActivity` ，使其包含 _所有_ 這些 `ConfigurationChanges` 選項：

    ```@csharp
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation
        | ConfigChanges.ScreenLayout | ConfigChanges.SmallestScreenSize | ConfigChanges.UiMode
    ```

    這些值是必要的，因此可以更可靠地報告設定變更和範圍狀態。 依預設只會將兩個 Xamarin.Forms 專案新增至專案中，因此請記得新增其餘部分以提供可靠的雙螢幕支援。

## <a name="troubleshooting"></a>疑難排解

如果 `DualScreenInfo` 類別或 `TwoPaneView` 版面配置未如預期般運作，請仔細檢查此頁面上的設定指示。 省略或 misconfiguring `Init` 方法或 `ConfigurationChanges` 屬性值是常見的錯誤原因。

請參閱[ Xamarin.Forms 雙重畫面範例](https://docs.microsoft.com/dual-screen/xamarin/samples)，以取得其他指引和參考實行。

## <a name="next-steps"></a>後續步驟

新增 NuGet 之後，請使用下列指引將雙重畫面功能新增至您的應用程式：

- [雙螢幕設計模式](design-patterns.md) -考慮如何在雙螢幕裝置上充分利用多個畫面時，請參閱此模式指引，以找出最適合您的應用程式介面。
- [TwoPaneView 版面](twopaneview.md) 配置- Xamarin.Forms `TwoPaneView` 具有相同名稱之 UWP 控制項所啟發的類別，是針對雙螢幕裝置優化的跨平臺版面配置。
- [DualScreenInfo](dual-screen-info.md) 協助程式類別- `DualScreenInfo` 類別可讓您判斷您的視圖所在的窗格、其大小、裝置所在的狀態、轉軸的角度等。
- [雙螢幕觸發](triggers.md) 程式- [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) 命名空間包含兩個狀態觸發程式， [`VisualState`](xref:Xamarin.Forms.VisualState) 當附加配置或視窗的視圖模式變更時，就會觸發變更。

如需詳細資訊，請流覽 [雙螢幕開發人員](https://docs.microsoft.com/dual-screen/) 檔。
