---
title: Xamarin.Forms雙畫面
description: 本指南說明如何建立 Xamarin.Forms 雙畫面裝置的應用程式。
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 967c38895faded88f7d102317f0d767daac738bd
ms.sourcegitcommit: 60f475a3d6ca880aff34f1177f76eff0b0f96233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/21/2020
ms.locfileid: "86869831"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Forms雙畫面

![](~/media/shared/preview.png "This API is currently pre-release")

Microsoft Surface 雙核這類雙螢幕裝置可協助您的應用程式有新的使用者體驗。 Xamarin.Forms包含 `TwoPaneView` 和 `DualScreenInfo` 類別，讓您可以為雙畫面裝置開發應用程式。

## <a name="get-started"></a>開始使用

請遵循下列步驟，將雙螢幕功能新增至 Xamarin.Forms 應用程式：

1. 開啟方案的 [ **NuGet 套件管理員**] 對話方塊。
2. 在 [瀏覽]  索引標籤底下搜尋 `Xamarin.Forms.DualScreen`。
3. 將 `Xamarin.Forms.DualScreen` 套件安裝到您的解決方案。
4. 在事件中，將下列初始化方法呼叫新增至 Android 專案的 `MainActivity` 類別 `OnCreate` ：

    ```csharp
    Xamarin.Forms.DualScreen.DualScreenService.Init(this);
    ```

    應用程式必須要有這個方法，才能偵測應用程式狀態的變更，例如跨越兩個畫面。

5. 更新 `Activity` Android 專案類別上的屬性 `MainActivity` ，使其包含_所有_這些 `ConfigurationChanges` 選項：

    ```@csharp
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation 
        | ConfigChanges.ScreenLayout | ConfigChanges.SmallestScreenSize | ConfigChanges.UiMode
    ```

    這些值是必要的，因此可以更可靠地報告設定變更和範圍狀態。 根據預設，只有兩個會加入至 Xamarin.Forms 專案，因此請記得新增其餘部分，以提供可靠的雙螢幕支援。

## <a name="troubleshooting"></a>疑難排解

如果 `DualScreenInfo` 類別或配置 `TwoPaneView` 未如預期般運作，請仔細檢查此頁面上的安裝指示。 省略或 misconfiguring `Init` 方法或 `ConfigurationChanges` 屬性值是常見的錯誤原因。

如需其他指引和參考的執行方式，請參閱[ Xamarin.Forms 雙重螢幕範例](https://docs.microsoft.com/dual-screen/xamarin/samples)。

## <a name="next-steps"></a>接下來的步驟

新增 NuGet 之後，請使用下列指導方針，在您的應用程式中新增雙畫面功能：

- [雙畫面設計模式](design-patterns.md)-考慮如何在雙螢幕裝置上充分利用多個畫面時，請參閱此模式指引，以找出最適合您的應用程式介面。
- [TwoPaneView 版面](twopaneview.md)配置- Xamarin.Forms `TwoPaneView` 由相同名稱的 UWP 控制項所啟發的類別，是針對雙螢幕裝置優化的跨平台佈建。
- [DualScreenInfo helper 類別](dual-screen-info.md)- `DualScreenInfo` 類別可讓您判斷您的視圖所在的窗格、其大小、裝置的狀態、轉軸的角度等等。
- [雙畫面觸發](triggers.md)程式- [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) 命名空間包含兩個狀態觸發程式，會 [`VisualState`](xref:Xamarin.Forms.VisualState) 在附加的版面配置或視窗的視圖模式變更時觸發變更。

如需詳細資訊，請流覽[雙重螢幕開發人員](https://docs.microsoft.com/dual-screen/)檔。
