---
title: 建置特定 ABI 的 APK
description: 本文將討論如何使用 Xamarin.Android 建置瞄準單一 ABI 的 APK。
ms.prod: xamarin
ms.assetid: D21B195B-4530-4EB2-8704-5C4349A2CDD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: b34ac82cf240b892e60707d76c82da64ff232a20
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827421"
---
# <a name="building-abi-specific-apks"></a>建置特定 ABI 的 APK

_本文將討論如何使用 Xamarin.Android 建置瞄準單一 ABI 的 APK。_



## <a name="overview"></a>總覽

在某些情況下，讓應用程式具備多重 APK，其中每個 APK 都使用相同的金鑰儲存區簽署並共用相同的套件名稱，但針對特定裝置或 Android 組態進行編譯，可能會比較有利。 這並非建議的方法 - 只有一個 APK 並讓其支援多個裝置和組態通常會比較簡單。 在某些情況下，建立多重 APK 會很有用，例如：

-  **縮小 APK 的大小** - Google Play 針對 APK 檔案具有 100 MB 的大小限制。 建立特定裝置的 APK 可以縮小 APK 的大小，因為您只需要為應用程式提供資產和資源的子集。

-  **支援不同的 CPU 架構** - 若您的應用程式具有適用於特定 CPU 的共用程式庫，您可以為該 CPU 僅散發共用程式庫。


多重 APK 可能會使散發變得更複雜 - 由 Google Play 提及的一項問題。 Google Play 會根據應用程式的版本代碼和其他與 **AndroidManifest.XML** 包含一起的中繼資料，來確保傳遞到裝置的是正確的 APK。 如需 Google Play 針對應用程式支援多重 APK 之方式的特定詳細資料，請查閱 [Google Play 的多重 APK 支援相關文件](https://developer.android.com/google/play/publishing/multiple-apks.html)。

本指南會說明如何編寫指令碼為 Xamarin.Android 應用程式建置多重 APK，並使每個 APK 都瞄準特定的 ABI。 它涵蓋了下列主題：

1.  為 APK 建立唯一的「版本代碼」  。
1.  建立會用於此 APK 的 **AndroidManifest.XML** 暫存版本。
1.  使用先前步驟中的 **AndroidManifest.XML** 來建置應用程式。
1.  透過簽署及 Zipalign 來準備 APK。


本指南的最後會提供展示如何使用 [Rake](http://martinfowler.com/articles/rake.html) 來為這些步驟撰寫指令碼的逐步解說。



### <a name="creating-the-version-code-for-the-apk"></a>為 APK 建立版本代碼

Google 建議針對使用七位數版本代碼的版本代碼使用特定的演算法 (請參閱 [Multiple APK Support](https://developer.android.com/google/play/publishing/multiple-apks.html) (多重 APK 支援) 文件中的＜Using a version code scheme＞ *U* (使用版本代碼配置) 一節。
藉由將此版本代碼配置展開至八位數，您可以在版本代碼中包含一些 ABI 資訊，以確保 Google Play 將正確的 APK 散發到裝置。 下列清單說明這個八位數版本代碼的格式 (已由左至右編製索引)：

-   **索引 0** (下列圖表中的紅色) &ndash; ABI 的整數：
    -   1 &ndash; `armeabi`
    -   2 &ndash; `armeabi-v7a`
    -   6 &ndash; `x86`

-   **索引 1-2** (下列圖表中的橘色) &ndash; 應用程式支援的最低 API 層級。

-   **索引 3-4** (下列圖表中的藍色) &ndash; 支援的螢幕大小：
    -   1 &ndash; small (小型)
    -   2 &ndash; normal (一般)
    -   3 &ndash; large (大型)
    -   4 &ndash; xlarge (超大型)

-   **索引 5-7** (下列圖表中的綠色) &ndash; 版本代碼的唯一號碼。 
    這是由開發人員所設定。 它應在每一次應用程式公開發行後增加。

下列圖表說明在上述清單中描述之每一個代碼的位置：

[![八位數版本代碼格式的圖表，以顏色進行編碼](abi-specific-apks-images/image00.png)](abi-specific-apks-images/image00.png#lightbox)


Google Play 會根據 `versionCode` 及 APK 組態來確保傳遞到裝置的是正確的 APK。 具有最高版本代碼的 APK 會傳遞到裝置。 例如，應用程式可能會有三個 APK，並具有下列版本代碼：

-  11413456 - ABI 為 `armeabi`，瞄準的 API 層級為 14，適用於小型至大型螢幕，版本號碼為 456。
-  21423456 - ABI 為 `armeabi-v7a`，瞄準的 API 層級為 14，適用於一般 &amp; 大型螢幕，版本號碼為 456。
-  61423456 - ABI 為 `x86`，瞄準的 API 層級為 14，適用於一般 &amp; 大型螢幕，版本號碼為 456。

繼續進行此範例，假設針對 `armeabi-v7a` 修正了一個 Bug)。 應用程式版本會增加到 457，並且新的 APK 會將 `android:versionCode` 設為 21423457 進行建置。 `armeabi` 及 `x86` 版本的版本代碼維持不變。

現在，想像 x86 版本接收到一些針對更新版本 API (API 層級 19) 的更新或 Bug 修正，使其成為應用程式的版本 500。 新的 `versionCode` 會變更為 61923500，armeabi/armeabi-v7a 的版本代碼則維持不變。 此時，版本代碼將為：

-  11413456 - ABI 為 `armeabi`，瞄準的 API 層級為 14，適用於小型至大型螢幕，版本名稱為 456。
-  21423457 - ABI 為 `armeabi-v7a`，瞄準的 API 層級為 14，適用於一般 &amp; 大型螢幕，版本名稱為 457。
-  61923500 - ABI 為 `x86`，瞄準的 API 層級為 19，適用於一般 &amp; 大型螢幕，版本名稱為 500。


手動維持這些版本代碼對開發人員來說將會是沉重的負擔。 計算正確 `android:versionCode` 及建置 APK 的程序應該自動化。
本文的最後的逐步解說會說明如何進行此動作。


### <a name="create-a-temporary-androidmanifestxml"></a>建立暫存 AndroidManifest.XML

雖然並非絕對必要，但為每個 ABI 建立暫存的 **AndroidManifest.XML** 可協助防止資訊從一個 APK 洩漏到另一個 APK 時可能發生的問題。 例如，讓每個 APK 都有唯一的 `android:versionCode` 屬性是非常重要的。

執行此動作的方法取決於牽涉其中的指令碼系統，但通常會牽涉取得在開發過程中使用的 Android 資訊清單複本，請修改它然後在建置過程中使用該修改過後的資訊清單。



### <a name="compiling-the-apk"></a>編譯 APK

針對每個 ABI 建置 APK 可透過使用 `xbuild` 或 `msbuild` 來達成，如下列範例命令列所示：

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:Package /p:AndroidSupportedAbis=<TARGET_ABI> /p:IntermediateOutputPath=obj.<TARGET_ABI>/ /p:AndroidManifest=<PATH_TO_ANDROIDMANIFEST.XML> /p:OutputPath=bin.<TARGET_ABI> /p:Configuration=Release <CSPROJ FILE>
```

下列清單說明每個命令列參數：

-   `/t:Package` &ndash; 建立使用偵錯金鑰儲存區簽署的 Android APK

-   `/p:AndroidSupportedAbis=<TARGET_ABI>` &ndash; 要瞄準的 ABI。 必須為 `armeabi`、`armeabi-v7a`，或 `x86` 中的其中一個

-   `/p:IntermediateOutputPath=obj.<TARGET_ABI>/` &ndash; 此為保有在建置時建立之中繼檔案的目錄。 若有必要，Xamarin.Android 會根據 ABI 的名稱建立目錄，例如 `obj.armeabi-v7a`。 通常建議針對每個 ABI 各自使用一個資料夾，因為這可以防止發生檔案從其中一個組建「洩漏」到另一個組建的問題。 請注意，這個值會使用目錄分隔符號來終止 (若為 OS X 則為 `/`)。

-   `/p:AndroidManifest` &ndash; 此屬性指定在建置時使用之 **AndroidManifest.XML** 的路徑。

-   `/p:OutputPath=bin.<TARGET_ABI>` &ndash; 此為儲存最終 APK 的目錄。 Xamarin.Android 會根據 ABI 的名稱來建立目錄，例如 `bin.armeabi-v7a`。

-   `/p:Configuration=Release` &ndash; 執行 APK 的發行組建。 偵錯組建無法上傳至 Google Play。

-   `<CS_PROJ FILE>` &ndash; 此為 Xamarin.Android `.csproj` 檔案的路徑。



### <a name="sign-and-zipalign-the-apk"></a>針對 APK 進行簽署及 Zipalign

在透過 Google Play 散發 APK 之前，必須先簽署 APK。 這可透過使用 `jarsigner` 應用程式來執行，其為 Java 開發人員套件的一部分。 下列命令列展示如何在命令列中使用 `jarsigner`：

```shell
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore <PATH/TO/KEYSTORE> -storepass <PASSWORD> -signedjar <PATH/FOR/SIGNED_JAR> <PATH/FOR/JAR/TO/SIGN> <NAME_OF_KEY_IN_KEYSTORE>
```

所有的 Xamarin.Android 應用程式都必須經過 Zipalign，才能在裝置上執行。 此為要使用之命令列的格式：

```shell
zipalign -f -v 4 <SIGNED_APK_TO_ZIPALIGN> <PATH/TO/ZIP_ALIGNED.APK>
```


## <a name="automating-apk-creation-with-rake"></a>使用 Rake 自動建立 APK

範例專案 [OneABIPerAPK](https://github.com/xamarin/monodroid-samples/tree/master/OneABIPerAPK) 是一個簡單的 Android 專案，其將展示如何計算特定 ABI 的版本號碼，並為下列每個 ABI 建置共三個 APK：

-  armeabi
-  armeabi-v7a
-  x86


範例專案中的 [rakefile](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb) 會執行先前章節中描述的每個步驟：

1. 為 APK [建立 android:versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L30)。

1. [將 android:versionCode 寫入](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L55)該 APK 的自訂 **AndroidManifest.XML**。

1. [編譯 Xamarin.Android 專案的發行組建](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L63)，瞄準單一目標 ABI，並使用先前步驟中建立的 **AndroidManifest.XML**。

1. 使用生產環境金鑰儲存區[簽署 APK](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L66)。

1. 針對 APK 進行 [Zipalign](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L67)。


若要建置應用程式的所有 APK，請從命令列執行 `build` Rake 工作：

```shell
$ rake build
==> Building an APK for ABI armeabi with ./Properties/AndroidManifest.xml.armeabi, android:versionCode = 10814120.
==> Building an APK for ABI x86 with ./Properties/AndroidManifest.xml.x86, android:versionCode = 60814120.
==> Building an APK for ABI armeabi-v7a with ./Properties/AndroidManifest.xml.armeabi-v7a, android:versionCode = 20814120.
```

Rake 工作完成之後，便會有三個 `bin` 資料夾，以及一個 `xamarin.helloworld.apk` 檔案。 下一個螢幕擷取畫面顯示每個資料夾及其內容：

[![包含 xamarin.helloworld.apk 的特定平台資料夾位置](abi-specific-apks-images/image01.png)](abi-specific-apks-images/image01.png#lightbox)


> [!NOTE]
> 本指南中描述的建置過程可在許多不同建置系統的其中一個內實作。 雖然我們並未預先撰寫範例，但它也可以使用 [Powershell](http://technet.microsoft.com/scriptcenter/powershell.aspx) / [psake](https://github.com/psake/psake) 或 [Fake](http://fsharp.github.io/FAKE/) 來進行。


## <a name="summary"></a>總結

本指南提供如何建立瞄準特定 ABI 之 Android APK 的一些建議。 它也討論建立 `android:versionCodes` 的一個可能配置，其可識別 APK 適用的 CPU 架構。 逐步解說則包含使用 Rake 為建置撰寫指令碼的範例專案。



## <a name="related-links"></a>相關連結

- [OneABIPerAPK (範例)](https://developer.xamarin.com/samples/monodroid/OneABIPerAPK/)
- [發行應用程式](~/android/deploy-test/publishing/index.md)
- [Google Play 的多重 APK 支援](https://developer.android.com/google/play/publishing/multiple-apks.html) ()
