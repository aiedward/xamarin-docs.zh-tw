---
title: Android Wear
description: 建立 Android 穿戴式裝置的應用程式。
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/16/2018
ms.openlocfilehash: 5768a8fb402304d399e619ddb111aea24d975daf
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758323"
---
# <a name="android-wear"></a>Android Wear

Android 磨損是針對穿戴式裝置（例如智慧型監看）所設計的 Android 版本。 本節包含有關如何安裝和設定磨損開發所需工具的指示、建立第一個磨損裝置的逐步解說，以及您可以參考以建立自己的磨損應用程式的範例清單。

## <a name="getting-startedandroidwearget-startedindexmd"></a>[快速入門](~/android/wear/get-started/index.md)

引進了 Android 磨損，說明如何安裝和設定您的電腦以進行磨損開發，並提供可協助您在模擬器或磨損裝置上建立及執行第一個 Android 磨損應用程式的步驟。

## <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[使用者介面](~/android/wear/user-interface/index.md)

說明 Android 磨損特定控制項，並提供示範如何使用這些控制項的範例連結。

## <a name="platform-featuresandroidwearplatformindexmd"></a>[平台功能](~/android/wear/platform/index.md)

本節中的檔涵蓋 Android 磨損特有的功能。 您會在這裡找到一個主題，說明如何建立 WatchFace。

## <a name="screen-sizesandroidwearscreen-sizesmd"></a>[螢幕大小](~/android/wear/screen-sizes.md)

針對可用的螢幕大小，預覽並優化您的使用者介面。

## <a name="deployment--testingandroidweardeploy-testindexmd"></a>[部署與測試](~/android/wear/deploy-test/index.md)

說明如何將您的 Android 磨損應用程式部署到 Android 磨損裝置，或設定為磨損的 Android 模擬器。 其中也包含了偵錯工具的秘訣，以及如何在您的開發電腦與 Android 裝置之間設定藍牙連線的資訊。

## <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[磨損 Api](https://developer.android.com/reference/android/support/wearable)

Android 開發人員網站提供有關金鑰磨損 Api 的詳細資訊，例如[穿戴式活動](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html)、[意圖](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html)、[驗證](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html)、[複雜](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html)、[複雜](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html)的轉譯、[通知](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html)、 [Views](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)和[WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html)。

## <a name="samples"></a>範例

您可以使用 Android 磨損（或直接前往[github](https://github.com/xamarin/monodroid-samples/tree/master/wear)）來尋找一些[範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android+wear)。

|範例|描述|螢幕擷取畫面|
|--- |--- |--- |
|[SkeletonWear](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)|穿戴式專案基本概念的簡單範例，包括 GridViewPager 和互動式通知。|![Skeletonwear 的螢幕擷取畫面](images/skeleton.png)|
|[WatchViewStub](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchviewstub)|WatchViewStub 控制項的簡單示範，它會偵測螢幕圖形並自動載入正確的版面配置。 查看 WatchViewStub 在**Resources/layout/main_activity**配置中的運作方式。|![WatchViewStub 的螢幕擷取畫面](images/watchview.png)|
|[RecipeAssistant](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-recipeassistant)|以配方步驟的形式示範磨損通知頁面。 通知是在 RecipeService.cs 中建立的。|![RecipeAssistant 的螢幕擷取畫面](images/recipeassist.png)|
|[ElizaChat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-elizachat)|與名為 Eliza 的「個人助理」互動的有趣範例，其使用磨損互動式通知來建立使用已加入回應的交談。|![ElizaChat 的螢幕擷取畫面](images/eliza.png)|
|[GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)|GridViewPager 會實作為2D 導覽模式，讓使用者以垂直方式撥動，然後水準導覽以流覽選項和內容。|![GridViewPager 的螢幕擷取畫面](images/gridviewpager.png)|
|[WatchFace](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchface)|WatchFace 是一種自訂監看面，其中包含類比樣式的小時、分鐘和第二次手。 這個範例會示範如何建立可繪製目前時間並處理環境模式和可見度變更事件的監看式臉部服務。 它包含接聽時區變更的廣播接收器，並會依適當情況自動更新時間。|![WatchFace 的螢幕擷取畫面](images/gridviewpager.png)|

## <a name="videos"></a>視訊

請查看下列影片連結，其中討論具有磨損支援的 Xamarin. Android：

|描述|螢幕擷取畫面|
|--- |--- |
|[Android L，還有更多](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/)&ndash; Android L 開發人員預覽版引進了新的 api 眾多，可供開發人員利用，包括材質設計、通知和新的動畫等等。|![簡報的影片螢幕擷取畫面](images/video-android-l.png)|
|[C#在我的耳和我的眼睛中：Google 玻璃和 Android 磨損](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash;穿戴式運算看起來可能像是未來的東西（或偵測器小工具），但許多人現在都已經在採用未來！ C#開發人員知道這一點，而且已經有充分利用穿戴式裝置功能的工具和技能（從演進2014開始）。|![簡報的影片螢幕擷取畫面](images/video-eyes-ears.png)|
|[Xamarin 的新功能](https://www.youtube.com/watch?v=Gpqc2XZIQfU)&ndash; Android L、android 磨損、android 電視、android 自動、材質設計和美工; 這對您身為 Xamarin 開發人員而言是什麼意思？從進化2014。|![簡報的影片螢幕擷取畫面](Images/video-whats-new.png)|

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
