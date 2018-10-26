---
title: 其他資源
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 0384d96ddc96f8d0b16a42f691305f26ea25881d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108752"
---
# <a name="alternate-resources"></a>其他資源

替代的資源為目標的特定裝置或執行階段組態，例如目前的語言、 特定的螢幕大小或像素密度這些資源。 如果 Android 可以比對特定裝置或設定預設的資源比更特定的資源，則該資源將會改用。 如果找不到符合目前的組態的其他資源，然後將其載入的預設資源。 如何決定 Android 應用程式將使用哪些資源將涵蓋更詳細，在 [資源位置] 區段

替代的資源會組織為根據的資源類型，如同預設資源的 [資源] 資料夾內的子目錄。 替代資源子目錄的名稱的格式： _ResourceType_-_限定詞_

*限定詞*會識別特定的裝置組態的名稱。
名稱，每個破折號分隔，可能會有一個以上的限定詞。 例如，以下螢幕擷取畫面會顯示簡單的專案具有不同的設定，例如地區設定、 螢幕密度、 螢幕大小和方向的替代資源：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![其他資源](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![其他資源](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

加入資源類型的限定詞時，就會適用下列規則：

1. 可能有多個識別碼辨識符號，兩者以破折號分隔每個辨識符號。

2. 也許只能指定一次限定詞。

3. 限定詞必須出現在下表中的順序。

以下列出可能的辨識符號的參考：

- **MCC 和 MNC** &ndash; [行動裝置的國家/地區代碼](http://en.wikipedia.org/wiki/List_of_mobile_country_codes)(MCC) 和 （選擇性）[行動網路代碼](http://en.wikipedia.org/wiki/Mobile_Network_Code)(MNC)。 SIM 卡將提供的 MCC，而裝置連線到網路會提供 MNC。 雖然可以使用行動裝置的國家/地區程式碼的目標地區設定，但建議方法是使用下面指定的語言辨識符號。 例如，德國、 目標資源限定詞會`mcc262`。 在美國，T-行動裝置的目標資源限定詞`mcc310-mnc026`。
  如需完整的行動裝置的國家/地區代碼及行動網路代碼清單，請參閱<http://mcc-mnc.com/>。

- **語言**&ndash;兩個字母[ISO 639-1 語言代碼](http://en.wikipedia.org/wiki/ISO_639-1)並可選擇性地跟著兩個字母[ISO 3166-alpha 2 地區碼](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)。 
  如果提供兩個限定詞，則會以分隔`-r`。 例如，以目標法語地區設定，然後的辨識符號`fr`用。 若要針對加拿大的法文地區設定中，`fr-rCA`可用。 語言代碼和區域碼的完整清單，請參閱 <<c0> [ 表示法的名稱的語言代碼](http://www.loc.gov/standards/iso639-2/php/English_list.php)並[國家/地區名稱和程式碼項目](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm)。

- **最小寬度**&ndash;指定應用程式要執行的最小的螢幕寬度。 中的更詳細地討論[建立的資源，針對不同螢幕](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在 API 層級 13 (Android 3.2) 及更新版本可用。 比方說，限定詞`sw320dp`用於目標裝置，其高度和寬度至少是 320dp。

- **可用的寬度**&ndash;格式 w 中螢幕的最小寬度*N*dp 時，其中*N*是無關的像素的寬度，密度。
  在使用者旋轉裝置時可能會變更此值。 中的更詳細地討論[建立的資源，針對不同螢幕](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  適用於在 API 層級 13 (Android 3.2) 及更新版本。 範例： 限定詞 w720dp 可具有的最低 720dp 寬度的裝置為目標。

- **可用高度**&ndash;螢幕格式小時內的最小高度*N*dp 時，其中*N*中 dp 的高度。 在使用者旋轉裝置時可能會變更此值。 中的更詳細地討論[建立的資源，針對不同螢幕](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  適用於在 API 層級 13 (Android 3.2) 及更新版本。 比方說，將限定詞 h720dp 用的最少 720dp 高度的裝置為目標

- **螢幕大小**&ndash;這個限定詞為一般化的這些資源是螢幕大小。 它涵蓋更詳細地[建立的資源，針對不同螢幕](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  可能的值為 `small`、`normal`、`large` 和 `xlarge`。 在 API 層級 9 (Android 2.3/Android 2.3.1/Android 2.3.2) 中新增

- **螢幕外觀**&ndash;這是基於寬高比，而不是屏幕方向。 長螢幕的寬度。 新增 API 層級 4 (Android 1.6)。 可能的值為長時間和 notlong。

- **螢幕方向**&ndash;直向或橫向螢幕方向。 這可以變更應用程式的存留期間。
  可能的值為 `port` 和 `land`。

- **停駐模式**&ndash;停駐在車上的裝置，或在電腦桌前停駐。 在 API 層級 8 （Android 2.2.x 版本） 中新增。 可能的值為 `car` 和 `desk`。

- **夜間模式**&ndash;在夜間或在一天，應用程式是否正在執行。 這可能會變更應用程式的存留期間，旨在讓開發人員有機會在夜間使用較深版本的介面。 在 API 層級 8 （Android 2.2.x 版本） 中新增。 可能的值為 `night` 和 `notnight`。

- **畫面 (dpi) 的像素密度**&ndash;實體的螢幕上的指定區域中的像素數目。 通常會以每英吋點數 (dpi) 表示。 可能的值為：

    - `ldpi` &ndash; 低密度的螢幕。

    - `mdpi` &ndash; 中密度畫面

    - `hdpi` &ndash; 高密度的螢幕

    - `xhdpi` &ndash; 額外高密度的螢幕

    - `nodpi` &ndash; 不想要調整的資源

    - `tvdpi` &ndash; 在 API 層級 13 (Android 3.2) 針對螢幕之間 mdpi 和 hdpi 引進。

- **觸控式螢幕類型**&ndash;指定觸控式螢幕裝置上可能有的類型。 可能的值為`notouch`（沒有觸控式螢幕）， `stylus` （電阻式觸控螢幕適用於手寫筆），和`finger`（觸控式螢幕）。

- **鍵盤可用性**&ndash;指定可以使用鍵盤的種類。 這可能會變更應用程式的存留期間&ndash;，例如當使用者開啟硬體鍵盤。 可能的值為：

    - `keysexposed` &ndash; 裝置有可用的鍵盤。 如果不沒有啟用任何軟體鍵盤，然後這才會使用硬體鍵盤開啟時。

    - `keyshidden` &ndash; 沒有硬體鍵盤裝置。 它隱藏但沒有軟體鍵盤啟用。

    - `keyssoft` &ndash; 裝置已啟用軟體鍵盤。

- **主要的文字輸入法**&ndash;指定何種類型的硬體金鑰可供輸入使用。 可能的值為：

    - `nokeys` &ndash; 沒有硬體金鑰的輸入。

    - `qwerty` &ndash; 沒有可用的 qwerty 鍵盤。

    - `12key` &ndash; 沒有 12 鍵的硬體鍵盤


- **瀏覽金鑰可用性** &ndash; 5 方式或方向鍵 (定向 pad) 瀏覽時可用。 這可以變更您的應用程式的存留期間。 可能的值為：

    - `navexposed` &ndash; 瀏覽的索引鍵是可供使用者

    - `navhidden` &ndash; 無法使用瀏覽的按鍵。

-  **主要的非觸控式瀏覽方式**&ndash;瀏覽裝置上可用的類型。 可能的值為：

    - `nonav` &ndash; 提供唯一的瀏覽設施是觸控式螢幕

    - `dpad` &ndash; d pad (pad 方向) 可供瀏覽

    - `trackball` &ndash; 裝置已進行瀏覽軌跡球

    - `wheel` &ndash; 常見的案例，其中有一或多個方向性輪圈可用

-  **平台版本 （API 層級）** &ndash;格式 v 裝置所支援的 API 層級*N*，其中*N*是 API 層級，其為目標。 例如，v11 將目標 API 層級 11 (Android 3.0) 裝置。


如需完整資訊的相關資源限定詞請參閱[提供的資源](http://developer.android.com/guide/topics/resources/providing-resources.html)Android 開發人員網站上。


## <a name="how-android-determines-what-resources-to-use"></a>Android 如何決定要使用哪些資源

它是非常可能和可能的 Android 應用程式將會包含許多資源。 請務必了解 Android 如何選取在裝置上執行的應用程式的資源。

Android 會決定基底的資源，藉由反覆規則的下列測試：

- **消除矛盾的限定詞**&ndash;比方說，如果裝置方向是直向，然後所有的橫向資源目錄將會遭到拒絕。

- **忽略限定詞不支援**&ndash;並非所有的限定詞可用於所有的 API 層級。 如果資源目錄包含不支援裝置的限定詞，則會忽略該資源目錄。

- **找出下一個最高的優先順序限定詞**&ndash;參考上述資料表選取 [下一步] 的最高優先順序限定詞 （由上往下）。

- **保留 辨識符號的任何資源目錄**&ndash;如果有任何符合上述的資料表限定詞的資源目錄選取 下一步 的最高優先順序限定詞 （由上往下）。

這些規則也會是由以下流程圖所示：

[![資源的流程圖](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

當系統正在尋找密度專屬的資源，且找不到時，它會嘗試找出其他密度的特定資源，並調整其規模。 Android 可能不一定要使用的預設資源。
比方說，尋求低密度的資源，而且無法使用時，Android 可能對預設或中密度資源選取高密度版本的資源。 這是因為會高密度的資源可以相應減少為 0.5，這會導致較少的可見性問題比相應減少中密度資源會需要進行倍 0.75 倍。

例如，請考慮具有下列可繪製資源目錄的應用程式：

    drawable
    drawable-en
    drawable-fr-rCA
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

現在應用程式是裝置上和執行使用下列組態：

- **地區設定** &ndash; EN-GB
- **方向**&ndash;連接埠
- **螢幕密度** &ndash; hdpi
- **觸控式螢幕類型** &ndash; notouch
- **主要的輸入的法** &ndash; 12key

因為它們與地區設定的衝突，一開始，消除法文資源`en-GB`，讓我們使用：

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

從上述的資料表限定詞接下來，選取第一個限定詞： MCC 和 MNC。 沒有包含這個限定詞，因此會忽略的 MCC/MNC 程式碼的資源目錄。

會選取下一步的限定詞，這是語言。 沒有相符的語言代碼的資源。 不相符的語言代碼的所有資源目錄`en`會遭到拒絕，以便資源的清單現在是：

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

下一步的限定詞存在於為，讓所有不相符的螢幕方向的資源目錄`port`也會刪除：

    drawable-en-port
    drawable-en-port-ldpi

接下來是螢幕密度的限定詞`ldpi`，這會導致其中一個排除更多的資源目錄：

    drawable-en-port-ldpi

由於此程序中，Android 會在資源目錄中使用的可繪製資源`drawable-en-port-ldpi`裝置。

> [!NOTE]
> 螢幕大小限定詞會提供此選取項目程序的例外狀況。 很可能適用於 Android 選取專為較小的螢幕比哪些目前的裝置提供的資源。 比方說，大螢幕裝置可能會使用資源提供一般大小的螢幕。 不過這並非反之亦然： 相同的大螢幕裝置不會使用提供給 xlarge （超大型） 畫面的資源。 如果 Android 找不到符合指定的螢幕大小的資源集，應用程式將會損毀。
