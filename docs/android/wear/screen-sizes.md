---
title: 使用 Xamarin.Android 和損耗的作業系統中的螢幕大小
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: 40e7850ffe239b0ede43e4d0cd3c6da08bce3a40
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436072"
---
# <a name="working-with-screen-sizes"></a>使用的螢幕大小

Android 損耗裝置可以有矩形或圓形的顯示，也可以是不同的大小。

![矩形和圓形損耗的螢幕擷取畫面顯示](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>識別螢幕類型

損耗支援程式庫會提供一些控制項，可協助您偵測並調整到不同的螢幕圖形，例如`WatchViewStub`和`BoxInsetLayout`。

請注意一些其他支援程式庫控制項 (例如`GridViewPager`)*自動*偵測螢幕圖形本身並不應該如下所述的控制項的子系加入。

### <a name="watchviewstub"></a>WatchViewStub

請參閱[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)範例，請參閱如何偵測畫面中，輸入並顯示每個型別不同的版面配置。

主要的版面配置檔案包含`android.support.wearable.view.WatchViewStub`會參考不同的版面配置使用的矩形和圓形螢幕`app:rectLayout`和`app:roundLayout`屬性：

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

解決方案包含每個樣式會在執行階段選取不同的版面配置：

![顯示在 資源/版面配置的檔案](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

而不是建置的每個畫面中，輸入不同的版面配置，您也可以建立單一檢視，適用於矩形或圓形的螢幕。

這[Google 範例](https://developer.android.com/training/wearables/ui/layouts.html#same-layout)示範如何使用`BoxInsetLayout`矩形和圓形螢幕上使用相同的配置。


## <a name="wear-ui-designer"></a>有 UI 設計工具

Xamarin Android 設計工具支援矩形和圓形螢幕：

![Xamarin Android 設計工具中選取 Android 損耗方畫面](screen-sizes-images/design-screen-type.png)

矩形樣式中的設計介面會如下所示：

![矩形樣式中的設計介面](screen-sizes-images/design-rect.png) 

Round 樣式中的設計介面會如下所示：

![Round 樣式中的設計介面](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>穿模擬器

**Google 模擬器管理員**包含裝置定義螢幕類型。 您可以建立矩形和圓形模擬器測試應用程式。

![有顯示 Google 模擬器管理員中的裝置定義](screen-sizes-images/emulator-devices.png)

矩形的螢幕，模擬器會轉譯像這樣：

![模擬器矩形螢幕的轉譯](screen-sizes-images/recipe-2.png) 

Round 螢幕，它會轉譯像這樣：

![模擬器 round 螢幕的轉譯](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>視訊

[全螢幕應用程式的 Android 損耗](https://www.youtube.com/watch?v=naf_WbtFAlY)從[developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw)。

