---
title: 啟動地圖應用程式
description: 如何從 Xamarin.Android 應用程式中啟動內建地圖應用程式。
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 7b74f564f2b6e9613874a774258a7e999002e61a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027071"
---
# <a name="launching-the-maps-application"></a>啟動地圖應用程式

在 Xamarin.Android 中處理地圖的最簡單方法是利用下面所示的內建地圖應用程式:

[![內建 Google 地圖應用的範例螢幕截圖](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

使用地圖應用程式時,地圖將不是應用程式的一部分。 相反,應用程式將啟動地圖應用程式並在外部載入地圖。 下一節將探討如何使用 Xamarin.Android 來啟動上述地圖。

## <a name="creating-the-intent"></a>建立意圖

使用地圖應用程式非常簡單,只需使用適當的 URI 創建意圖、將操作設置為 ActionView 以及調用 StartActivity 方法一樣簡單。 例如,以下代碼啟動以給定緯度和經度為中心的地圖應用程式:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

此代碼是啟動上一張螢幕截圖中顯示的地圖所需的全部代碼。 除了指定緯度和經度外,地圖的URI方案還支援其他幾個選項。

## <a name="geo-uri-scheme"></a>地理 URI 專案

上述代碼使用地理方案創建URI。 此 URI 方案支援多種格式,如下所示:

- `geo:latitude,longitude`&ndash;打開以 lat/lon 為中心的地圖應用程式。 

- `geo:latitude,longitude?z=zoom`&ndash;打開以 lat/lon 為中心的地圖應用程式,然後縮放到指定級別。 縮放級別的範圍可以從 1 到 23:1 顯示整個地球,23 是最接近的縮放級別。

- `geo:0,0?q=my+street+address`&ndash;將地圖應用程式打開到街道位址的位置。 

- `geo:0,0?q=business+near+city`&ndash;打開地圖應用程式並顯示有字的搜尋結果。 

採用查詢(即街道位址或搜索詞)的URI版本使用Google的地理編碼器服務來檢索隨後顯示在地圖上的位置。 例如,URI`geo:0,0?q=coop+Cambridge`在下圖中產生:

[![顯示有搜尋詞的 Google 地圖的範例螢幕擷圖](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)

關於地理 URI 專案的詳細資訊,請參閱[在地圖上顯示位置](https://developer.android.com/guide/components/intents-common.html#Maps)。

## <a name="street-view"></a>街景

除了地理方案外,Android 還支援從意圖載入街景。 從 Xamarin.Android 啟動的街景應用程式的示例如下所示:

[![街道檢視的範例螢幕截圖](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

要啟動街景視圖,只需使用`google.streetview`URI方案,如下代碼所示:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

上面使用的 google.streetview URI 方案採用以下形式:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

如下面所示,支援多個參數:

- `lat`&ndash;要在街景中顯示的位置的緯度。

- `lng`&ndash;要在街景中顯示的位置的經度。

- `pitch`&ndash;街景全景的角度,從中心以度為單位測量,其中 90 度是直降的,-90 度是直線上升的。

- `yaw`&ndash;街景全景的視圖中心,以北度順時針測量。

- `zoom`&ndash;街景全景的縮放倍增器,其中 1.0 = 正常縮放,2.0 = 縮放 2 倍,3.0 = 縮放 4 倍等。

- `mz`&ndash;從街景到地圖應用程式時將使用的地圖縮放級別。

使用內建地圖應用程式或街景是快速添加映射支援的簡便方法。 但是,Android 的地圖 API 提供了對映射體驗的更精細的控制。
