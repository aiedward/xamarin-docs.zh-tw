---
title: 與 Xcode 的 Xamarin.iOS 應用程式偵錯
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5FDDEDB3-AEB9-4D9C-9F7B-FEFAA9AF0031
ms.technology: xamarin-ios
author: migueldeicaza
ms.author: miguel
ms.date: 02/22/2018
ms.openlocfilehash: e0127d4b24236d350e5fa967110316544c320d0f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422437"
---
# <a name="debugging-xamarinios-apps-with-xcode"></a>與 Xcode 的 Xamarin.iOS 應用程式偵錯

可能情況下，您要使用 Xcode 偵錯您的 Xamarin.iOS 應用程式的某些部分。 雖然您無法偵錯.NET 程式碼中的，您仍然能夠偵錯原生程式碼，並在 Xcode 中使用某些原生視覺化檢視。

## <a name="walkthrough"></a>逐步解說

沒有內建支援 Xcode 中的偵錯 Visual Studio for Mac 時，您可以使用下列步驟來達成此目的：

1. 建立 Xcode 的 iOS 應用程式與 Xamarin 應用程式中的同一個套件組合識別碼。
   
    - 您可以找到您的 Xamarin.iOS 專案套件組合識別碼 %installationdirectory **Info.plist**檔案：

        ![編輯 Info.plist](debugging-with-xcode-images/vsmac-infoplist.png "編輯 Info.list")

    - 在 Xcode 中，您會設定套件組合識別碼，建立您的專案時，也可以在專案中選取您的目標：

        ![在 Xcode 中設定 Bundle Identifier](debugging-with-xcode-images/xcode-bundle.png "在 Xcode 中設定套件組合識別碼")

2. 將 Xcode 專案變更為等候啟動，而不是自動啟動應用程式：

    - 開啟**編輯配置面板**藉由選取**產品 > 配置 > 編輯配置**，或使用**cmd⌘ + <** 鍵盤快速鍵。

    - 選取 **執行**配置，並在右側面板您應該會看到**啟動**選項。 選取 **等候要啟動的可執行檔**然後按一下**關閉**。

        ![等候要啟動的可執行檔](debugging-with-xcode-images/xcode-schemes.png "等候要啟動的可執行檔")

3. 執行 Xcode 專案。

    這會在您的裝置上安裝虛擬的 Xcode 應用程式，但不是會啟動它。

4. 執行 Xamarin 應用程式。

    Xcode 應該附加至 Xamarin 應用程式中，當它啟動。

### <a name="caveats"></a>警告

您可能必須進行小幅變更至 Xamarin.iOS 應用程式，每次您啟動。 否則，Visual Studio for Mac 將會偵測應用程式不需要建置*和*已安裝，而且它不會在透過 Xcode 虛擬應用程式重新安裝。

## <a name="alternative---using-lldb"></a>其他選項-使用 lldb

如果您想要使用**lldb**從命令列中，沒有比較簡單的解決方案。

在殼層中，輸入下列命令：

```bash
touch ~/.mtouch-launch-with-lldb
```

您將能夠取得指示**應用程式輸出** 視窗上時該怎麼辦，但基本上，您會執行您的應用程式中，您將能夠使用**lldb**從命令列偵錯應用程式。
