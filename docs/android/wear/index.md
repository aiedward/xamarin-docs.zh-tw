---
title: Android Wear
description: 建置適用於 Android 的到穿戴裝置的應用程式。
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/16/2018
ms.openlocfilehash: fca72291dd726d4f2a6635d26390baa103ee0d2d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864910"
---
# <a name="android-wear"></a>Android Wear

Android Wear 是 android 的專為到穿戴裝置，例如智慧型手錶版本。 本節包含有關如何安裝及設定 Wear 開發，建立第一個 Wear 裝置，以及一份您可以參考來建立您自己穿戴式應用程式的範例的逐步解說所需的工具。

## <a name="getting-startedandroidwearget-startedindexmd"></a>[快速入門](~/android/wear/get-started/index.md)

介紹 Android Wear、 說明如何安裝和設定您的電腦進行 Wear 開發，並提供可協助您建立並執行第一個 Android Wear 應用程式在模擬器或在 Wear 裝置上的步驟。

## <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[使用者介面](~/android/wear/user-interface/index.md)

說明 Android Wear 特定控制項，並提供範例，示範如何使用這些控制項的連結。

## <a name="platform-featuresandroidwearplatformindexmd"></a>[平台功能](~/android/wear/platform/index.md)

在本節中的文件涵蓋 Android Wear 的特定功能。 您可以在這裡找到的主題說明如何建立 WatchFace。

## <a name="screen-sizesandroidwearscreen-sizesmd"></a>[螢幕大小](~/android/wear/screen-sizes.md)

預覽，並最佳化您的使用者介面，可用的螢幕大小。

## <a name="deployment--testingandroidweardeploy-testindexmd"></a>[部署與測試](~/android/wear/deploy-test/index.md)

說明如何將 Android Wear 應用程式部署到 Android 穿戴式裝置或至針對 Wear 的 Android 模擬器。 它也會包含偵錯秘訣和如何設定您的開發電腦與 Android 裝置的藍牙連線的資訊。

## <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[Wear Api](https://developer.android.com/reference/android/support/wearable)

Android 開發人員網站會提供這類金鑰穿戴式 Api 的詳細的資訊[活動到穿戴](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html)，[意圖](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html)，[驗證](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html)， [複雜性](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html)，[轉譯的複雜性](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html)，[通知](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html)，[檢視](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)，並[WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html)。



## <a name="samples"></a>範例

您可以找到許多[範例](https://developer.xamarin.com/samples/android/Android%20Wear/)使用 Android Wear (或直接前往[github](https://github.com/xamarin/monodroid-samples/tree/master/wear))。

|範例|說明|螢幕擷取畫面|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/monodroid/wear/SkeletonWear/)|到穿戴的專案，包括 GridViewPager 和互動式的通知的基本概念的簡單範例。|![Skeletonwear 的螢幕擷取畫面](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/monodroid/wear/WatchViewStub/)|偵測到畫面的圖形，並自動載入正確的版面配置的 WatchViewStub 控制項的簡單示範。 請參閱 WatchViewStub 中的運作方式**Resources/layout/main_activity.xml**版面配置。|![WatchViewStub 的螢幕擷取畫面](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/)|中的配方步驟形式的損耗通知頁面的示範。 通知被建立在 RecipeService.cs。|![RecipeAssistant 的螢幕擷取畫面](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/monodroid/wear/ElizaChat/)|與 「 個人助理 」 互動的有趣的範例會呼叫 Eliza，若要建立使用現成的回應的交談使用 Wear 互動式的通知。|![ElizaChat 的螢幕擷取畫面](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)|GridViewPager 實作 2D 瀏覽模式中，其中使用者 swipes 垂直和水平擴展至瀏覽選項和內容。|![Screenshot of GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace 是自訂錶面與類比樣式小時、 分鐘和第二個實際操作。 這個範例會示範如何建立監看式臉部服務繪製目前的時間和環境控制代碼的模式和可見度變更事件。 它包含廣播的接收器接聽時區的變更，並會自動隨之更新的時間。|![WatchFace 的螢幕擷取畫面](images/gridviewpager.png)|


## <a name="videos"></a>視訊

請參閱這些影片討論 Wear 使用 Xamarin.Android 連結支援：

|描述|螢幕擷取畫面|
|--- |--- |
|[Android L 及更多](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; Android L Developer Preview 導入眾多新的 Api，讓開發人員充分發揮運用，包括 Material Design、 通知和新的動畫，等等。|![簡報的影片的螢幕擷取畫面](images/video-android-l.png)|
|[C#是我的眼睛和我耳朵中：Google Glass 和 Android Wear](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash;到穿戴運算可能看起來很喜歡從未來 （或偵測器的小工具集），但許多人目前已採用未來 ！ C#開發人員知道這，而且已經有的工具和技巧，以充分利用到穿戴裝置 （從發展 2014)。|![簡報的影片的螢幕擷取畫面](images/video-eyes-ears.png)|
|[在 Xamarin.Android 中最新消息](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; Android L、 Android Wear、 Android TV、 Android Auto、 材料設計和圖案; 此為 Xamarin 開發人員呢？ 從發展 2014年。|![簡報的影片的螢幕擷取畫面](Images/video-whats-new.png)|


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
