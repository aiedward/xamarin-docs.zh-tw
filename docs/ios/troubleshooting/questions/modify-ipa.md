---
title: 在 Visual Studio 中建立檔案之後，我可以在 .IPA 檔案中新增檔案或從中移除檔案嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: f63cc544b251ca284a8d087e09f9cbc4dfb3f769
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030962"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>在 Visual Studio 中建立檔案之後，我可以在 .IPA 檔案中新增檔案或從中移除檔案嗎？

沒錯，這是可行的，但通常會要求您在進行變更之後重新簽署 `.app` 配套。

請注意，在一般用途中，不需要修改 `.ipa` 檔案。 本文僅供資訊參考之用。

## <a name="example-removing-a-file-from-a-ipa-archive"></a>範例：從 `.ipa` 封存中移除檔案

在此範例中，假設已 `iPhoneApp1` Xamarin iOS 專案的名稱，而且 `generated session id` `cc530d20d6b19da63f6f1c6f67a0a254`

1. 從 Visual Studio 建立一般 `.ipa` 檔案。

2. 切換至 Mac 組建主機。

3. 在 [`~/Library/Caches/Xamarin/mtbs/builds`] 資料夾中尋找組建。 您可以將此路徑貼入搜尋**工具中 > 移 > 移至資料夾**以流覽搜尋工具中的資料夾。 尋找符合專案名稱的資料夾。 在該資料夾中，尋找符合組建 `generated session id` 的資料夾。 這很可能是具有最近修改時間的子資料夾。

4. 開啟新的 `Terminal.app` 視窗。

5. 在 [終端機應用程式] 視窗中輸入 `cd`，然後將 [`generated session id`] 資料夾 & 拖放到 [`Terminal.app`] 視窗中：

    ![](modify-ipa-images/session-id-folder.png "Locating the generated session id folder in Finder")

6. 輸入要將目錄變更為 `generated session id` 資料夾的傳回碼。

7. 使用下列命令，將 `.ipa` 檔案解壓縮至暫存的 `old/` 資料夾。 視需要調整特定專案的 `Ad-Hoc` 和 `iPhoneApp1` 名稱。

    > ditto-xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. .ipa old/

8. 將 [`Terminal.app`] 視窗保持開啟。

9. 從 `.ipa`中刪除所需的檔案。 您可以使用 Finder 將它們移至垃圾桶，或使用 `Terminal.app`在命令列上刪除它們。 若要在搜尋工具中查看 `Payload/iPhone` 檔案的內容，請按一下檔案，然後選取 [**顯示封裝內容**]。

10. 使用與步驟3相同的一般方法，在 [`~/Library/Logs/Xamarin/MonoTouchVS/`] 下尋找具有專案名稱和 `generated session id` 名稱的記錄檔：![](modify-ipa-images/build-log.png "在搜尋工具中找出專案組建記錄檔")

11. 開啟步驟10中的組建記錄檔，例如，按兩下它。

12. 尋找包含 `tool /usr/bin/codesign execution started with arguments: -v --force --sign`的程式列。

13. 在步驟8中，將 `/usr/bin/codesign` 輸入至 Terminal. 應用程式視窗。

14. 從步驟12的行中，複製以 `-v` 開頭的所有引數，並將其貼到 [終端機] 應用程式視窗中。

15. 將最後一個引數變更為位於 `old/Payload/` 資料夾內的 `.app` 配套，然後執行命令。

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. 變更為 [終端機] 中的 `old/` 目錄：

    ```bash
    cd old
    ```

17. 使用 `zip` 命令，將目錄的內容壓縮至新的 `.ipa` 檔案。 您可以變更 `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` 引數，以便在您想要的任何位置輸出 `.ipa` 檔案：

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>常見的錯誤訊息

如果您看到 `Invalid Signature. A sealed resource is missing or invalid.`，這通常表示 `.app` 組合中的某個專案已變更，而且之後未正確重新簽署 `.app` 套件組合。 另請注意，如果您想要建立具有散發設定檔的 `.ipa`，則_必須_使用散發設定檔建立原始的 `.ipa`。 否則，`Entitlements.xcent` 將會是不正確的。

若要提供此錯誤可能發生方式的具體範例，如果您在步驟9之後的終端機視窗中執行下列 `codesign --verify` 命令，將會看到錯誤以及錯誤的確切原因：

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

而 App Store 驗證程式會報告類似的錯誤訊息：

> 錯誤 ITMS-90035：「不正確簽章。 密封的資源遺失或無效。 路徑 [iPhoneApp1/iPhoneApp1] 的二進位檔包含不正確簽章。 請確定您已使用散發憑證來簽署應用程式，而不是特定憑證或開發憑證。 確認 Xcode 中的程式碼簽署設定在目標層級是正確的（覆寫專案層級的任何值）。 此外，請確定您上傳的配套是使用 Xcode 中的發行目標所建立，而不是模擬器目標。 如果您確定您的程式碼簽署設定正確，請選擇 Xcode 中的 [全部清除]、刪除搜尋工具中的「組建」目錄，然後重建您的發行目標。 如需詳細資訊，請參閱[https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)」
