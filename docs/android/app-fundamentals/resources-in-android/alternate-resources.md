---
title: "其他資源"
ms.topic: article
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 7ebbf2a9215c8472ae2f286728cb2f819e8331cb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="alternate-resources"></a>其他資源

其他資源則為目標的特定裝置或執行階段組態，例如目前的語言、 特定的螢幕大小或像素密度。 如果 Android 可以比特定裝置或組態的預設資源比更特定的資源，然後該資源將會改用。 如果找不到符合目前設定的替代資源，就會載入預設資源。 如何決定 Android 應用程式將使用哪些資源都將涵蓋於資源位置 區段中會更詳細探討，

其他資源會組織為根據的資源類型，如同預設資源的 [資源] 資料夾內的子目錄。 在表單中的替代資源子目錄的名稱是： _ResourceType_-_限定詞_

*限定詞*會識別特定的裝置組態的名稱。
在每個以虛線分隔的名稱可能有多個限定詞。 例如，下列螢幕擷取畫面顯示簡單的專案具有不同的設定，例如地區設定、 螢幕密度，螢幕大小和方向的替代資源：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![其他資源](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![其他資源](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

加入資源類型的限定詞時，就會適用下列規則：

1. 可能有多個限定詞，與每個限定詞，兩者以破折號分隔。

2. 或許只能指定一次限定詞。

3. 限定詞必須是下表中出現的順序。

以下列出可能的辨識符號的參考：

- **MCC 和 MNC** &ndash; [行動國碼](http://en.wikipedia.org/wiki/List_of_mobile_country_codes)(MCC) 並選擇性地[行動網路程式碼](http://en.wikipedia.org/wiki/Mobile_Network_Code)(MNC)。 當裝置連線到網路會提供 MNC SIM 卡會提供 MCC。 雖然可以使用行動裝置的國家/地區的程式碼的目標地區設定，但是建議方法是使用以下指定的語言辨識符號。 例如，目標資源設為德國，限定詞會`mcc262`。 在美國太平洋時區，T 行動裝置的目標資源的辨識符號`mcc310-mnc026`。
  如需行動裝置的國家/地區碼和行動網路代碼的完整清單，請參閱<http://mcclist.com/>。

- **語言**&ndash;兩個字母[ISO 639-1 語言代碼](http://en.wikipedia.org/wiki/ISO_639-1)並可選擇性地跟著兩個字母[ISO 3166-alpha 2 地區碼](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)。 
  如果提供了兩個限定詞，則會以分隔`-r`。 例如，以目標法語地區設定，然後的限定詞`fr`用。 若要針對加拿大的法文地區設定中，`fr-rCA`就會使用。 如需語言代碼，以及地區碼的完整清單，請參閱[的表示法的名稱的語言代碼](http://www.loc.gov/standards/iso639-2/php/English_list.php)和[國家 （地區） 名稱和程式碼項目](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm)。

- **最小寬度**&ndash;指定應用程式是在上執行的最小的螢幕寬度。 中的更詳細地討論[建立的資源，針對不同螢幕](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  可用的 API 層級 13 (Android 3.2) 和更新版本。 例如，限定詞`sw320dp`用於目標裝置之高度和寬度至少為 320dp。

- **可用寬度**&ndash;格式 w 中螢幕的最小寬度*N*dp 位置*N*是無關的像素密度的寬度。
  使用者旋轉裝置時可能會變更此值。 中的更詳細地討論[建立的資源，針對不同螢幕](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  適用於應用程式開發介面層級 13 (Android 3.2) 和更新版本。 範例： 限定詞 w720dp 用於具有的最小 720dp 寬度的裝置為目標。

- **可用高度**&ndash;格式 h 中螢幕的最小高度*N*dp 位置*N*中 dp 的高度。 使用者旋轉裝置時可能會變更此值。 中的更詳細地討論[建立的資源，針對不同螢幕](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  適用於應用程式開發介面層級 13 (Android 3.2) 和更新版本。 限定詞 h720dp，例如用於具有最低 720dp 高度的裝置為目標

- **螢幕大小**&ndash;這個限定詞為一般化的這些資源是為的螢幕大小。 涵蓋更詳細地[建立的資源，針對不同螢幕](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  可能的值為 `small`、`normal`、`large` 和 `xlarge`。 加入應用程式開發介面層級 9 (Android 2.3/Android 2.3.1/Android 2.3.2)

- **螢幕外觀**&ndash;這根據外觀比例，而不是螢幕方向。 長螢幕的寬度。 加入應用程式開發介面層級 4 (Android 1.6)。 可能的值為 long 和 notlong。

- **螢幕方向**&ndash;直向或橫向螢幕方向。 如此便可以變更應用程式的存留期間。
  可能的值為 `port` 和 `land`。

- **停駐模式**&ndash;停駐在車上的裝置，或支援人員停駐。 加入應用程式開發介面層級 8 (Android 2.2.x) 中。 可能的值為 `car` 和 `desk`。

- **系統會在晚上模式**&ndash;到了晚上，或在一天，應用程式是否正在執行。 這可能會變更應用程式的存留期間，而是讓開發人員有機會在夜間使用深版本介面。 加入應用程式開發介面層級 8 (Android 2.2.x) 中。 可能的值為 `night` 和 `notnight`。

- **畫面像素密度 (dpi)** &ndash;實際螢幕上的指定區域中的像素數目。 通常會以每英吋點數 (dpi) 表示。 可能的值為：

    - `ldpi` &ndash; 較低的密度的螢幕。

    - `mdpi` &ndash; 中密度的螢幕

    - `hdpi` &ndash; 較高的密度的螢幕

    - `xhdpi` &ndash; 額外較高的密度的螢幕

    - `nodpi` &ndash; 要調整的資源

    - `tvdpi` &ndash; 應用程式開發介面層級導入 13 (Android 3.2) 針對螢幕 mdpi 和 hdpi 之間。

- **觸控螢幕類型**&ndash;指定類型的裝置可能具有觸控螢幕。 可能的值為`notouch`（沒有觸控式螢幕）， `stylus` （電阻式觸控螢幕適用於手寫筆），和`finger`（觸控螢幕）。

- **鍵盤可用性**&ndash;指定可以使用鍵盤的種類。 這可能會變更應用程式的存留期間&ndash;，例如當使用者開啟硬體鍵盤。 可能的值為：

    - `keysexposed` &ndash; 裝置有可用的鍵盤。 如果不沒有啟用任何軟體鍵盤，則這僅時，使用開啟的硬體鍵盤。

    - `keyshidden` &ndash; 裝置沒有硬體鍵盤，但已隱藏且沒有軟體鍵盤已啟用。

    - `keyssoft` &ndash; 裝置必須啟用軟體鍵盤。

- **主要的文字輸入方法**&ndash;指定何種類型的硬體金鑰可供輸入使用。 可能的值為：

    - `nokeys` &ndash; 沒有任何硬體金鑰的輸入。

    - `qwerty` &ndash; 沒有可用的 qwerty 鍵盤。

    - `12key` &ndash; 沒有 12 金鑰硬體鍵盤


- **瀏覽鍵可用性** &ndash; 5 方式或方向鍵 （方向鍵） 瀏覽時可用。 如此便可以變更您的應用程式的存留期間。 可能的值為：

    - `navexposed` &ndash; 瀏覽按鍵都會提供給使用者

    - `navhidden` &ndash; 無法使用瀏覽的按鍵。

-  **主要的非觸控式導覽方法**&ndash;裝置上瀏覽的類型。 可能的值為：

    - `nonav` &ndash; 可用的唯一瀏覽設備是觸控式螢幕

    - `dpad` &ndash; 方向鍵 （方向鍵） 是可供瀏覽

    - `trackball` &ndash; 裝置具有瀏覽軌跡

    - `wheel` &ndash; 不常見的案例，其中有一或多個方向滾輪可用

-  **平台版本 （API 層級）** &ndash;格式 v 中的裝置所支援的 API 層級*N*，其中*N*是做為目標的應用程式開發介面層級。 例如，v11 將為目標的應用程式開發介面層級 11 (Android 3.0) 裝置。


如需更完整資訊資源限定詞請參閱[提供資源](http://developer.android.com/guide/topics/resources/providing-resources.html)Android 開發人員網站上。


## <a name="how-android-determines-what-resources-to-use"></a>Android 如何決定要使用哪些資源

它是非常可能可能會與 Android 應用程式會包含許多資源。 請務必了解 Android 如何選取在裝置上執行的應用程式的資源。

Android 會決定基底的資源，藉由重複處理規則的下列測試：

- **消除矛盾限定詞**&ndash;比方說，如果裝置方向為 [縱向]，然後橫印資源的所有目錄將會遭到都拒絕。

- **忽略不支援限定詞**&ndash;限定詞並非所有可用的所有應用程式開發介面層級。 如果資源目錄包含裝置不支援辨識符號，將會忽略該資源的目錄。

- **識別下一個最高的優先順序限定詞**&ndash;參考上述資料表選取的下一個最高優先權辨識符號 （從上到下）。

- **保留 辨識符號的任何資源目錄**&ndash;如果沒有符合上述的資料表限定詞的任何資源目錄選取的下一個最高優先權辨識符號 （從上到下）。

這些規則也會是由以下流程圖所示：

[![資源的流程圖](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

當系統正在尋找密度專屬的資源，並找不到它們時，它會嘗試找出其他密度的特定資源，並調整其大小。 Android 可能不一定要使用的預設資源。
比方說，尋找低的資源和它無法使用時，Android 可能高於預設值或媒體密度資源選取適用之高密度版本的資源。 這是因為會適用之高密度的資源可以縮小，因數為 0.5，而導致比規模媒體密度資源即需要 0.75 倍的可見性問題比較少。

例如，請考慮具有下列 drawable 資源目錄的應用程式：

    drawable
    drawable-en
    drawable-fr-rCA
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

現在應用程式是裝置上和執行以下列設定：

- **地區設定** &ndash; EN-GB
- **方向**&ndash;連接埠
- **畫面上密度** &ndash; hdpi
- **觸控螢幕類型** &ndash; notouch
- **主要的輸入的法** &ndash; 12key

因為它們與地區設定的衝突，一開始刪除法文資源`en-GB`，讓我們使用：

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

從上述的資料表限定詞接下來，選取第一個限定詞： MCC 和 MNC。 沒有任何資源目錄，其中包含這個限定詞，因此會忽略 MCC/MNC 程式碼。

選取 下一步的限定詞，這是語言。 沒有相符的語言代碼的資源。 不相符的語言代碼的所有資源目錄`en`會遭到拒絕，以便資源的清單現在是：

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

下一步的限定詞存在於為，讓所有不相符的螢幕方向的資源目錄`port`也會刪除：

    drawable-en-port
    drawable-en-port-ldpi

接下來是螢幕密度的限定詞`ldpi`，這會導致其中一個排除更多的資源目錄：

    drawable-en-port-ldpi

此程序，因為 Android 將資源目錄中使用 drawable 資源`drawable-en-port-ldpi`裝置。

> [!NOTE]
> 螢幕大小限定詞會提供這個選取項目處理序有一個例外狀況。 很可能適用於 Android 選取專為較小的螢幕較的目前裝置所提供的資源。 例如，大型螢幕裝置可能使用的資源提供一般大小螢幕。 不過這並非反之亦然： 相同的大型螢幕裝置不會使用 xlarge 檢測提供的資源。 如果 Android 找不到符合指定的螢幕大小的資源集，應用程式將會損毀。
