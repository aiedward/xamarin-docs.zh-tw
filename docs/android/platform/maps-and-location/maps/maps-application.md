---
title: 啟動對應應用程式
description: 如何啟動內建對應內的應用程式從您的 Xamarin.Android 應用程式。
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: fa32783617fce99514560677184f17be904cd42d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670316"
---
# <a name="launching-the-maps-application"></a>啟動對應應用程式

使用對應在 Xamarin.Android 中的最簡單方式是利用內建的地圖應用程式，如下所示：

[![內建的 Google Maps 應用程式的範例螢幕擷取畫面](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

當您使用的對應應用程式時，對應不會屬於您應用程式。 相反地，您的應用程式會啟動對應的應用程式，並載入外部的地圖。 下一節將探討如何使用 Xamarin.Android 來啟動如同上面的對應。


## <a name="creating-the-intent"></a>建立目的

使用對應應用程式是簡單，只要使用適當的 URI 建立意圖、 將動作設定為 ActionView，然後呼叫 StartActivity 方法。 比方說，下列程式碼會啟動位於指定的緯度和經度的對應應用程式：

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

此程式碼就必須啟動先前的螢幕擷取畫面所示的對應。 除了指定緯度和經度，對應的 URI 配置會支援數個其他選項。


## <a name="geo-uri-scheme"></a>異地 URI 配置

上述程式碼會使用異地配置來建立 URI。 此 URI 配置支援數種格式，如下所示：

-   `geo:latitude,longitude` &ndash; 開啟對應應用程式在 lat/lon 置中。 

-   `geo:latitude,longitude?z=zoom` &ndash; 開啟應用程式在 lat/lon 置中與縮放比例，以指定的層級的對應。 縮放層級的範圍可從 1 到 23:1 會顯示整個地球，23 是最接近的縮放層級。

-   `geo:0,0?q=my+street+address` &ndash; 開啟對應應用程式的街道地址的位置。 

-   `geo:0,0?q=business+near+city` &ndash; 開啟對應的應用程式並顯示已標註的搜尋結果。 


有個查詢 （也就是街道地址或搜尋詞彙） 之 URI 的版本會使用 Google 的 geocoder 服務來擷取接著會顯示在地圖的位置。 例如，URI`geo:0,0?q=coop+Cambridge`產生的對應如下所示：

[![顯示與搜尋詞彙的 Google 地圖的範例螢幕擷取畫面](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



如需地理 URI 配置的詳細資訊，請參閱[在地圖上顯示位置](https://developer.android.com/guide/components/intents-common.html#Maps)。


## <a name="street-view"></a>街道地檢視

除了異地配置中，Android 也支援從意圖載入街道的檢視。 從 Xamarin.Android 啟動街道地檢視應用程式的範例如下所示：

[![街道地檢視的範例螢幕擷取畫面](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

若要啟動的街道地檢視，只要使用`google.streetview`URI 配置，如下列程式碼所示：

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

Google.streetview URI 配置，上述範例中使用的格式如下：

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

如您所見，有數個參數的支援，如下所示：

-   `lat` &ndash; 要在 [街道] 檢視中顯示位置的緯度。

-   `lng` &ndash; 要在 [街道] 檢視中顯示位置的經度。

-   `pitch` &ndash; 街道地檢視全景，從中央中度 90 度的直向下和-90 度的角度已直接啟動。

-   `yaw` &ndash; 的-的置中檢視街道地檢視全景，會以度為單位從北方順時針旋轉。

-   `zoom` &ndash; 縮放的街道地檢視全景乘數，其中 1.0 = 正常縮放，2.0 = 已縮放的 2 x 3.0 = 縮小的 4 x，等等。

-   `mz` &ndash; 要從 [街道] 檢視的對應應用程式時，會使用地圖縮放層級。


使用內建對應應用程式，或 [街道] 檢視可輕鬆快速地加入對應的支援。 不過，Android 的地圖服務 API 提供更細微地控制對應體驗。
