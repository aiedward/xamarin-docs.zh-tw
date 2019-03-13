---
title: 使用 Xamarin.Android 和損耗的 OS 中的螢幕大小
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 9fc22a3c08b60a8474b006f1c9225155b9705507
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113114"
---
# <a name="working-with-screen-sizes"></a>使用 螢幕大小

Android Wear 裝置可以有矩形或圓形的顯示，也可以是不同的大小。

![矩形和圓形損耗的螢幕擷取畫面顯示](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>用來識別畫面中輸入

Wear 支援程式庫提供一些控制項，可協助您偵測並適應不同畫面的圖形，而這類`WatchViewStub`和`BoxInsetLayout`。

請注意的一些其他支援程式庫控制項 (例如`GridViewPager`)*自動*偵測畫面圖形本身並不應該在如下所述的控制項的子系加入。

### <a name="watchviewstub"></a>WatchViewStub

請參閱[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)範例以了解如何偵測畫面中，輸入，並顯示每種類型的不同版面配置。

主要的版面配置檔案包含`android.support.wearable.view.WatchViewStub`會參考不同的版面配置，針對使用的矩形和圓形螢幕`app:rectLayout`和`app:roundLayout`屬性：

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

解決方案包含每個樣式會在執行階段中選取不同的版面配置：

![顯示在 資源/版面配置檔](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

而不是建置的每個畫面中，輸入不同的版面配置，您也可以建立單一檢視，適用於矩形或圓形的畫面。

這[Google 範例](https://developer.android.com/training/wearables/ui/layouts.html#same-layout)示範如何使用`BoxInsetLayout`矩形和圓形畫面上使用相同的配置。


## <a name="wear-ui-designer"></a>穿戴式 UI 設計工具

Xamarin Android Designer 支援矩形和圓形畫面：

![選取 Xamarin Android Designer 中的 Android Wear 正方形畫面](screen-sizes-images/design-screen-type.png)

矩形的樣式中的設計介面如下所示：

![在矩形的樣式中的設計介面](screen-sizes-images/design-rect.png) 

捨入樣式中的設計介面如下所示：

![捨入樣式在設計介面](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Wear 模擬器

**Google 模擬器管理員**螢幕類型包含裝置定義。 您可以建立矩形和圓形的模擬器，來測試您的應用程式。

![穿戴式裝置定義顯示在 Google 模擬器管理員](screen-sizes-images/emulator-devices.png)

矩形螢幕，模擬器會轉譯如下：

![模擬器矩形螢幕的轉譯](screen-sizes-images/recipe-2.png) 

圓螢幕，它會轉譯如下：

![模擬器轉譯圓螢幕](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>視訊

[全螢幕應用程式的 Android Wear](https://www.youtube.com/watch?v=naf_WbtFAlY)從[developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw)。

