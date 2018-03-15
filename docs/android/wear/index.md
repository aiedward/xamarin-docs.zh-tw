---
title: Android Wear
description: "建置 Android 穿戴裝置的應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: 5db4c735205753810466c26535ba9e2f525709a8
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="android-wear"></a>Android Wear

Android 損耗是 android 的可供穿戴的裝置，例如智慧型的監看版本。 本節包含有關如何安裝和設定工具損耗程式開發，建立第一個損耗裝置，以及一份範例，您可以參考建立您自己有應用程式的逐步解說所需的指示。

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[快速入門](~/android/wear/get-started/index.md)

介紹 Android 穿、 描述如何安裝及設定您的電腦進行損耗開發，並提供可協助您建立並執行第一個 Android 戴上應用程式在模擬器或損耗裝置上的步驟。

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[使用者介面](~/android/wear/user-interface/index.md)

說明 Android 損耗特定控制項，並提供範例示範如何使用這些控制項的連結。

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[平台功能](~/android/wear/platform/index.md)

本節中的文件涵蓋 Android 戴上的特定功能。 您可以在這裡找到的主題說明如何建立 WatchFace。

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[螢幕大小](~/android/wear/screen-sizes.md)

預覽並最佳化您的使用者介面，可用的螢幕大小。

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[部署與測試](~/android/wear/deploy-test/index.md)

說明如何將穿 Android 應用程式部署戴上的 Android 裝置或設定損耗的 Android 模擬器。 它也包含偵錯秘訣及如何設定開發電腦和 Android 裝置之間的藍芽連線資訊。



## <a name="samples"></a>範例

您可以找到數[範例](https://developer.xamarin.com/samples/android/Android%20Wear/)使用 Android 戴上 (或直接移至[github](https://github.com/xamarin/monodroid-samples/tree/master/wear))。 

|範例|描述|螢幕擷取畫面|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/SkeletonWear/)|簡單的範例包括 GridViewPager 和互動式通知穿戴專案的基本概念。|![Skeletonwear 的螢幕擷取畫面](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)|偵測到畫面的圖形，並自動載入正確的版面配置的 WatchViewStub 控制項的簡單示範。  請參閱 WatchViewStub 中的運作方式**Resources/layout/main_actvity.xml**版面配置。|![WatchViewStub 的螢幕擷取畫面](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/RecipeAssistant/)|示範的損耗通知頁面，配方步驟的形式。 通知被建立在 RecipeService.cs。|![RecipeAssistant 的螢幕擷取畫面](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/ElizaChat/)|有趣的互動 」 個人助理 」 範例會呼叫 Eliza，若要建立使用傳達的回應的交談使用損耗互動式通知。|![ElizaChat 的螢幕擷取畫面](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)|GridViewPager 實作的 2D 瀏覽模式，其中使用者 swipes 垂直和水平擴展至瀏覽選項和內容。|![GridViewPager 的螢幕擷取畫面](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace 是類比樣式小時、 分鐘、 與第二個指針的自訂監看式字體。 這個範例會示範如何建立監看式朝服務繪製目前的時間和環境控制代碼的模式和可見度變更事件。 它包含廣播收件者，它可接聽時區變更，並會自動隨之更新的時間。|![WatchFace 的螢幕擷取畫面](images/gridviewpager.png)|


##  <a name="videos"></a>視訊

簽出這些視訊連結，討論 Xamarin.Android 損耗的支援：

|描述|螢幕擷取畫面|
|--- |--- |
|[Android L 和更](http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; Android L Developer Preview 導入的新應用程式開發介面，讓開發人員充分利用，包括材料設計、 通知和新的動畫，等等。|![視訊簡報螢幕擷取畫面](images/video-android-l.png)|
|[C# 中是在消息和我的眼睛： Google 玻璃和 Android 穿](https://www.youtube.com/watch?v=80H8tXByZQc)&ndash;穿戴計算看起來像是項目從以後的將來 （或偵測器小工具時段），但有許多人都已採用此未來今天 ！ C# 開發人員知道這項資訊，並已經有工具和技術來操控穿戴 （從 Evolve 2014) 的裝置。|![視訊簡報螢幕擷取畫面](images/video-eyes-ears.png)|
|[新功能 Xamarin.Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; Android L、 Android 穿、 Android 的電視、 Android 自動、 材料設計和圖片; 用途這 Xamarin 開發人員為平均？ 從 Evolve 2014。|![視訊簡報螢幕擷取畫面](Images/video-whats-new.png)|


<!--

March 18
http://blog.xamarin.com/android-wear/

August 14
http://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
http://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
