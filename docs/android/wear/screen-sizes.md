---
title: 使用 Xamarin. Android 和磨損 OS 中的螢幕大小
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 86e05dc0e9cd5df325126cc5a339b36dd27c1e45
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030364"
---
# <a name="working-with-screen-sizes"></a>使用螢幕大小

Android 磨損裝置可以有矩形或圓形顯示，這也可以是不同的大小。

![矩形和圓角磨損顯示的螢幕擷取畫面](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>識別螢幕類型

「磨損支援」程式庫提供一些控制項，可協助您偵測和適應不同的螢幕圖形，例如 `WatchViewStub` 和 `BoxInsetLayout`。

請注意，有些其他支援程式庫控制項（例如 `GridViewPager`）*會自動*偵測畫面圖形本身，而不應新增為下面所述控制項的子系。

### <a name="watchviewstub"></a>WatchViewStub

請參閱[WatchViewStub](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchviewstub)範例，以瞭解如何偵測螢幕類型，並針對每種類型顯示不同的版面配置。

主要版面配置檔案包含的 `android.support.wearable.view.WatchViewStub`，會使用 `app:rectLayout` 和 `app:roundLayout` 屬性來參考矩形和圓角畫面的不同版面配置：

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

解決方案包含各種不同的版面配置，適用于在執行時間選取的每種樣式：

![[資源]/[配置] 底下顯示的檔案](screen-sizes-images/solution.png)

### <a name="boxinsetlayout"></a>BoxInsetLayout

您也可以建立適應矩形或圓形畫面的單一視圖，而不是為每個螢幕類型建立不同的版面配置。

此[Google 範例](https://developer.android.com/training/wearables/ui/layouts.html#same-layout)示範如何使用 `BoxInsetLayout`，在矩形和圓角畫面上使用相同的版面配置。

## <a name="wear-ui-designer"></a>磨損 UI 設計工具

Xamarin Android Designer 同時支援矩形和圓角畫面：

![在 Xamarin Android Designer 中選取 Android 磨損正方形畫面](screen-sizes-images/design-screen-type.png)

矩形樣式的設計介面如下所示：

![矩形樣式的設計介面](screen-sizes-images/design-rect.png) 

Round 樣式中的設計介面如下所示：

![以圓角樣式呈現的設計介面](screen-sizes-images/design-round.png)

## <a name="wear-simulator"></a>磨損模擬器

「 **Google 模擬器管理員**」包含兩種螢幕類型的裝置定義。 您可以建立矩形和圓角模擬器來測試您的應用程式。

![Google 模擬器管理員中顯示的磨損裝置定義](screen-sizes-images/emulator-devices.png)

模擬器會針對矩形畫面呈現如下：

![矩形螢幕的模擬器呈現](screen-sizes-images/recipe-2.png) 

它會針對圓角畫面呈現如下：

![圓角畫面的模擬器呈現](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>視訊

[適用于 Android 的全螢幕應用程式](https://www.youtube.com/watch?v=naf_WbtFAlY)會從[developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw)磨損。
