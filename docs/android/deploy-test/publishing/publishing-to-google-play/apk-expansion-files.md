---
title: APK 擴充檔
ms.prod: xamarin
ms.assetid: DB7E38E8-3C4E-5191-27EA-22DE63044FE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 712322435614966348fc5c10cabf724870c307e4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021286"
---
# <a name="apk-expansion-files"></a>APK 擴充檔

有些應用程式 (例如某些遊戲) 所需的資源和資產超出 Google Play 所強制之 Android 應用程式大小上限所能提供的範圍。 此限制取決於您 APK 的目標 Android 版本：

- APK 的目標如果是 Android 4.0 或更新版本 (API 層級 14 或更高)，則為 100 MB。
- APK 的目標如果是 Android 3.2 或更舊版本 (API 層級 13 或更高)，則為 50 MB。

為了克服這項限制，Google Play 會隨著 APK 裝載並散發兩個「擴充檔」，讓應用程式能夠間接跨越這項限制。 

在大多數裝置上，安裝應用程式時，擴充檔會隨著 APK 一起下載並儲存至裝置上的共用儲存體位置 (SD 記憶卡或 USB 可掛接分割區)。 在一些較舊的裝置上，擴充檔可能不會自動隨著 APK 一起安裝。 在這些情況下，應用程式必須包含會在使用者第一次執行應用程式時下載擴充檔的程式碼。

擴充檔會被視為「不透明二進位 Blob (obb)」，且大小上限可達 2 GB。 在下載這些檔案之後，Android 不會對它們執行任何特殊處理 &ndash; 這些檔案可以採用適合應用程式的任何格式。 在概念上，對擴充檔建議的方法如下：

- **主要擴充**  &ndash; 此檔案是 APK 大小限制所無法容納之資源和資產的主要擴充檔。 主要擴充檔應該包含應用程式所需且應該極少更新的主要資產。
- **修補程式擴充**  &ndash; 這是用於對主要擴充檔的小規模更新。 此檔案可供更新。 應用程式需負責從此檔案執行所有必要的修補或更新。

擴充檔必須與 APK 同時上傳。
Google Play 不允許將擴充檔上傳到現有的 APK，或是更新現有的 APK。 如果必須更新擴充檔，則必須上傳已更新 `versionCode` 的新 APK。

## <a name="expansion-file-storage"></a>擴充檔儲存體

將檔案下載至裝置時，檔案會儲存在 **_shared-store_/Android/obb/package-name** 中：

- **_shared-store_** &ndash; 這是 `Android.OS.Environment.ExternalStorageDirectory` 所指定的目錄。
- **_package-name_** &ndash; 這是應用程式的 Java 式套件名稱。

下載擴充檔之後，就不應該將擴充檔移動、更改、重新命名或從其在裝置上的位置中刪除。 如果這麼做，將會導致重新下載這些擴充檔，並將舊檔案刪除。 此外，擴充檔目錄應該只包含擴充套件檔案。

擴充檔不會為其內容提供任何安全性或保護 &ndash; 其他應用程式或使用者或可存取儲存在共用儲存體上的任何檔案。

如果必須將擴充檔解壓縮，則應該將解壓縮的檔案儲存在個別的目錄中，例如 `Android.OS.Environment.ExternalStorageDirectory` 的目錄中。

有一個可以替代從擴充檔將檔案解壓縮的方式，就是直接從擴充檔讀取資產或資源。 擴充檔就是一個可與適當的 `ContentProvider` 搭配使用的 ZIP 檔案。 [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) 包含一個組件 [System.IO.Compression.Zip](https://github.com/mattleibow/Android.Play.ExpansionLibrary/tree/master/System.IO.Compression.Zip)，此組件包含可允許對某些媒體檔案進行直接檔案存取的 `ContentProvider`。 如果媒體檔案被封裝成 ZIP 檔案，則媒體播放呼叫可直接使用 ZIP 中的檔案，而無須將 ZIP 檔案解壓縮。 將媒體檔案新增至 ZIP 檔案時，不應該將媒體檔案壓縮。 

### <a name="filename-format"></a>FileName 格式

下載擴充檔時，Google Play 會使用下列配置來為擴充檔命名：

```
[main|patch].<expansion-version>.<package-name>.obb
```

此配置的三個元件為：

- `main` 或 `patch` &ndash; 這會指定此擴充檔是主要擴充檔還是修補擴充檔。 每種擴充檔只能有一個。
- `<expansion-version>`  &ndash; 這是一個整數，其與檔案第一個建立關聯之 APK 的 `versionCode` 相符。
- `<package-name>` &ndash; 這是應用程式的 Java 式套件名稱。

例如，如果 APK 版本為 21，而套件名稱為 `mono.samples.helloworld`，則主要擴充檔將會命名為 **main.21.mono.samples.helloworld**。

## <a name="download-process"></a>下載程序

從 Google Play 安裝應用程式時，擴充檔應該隨著 APK 一起下載並儲存。 在某些情況下，這可能不會發生，或擴充檔可能已被刪除。 為了處理此情況，應用程式必須檢查以了解擴充檔是否存在，然後如有必要，就會下載擴充檔。 以下流程圖顯示此程序的建議工作流程：

[![APK 擴充檔流程圖](apk-expansion-files-images/apkexpansion.png)](apk-expansion-files-images/apkexpansion.png#lightbox)

當應用程式啟動時，它應該檢查以了解目前的裝置上是否有適當的擴充檔。 如果沒有，應用程式就必須從 Google Play 的[應用程式授權](https://developer.android.com/google/play/licensing/index.html)提出要求。 進行這項檢查時，是藉由使用「授權驗證程式庫」(LVL)來進行，且不論是免費還是授權的應用程式都必須進行此檢查。 LVL 主要是供付費應用程式用來強制執行授權限制。 不過，Google 已將 LVL 延伸，讓它也可以與擴充程式庫搭配使用。 免費應用程式必須執行 LVL 檢查，但可以忽略授權限制。 LVL 要求會負責提供應用程式所需的下列擴充檔相關資訊： 

- **檔案大小** &ndash; 擴充檔的檔案大小會作為檢查的一部分，用來判斷是否已經下載正確的擴充檔。
- **檔案名稱** &ndash; 這是要用來儲存擴充套件的檔案名稱 (目前裝置上)。
- **下載 URL**  &ndash; 應該用來下載擴充套件的 URL。 這是每個下載的獨特 URL，並且會在提供之後很快就失效。

執行 LVL 檢查之後，應用程式應該會下載擴充檔，其中會將下列幾點納入作為執行下載過程中的考量：

- 裝置可能沒有足夠的空間來儲存擴充檔。
- 如果無 Wi-Fi 可用，應該允許使用者暫停或取消下載，以避免衍生不必要的數據費用。
- 擴充檔的下載會在背景進行，以避免妨礙使用者互動。
- 雖然下載是在背景進行，但應該顯示進度列指示器。
- 在下載期間發生的錯誤會依正常程序處理並且可復原。

## <a name="architectural-overview"></a>架構概觀

當主要活動開始時，它會檢查以了解是否已下載擴充檔。 如果已下載這些檔案，就必須檢查其是否有效。

如果尚未下載擴充檔，或目前的檔案無效，則必須下載新的擴充檔。 在建立應用程式的過程中會一併建立一個繫結的服務。 當應用程式的主要活動開始時，它會使用該繫結的服務來對照「Google 授權」服務執行檢查，以找出擴充檔名稱和所要下載之檔案的 URL。 接著，繫結的服務就會在背景執行緒上下載檔案。

為了簡化將擴充檔整合至應用程式時所需的工作，Google 以 Java 建立了數個程式庫。 這些程式庫包括：

- **下載程式程式庫**  &ndash; 這是可簡化將擴充檔整合至應用程式時所需工作的程式庫。 此程式庫會在背景服務中下載擴充檔、顯示使用者通知、處理網路連線問題、繼續執行下載等。
- **授權驗證程式庫 (LVL)**  &ndash; 此程式庫會對「應用程式授權」服務發出呼叫，並處理對此服務發出的呼叫。 它也可用來執行授權檢查，以了解應用程式是否已取得在裝置上使用的授權。
- **APK 擴充 ZIP 程式庫 (選用)**  &ndash; 如果擴充檔在 ZIP 檔案中，此程式庫將作為內容提供者，並允許應用程式直接從 ZIP 檔案讀取資源和資產，而無須將 ZIP 檔案展開。

這些程式庫已移植到 C#，並可在 Apache 2.0 授權下使用。 若要將擴充檔快速整合至現有的應用程式，可以將這些程式庫新增至現有的 Xamarin.Android 應用程式。 您可以從 GitHub 上的 [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) \(英文\) 取得程式碼。
