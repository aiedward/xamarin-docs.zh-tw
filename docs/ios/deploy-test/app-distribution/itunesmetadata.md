---
title: Xamarin.iOS 應用程式中的 iTunesMetadata.plist 檔案
description: 本文所涵蓋的 iTunesMetadata.plist 檔案，可為 iTunes 提供針對測試或企業部署使用臨機操作散發之 iOS 應用程式的相關資訊。
ms.prod: xamarin
ms.assetid: 70676eba-6a99-4a3a-bccc-84359fe9c2c3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 39e1af5edf91ffd6811fa8d221fd56580f3b33da
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784897"
---
# <a name="the-itunesmetadataplist-file-in-xamarinios-apps"></a>Xamarin.iOS 應用程式中的 iTunesMetadata.plist 檔案

_本文所涵蓋的 iTunesMetadata.plist 檔案，可為 iTunes 提供針對測試或企業部署使用臨機操作散發之 iOS 應用程式的相關資訊。_

在 iTunes Connect 中建立 iOS 應用程式時 (無論是要在 iTunes App Store 銷售或免費發行)，開發人員可指定像是應用程式的內容類型、子內容類型、著作權聲明、支援的 iOS 裝置以及所需裝置功能等資訊。 針對透過臨機操作散發提供給測試人員或企業使用者的 iOS 應用程式，則會缺少這項資訊。

若要將缺少的資訊提供給臨機操作散發，可以建立選擇性的 `iTunesMetadata.plist` 檔案，並將其包含在應用程式 IPA 檔案中。 此 plist 檔案為特殊格式的 XML 檔案 (如需詳細資訊，請參閱 Apple 的[屬性清單程式設計指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html) \(英文\))，其中包含定義指定 iOS 應用程式之相關資訊的索引鍵/值組。

<a name="iTunesMetadata_contents" />

## <a name="the-itunesmetadataplist-contents"></a>iTunesMetadata.plist 內容

以下是典型 `iTunesMetadata.plist` 檔案的範例，此檔案可用來針對臨機操作散發定義 iTunes 資訊：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>UIRequiredDeviceCapabilities</key>
    <dict>
        <key>armv7</key>
        <true/>
        <key>front-facing-camera</key>
        <true/>
    </dict>
    <key>artistName</key>
    <string>Company, Inc.</string>
    <key>bundleDisplayName</key>
    <string>App Name</string>
    <key>bundleShortVersionString</key>
    <string>1.5.1</string>
    <key>bundleVersion</key>
    <string>1.5.1</string>
    <key>copyright</key>
    <string>© 2015 Company, Inc.</string>
    <key>drmVersionNumber</key>
    <integer>0</integer>
    <key>fileExtension</key>
    <string>.app</string>
    <key>gameCenterEnabled</key>
    <false/>
    <key>gameCenterEverEnabled</key>
    <false/>
    <key>genre</key>
    <string>Games</string>
    <key>genreId</key>
    <integer>6014</integer>
    <key>itemName</key>
    <string>App Name</string>
    <key>kind</key>
    <string>software</string>
    <key>playlistArtistName</key>
    <string>Company, Inc.</string>
    <key>playlistName</key>
    <string>App Name</string>
    <key>releaseDate</key>
    <string>2015-11-18T03:23:10Z</string>
    <key>s</key>
    <integer>143441</integer>
    <key>softwareIconNeedsShine</key>
    <false/>
    <key>softwareSupportedDeviceIds</key>
    <array>
        <integer>9</integer>
    </array>
    <key>softwareVersionBundleId</key>
    <string>com.company.appid</string>
    <key>subgenres</key>
    <array>
        <dict>
            <key>genre</key>
            <string>Puzzle</string>
            <key>genreId</key>
            <integer>7012</integer>
        </dict>
        <dict>
            <key>genre</key>
            <string>Word</string>
            <key>genreId</key>
            <integer>7019</integer>
        </dict>
    </array>
    <key>versionRestrictions</key>
    <integer>16843008</integer>
</dict>
</plist>

```

各索引鍵的值將在下面詳細說明。

### <a name="uirequireddevicecapabilities"></a>UIRequiredDeviceCapabilities

`UIRequiredDeviceCapabilities` 索引鍵可讓 iTunes 知道 iOS 應用程式所需的裝置專屬功能，以順利將該應用程式安裝於特定的 iOS 裝置上。 它會以功能 (`<key>...</key>`) 的字典 (`<dict>...</dict>`)，以及針對每個功能之布林值的形式提供。 如果某個功能的值為 `true`，則該功能必須存在。 如果是 `false`，則該功能不得存在於裝置上。 例如: 

```xml
<key>UIRequiredDeviceCapabilities</key>
<dict>
    <key>armv7</key>
    <true/>
    <key>front-facing-camera</key>
    <true/>
</dict>
```
指定 iOS 裝置必須支援 ARM7 指令集，並且需配備前方相機，才能在裝置上安裝此應用程式。 如需所允許值的完整清單，請參閱 Apple 的 [UIRequiredDeviceCapabilities](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW3) \(英文\) 文件。

### <a name="artistname-and-playlistartistname"></a>artistName 和 playlistArtistName

使用 `artistName` 和 `playlistArtistName` 索引鍵來定義將會顯示在 iTunes 中，建立該 iOS 應用程式的公司名稱。 範例：

```xml
<key>artistName</key>
<string>Company, Inc.</string>
...
<key>playlistArtistName</key>
<string>Company, Inc.</string>
```

### <a name="bundledisplayname-itemname-and-playlistname"></a>bundleDisplayName、itemName 與 playlistName

使用 `bundleDisplayName`、`itemName` 與 `playlistName` 索引鍵來定義將會顯示在 iTunes 內的 iOS 應用程式名稱。 範例：

```xml
<key>bundleDisplayName</key>
<string>App Name</string>
...
<key>itemName</key>
<string>App Name</string>
...
<key>playlistName</key>
<string>App Name</string>
```

### <a name="bundleshortversionstring-and-bundleversion"></a>bundleShortVersionString 和 bundleVersion

使用 `bundleShortVersionString` 和 `bundleVersion` 索引鍵來定義將會顯示在 iTunes 中的 iOS 應用程式版本號碼。 範例：

```xml
<key>bundleShortVersionString</key>
<string>1.5.1</string>
<key>bundleVersion</key>
<string>1.5.1</string>
```

### <a name="softwareversionbundleid"></a>softwareVersionBundleId

使用 `softwareVersionBundleId` 索引鍵來指定 iOS 應用程式的套件組合識別碼。 範例：

```xml
<key>softwareVersionBundleId</key>
<string>com.company.appid</string>
```

### <a name="copyright"></a>Copyright

使用 `copyright` 索引鍵來定義會顯示在 iTunes 中的著作權聲明。 範例：

```xml
<key>copyright</key>
<string>© 2015 Company, Inc.</string>
```

### <a name="releasedate"></a>releaseDate

使用 `releaseDate` 索引鍵來提供將會顯示在 iTunes 中的 iOS 應用程式發行日期。 範例：

```xml
<key>releaseDate</key>
<string>2015-11-18T03:23:10Z</string>
```

### <a name="softwareiconneedsshine"></a>softwareIconNeedsShine

使用 `softwareIconNeedsShine` 索引鍵來告訴 iTunes 該 iOS 應用程式的圖示是否需要針對 iOS 6 (和更舊版本) 以_發亮醒目提示_的方式呈現。 範例：

```xml
<key>softwareIconNeedsShine</key>
<false/>
```

### <a name="gamecenterenabled-and-gamecentereverenabled"></a>gameCenterEnabled 和 gameCenterEverEnabled

使用 `gameCenterEnabled` 和 `gameCenterEverEnabled` 索引鍵來告訴 iTunes 此 iOS 應用程式支援 Apple 的 Game Center。 範例：

```xml
<key>gameCenterEnabled</key>
<false/>
<key>gameCenterEverEnabled</key>
<false/>
```

### <a name="genre-genreid-and-subgenres"></a>genre、genreId 與 subgenres

使用 `genre` 和 `genreId` 索引鍵來告訴 iTunes 該 iOS 應用程式所屬的內容類型。 範例：

```xml
<key>genre</key>
<string>Games</string>
<key>genreId</key>
<integer>6014</integer>
```

選擇性地使用 `subgenres` 索引鍵來為 iOS 應用程式額外定義最多兩個子內容類型。 範例：

```xml
<key>subgenres</key>
<array>
    <dict>
        <key>genre</key>
        <string>Puzzle</string>
        <key>genreId</key>
        <integer>7012</integer>
    </dict>
    <dict>
        <key>genre</key>
        <string>Word</string>
        <key>genreId</key>
        <integer>7019</integer>
    </dict>
</array>
```

針對 iOS 應用程式，Apple 目前會定義下列內容類型和內容類型識別碼：

[!include[](~/ios/includes/table-appstore.md)]

如需詳細資訊，請參閱 Apple 的[內容類型識別碼附錄](http://www.apple.com/itunes/affiliates/resources/documentation/genre-mapping.html) \(英文\) 文件。

### <a name="softwaresupporteddeviceids"></a>softwareSupportedDeviceIds

使用 `softwareSupportedDeviceIds` 索引鍵來告訴 iTunes 此 iOS 應用程式所支援的 iOS 裝置。 範例：

```xml
<key>softwareSupportedDeviceIds</key>
<array>
    <integer>9</integer>
</array>
```

可用的值如下：

- 1：傳統 iPhone
- 2：iPod Touch
- 4：iPad
- 9：新式 iPhone

### <a name="standard-keys"></a>標準索引鍵

下列索引鍵會包含在 iOS 應用程式所有的 `iTunesMetadata.plist` 檔案中，而且一律擁有相同的值：

```xml
<key>drmVersionNumber</key>
<integer>0</integer>
<key>fileExtension</key>
<string>.app</string>
...
<key>kind</key>
<string>software</string>
...
<key>s</key>
<integer>143441</integer>
...
<key>versionRestrictions</key>
<integer>16843008</integer>
```

<a name="iTunesMetadata_creating" />

## <a name="creating-an-itunesmetadataplist-file"></a>建立 iTunesMetadata.plist 檔案

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 在 Visual Studio for Mac 中處理 `iTunesMetadata.plist` 檔案時，您有兩個選項：

- 使用 Visual Studio for Mac 的視覺化 plist 編輯器來建立和維護檔案。
- 在純文字編輯器中建立和維護檔案。

 這兩個選項都將在下方詳細說明。

### <a name="using-the-visual-plist-editor"></a>使用 Visual Plist 編輯器

請執行下列動作：

1. 在 [方案總管] 中，以滑鼠右鍵按一下 Xamarin.iOS 專案檔，然後選取 [新增] > [新增檔案]
2. 從 [新增檔案] 對話方塊中，選取 [iOS] > [屬性清單]：

    ![](itunesmetadata-images/image01.png "選取 iOS 屬性清單")
3. 輸入 `iTunesMetadata` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 在 [方案總管] 中，按兩下 `iTunesMetadata.plist` 檔案來開啟它以進行編輯：

    ![](itunesmetadata-images/image02.png "iTunesMetadata.plist 編輯器")
5. 按一下綠色的 **+** 來建立新的項目，並輸入 `UIRequiredDeviceCapabilities` 作為索引鍵名稱：

    ![](itunesmetadata-images/image03.png "建立新的項目，並輸入 UIRequiredDeviceCapabilities 作為索引鍵名稱")
6. 按一下 [字串] 值類型，然後從快顯清單中選取 [字典]：

    ![](itunesmetadata-images/image04.png "從快顯清單中選取 [字典]")
7. 按一下屬性名稱左邊的向下箭頭以顯示字典的項目：

    ![](itunesmetadata-images/image05.png "顯示字典項目")
8. 按一下 [新增項目] 文字，然後按一下綠色的 **+** 來將項目新增至字典：

    ![](itunesmetadata-images/image06.png "將項目新增至字典")
9. 輸入 `armv7` 作為索引鍵名稱、選取一種 [布林值] 類型，然後輸入 **Yes** 作為值：

    ![](itunesmetadata-images/image07.png "輸入 armv7 作為索引鍵名稱，選取一種 [布林值] 類型，然後輸入 Yes 作為值")
10. 重複上述步驟，直到您已在 `iTunesMetadata.plist` 檔案中填入所需的所有索引鍵/值組為止 (如需詳細資料，請參閱 [iTunesMetadata.plist 內容](#iTunesMetadata_contents)一節)。

11. 儲存對 plist 檔案所做的變更。

### <a name="using-a-plain-text-editor"></a>使用純文字編輯器

請執行下列動作：

1. 在純文字編輯器中，建立新的文字檔，並將其命名為 `iTunesMetadata.plist`。
2. 從上方的 [iTunesMetadata.plist 內容](#iTunesMetadata_contents)小節，複製範例內容。
3. 將內容貼到檔案中，並視需要加以編輯。
4. 儲存檔案，並返回 Visual Studio for Mac。
5. 在 [方案總管] 中，以滑鼠右鍵按一下 Xamarin.iOS 專案檔，然後選取 [新增] > [現有的檔案]。
6. 在 [開啟檔案] 對話方塊中，選取上面所建立的 `iTunesMetadata.plist` 檔案，然後按一下 [確定] 按鈕。
7. 將此檔案的 [建置動作] 保持設定為 [無]。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

適用於 Visual Studio 的 Xamarin 外掛程式僅支援 `Info.plist` 和 `Entitlement.plist` 檔案的視覺化編輯器，因此您必須在標準文字編輯器中建立 `iTunesMetadata.plist` 檔案，並將它手動包含到 Xamarin.iOS 專案中。

請執行下列動作：

1. 在純文字編輯器中，建立新的文字檔，並將其命名為 `iTunesMetadata.plist`。
2. 從上方的 [iTunesMetadata.plist 內容](#iTunesMetadata_contents)小節，複製範例內容。
3. 將內容貼到檔案中，並視需要加以編輯。
4. 儲存檔案，並返回 Visual Studio。
5. 在 [方案總管] 中，以滑鼠右鍵按一下 Xamarin.iOS 專案檔，然後選取 [新增] > [現有的檔案]。
6. 在 [開啟檔案] 對話方塊中，選取您在上面建立的 `iTunesMetadata.plist` 檔案，然後按一下 [開啟] 按鈕。
7. 將此檔案的 [建置動作] 保持設定為 [無]。

-----

之後，當您準備在 IDE 中建置 IPA 時，請選取這個 `iTunesMetadata.plist` 檔案。

## <a name="summary"></a>總結

本文已涵蓋 `iTunesMetadata.plist` 檔案，此檔案可用來告訴 iTunes 有關以臨機操作傳遞之 iOS 應用程式的相關資訊。 本文也討論 plist 檔案中的標準索引鍵，以及如何在 Visual Studio 和 Visual Studio for Mac 中建立和維護該檔案。

## <a name="related-links"></a>相關連結

- [散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [發行至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [內部作業散發](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [臨機操作散發](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [IPA 支援](~/ios/deploy-test/app-distribution/ipa-support.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
