---
title: 在 Visual Studio 中建立檔案之後，我可以在 .IPA 檔案中新增檔案或從中移除檔案嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: e3b535b8624f03e9c832f1719237409cb9ff26c2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939902"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>在 Visual Studio 中建立檔案之後，我可以在 .IPA 檔案中新增檔案或從中移除檔案嗎？

沒錯，這是可行的，但通常會要求您在 `.app` 進行變更之後重新簽署配套。

請注意， `.ipa` 一般使用時不需要修改檔案。 本文僅供資訊參考之用。

## <a name="example-removing-a-file-from-a-ipa-archive"></a>範例：從封存中移除 `.ipa` 檔案

在此範例中，假設 Xamarin. iOS 專案的名稱為 `iPhoneApp1` ，且 `generated session id` 為`cc530d20d6b19da63f6f1c6f67a0a254`

1. 從 Visual Studio 將檔案建立 `.ipa` 為正常。

2. 切換至 Mac 組建主機。

3. 在資料夾中尋找組建 `~/Library/Caches/Xamarin/mtbs/builds` 。 您可以將此路徑貼入搜尋**工具中 > 移 > 移至資料夾**以流覽搜尋工具中的資料夾。 尋找符合專案名稱的資料夾。 在該資料夾中，尋找符合組建之的資料夾 `generated session id` 。 這很可能是具有最近修改時間的子資料夾。

4. 開啟新 `Terminal.app` 視窗。

5. 在 `cd` [Terminal. 應用程式] 視窗中輸入，然後拖曳 & 將資料夾拖放 `generated session id` 到 `Terminal.app` 視窗中：

    ![在搜尋工具中尋找產生的會話識別碼資料夾](modify-ipa-images/session-id-folder.png)

6. 輸入將目錄變更至資料夾的傳回碼 `generated session id` 。

7. `.ipa` `old/` 使用下列命令將檔案解壓縮至暫存資料夾。 `Ad-Hoc` `iPhoneApp1` 視需要針對您的特定專案調整和名稱。

    > ditto-xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. .ipa old/

8. 讓 `Terminal.app` 視窗保持開啟。

9. 從中刪除所需的檔案 `.ipa` 。 您可以使用 Finder 將它們移至垃圾桶，或使用在命令列上將其刪除 `Terminal.app` 。 若要在搜尋工具中查看檔案的內容 `Payload/iPhone` ，請以 ctrl 按一下檔案，然後選取 [**顯示封裝內容**]。

10. 使用與步驟3相同的一般方法，尋找底下 `~/Library/Logs/Xamarin/MonoTouchVS/` 具有專案名稱和名稱的記錄檔 `generated session id` ： ![ 尋找搜尋工具中的專案組建記錄檔](modify-ipa-images/build-log.png)

11. 開啟步驟10中的組建記錄檔，例如，按兩下它。

12. 尋找包含的程式程式碼 `tool /usr/bin/codesign execution started with arguments: -v --force --sign` 。

13. 輸入 `/usr/bin/codesign` 步驟8中的 [Terminal. 應用程式] 視窗。

14. 從步驟12中的行開始複製以開頭的所有引數 `-v` ，並將其貼到 [終端機] 應用程式視窗中。

15. 將最後一個引數變更為 `.app` 位於資料夾內的組合 `old/Payload/` ，然後執行命令。

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. 變更至 `old/` 終端機中的目錄：

    ```bash
    cd old
    ```

17. 使用命令，將目錄的內容壓縮到新檔案中 `.ipa` `zip` 。 您可以隨時變更 `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` 引數，以輸出檔案 `.ipa` ：

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>常見的錯誤訊息

如果您看到 `Invalid Signature. A sealed resource is missing or invalid.` ，這通常表示已在組合中變更某個專案 `.app` ，而且 `.app` 之後未正確重新簽署套件組合。 另請注意，如果您想要建立 `.ipa` 具有散發設定檔的，則_必須_ `.ipa` 使用散發設定檔建立原始的。 否則， `Entitlements.xcent` 將會是不正確的。

若要提供此錯誤可能發生方式的具體範例，如果您 `codesign --verify` 在步驟9之後的終端機視窗中執行下列命令，將會看到錯誤以及錯誤的確切原因：

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

而 App Store 驗證程式會報告類似的錯誤訊息：

> 錯誤 ITMS-90035：「不正確簽章。 密封的資源遺失或無效。 路徑 [iPhoneApp1/iPhoneApp1] 的二進位檔包含不正確簽章。 請確定您已使用散發憑證來簽署應用程式，而不是特定憑證或開發憑證。 確認 Xcode 中的程式碼簽署設定在目標層級是正確的（覆寫專案層級的任何值）。 此外，請確定您上傳的配套是使用 Xcode 中的發行目標所建立，而不是模擬器目標。 如果您確定您的程式碼簽署設定正確，請選擇 Xcode 中的 [全部清除]、刪除搜尋工具中的「組建」目錄，然後重建您的發行目標。 如需詳細資訊，請 [https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html) 參閱
