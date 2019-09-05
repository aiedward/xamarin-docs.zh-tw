---
title: 在 Visual Studio 中建立檔案之後，我可以在 .IPA 檔案中新增檔案或從中移除檔案嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: d1546b83304d8c66f7433bd77c5ebecc9dc95aaa
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278474"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>在 Visual Studio 中建立檔案之後，我可以在 .IPA 檔案中新增檔案或從中移除檔案嗎？

沒錯，這是可行的，但通常會要求您在進行變更之後`.app`重新簽署配套。

請注意，一般`.ipa`使用時不需要修改檔案。 本文僅供資訊參考之用。

## <a name="example-removing-a-file-from-a-ipa-archive"></a>範例：從封存中`.ipa`移除檔案

在此範例中，假設 Xamarin. iOS 專案的名稱為`iPhoneApp1` `generated session id` ，且為`cc530d20d6b19da63f6f1c6f67a0a254`

1. 從 Visual Studio `.ipa`將檔案建立為正常。

2. 切換至 Mac 組建主機。

3. 在`~/Library/Caches/Xamarin/mtbs/builds`資料夾中尋找組建。 您可以將此路徑貼入搜尋**工具中 > 移 > 移至資料夾**以流覽搜尋工具中的資料夾。 尋找符合專案名稱的資料夾。 在該資料夾中，尋找符合`generated session id`組建之的資料夾。 這很可能是具有最近修改時間的子資料夾。

4. 開啟新`Terminal.app`視窗。

5. 在`cd` [Terminal. 應用程式] 視窗中輸入，然後拖曳 & `generated session id`將資料夾拖`Terminal.app`放到視窗中：

    ![](modify-ipa-images/session-id-folder.png "在搜尋工具中尋找產生的會話識別碼資料夾")

6. 輸入將目錄`generated session id`變更至資料夾的傳回碼。

7. 使用下列命令將檔案解壓縮`old/`至暫存資料夾。 `.ipa` 視需要針對`iPhoneApp1`您的特定專案調整和名稱。`Ad-Hoc`

    > ditto-xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. .ipa old/

8. `Terminal.app`讓視窗保持開啟。

9. 從中`.ipa`刪除所需的檔案。 您可以使用 Finder 將它們移至垃圾桶，或使用`Terminal.app`在命令列上將其刪除。 若要在搜尋工具中`Payload/iPhone`查看檔案的內容，請以 ctrl 按一下檔案，然後選取 [**顯示封裝內容**]。

10. 使用與步驟3相同的一般方法，尋找底下的記錄`~/Library/Logs/Xamarin/MonoTouchVS/`檔，其中包含專案名稱`generated session id`和名稱：![](modify-ipa-images/build-log.png "在搜尋工具中找出專案組建記錄檔")

11. 開啟步驟10中的組建記錄檔，例如，按兩下它。

12. 尋找包含`tool /usr/bin/codesign execution started with arguments: -v --force --sign`的程式程式碼。

13. 輸入`/usr/bin/codesign`步驟8中的 [Terminal. 應用程式] 視窗。

14. 從步驟12中的行開始`-v`複製以開頭的所有引數，並將其貼到 [終端機] 應用程式視窗中。

15. 將最後一個引數變更為`.app`位於`old/Payload/`資料夾內的組合，然後執行命令。

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. 變更至終端`old/`機中的目錄：

    ```bash
    cd old
    ```

17. 使用命令， `zip`將目錄的內容壓縮到新`.ipa`檔案中。 您可以隨時變更`"$HOME/Desktop/iPhoneApp1-1.0.ipa"`引數，以`.ipa`輸出檔案：

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>常見的錯誤訊息

如果您看到`Invalid Signature. A sealed resource is missing or invalid.`，這通常表示已在組合`.app`中變更某個專案，而且`.app`之後未正確重新簽署套件組合。 另請注意，如果您想要建立`.ipa`具有散發設定檔的，則_必須_使用散發`.ipa`設定檔建立原始的。 否則， `Entitlements.xcent`將會是不正確的。

若要提供此錯誤可能發生方式的具體範例，如果您在步驟 9 `codesign --verify`之後的終端機視窗中執行下列命令，將會看到錯誤以及錯誤的確切原因：

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

而 App Store 驗證程式會報告類似的錯誤訊息：

> 錯誤 ITMS-90035：「不正確簽章。 密封的資源遺失或無效。 路徑 [iPhoneApp1/iPhoneApp1] 的二進位檔包含不正確簽章。 請確定您已使用散發憑證來簽署應用程式，而不是特定憑證或開發憑證。 確認 Xcode 中的程式碼簽署設定在目標層級是正確的（覆寫專案層級的任何值）。 此外，請確定您上傳的配套是使用 Xcode 中的發行目標所建立，而不是模擬器目標。 如果您確定您的程式碼簽署設定正確，請選擇 Xcode 中的 [全部清除]、刪除搜尋工具中的「組建」目錄，然後重建您的發行目標。 如需詳細資訊，請[https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)參閱
