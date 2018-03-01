---
title: "Android 損耗"
description: "建置 Android 穿戴裝置的應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1dad5e859efdf69e7003b45724f718b16faffd62
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="android-wear"></a>Android 損耗

## <a name="android-wear"></a>Android 損耗

Android 損耗是 android 的可供穿戴的裝置，例如智慧型的監看版本。 本節包含有關如何安裝和設定工具損耗程式開發，建立第一個損耗裝置，以及一份範例，您可以參考建立您自己有應用程式的逐步解說所需的指示。

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[快速入門](~/android/wear/get-started/index.md)

介紹 Android 穿、 描述如何安裝及設定您的電腦進行損耗開發，並提供可協助您建立並執行第一個 Android 戴上應用程式在模擬器或損耗裝置上的步驟。

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[使用者介面](~/android/wear/user-interface/index.md)

說明 Android 損耗特定控制項，並提供範例示範如何使用這些控制項的連結。

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[平台功能](~/android/wear/platform/index.md)

本節中的文件涵蓋 Android 戴上的特定功能。 您可以在這裡找到的主題說明如何建立 WatchFace。

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[螢幕大小](~/android/wear/screen-sizes.md)

預覽並最佳化您的使用者介面，可用的螢幕大小。

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[部署和測試](~/android/wear/deploy-test/index.md)

說明如何將穿 Android 應用程式部署戴上的 Android 裝置或設定損耗的 Android 模擬器。 它也包含偵錯秘訣及如何設定開發電腦和 Android 裝置之間的藍芽連線資訊。


<a name="Samples" />

## <a name="samples"></a>範例

您可以找到數[範例](https://developer.xamarin.com/samples/android/Android%20Wear/)使用 Android 戴上 (或直接移至[github](https://github.com/xamarin/monodroid-samples/tree/master/wear))。 

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <thead>
      <th>
          <strong>範例</strong>
      </th>
      <th>
          <strong>描述</strong>
      </th>
      <th>
          <strong>螢幕擷取畫面</strong>
      </th>
  </thead>
  <tbody>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/SkeletonWear/">SkeletonWear</a>
      </td>
      <td valign="top">
簡單的範例包括 GridViewPager 和互動式通知穿戴專案的基本概念。
      </td>
      <td>
          <img src="Images/skeleton.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/WatchViewStub/">WatchViewStub</a>
      </td>
      <td valign="top">
偵測到畫面的圖形，並自動載入正確的版面配置的 WatchViewStub 控制項的簡單示範。
請參閱 WatchViewStub 中的運作方式<b>Resources/layout/main_actvity.xml</b>版面配置。
      </td>
      <td>
          <img src="Images/watchview.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/RecipeAssistant/">RecipeAssistant</a>
      </td>
      <td valign="top">
示範的損耗通知頁面，配方步驟的形式。 通知建立在<b>RecipeService.cs</b>。
      </td>
      <td>
          <img src="Images/recipeassist.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/ElizaChat/">ElizaChat</a>
      </td>
      <td valign="top">
有趣的互動 」 個人助理 」 範例會呼叫 Eliza，若要建立使用傳達的回應的交談使用損耗互動式通知。
      </td>
      <td>
          <img src="Images/eliza.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/GridViewPager/">GridViewPager</a>
      </td>
      <td valign="top">
GridViewPager 實作的 2D 瀏覽模式，其中使用者 swipes 垂直和水平擴展至瀏覽選項和內容。
      </td>
      <td>
          <img src="Images/gridviewpager.png" class="tableimg">
      </td>
  </tr>
  <tr>
      <td valign="top">
          <a href="https://developer.xamarin.com/samples/monodroid/wear/WatchFace">WatchFace</a>
      </td>
      <td valign="top">
          <b>WatchFace</b>是類比樣式小時、 分鐘、 與第二個指針的自訂監看式字體。 這個範例會示範如何建立監看式朝服務繪製目前的時間和環境控制代碼的模式和可見度變更事件。 它包含廣播收件者，它可接聽時區變更，並會自動隨之更新的時間。
      </td>
      <td>
          <img src="Images/watchface.png" class="tableimg">
      </td>
  </tr>
  </tbody>
</table>

##  <a name="videos"></a>視訊

簽出這些視訊連結，討論 Xamarin.Android 損耗的支援。

<table align="center" border="0" cellpadding="1" cellspacing="1">
    <tr>
        <td>
        <a href="http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/"><img src="Images/video-android-l.png" border="0"/ /></td>
        <td><a href="http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/">Android L 等等</a>
        <br />
Android L Developer Preview 導入的新應用程式開發介面，讓開發人員充分利用，包括材料設計、 通知和新的動畫，等等。</td>
    </tr>
    <tr>
        <td>
        <a href="https://www.youtube.com/watch?v=80H8tXByZQc"><img src="Images/video-eyes-ears.png" border="0" / /></td>
        <td><a href="https://www.youtube.com/watch?v=80H8tXByZQc">C# 中是在消息和我的眼睛： Google 玻璃和 Android 戴上</a>
        <br />
穿戴計算看起來像是項目從以後的將來 （或偵測器小工具時段），但有許多人都已採用此未來今天 ！ C# 開發人員知道這項資訊，並已經有工具和技術來操控穿戴 （從 Evolve 2014) 的裝置。</td>
    </tr>
    <tr>
        <td>
        <a href="https://www.youtube.com/watch?v=Gpqc2XZIQfU"><img src="Images/video-whats-new.png" border="0" / /></td>
        <td><a href="https://www.youtube.com/watch?v=Gpqc2XZIQfU">Xamarin.Android 中最新消息</a>
        <br />
        <i>Android L、 Android 損耗、 Android 的電視、 Android 自動、 材料設計和圖案。這代表什麼您身為 Xamarin 開發人員？</i>從發展 2014年。</td>
    </tr>
</table>


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
