---
title: 在 Xamarin. Android 和磨損作業系統中使用螢幕大小
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 1747601596cd1772210d9a66755d7aa98ca14052
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457025"
---
# <a name="working-with-screen-sizes"></a>使用螢幕大小

Android 磨損裝置可以是矩形或圓形顯示，也可以是不同的大小。

![矩形和圓形磨損顯示的螢幕擷取畫面](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>識別螢幕類型

「磨損支援程式庫」提供一些控制項，可協助您偵測及適應不同的螢幕圖形，例如 `WatchViewStub` 和 `BoxInsetLayout` 。

請注意，其他支援程式庫會控制 (例如 `GridViewPager`) *自動* 偵測螢幕圖形本身，不應新增為下列所述控制項的子系。

### <a name="watchviewstub"></a>WatchViewStub

請參閱 [WatchViewStub](/samples/xamarin/monodroid-samples/wear-watchviewstub) 範例，以瞭解如何偵測螢幕類型，並針對每種類型顯示不同的版面配置。

主要版面配置檔案包含， `android.support.wearable.view.WatchViewStub` 它會使用和屬性參考矩形和圓形的不同版面配置 `app:rectLayout` `app:roundLayout` ：

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

方案會針對每個將在執行時間選取的樣式包含不同的版面配置：

![在資源/配置下顯示的檔案](screen-sizes-images/solution.png)

### <a name="boxinsetlayout"></a>BoxInsetLayout

您也可以建立可適應矩形或圓形螢幕的單一視圖，而不是針對每個螢幕類型建立不同的版面配置。

這個 [Google 範例](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) 示範如何使用在 `BoxInsetLayout` 矩形和圓形畫面上使用相同的版面配置。

## <a name="wear-ui-designer"></a>磨損 UI 設計工具

Xamarin Android Designer 支援矩形和圓形畫面：

![在 Xamarin Android Designer 中選取 Android 磨損正方形畫面](screen-sizes-images/design-screen-type.png)

矩形樣式中的設計介面如下所示：

![矩形樣式的設計介面](screen-sizes-images/design-rect.png) 

迴圈樣式中的設計介面如下所示：

![圓形樣式的設計介面](screen-sizes-images/design-round.png)

## <a name="wear-simulator"></a>磨損模擬器

**Google 模擬器管理員**包含這兩種螢幕類型的裝置定義。 您可以建立矩形和圓形模擬器來測試您的應用程式。

![Google 模擬器管理員中顯示的磨損裝置定義](screen-sizes-images/emulator-devices.png)

模擬器的呈現方式會像是矩形畫面一樣：

![矩形螢幕的模擬器轉譯](screen-sizes-images/recipe-2.png) 

它會呈現如下的圓形畫面：

![圓形螢幕的模擬器轉譯](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>影片

[適用于 Android 的全螢幕應用程式](https://www.youtube.com/watch?v=naf_WbtFAlY) 從 [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw)磨損。