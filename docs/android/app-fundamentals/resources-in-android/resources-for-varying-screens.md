---
title: 針對不同螢幕建立資源
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: 63cbe556783ffe22512ff5312817d522120bd15e
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670567"
---
# <a name="creating-resources-for-varying-screens"></a>建立針對不同螢幕的資源

Android 本身上執行許多不同的裝置，有各種不同的解析度、 螢幕大小和螢幕密度。 Android 將會執行縮放和調整大小以進行您在這些裝置上運作的應用程式，但這可能會導致次佳的使用者體驗。 例如，映像可能會顯示為模糊的或它們可能會如預期般在檢視上放置。


## <a name="concepts"></a>概念

少數的詞彙和概念是一定要了解以支援多個畫面。

- **螢幕大小**&ndash;顯示您的應用程式的實體空間量

- **螢幕密度**&ndash;在螢幕上任何指定的區域中的像素數目。 典型的測量單位為每英吋點數 (dpi)。

- **解析度**&ndash;在螢幕上的像素為單位的總數。 當開發應用程式，解析不與螢幕大小和密度一樣重要。

- **密度獨立像素 (dp)** &ndash;虛擬度量單位，以允許版面配置，以設計為獨立的密度。 此公式用來將 dp 轉換成螢幕像素：

    像素&equals;dp &times; dpi &divide; 160

- **方向** &ndash; 當屏幕的寬度高於高度時，屏幕的方向被認為是橫向的。 相反地，直式方向時，螢幕的高度大於寬度。 在使用者旋轉裝置方向可以變更應用程式的存留期間。

請注意，這些概念的前三個彼此相關之&ndash;沒有增加密度會提高系統的螢幕大小增加的解析度。 不過如果增加密度和解析度，然後螢幕大小可維持不變。 螢幕大小、 密度和解析度之間的關係會快速地使螢幕支援。

為了處理這種複雜性，Android 架構使用的慣用*密度無關的像素 (dp)* 螢幕配置。 藉由使用密度無關的像素，使用者在使用不同密度的螢幕上具有相同的實體大小會出現 UI 項目。


## <a name="supporting-various-screen-sizes-and-densities"></a>支援各種螢幕大小和密度

Android 會處理大部分的工作來呈現正確的每個畫面設定配置。 不過，有一些可以幫助系統採取的動作。

在大部分情況下，以確保密度獨立性的密度獨立像素，而不是實際的像素版面配置中使用不足。
Android 會調整的可繪製資源來為適當大小的執行階段。
不過，就可以調整會導致看起來是模糊的點陣圖。 若要解決此問題，請提供不同密度的其他資源。 當多個解析度和螢幕密度的設計裝置，能更容易開始使用較高的解析度或密度映像，再相應減少而變更。


### <a name="declare-the-supported-screen-size"></a>宣告支援的螢幕大小

宣告的螢幕大小，可確保只有受支援的裝置可以下載應用程式。 這可以藉由設定[支援畫面](https://developer.android.com/guide/topics/manifest/supports-screens-element.html)中的項目**AndroidManifest.xml**檔案。 這個項目用來指定應用程式支援哪些螢幕大小。 如果應用程式可以正確地放置其版面配置，以填滿螢幕可支援屬於指定的螢幕。 藉由使用此資訊清單的項目，應用程式將不會顯示在[ *Google Play* ](https://play.google.com/)螢幕規格不符合的裝置。 不過，應用程式仍會使用不支援畫面中，在裝置上執行，但配置可能會出現模糊並不美觀。

宣告支援的畫面 sixes **Properites/AndroidManifest.xml**解決方案檔：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Android 資訊清單](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![Android 資訊清單](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

編輯**AndroidManifest.xml**包含[支援畫面](https://developer.android.com/guide/topics/manifest/supports-screens-element.html):

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="HelloWorld.HelloWorld">
      <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="27" />
      <supports-screens android:resizable="true"
                        android:smallScreens="true"
                        android:normalScreens="true"
                        android:largeScreens="true" />
      <application android:allowBackup="true"
                   android:icon="@mipmap/ic_launcher"
                   android:label="@string/app_name"
                   android:roundIcon="@mipmap/ic_launcher_round"
                   android:supportsRtl="true" android:theme="@style/AppTheme">
  </application>
</manifest>
```

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>提供了替代的版面配置的不同螢幕大小


替代的版面配置可使您能夠自訂特定螢幕大小、 變更的位置或元件的 UI 項目大小的檢視。

從 API 層級 13 (Android 3.2)，螢幕大小已取代為使用 sw*N*dp 限定詞。 這個新限定詞宣告必須指定配置的空間數量。 建議要在 Android 3.2 或更新版本執行的應用程式應該使用這些較新的限定詞。

例如，如果配置所需的最小 700 dp 螢幕寬度時，替代的版面配置會放置在資料夾**版面配置 sw700dp**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![700 dp 螢幕寬度的版面配置資料夾](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![700 dp 螢幕寬度的版面配置資料夾](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


一般來說，以下是一些針對各種裝置的數字：

- **典型的電話** &ndash; 320 dp： 典型的電話

- **5 個 「 tablet /"tweener 」 裝置** &ndash; 480 dp： 例如 Samsung 附註

- **7 」 tablet** &ndash; 600 dp： 例如 Barnes &amp; Noble Nook

- **10 個 「 tablet** &ndash; 720 dp： 例如 Motorola Xoom

應用程式的目標 API 層級最多 12 (Android 3.1)，配置都應在目錄使用限定詞**小型**/**正常**/**大型** / **xlarge （超大型)** 為技中大部分的裝置所提供的各種畫面大小。 例如，在下圖中，有四個不同的螢幕大小的其他資源：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![四個螢幕大小的其他資源](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![四個螢幕大小的其他資源](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

以下是較舊的 pre-API 層級 13 螢幕大小限定詞為密度獨立像素的比較方式的比較：

- 426 x 320 dp 的 dp**小**

- 470 x 320 dp 的 dp**正常**

- 640 x 480 dp 的 dp**大型**

- 960 x 720 dp 的 dp **xlarge （超大型)**

較新的畫面大小在 API 層級 13 中的限定詞，並設定的優先順序高於 API 層級 12 和較低的較舊的畫面限定詞。
對於橫跨舊和新的 API 層級的應用程式，可能必須建立替代的資源使用這兩組限定詞，如下列螢幕擷取畫面所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![以這兩個限定詞的替代資源](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![以這兩個限定詞的替代資源](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----



### <a name="provide-different-bitmaps-for-different-screen-densities"></a>提供針對不同螢幕密度的不同點陣圖

雖然 Android 會縮放點陣圖，視您的裝置，但本身的點陣圖可能不誇張地相應增加或減少： 他們可能會變成模糊或模糊。 提供適用於螢幕密度的點陣圖，將會減輕這個問題。

比方說下, 圖是範例中的配置和外觀可能會發生的問題時密度-指定資源不會提供。

![不含密度資源的螢幕擷取畫面](resources-for-varying-screens-images/06-density-not-provided.png)

比較此密度專屬的資源設計為版面配置：

![螢幕擷取畫面與密度特有資源](resources-for-varying-screens-images/07-density-specific-resources.png)


### <a name="create-varying-density-resources-with-android-asset-studio"></a>Android Asset Studio 是建立各種不同密度資源

建立這些點陣圖的不同密度可以是有點繁瑣。 Google 已在此情況下，建立線上的公用程式，可以減少部分涉及呼叫這些點陣圖建立了冗長[ **Android Asset Studio**](https://romannurik.github.io/AndroidAssetStudio/)。

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

此網站可協助建立目標提供一個映像的四個常見的螢幕密度的點陣圖。 Android Asset Studio 將會再建立的自訂設定的點陣圖，並可讓使用者下載為 zip 檔案。


## <a name="tips-for-multiple-screens"></a>多個畫面的秘訣

Android 上執行會令人困擾的裝置數目，而螢幕大小和螢幕密度的組合可以令人望而生畏。 下列秘訣可協助您支援各種裝置所需的投入時間降至最低：

- **只設計和開發適用於您的需要**&ndash;有許多不同的裝置，但某些罕見的板型規格，可能需要更多心力來設計和開發的存在。 [**螢幕大小和密度**](https://developer.android.com/resources/dashboard/screens.html)儀表板是由提供資料的螢幕大小/螢幕密度矩陣分解的 Google 提供的頁面。 這次的中斷會提供有關如何開發工作上支援畫面的深入解析。

- **使用 DPs，而不是像素**-像素變得棘手螢幕密度的變更。 請勿像素值。 避免改用 dp （密度獨立像素為單位） 的像素為單位。

- **避免** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **隨時隨地可能**&ndash;它已被取代的 API 層級 3 (Android 1.5)，並會導致很容易損毀的版面配置。 應該不會使用它。 因此，請使用類似如下的更有彈性的配置 widget [ **LinearLayout**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)， [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)，或新[**GridLayout**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/)。

- **挑選其中一個版面配置方向，為您的預設值**&ndash;比方說，而不是提供替代資源**版面配置 land**並**配置連接埠**，放置的資源在橫向**版面配置**，並針對至直向資源**版面配置連接埠**。

- **用於高度及寬度的 LayoutParams** -XML 版面配置檔案中定義的 UI 項目時的 Android 應用程式使用**wrap_content**並**fill_parent**值將會擁有更多成功案例跨不同的裝置，比使用像素或密度獨立單位，請確定適當的外觀。 這些維度值會導致 Android 可以適當地調整點陣圖資源。 基於這個相同的理由，密度無關的單位是最適合保留的時指定的邊界和填補的 UI 項目。


## <a name="testing-multiple-screens"></a>測試多個畫面

Android 應用程式必須針對將支援的所有組態進行測試。 在理想情況下應該在實際裝置本身上測試裝置，但在許多情況下這並不可行或切合實際。
在此情況下，使用 [模擬器] 和 Android 虛擬裝置的安裝程式針對每個裝置組態很有用。

Android SDK 提供某些模擬器面板可能會用來建立 Avd 將複寫的大小、 密度和解決許多裝置。
同樣地，許多硬體廠商會提供面板為他們的裝置。

另一個選項是使用協力廠商測試服務的服務。
這些服務將採取 APK，在許多不同的裝置上執行它，然後提供應用程式的運作方式的意見反應。
