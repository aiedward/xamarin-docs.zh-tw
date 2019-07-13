---
title: 我可以將檔案加入或移除的 IPA 檔案中的檔案之後在 Visual Studio 中建立, 嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 047ee06522d4b2c07937e0e1bd9985248a164f01
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865024"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>我可以將檔案加入或移除的 IPA 檔案中的檔案之後在 Visual Studio 中建立, 嗎？

是的但它通常需要重新簽署`.app`進行變更之後的套件組合。

請注意，修改`.ipa`檔案不需要在正常使用。 這篇文章只供僅供參考之用。

## <a name="example-removing-a-file-from-a-ipa-archive"></a>範例： 移除檔案從`.ipa`封存

此範例假設的 Xamarin.iOS 專案名稱`iPhoneApp1`而`generated session id`是 `cc530d20d6b19da63f6f1c6f67a0a254`

1. 建置`.ipa`檔案正常從 Visual Studio。

2. 切換到 Mac 組建主機。

3. 尋找組建`~/Library/Caches/Xamarin/mtbs/builds`資料夾。 您可以貼上到此路徑**Finder > 移 > 移至資料夾**瀏覽在 Finder 中的資料夾。 尋找符合專案名稱的資料夾。 在該資料夾中，尋找符合的資料夾`generated session id`的組建。 這很可能會有最新的修改時間的子資料夾。

4. 開啟新`Terminal.app`視窗。

5. 型別`cd`Terminal.app 視窗中，然後拖曳 & 拖放到`generated session id`資料夾`Terminal.app`視窗：

    ![](modify-ipa-images/session-id-folder.png "在 Finder 中尋找產生的工作階段識別碼資料夾")

6. 輸入傳回的索引鍵，將目錄變更至`generated session id`資料夾。

7. 將解壓縮`.ipa`暫存檔案`old/`資料夾使用下列命令。 調整`Ad-Hoc`和`iPhoneApp1`視需要針對特定專案的名稱。

    > ditto -xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa old/

8. 保留`Terminal.app`視窗中開啟。

9. 刪除所需的檔案，從`.ipa`。 您可以將它們移至 資源回收筒使用搜尋工具，或刪除它們在命令列使用`Terminal.app`。 若要檢視的內容`Payload/iPhone`檔案中搜尋工具，Control + 按一下檔案，然後選取**顯示封裝內容**。

10. 使用相同的一般方法，如步驟 3 中，找到記錄檔底下`~/Library/Logs/Xamarin/MonoTouchVS/`具有這兩個專案名稱和`generated session id`名稱中：![](modify-ipa-images/build-log.png "在 Finder 中找出專案組建記錄檔")

11. 組建記錄檔來自步驟 10，例如按兩下開啟它。

12. 尋找包含`tool /usr/bin/codesign execution started with arguments: -v --force --sign`。

13. 型別`/usr/bin/codesign`到步驟 8 中的 [Terminal.app] 視窗。

14. 複製所有引數開頭`-v`從列在步驟 12，並貼到 [Terminal.app] 視窗。

15. 變更為最後一個引數`.app`配套位於`old/Payload/`資料夾，然後再執行命令。

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. 將變更`old/`目錄中終端機：

    ```bash
    cd old
    ```

17. 壓縮至新目錄的內容`.ipa`檔案中，使用`zip`命令。 您可以變更`"$HOME/Desktop/iPhoneApp1-1.0.ipa"`輸出的引數`.ipa`每當您想要的檔案：

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>常見的錯誤訊息

如果您看到`Invalid Signature. A sealed resource is missing or invalid.`，這通常表示的項目已變更內`.app`組合，而且`.app`組合未正確重新簽署之後。 也請注意，如果您想要建立`.ipa`發佈設定檔中，您_必須_建置原始`.ipa`與發佈設定檔。 否則`Entitlements.xcent`可能會不正確。

如果您執行下列命令，提供具體的範例的方式可能會發生此錯誤，`codesign --verify`命令在步驟 9 之後終端機視窗中，您會看到錯誤的精確的原因以及錯誤：

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

和 App Store 驗證程序會報告類似的錯誤訊息：

> 錯誤 ITMS-90035:「 無效的簽章。 密封的資源是遺漏或無效。 二進位檔位於路徑 [iPhoneApp1.app/iPhoneApp1] 包含無效的簽章。 請確定您已簽署您的應用程式，以發佈憑證，而不臨機操作的憑證或開發憑證。 請確認在目標層級 （會覆寫專案層級的任何值），在 Xcode 中的程式碼簽署設定正確。 此外，請確定您要上傳套件組合是使用在 Xcode 中，不是模擬器目標的發行目標。 如果您確定您的程式碼簽署的設定正確無誤，在 Xcode 中選擇 [全新全部]，刪除 [建置] 目錄中搜尋工具中後重建發行目標。 如需詳細資訊，請參閱[ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
