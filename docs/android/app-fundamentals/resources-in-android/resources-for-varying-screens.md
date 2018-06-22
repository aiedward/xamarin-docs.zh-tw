---
title: 針對不同螢幕中建立資源
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a98d65c6c04ae2400572a2405d487035ac466678
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30770959"
---
# <a name="creating-resources-for-varying-screens"></a>針對不同螢幕中建立資源

Android 本身上執行許多不同裝置，每個具有各種不同的解析度，螢幕大小和螢幕密度。 Android 將會執行縮放和調整大小以進行您在這些裝置上運作的應用程式，但這可能會導致次佳使用者體驗。 例如，影像可能會出現模糊，映像可能佔用太多 （或沒有足夠） 螢幕空間可以讓版面配置中的 UI 項目位置將會重疊或是太遠。


## <a name="concepts"></a>概念

是一定要了解以支援多個螢幕幾個詞彙和概念。

- **螢幕大小**&ndash;顯示您的應用程式的實體空間量

- **畫面上密度**&ndash;螢幕上任何給定的區域中的像素數目。 典型單位是測量的每英吋點數 (dpi)。

- **解析**&ndash;螢幕上的像素為單位的總數。 在開發應用程式時，沒有與螢幕大小和密度一樣重要解析。

- **密度獨立畫素 (dp)** &ndash;這是虛擬單位量值，以允許版面配置，以設計成獨立的密度。 若要將 dp 轉換成螢幕像素使用下列公式：

    px &equals; dp &times; dpi &divide; 160

- **方向** &ndash; 會被視為橫向寬度比高度長時。 相反地，直式方向時，螢幕的高度大於寬度。 使用者旋轉裝置方向可以變更應用程式的存留期間。

請注意這些概念的前三個間相關&ndash;增加而增加密度會增加的螢幕大小的解析度。 不過如果增加密度和解析然後螢幕大小可以維持不變。 此螢幕大小、 密度和解析度之間的關聯性會使得螢幕支援非常快速。

為了處理這種複雜性，Android 架構偏好使用*密度無關的像素 (dp)* 螢幕配置。 藉由使用密度無關的像素，使用者擁有相同的實體大小，使用的不同密度的螢幕會顯示 UI 項目。


## <a name="supporting-various-screen-sizes-and-densities"></a>支援各種螢幕大小和密度

Android 會處理大部分的工作來呈現正確的每個螢幕設定版面配置。 不過，有一些可以幫助系統採取的動作。

密度無關的像素，而不是在版面配置中的實際像素的用途是足以應付大部分的情況下，以確保密度獨立性。
Android 將會在執行階段為適當大小調整 drawables。
不過，很可能在此項縮放會導致出現模糊的點陣圖。 若要避免這個問題，可能必須提供的不同密度的替代資源。 設計多個解決方式和螢幕密度會證明您更輕鬆的裝置時開始使用更高的解析度或密度映像，然後向下調整而變更。 這將導致任何模糊或扭曲程度可能會造成從的調整大小。


### <a name="declare-the-screen-size-the-application-supports"></a>宣告應用程式所支援的螢幕大小

宣告的螢幕大小可確保只支援的裝置可以下載應用程式。 這是藉由設定[支援畫面](http://developer.android.com/guide/topics/manifest/supports-screens-element.html)中的項目**AndroidManifest.xml**檔案。 這個項目用來指定應用程式支援何種螢幕大小。 如果應用程式才能正確地支援會被視為指定的螢幕的配置，以填滿螢幕。 藉由使用這個資訊清單的項目，應用程式將不會顯示在[ *Google Play* ](https://play.google.com/)不符合螢幕規格的裝置。 不過，應用程式仍會在具有不支援的螢幕裝置上執行，但配置可能會出現模糊和不太美觀。

若要在 Xamarin.Android 這樣做，就必須先新增**AndroidManifest.xml**檔案加入專案中，如果不存在：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Android 資訊清單](resources-for-varying-screens-images/01-android-manifest-vs-sml.png)](resources-for-varying-screens-images/01-android-manifest-vs.png#lightbox)

**AndroidManifest.xml**加入至**屬性**目錄。 檔案然後編輯包含[支援畫面](http://developer.android.com/guide/topics/manifest/supports-screens-element.html):

[![加入支援螢幕](resources-for-varying-screens-images/02-adding-supports-screens-vs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Android 資訊清單](resources-for-varying-screens-images/01-android-manifest-xs-sml.png)](resources-for-varying-screens-images/01-android-manifest-xs.png#lightbox)

**AndroidManifest.xml**加入至**屬性**目錄。 檔案然後編輯包含[支援畫面](http://developer.android.com/guide/topics/manifest/supports-screens-element.html):

[![加入支援螢幕](resources-for-varying-screens-images/02-adding-supports-screens-xs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-xs.png#lightbox)

-----

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>提供了替代的版面配置的不同螢幕大小

雖然 Android 會根據螢幕大小來調整大小，這可能無法完全在某些情況下。 它可能需要增加的大小較大的螢幕上部分 UI 項目，或變更的較小的螢幕的 UI 項目位置。

從應用程式開發介面層級 13 (Android 3.2) 開始，螢幕大小已取代為使用 sw*N*dp 限定詞。 這個新限定詞會宣告需要指定配置的空間數量。 強烈建議的用意是要執行 Android 3.2 或更新版本的應用程式會使用這些較新的限定詞。

例如，如果配置需要螢幕寬度最小 700dp，替代的配置將會進入資料夾中**配置 sw700dp**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![700dp 螢幕寬度的版面配置資料夾](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![700dp 螢幕寬度的版面配置資料夾](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


一般來說，以下是各種裝置的一些數字：

- **一般電話** &ndash; 320dp： 典型的電話

- **5 」 tablet /"tweener"裝置** &ndash; 480dp： 例如 Samsung 附註

- **7 「 tablet** &ndash; 600dp： 例如 Barnes&amp;貴族 Nook

- **10"tablet** &ndash; 720dp： 例如 Motorola Xoom

應用程式的目標應用程式開發介面層級最多 12 (Android 3.1)，在配置應使用限定詞的目錄中**小**/**正常**/**大型** / **xlarge**為一般化的大部分裝置中可用的各種螢幕大小。 比方說，在下面的影像中，有四個不同的螢幕大小的替代資源：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![四個螢幕大小的其他資源](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![四個螢幕大小的其他資源](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

以下是比較舊的前置應用程式開發介面層級 13 螢幕大小限定詞與密度無關的像素的相比較：

- 426dp x 320dp 是**小**

- 470dp x 320dp 是**正常**

- 640dp x 480dp 是**大型**

- 960dp x 720dp 是**xlarge**

較新的螢幕大小在 API 層級 13 限定詞，並設定優先順序高於應用程式開發介面層級 12 和較低的較舊的螢幕限定詞。
對於橫跨舊的和新的應用程式開發介面層級的應用程式，可能需要建立其他資源，使用這兩組辨識符號，如下列螢幕擷取畫面所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![以這兩個限定詞的其他資源](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![以這兩個限定詞的其他資源](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----



### <a name="provide-different-bitmaps-for-different-screen-densities"></a>提供不同的螢幕密度不同點陣圖

雖然 Android 將會縮放點陣圖另存為所需的裝置，但其本身的點陣圖可能不優雅地相應增加或減少： 它們變得模糊或模糊。 提供適用於螢幕密度點陣圖會減輕這個問題。

例如下, 圖是版面配置和外觀可能會發生的問題範例密度指定資源不會提供。

![不含資源密度的螢幕擷取畫面](resources-for-varying-screens-images/06-density-not-provided.png)

比較這設計用與密度的特定資源的配置：

![密度特定資源的螢幕擷取畫面](resources-for-varying-screens-images/07-density-specific-resources.png)


### <a name="create-varying-density-resources-with-android-asset-studio"></a>建立資產 Android Studio 中的不同密度資源

建立這些點陣圖之各種密度就必須等待冗長的位元。 因此，Google 已建立一個線上公用程式，這樣可以減少一些 tedium 涉及呼叫這些點陣圖建立[ **Android 資產 Studio**](https://romannurik.github.io/AndroidAssetStudio/)。

[![資產 android Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

此網站可協助建立目標四個常見的螢幕密度藉由提供一個影像的點陣圖。 Android 資產 Studio 將再建立點陣圖的自訂設定，並讓其下載為 zip 檔案。


## <a name="tips-for-multiple-screens"></a>多個螢幕的秘訣

Android 上執行的裝置，令人困擾的數目，而且螢幕大小和螢幕密度的組合可以令人望而生畏。 下列秘訣可協助支援各種裝置所需投入時間降至最低：

- **只有設計和開發，您需要針對**&ndash;有很多不同的裝置，但某些極少數的尺寸，可能需要一些努力來設計和開發適用於許多存在。 [**螢幕大小和密度**](http://developer.android.com/resources/dashboard/screens.html)儀表板是由提供資料的螢幕大小/螢幕密度矩陣分解的 Google 提供的頁面。 此分解提供深入了解如何開發投入時間集中用於支援螢幕。

- **使用 Dp，而不是像素為單位**-像素為單位變成螢幕密度變更為有問題。 請勿像素值。 避免以利於 dp （密度無關的像素） 的像素為單位。

- **避免** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **任一處可能**&ndash;它已被取代 API 層級 3 (Android 1.5) 中，將會導致容易損毀的版面配置。 應該不使用。 相反地，嘗試使用更具彈性的配置 widget，例如[**線性**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)， [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)，或新[**格線**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/)。

- **挑選其中一個做為預設的版面配置方向**&ndash;比方說，而不是提供替代資源**配置土地**和**配置連接埠**，將的資源在橫向**配置**，適用於縱向到的資源和**配置連接埠**。

- **用於高度及寬度的 LayoutParams** -XML 配置檔案中定義 UI 項目時的 Android 應用程式使用**wrap_content**和**fill_parent**值將會擁有多個成功確定適當的外觀，跨不同裝置比使用像素或密度獨立的單位。 這些維度值會導致 Android，可以視需要調整點陣圖資源。 同樣地，密度無關的單位是最適合保留的時指定的邊界和邊框距離的 UI 項目。


## <a name="testing-multiple-screens"></a>測試多個螢幕

Android 應用程式必須針對將支援的所有組態進行測試。 在理想情況下應該本身在實際裝置上測試裝置，但在許多情況下這是不可能或切實的方法。
在此情況下使用模擬器和 Android 虛擬裝置的安裝程式對每個裝置設定很有用。

Android SDK 提供大小、 密度，以及解決的許多裝置，將複寫面板可能用來建立 AVD 的某些模擬器。
同樣地，許多硬體廠商會提供面板其裝置。

另一個選項是使用協力廠商測試服務的服務。
這些服務將採取 APK，在許多不同的裝置上執行它，然後提供 意見反應應用程式的運作方式。
