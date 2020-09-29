---
title: Android Wear
description: 建立 Android 穿戴式裝置的應用程式。
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/16/2018
ms.openlocfilehash: 67698ae7fe3ef9a7586d83e26ed276ba473396e5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457506"
---
# <a name="android-wear"></a>Android Wear

Android 磨損是針對穿戴式裝置（例如智慧型監看）所設計的 Android 版本。 本節包含有關如何安裝和設定磨損開發所需工具的指示、建立第一個磨損裝置的逐步解說，以及您可以參考來建立您自己的磨損應用程式的範例清單。

## <a name="getting-started"></a>[快速入門](~/android/wear/get-started/index.md)

介紹 Android 磨損、說明如何安裝及設定您的電腦以進行磨損開發，並提供步驟來協助您在模擬器或磨損裝置上建立並執行第一個 Android 磨損應用程式。

## <a name="user-interface"></a>[使用者介面](~/android/wear/user-interface/index.md)

說明 Android 磨損特定控制項，並提供示範如何使用這些控制項之範例的連結。

## <a name="platform-features"></a>[平台功能](~/android/wear/platform/index.md)

本節中的檔涵蓋 Android 磨損的特定功能。 您可以在這裡找到說明如何建立 WatchFace 的主題。

## <a name="screen-sizes"></a>[螢幕大小](~/android/wear/screen-sizes.md)

預覽並優化您的使用者介面，以取得可用的螢幕大小。

## <a name="deployment--testing"></a>[部署與測試](~/android/wear/deploy-test/index.md)

說明如何將 Android 磨損應用程式部署至 Android 磨損裝置，或部署至設定為磨損的 Android 模擬器。 它也包含偵錯工具提示，以及如何在您的開發電腦與 Android 裝置之間設定藍牙連線的資訊。

## <a name="wear-apis"></a>[磨損 Api](https://developer.android.com/reference/android/support/wearable)

Android 開發人員網站提供有關主要磨損 Api 的詳細資訊，例如 [穿戴式活動](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html)、 [意圖](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html)、 [驗證](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html)、 [複雜](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html)、 [複雜](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html)轉譯、 [通知](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html)、 [查看](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)和 [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html)。

## <a name="samples"></a>範例

您可以使用 Android 磨損 (找到一些 [範例](/samples/browse/?products=xamarin&term=Xamarin.Android%2bwear) ，或直接前往 [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)) 。

|範例|描述|螢幕擷取畫面|
|--- |--- |--- |
|[SkeletonWear](/samples/xamarin/monodroid-samples/wear-skeletonwear)|穿戴式專案基本概念的簡單範例，包括 GridViewPager 和互動式通知。|![Skeletonwear 的螢幕擷取畫面](images/skeleton.png)|
|[WatchViewStub](/samples/xamarin/monodroid-samples/wear-watchviewstub)|WatchViewStub 控制項的簡單示範，可偵測螢幕圖形並自動載入正確的版面配置。 查看 WatchViewStub 在 **資源/版面配置/main_activity.xml** 配置中的運作方式。|![WatchViewStub 的螢幕擷取畫面](images/watchview.png)|
|[RecipeAssistant](/samples/xamarin/monodroid-samples/wear-recipeassistant)|「磨損通知」頁面的示範，以配方步驟的形式呈現。 通知會在 RecipeService.cs 中建立。|![RecipeAssistant 的螢幕擷取畫面](images/recipeassist.png)|
|[ElizaChat](/samples/xamarin/monodroid-samples/wear-elizachat)|與名為 Eliza 的「個人助理」互動的有趣範例，使用磨損互動式通知來建立使用預先發出回應的交談。|![ElizaChat 的螢幕擷取畫面](images/eliza.png)|
|[GridViewPager](/samples/xamarin/monodroid-samples/wear-gridviewpager)|GridViewPager 會實作為2D 導覽模式，使用者可在此模式中垂直撥動，然後以水準方式流覽選項和內容。|![GridViewPager 的螢幕擷取畫面](images/gridviewpager.png)|
|[WatchFace](/samples/xamarin/monodroid-samples/wear-watchface)|WatchFace 是一種自訂的監看式臉部，具有類比樣式的小時、分鐘和第二個手。 這個範例示範如何建立監看臉部服務，以繪製目前的時間，並處理環境模式和可見度變更事件。 它包含接聽時區變更的廣播接收器，並會自動更新時間。|![WatchFace 的螢幕擷取畫面](images/gridviewpager.png)|

## <a name="videos"></a>影片

查看這些與支援磨損的 Xamarin 相關的影片連結：

|描述|螢幕擷取畫面|
|--- |--- |
|[Android L 和其他更多](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; Android L 開發人員預覽引進了新 Api 的眾多，讓開發人員可以利用，包括材質設計、通知和新的動畫等等。|![簡報的影片螢幕擷取畫面](images/video-android-l.png)|
|[C # 位於我的耳中，並在我的眼睛中： Google 玻璃和 Android 磨損](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; 穿戴式運算看起來可能像未來的 (或偵測工具小工具集) ，但許多人現在都已在使用未來了！ C # 開發人員知道這一點，而且已經有工具和技能可充分利用穿戴式裝置的強大功能， (從 2014) 演進。|![簡報的影片螢幕擷取畫面](images/video-eyes-ears.png)|
|Xamarin 的新[功能](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash;Android L、Android 磨損、Android 電視、Android 自動、材質設計和藝術;這對您是 Xamarin 開發人員來說是什麼意思？從演進2014。|![簡報的影片螢幕擷取畫面](Images/video-whats-new.png)|

<!--

March 18
https://blog.xamarin.com/android-wear/

August 14
https://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
https://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->