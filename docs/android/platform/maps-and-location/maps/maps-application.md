---
title: 啟動 Maps 應用程式
description: 如何從您的 Xamarin Android 應用程式啟動內建的 Maps 應用程式。
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: b436ea4f5d8c70f0ca641bea72d1230fc07c5086
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523777"
---
# <a name="launching-the-maps-application"></a>啟動 Maps 應用程式

在 Xamarin 中使用對應的最簡單方式是利用內建的地圖應用程式, 如下所示:

[![內建 Google Maps 應用程式的範例螢幕擷取畫面](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

當您使用 maps 應用程式時, 對應將不會是應用程式的一部分。 相反地, 您的應用程式會啟動地圖應用程式, 並在外部載入對應。 下一節將探討如何使用 Xamarin 來啟動對應, 如上所示。


## <a name="creating-the-intent"></a>建立意圖

使用 maps 應用程式就像使用適當的 URI 建立意圖、將動作設定為 ActionView, 以及呼叫 StartActivity 方法一樣簡單。 例如, 下列程式碼會啟動以指定緯度和經度為中心的地圖應用程式:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

此程式碼就是啟動上一個螢幕擷取畫面中所示的地圖所需的一切。 除了指定緯度和經度以外, maps 的 URI 配置也支援其他幾個選項。


## <a name="geo-uri-scheme"></a>地理 URI 配置

上述程式碼使用地理位置配置來建立 URI。 此 URI 配置支援數種格式, 如下所示:

- `geo:latitude,longitude`&ndash;開啟以 lat/lon 為中心的地圖應用程式。 

- `geo:latitude,longitude?z=zoom`&ndash;開啟以 lat/lon 為中心的地圖應用程式, 並將其縮放至指定的層級。 縮放層級的範圍可以從1到 23:1顯示整個地球, 23 是最接近的縮放層級。

- `geo:0,0?q=my+street+address`&ndash;開啟 [maps] 應用程式至街道位址的位置。 

- `geo:0,0?q=business+near+city`&ndash;開啟 maps 應用程式, 並顯示標注的搜尋結果。 


接受查詢的 URI 版本 (也就是街道位址或搜尋詞彙) 會使用 Google 的 geocoder 服務來抓取顯示在地圖上的位置。 例如, URI `geo:0,0?q=coop+Cambridge`會產生對應, 如下所示:

[![顯示具有搜尋詞彙的 Google Maps 的範例螢幕擷取畫面](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



如需地理 URI 配置的詳細資訊, 請參閱在[地圖上顯示位置](https://developer.android.com/guide/components/intents-common.html#Maps)。


## <a name="street-view"></a>街道視圖

除了地區配置以外, Android 也支援從意圖載入街道視圖。 從 Xamarin 啟動的街道視圖應用程式範例如下所示:

[![街道視圖的範例螢幕擷取畫面](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

若要啟動街道視圖, 只要使用`google.streetview` URI 配置, 如下列程式碼所示:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

上述使用的 streetview URI 配置採用下列形式:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

如您所見, 有數個支援的參數, 如下所示:

- `lat`&ndash;要在 [街道] 視圖中顯示之位置的緯度。

- `lng`&ndash;要在街道視圖中顯示之位置的經度。

- `pitch`&ndash;街道視圖全景的角度, 以度為單位, 其中90度為正向下,-90 度為直線。

- `yaw`&ndash;街道視圖全景的中心, 以順時針的角度從北部測量。

- `zoom`&ndash;街道視圖全景的縮放乘數, 其中 1.0 = 一般縮放、2.0 = 縮小2、3.0 = 縮放4x 等等。

- `mz`&ndash;從街道視圖前往地圖應用程式時, 將使用的地圖縮放層級。


使用內建地圖應用程式或街道視圖, 是快速新增對應支援的簡單方式。 不過, Android 的地圖服務 API 可讓您更精確地控制對應體驗。
