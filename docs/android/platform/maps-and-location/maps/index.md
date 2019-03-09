---
title: 如何搭配 Xamarin.Android 使用 Google 地圖與位置
description: 這篇文章討論如何使用 Xamarin.Android 的地圖與位置。 它涵蓋了從運用內建的地圖應用程式直接使用 Google 對應 Android API V2 的所有項目。
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
---

# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>如何搭配 Xamarin.Android 使用 Google 地圖與位置

_這篇文章討論如何使用 Xamarin.Android 的地圖與位置。它涵蓋了從運用內建的地圖應用程式直接使用 Google 對應 Android API V2 的所有項目。_

## <a name="maps-overview"></a>對應概觀

行動裝置的通用補充所對應技術。 桌上型電腦和膝上型電腦不容易有內建的定位知悉。 相反地，行動裝置使用這類應用程式，找出裝置，並顯示變更的位置資訊。 Android 提供功能強大的內建技術，使用可在裝置的位置硬體的 map 上顯示位置資料。 本文章涵蓋各種 Xamarin.Android 中對應的應用程式有提供，包括： 

-  使用內建地圖服務快速地加入對應功能的應用程式。
-  使用地圖服務 API，來控制地圖顯示。
-  您可以使用各種技術，新增圖形化的重疊。

在本節中的主題涵蓋了廣泛的對應功能。
首先，這些主題會說明如何利用 Android 的內建的地圖應用程式以及如何顯示全景的街道地檢視的位置。 然後他們會討論如何使用地圖服務 API 將直接在應用程式，涵蓋這兩個控制項的位置和地圖，顯示的方式內的對應功能，以及如何新增圖形化的重疊影像。


## <a name="related-links"></a>相關連結

- [MapsAndLocationDemo_v3 (sample)](https://developer.xamarin.com/samples/monodroid/MapsAndLocationDemo_v3/)
- [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Intents 清單：叫用 Android 裝置上的 Google 應用程式](https://developer.android.com/guide/appendix/g-app-intents.html)
- [位置與地圖](https://developer.android.com/guide/topics/location/index.html)
