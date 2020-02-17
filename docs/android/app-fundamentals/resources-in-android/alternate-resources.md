---
title: 替代資源
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 644262310614874794810fd083ba1823abfd0da2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025408"
---
# <a name="alternate-resources"></a>替代資源

替代資源是以特定裝置或執行時間設定為目標的資源，例如目前語言、特定螢幕大小或圖元密度。 如果 Android 可以比對特定裝置或設定的特定資源，而不是預設資源，則會改為使用該資源。 如果找不到符合目前設定的替代資源，則會載入預設資源。 在資源位置一節中，Android 如何決定應用程式所使用的資源將會更詳細地涵蓋

替代資源會根據資源類型組織成 Resources 資料夾內的子目錄，就像預設資源一樣。 替代資源子目錄的名稱格式如下： _ResourceType_-_限定詞_

辨識*符號*是識別特定裝置設定的名稱。
名稱中可能會有一個以上的辨識符號，每個都以破折號分隔。 例如，下列螢幕擷取畫面顯示了一個簡單的專案，其中包含不同設定的替代資源，例如地區設定、螢幕密度、螢幕大小和方向：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![替代資源](alternate-resources-images/alternate-resources-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![替代資源](alternate-resources-images/alternate-resources-xs.png)

-----

將限定詞新增至資源類型時，適用下列規則：

1. 可能會有一個以上的限定詞，其中每個辨識符號都以破折號分隔。

2. 限定詞可能只指定一次。

3. 限定詞的順序必須依照下表所示。

以下列出可能的限定詞供您參考：

- **Mcc 和 MNC** &ndash; 行動[國家/地區代碼](https://en.wikipedia.org/wiki/List_of_mobile_country_codes)（MCC），以及選擇性的行動[網路代碼](https://en.wikipedia.org/wiki/Mobile_Network_Code)（MNC）。 SIM 卡會提供 MCC，而裝置所連線的網路則會提供 MNC。 雖然可以使用行動國家/地區代碼來鎖定地區設定，但建議的方法是使用以下指定的語言限定詞。 例如，若要將資源的目標設為德國，辨識符號會是 `mcc262`。 若要以美國的 T 行動裝置資源為目標，辨識符號為 `mcc310-mnc026`。
  如需行動國家/地區代碼和行動網路程式碼的完整清單，請參閱 <http://mcc-mnc.com/>。

- **Language** &ndash; 兩個字母的[iso 639-1 語言代碼](https://en.wikipedia.org/wiki/ISO_639-1)，並選擇性地加上兩個字母的[iso-3166-](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)英數位元-2 個區域程式碼。 
  如果同時提供這兩個限定詞，則會以 `-r`分隔。 例如，若要以法文說話地區設定為目標，則會使用 `fr` 的限定詞。 若要以法文加拿大地區設定為目標，則會使用 `fr-rCA`。 如需語言代碼和區域程式碼的完整清單，請參閱撰寫語言名稱和[國家/地區名稱和程式碼](https://www.iso.org/iso-3166-country-codes.html)專案的[程式碼](https://www.loc.gov/standards/iso639-2/php/English_list.php)。

- **最小寬度**&ndash; 指定應用程式要執行的最小畫面寬度。 在[建立不同螢幕的資源中有](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)更詳細的討論。 
  適用于 API 層級13（Android 3.2）和更新版本。 例如，辨識符號 `sw320dp` 用來將高度和寬度至少為320dp 的裝置設為目標。

- **可用寬度**&ndash; 畫面的最小寬度，格式為 w*n*dp，其中*N*是以密度獨立圖元為單位的寬度。
  當使用者旋轉裝置時，這個值可能會變更。 在[建立不同螢幕的資源中有](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)更詳細的討論。 
  適用于 API 層級13（Android 3.2）和更新版本。 範例：限定詞 w720dp 是用來將寬度最低為720dp 的裝置設為目標。

- **可用高度**&ndash; 螢幕的最小高度，格式為 h*N*dp，其中*N*是 dp 中的高度。 當使用者旋轉裝置時，這個值可能會變更。 在[建立不同螢幕的資源中有](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)更詳細的討論。 
  適用于 API 層級13（Android 3.2）和更新版本。 例如，限定詞 h720dp 是用來將高度為最低720dp 的裝置設為目標

- **螢幕大小**&ndash; 此限定詞是這些資源所適用的螢幕大小一般化。 [建立不同畫面的資源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)時，將會更詳細地涵蓋此功能。 
  可能的值為 `small`、`normal`、`large` 和 `xlarge`。 API 層級9中的新增功能（Android 2.3/Android 2.3.1/Android 2.3.2）

- **畫面外觀**&ndash; 這是以外觀比例為基礎，而不是螢幕方向。 較長的螢幕較寬。 已在 API 層級4中新增（Android 1.6）。 可能的值為 long 和 notlong。

- **螢幕方向**&ndash; 縱向或橫向螢幕方向。 這可能會在應用程式的存留期間變更。
  可能的值為 `port` 和 `land`。

- 汽車 dock 或書桌 dock 中裝置的**Dock 模式**&ndash;。 已在 API 層級8（Android 2.2. x）中新增。 可能的值為 `car` 和 `desk`。

- **夜間模式**&ndash; 應用程式是在晚上或每天執行。 這在應用程式的存留期間可能會變更，其目的是讓開發人員有機會在夜間使用較暗版本的介面。 已在 API 層級8（Android 2.2. x）中新增。 可能的值為 `night` 和 `notnight`。

- [**螢幕圖元密度（DPI）** ] &ndash; 實體螢幕上指定區域中的圖元數目。 通常以每英寸的點（DPI）表示。 可能的值為：

  - `ldpi` &ndash; 低密度畫面。

  - `mdpi` &ndash; 中等密度畫面

  - `hdpi` &ndash; 高密度畫面

  - `xhdpi` &ndash; 額外的高密度畫面

  - `nodpi` 不會調整的 &ndash; 資源

  - 在 mDPI 與 hDPI 之間的畫面，API 層級13（Android 3.2）中引進的 `tvdpi` &ndash;。

- **觸控式螢幕類型**&ndash; 指定裝置可能擁有的觸控式螢幕類型。 可能的值為 `notouch` （沒有觸控式螢幕）、`stylus` （適用于手寫筆的電阻式觸控式螢幕）和 `finger` （觸控觸控）。

- **鍵盤可用性**&ndash; 指定可用的鍵盤類型。 這可能會在應用程式的存留期內變更 &ndash; 例如，當使用者開啟硬體鍵盤時。 可能的值為：

  - `keysexposed` &ndash; 裝置有可用的鍵盤。 如果未啟用任何軟體鍵盤，則只有在開啟硬體鍵盤時才會使用此功能。

  - `keyshidden` &ndash; 裝置有硬體鍵盤，但它是隱藏的，而且未啟用任何軟體鍵盤。

  - `keyssoft` &ndash; 裝置已啟用軟體鍵盤。

- **主要文字輸入法**&ndash; 用來指定哪些類型的硬體金鑰可用於輸入。 可能的值為：

  - `nokeys` &ndash; 沒有用於輸入的硬體金鑰。

  - `qwerty` &ndash; 有一個可供使用的鍵盤。

  - `12key` &ndash; 有12個關鍵的硬體鍵盤

- 當有5向或 d-pad （雙向）導覽可供使用時，可用的**導覽金鑰可用性**&ndash;。 這可能會在應用程式的存留期間變更。 可能的值為：

  - `navexposed` &ndash; 使用者可以使用導覽金鑰

  - `navhidden` &ndash; 無法使用導覽金鑰。

- **主要的非觸控流覽方法**&ndash; 裝置上可用的導覽種類。 可能的值為：

  - `nonav` &ndash; 唯一可用的導覽功能是觸控式螢幕

  - `dpad` &ndash; d-pad （方向面板）可供導覽

  - `trackball` &ndash; 裝置具有導覽的軌跡球

  - `wheel` &ndash; 有一或多個方向滑軌可用的罕見案例

- **平臺版本（API 層級）** &ndash; 裝置支援的 api 層級，格式為 v*n*，其中*N*是作為目標的 api 層級。 例如，v11 會以 API 層級11（Android 3.0）裝置為目標。

如需資源限定詞的完整資訊，請參閱在 Android 開發人員網站上[提供資源](https://developer.android.com/guide/topics/resources/providing-resources.html)。

## <a name="how-android-determines-what-resources-to-use"></a>Android 如何判斷要使用的資源

Android 應用程式很有可能會包含許多資源。 請務必瞭解 Android 在裝置上執行時，如何選取應用程式的資源。

Android 會逐一查看下列規則測試來判斷資源的基礎：

- **排除矛盾的限定詞**&ndash; 例如，如果裝置方向是直向，則會拒絕所有的橫向資原始目錄。

- **忽略不支援的限定詞**&ndash; 並非所有的限定詞都可供所有 API 層級使用。 如果資原始目錄包含裝置不支援的辨識符號，則會忽略該資原始目錄。

- **識別下一個最高優先順序的限定詞**&ndash; 參考上表，選取下一個最高優先順序的辨識符號（從上到下）。

- **保留任何資原始目錄的辨識符號**&ndash; 如果有任何符合上述資料表之辨識符號的資原始目錄，請選取下一個最高優先順序辨識符號（從上到下）。

下列流程圖也會說明這些規則：

[![資源流程圖](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

當系統尋找密度特定資源且找不到時，它會嘗試找出其他的密度特定資源並加以調整。 Android 可能不一定會使用預設資源。
例如，在尋找低密度資源且無法使用時，Android 可能會透過預設或中等密度資源來選取資源的高密度版本。 這是因為高密度資源可以調整為0.5 的因素，這會導致較少的可見度問題，而不是向下調整需要0.75 的中等密度資源。

例如，假設有一個應用程式具有下列可繪製的資原始目錄：

```
drawable
drawable-en
drawable-fr-rCA
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

現在，應用程式會在具有下列設定的裝置上執行：

- **地區**設定 &ndash; EN-GB
- **方向**&ndash; 埠
- **螢幕密度**&ndash; hDPI
- **觸控式螢幕類型**&ndash; notouch
- **主要輸入法**&ndash; 12key

一開始，會消除法文資源，因為它們與 `en-GB`的地區設定衝突，讓我們：

```
drawable
drawable-en
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
drawable-port-ldpi
drawable-port-notouch-12key
```

接下來，從上面的限定詞資料表中選取第一個辨識符號： MCC 和 MNC。 沒有包含此辨識符號的資原始目錄，因此會忽略 MCC/MNC 程式碼。

選取下一個辨識符號，也就是 Language。 有符合語言代碼的資源。 所有與 `en` 語言代碼不相符的資原始目錄都會遭到拒絕，因此資源清單現在是：

```
drawable-en-port
drawable-en-notouch-12key
drawable-en-port-ldpi
```

下一個出現的限定詞是針對螢幕方向，因此不符合 `port` 螢幕方向的所有資原始目錄都會被排除：

```
drawable-en-port
drawable-en-port-ldpi
```

[下一步] 是螢幕密度的辨識符號，`ldpi`，這會導致排除一個以上的資原始目錄：

```
drawable-en-port-ldpi
```

由於此程式的結果，Android 會在裝置的資原始目錄 `drawable-en-port-ldpi` 中，使用可繪製的資源。

> [!NOTE]
> 螢幕大小限定詞會為此選取程式提供一個例外狀況。 Android 可能會選取針對比目前裝置所提供的螢幕更小的資源。 例如，大型螢幕裝置可能會使用針對一般大小的螢幕提供的資源。 不過，這不是正確的：相同的大型螢幕裝置不會使用提供給 m3.xlarge 螢幕的資源。 如果 Android 找不到符合指定螢幕大小的資源集，應用程式將會損毀。
