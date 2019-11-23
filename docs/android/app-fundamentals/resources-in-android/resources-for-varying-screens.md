---
title: 針對不同螢幕建立資源
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/28/2018
ms.openlocfilehash: cbd392dcae173eb3baf0fb8f0c3c4ec7c0da23a1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025121"
---
# <a name="creating-resources-for-varying-screens"></a>建立不同畫面的資源

Android 本身會在許多不同的裝置上執行，各有各種解析度、螢幕大小和螢幕密度。 Android 會執行調整和調整大小，讓您的應用程式在這些裝置上工作，但這可能會產生一次最佳的使用者體驗。 例如，影像可能看起來模糊，或可能在視圖上以預期的方式放置。

## <a name="concepts"></a>概念

有幾個詞彙和概念必須瞭解以支援多個畫面。

- **螢幕大小**&ndash; 用來顯示應用程式的實體空間量

- **螢幕密度**&ndash; 螢幕上任何指定區域的圖元數目。 典型的測量單位是以每英寸（DPI）為點。

- **解決**方式 &ndash; 螢幕上的圖元總數。 開發應用程式時，解決方式不如螢幕大小和密度那麼重要。

- **密度獨立圖元（dp）** &ndash; 測量的虛擬單位，可讓配置的設計與密度無關。 此公式是用來將 dp 轉換成螢幕圖元：

    px &equals; dp &times; DPI &divide; 160

- 當畫面的方向超出高度時，**方向**&ndash; 會被視為橫向。 相反地，當螢幕的高度高於寬度時，縱向方向就會是。 當使用者旋轉裝置時，該方向可能會在應用程式的存留期間變更。

請注意，這些概念的前三個是相互關聯 &ndash; 增加解析度而不增加密度，會增加螢幕大小。 不過，如果密度和解析度都增加，則螢幕大小會保持不變。 螢幕大小、密度和解析度之間的這種關聯性，很快就會變得更複雜。

為了協助處理這種複雜性，Android framework 偏好使用與*密度無關的圖元（dp）* 來進行螢幕佈局。 藉由使用與密度無關的圖元，使用者就會看到 UI 元素在具有不同密度的螢幕上具有相同的實體大小。

## <a name="supporting-various-screen-sizes-and-densities"></a>支援各種螢幕大小和密度

Android 會處理大部分的工作，以便針對每個畫面設定適當地呈現版面配置。 不過，您可以採取一些動作來協助系統。

在大部分情況下，使用與密度無關的圖元，而不是實際的圖元來確保密度獨立性。
Android 會在執行時間將可繪製資源調整為適當的大小。
不過，調整可能會導致點陣圖看起來模糊。 若要解決此問題，請提供不同密度的替代資源。 當您針對多個解析度和螢幕密度設計裝置時，可以更輕鬆地從較高的解析度或密度影像開始，然後再相應減少。

### <a name="declare-the-supported-screen-size"></a>宣告支援的螢幕大小

宣告螢幕大小可確保只有支援的裝置可以下載應用程式。 這是藉由在**androidmanifest.xml**中設定[支援-螢幕](https://developer.android.com/guide/topics/manifest/supports-screens-element.html)元素來完成。 這個元素是用來指定應用程式支援的螢幕大小。 如果應用程式可以正確地將其版面配置放在 [填滿] 畫面，則會將指定的畫面視為受支援。 藉由使用此資訊清單元素，應用程式不會顯示在不符合螢幕規格之裝置的[*Google Play*](https://play.google.com/)中。 不過，應用程式仍會在具有不支援螢幕的裝置上執行，但配置可能會呈現模糊和像素化。

支援的螢幕 sixes 會在解決方案的**屬性/androidmanifest.xml**檔案中宣告：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Android 資訊清單](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![Android 資訊清單](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

編輯**androidmanifest.xml**以包含[支援-畫面](https://developer.android.com/guide/topics/manifest/supports-screens-element.html)：

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

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>提供不同螢幕大小的替代版面配置

替代版面配置可讓您自訂特定螢幕大小的視圖，變更元件 UI 元素的位置或大小。

從 API 層級13（Android 3.2）開始，螢幕大小會被取代為使用 sw*N*dp 辨識符號。 這個新的限定詞會宣告指定配置所需的空間量。 建議要在 Android 3.2 或更高版本上執行的應用程式應該使用這些較新的限定詞。

例如，如果版面配置所需的螢幕寬度最低為700，則替代配置會在資料夾配置中**sw700dp**：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![700 dp 畫面寬度的版面配置資料夾](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![700 dp 畫面寬度的版面配置資料夾](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----

指導方針是適用于各種裝置的一些數位：

- **典型的電話**&ndash; 320 dp：一般電話

- **5 "平板電腦/" tweener "裝置**&ndash; 480 dp：例如 Samsung Note

- **7 "平板**電腦 &ndash; 600 dp：例如 Barnes and &amp; Noble Nook

- **10 "平板**電腦 &ndash; 720 dp：例如 Motorola Xoom

對於以最多12個（Android 3.1）為目標之 API 層級的應用程式，配置應該放在使用限定詞**small**/**一般**/**大型**/**m3.xlarge**的目錄中，以做為大部分裝置中可用的各種螢幕大小的一般化。 例如，在下圖中，有四種不同的螢幕大小的替代資源：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![四個螢幕大小的替代資源](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![四個螢幕大小的替代資源](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

以下是較舊的前置 API 層級13螢幕大小限定詞與密度無關圖元的比較：

- 426 dp x 320 dp 很**小**

- 470 dp x 320 dp 是**正常**的

- 640 dp x 480 dp 很**大**

- 960 dp x 720 dp 已**m3.xlarge**

API 層級13中較新的螢幕大小辨識符號，其優先順序高於 API 層級12和較低的舊版螢幕限定詞。
對於將跨越舊版和新 API 層級的應用程式，可能需要使用這兩組限定詞來建立替代資源，如下列螢幕擷取畫面所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![具有這兩個限定詞的替代資源](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![具有這兩個限定詞的替代資源](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----

### <a name="provide-different-bitmaps-for-different-screen-densities"></a>為不同的螢幕密度提供不同的點陣圖

雖然 Android 會視需要調整裝置的點陣圖，但點陣圖本身可能不會適當地相應增加或減少：它們可能會變得模糊或模糊。 提供適用于螢幕密度的點陣圖，將可減輕這個問題。

例如，下面的影像是當未提供密度指定的資源時，可能會發生的版面配置和外觀問題的範例。

![沒有密度資源的螢幕擷取畫面](resources-for-varying-screens-images/06-density-not-provided.png)

將此相較于以密度特定資源設計的版面配置：

![包含密度特定資源的螢幕擷取畫面](resources-for-varying-screens-images/07-density-specific-resources.png)

### <a name="create-varying-density-resources-with-android-asset-studio"></a>使用 Android 資產 Studio 建立不同的密度資源

建立各種密度的點陣圖可能有點繁瑣。 因此，Google 建立了一個線上公用程式，可減少建立這些點陣圖（稱為[**Android 資產 Studio**](https://romannurik.github.io/AndroidAssetStudio/)）時所牽涉到的部分冗長。

[![Android 資產 Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

這個網站會提供一個影像，協助建立以四個常見螢幕密度為目標的點陣圖。 然後，Android 資產 Studio 會建立具有一些自訂的點陣圖，然後讓它們以 zip 檔案的形式下載。

## <a name="tips-for-multiple-screens"></a>多個畫面的秘訣

Android 會在令人困擾數目的裝置上執行，而且螢幕大小和螢幕密度的組合可能會很龐大。 下列秘訣可協助將支援各種裝置所需的工作降到最低：

- **僅針對您所需的設計和開發** &ndash;其中有許多不同的裝置，但有些則有極少的外型規格，可能需要大量的設計和開發。 [**螢幕大小 和 密度**](https://developer.android.com/resources/dashboard/screens.html) 儀表板是 Google 提供的頁面，可提供螢幕大小/螢幕密度矩陣的詳細資料。 此細目提供如何在支援螢幕上進行開發的深入解析。

- **使用 DPs 而非圖元**-圖元會在螢幕密度變更時變得很麻煩。 不要將圖元值硬式編碼。 避免以圖元為單位（與密度無關的圖元）。

- **盡可能** **避免** [AbsoluteLayout](xref:Android.Widget.AbsoluteLayout)
  ，&ndash; 它在 API 層級3（Android 1.5）中被取代，並會導致脆弱配置。 不應使用它。 相反地，請嘗試使用更有彈性的版面配置 widget，例如[**LinearLayout**](xref:Android.Widget.LinearLayout)、 [**RelativeLayout**](xref:Android.Widget.RelativeLayout)或新的[**GridLayout**](xref:Android.Widget.GridLayout)。

- **挑選一個版面配置方向做為預設**&ndash; 例如，而不是提供替代資源配置 **-land**和**版面配置-埠**，將配置的資源放在**版面**配置中，並將資源垂直置入配置**埠**。

- **使用 LayoutParams 的高度和寬度**-在 XML 配置檔案中定義 UI 元素時，使用**wrap_content**和**fill_parent**值的 Android 應用程式將會更成功地確保在不同裝置之間進行適當的查看，而不是使用圖元或與密度無關的單位。 這些維度值會使 Android 適當地縮放點陣圖資源。 基於此相同原因，當指定 UI 元素的邊界和填補時，高密度獨立單位是最適合的保留。

## <a name="testing-multiple-screens"></a>測試多個畫面

Android 應用程式必須針對將支援的所有設定進行測試。 理想的裝置應該在實際的裝置上進行測試，但在許多情況下，這是不可能的，也不可行。
在此情況下，針對每個裝置設定使用模擬器和 Android 虛擬裝置安裝將會很有用。

Android SDK 提供一些模擬器的外觀，可以用來建立 Avd，將會複寫許多裝置的大小、密度和解析度。
許多硬體廠商同樣都會提供裝置的外觀。

另一個選項是使用協力廠商測試服務的服務。
這些服務將會 APK，在許多不同的裝置上執行，然後提供應用程式運作方式的意見反應。
