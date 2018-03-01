---
title: "對應應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/05/2017
ms.openlocfilehash: a6bfebb5272da3fd50f4f165fc25bb75574a0b63
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="maps-application"></a>對應應用程式

將對應中 Xamarin.Android 最簡單的方式是利用內建的對應應用程式，如下所示：

[![內建 Google 地圖應用程式的範例螢幕擷取畫面](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png)

當您使用對應的應用程式時，對應不會應用程式的一部分。 相反地，您的應用程式會啟動對應的應用程式，並載入外部的地圖。 下一節探討如何使用 Xamarin.Android 啟動如同上面的對應。

<a name="Creating_the_Intent" />

## <a name="creating-the-intent"></a>建立目的

使用對應應用程式是與用途建立具有適當的 URI，將動作設定為 ActionView，以及呼叫 StartActivity 方法一樣容易。 例如，下列程式碼啟動置中在給定的緯度和經度的對應應用程式：

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

此程式碼是只需要啟動上一個螢幕擷取畫面所示的對應。 除了指定緯度和經度，對應的 URI 配置支援數個其他選項。

<a name="Geo_Uri_Scheme" />

## <a name="geo-uri-scheme"></a>地理 URI 配置

上述程式碼會使用地理配置來建立 URI。 此 URI 配置支援數種格式，如下所示：

-   `geo:latitude,longitude` &ndash; 開啟對應應用程式在 lat/lon 置中。 

-   `geo:latitude,longitude?z=zoom` &ndash; 開啟應用程式在 lat/lon 置中與縮放比例，以指定的層級的對應。 縮放層級範圍從 1 到 23: 1 顯示整個地球，23 是最接近的縮放層級。

-   `geo:0,0?q=my+street+address` &ndash; 開啟對應應用程式位置的街道地址。 

-   `geo:0,0?q=business+near+city` &ndash; 會開啟對應的應用程式，並顯示註解式的搜尋結果。 


URI 的版本採取查詢 （也就是街道地址或搜尋詞彙） 使用 Google geocoder 服務擷取接著會顯示在地圖的位置。 例如，URI`geo:0,0?q=coop+Cambridge`產生的對應如下所示：

[![顯示與搜尋詞彙的 Google 地圖的範例螢幕擷取畫面](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png)


<a name="Street_View" />

如需地理 URI 配置的詳細資訊，請參閱[map 上顯示位置](http://developer.android.com/guide/components/intents-common.html#Maps)。


## <a name="street-view"></a>街道地檢視

除了異地配置中，Android 也支援從意圖載入街道地檢視。 從 Xamarin.Android 啟動街道地檢視應用程式的範例如下所示：

[![街道地檢視的範例螢幕擷取畫面](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png)

若要啟動的街道地檢視，只要使用`google.streetview`URI 配置，如下列程式碼所示：

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

使用上述的 google.streetview URI 配置會有下列形式：

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

如您所見，有數個參數的支援，如下所示：

-   `lat` &ndash; [街道] 檢視中顯示位置的緯度。

-   `lng` &ndash; [街道] 檢視中顯示位置的經度。

-   `pitch` &ndash; 街道地檢視全景，起的中心所在直向 90 度的度和-90 度的角度已直接啟動。

-   `yaw` &ndash; 的-的置中檢視街道地檢視全景會以度數為單位，從 North 順時針旋轉。

-   `zoom` &ndash; 縮放的街道地檢視全景乘數，其中 1.0 = 正常顯示比例、 2.0 = 放大的 2 x 3.0 = 放大的 4 x 等。

-   `mz` &ndash; 移至對應的應用程式，從 [街道] 檢視時，將會使用地圖縮放層級。


使用內建對應應用程式或街道地檢視輕鬆快速地加入對應的支援。 不過，Android 的 Maps API 提供更細微地控制對應體驗。
