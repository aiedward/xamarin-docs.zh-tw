---
title: 使用 mtouch 搭售 Xamarin.iOS 應用程式
description: 本文件描述 mtouch，此工具會驅動許多步驟，將 Xamarin.iOS 應用程式變成搭售方案、在模擬器中啟動它，並將它部署到實體裝置。
ms.prod: xamarin
ms.assetid: BCA491DA-E4C1-8689-3EC9-E4C72495A798
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: c67544824f23ea72ad325d39347330a863846c2b
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526544"
---
# <a name="using-mtouch-to-bundle-xamarinios-apps"></a>使用 mtouch 搭售 Xamarin.iOS 應用程式

iPhone 應用程式會以應用程式套件組合的形式來提供。 這些套件組合是副檔名為 `.app` 的目錄，內含您的程式碼、資料、組態檔以及 iPhone 用來了解您應用程式的資訊清單。

將 .NET 可執行檔轉變為應用程式的程序大多由 `mtouch` 命令來驅動，這個工具會整合許多將應用程式轉變為套件組合所需的步驟。 此工具也可用來在模擬器中啟動您的應用程式，以及將軟體部署到實際的 iPhone 或 iPod Touch 裝置。

## <a name="detailed-instructions"></a>詳細指示

請查看 [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) 手冊頁面，內有 mtouch 工具的所有可能使用方式。

## <a name="installation"></a>安裝

在 Mac 上，`mtouch` 會與 Xamarin.iOS 配套。 您可以在下列目錄找到它：

**/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin**

若要讓 `mtouch` 方便使用，請將它的上層目錄加入您系統的 `PATH` 環境變數。  

例如，若要在 Bash 中這樣做，請將下行加入 **~/.bash_profile** 檔案的結尾：

```bash
export PATH=$PATH:/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin
```

> [!WARNING]
> 若要使用 `mtouch`，請勿依賴 **/Developer/MonoTouch/usr/bin**，這是指向 **/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin** 的符號連結。 此符號連結存在的原因僅是為了相容於未安裝在 **/Library/Frameworks/...** 中的舊版 MonoTouch，且可能會在未來版本中消失。

## <a name="building"></a>建置

`mtouch` 命令可使用三種不同的方式編譯程式碼：

- 編譯來進行模擬器測試。
- 編譯來部署裝置。
- 將可執行檔部署至裝置。


### <a name="building-for-the-simulator"></a>模擬器的建置

當您開始使用時，最常見的案例是在模擬器中試驗應用程式，因此您會使用 `mtouch -sim` 將程式碼編譯為模擬器套件。 其作法如下：

```bash
$ mtouch -sim Hello.app hello.exe
```

### <a name="building-for-the-device"></a>裝置的建置

若要為您將會使用 `mtouch -dev` 選項來建置應用程式的裝置建置軟體，您還需要提供用來簽署應用程式的憑證名稱。 下列範例顯示如何為裝置建置應用程式：

```bash
$ mtouch -dev -c "iPhone Developer: Miguel de Icaza" foo.exe
```

在此個案中，我們使用 "iPhone Developer:Miguel de Icaza" 憑證來簽署應用程式。 這個步驟很重要，否則實體裝置會拒絕載入應用程式。

 <a name="Running_your_Application" />


## <a name="running-your-application"></a>執行應用程式


### <a name="launching-on-the-simulator"></a>在模擬器上啟動

擁有應用程式套件組合後，在模擬器上啟動就變得非常簡單：

```bash
$ mtouch --sdkroot /Applications/Xcode.app -launchsim Hello.app 
```

如果未設定 `--sdkroot` 旗標，則其值會預設為 xcode-select 路徑，並導致下列警告：

> 例如：警告 MT0061:如 'xcode-select --print-path': /Applications/Xcode.app/Contents/Developer 所回報，未透過系統 Xcode (使用 --sdkroot) 指定任何 Xcode.app 

上述命令列會產生一些輸出，如下所示：

```bash
Launching application
Application launched
PID: 98460
Press enter to terminate the application
```



強烈建議您也要保存標準輸出和標準錯誤檔案的記錄，以便協助您進行偵錯。 `Console.WriteLine` 的輸出會傳送至 `stdout`，而 `Console.Error.WriteLine` 和任何其他執行階段錯誤訊息的輸出則會傳送至 `stderr`。

若要這樣做，請使用 `--stdout` 和 `--stderr` 旗標：

```bash
../../tools/mtouch/mtouch --launchsim=Hello.app --stdout=output --stderr=error
```

如果您的應用程式失敗，您可以查看輸出和錯誤來診斷問題。


### <a name="deploying-to-a-device"></a>部署至裝置

若要部署至您的裝置，您需要佈建裝置，如 Apple 的[管理裝置](https://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/00-About_the_iOS_Application_Development_Workflow/introduction.html)文件所述。 正確佈建裝置後，您可以使用 mtouch 命令將已編譯的 ".app" 部署至裝置。 您可以使用下列命令來進行此作業：

```bash
$ mtouch —sdkroot /Applications/Xcode.app -installdev=MyApp.app
```

如果未設定 `--sdkroot` 旗標，則其值會預設為 xcode-select 路徑，並導致下列警告：

> 例如：警告 MT0061:如 'xcode-select --print-path': /Applications/Xcode.app/Contents/Developer 所回報，未透過系統 Xcode (使用 --sdkroot) 指定任何 Xcode.app 

這些步驟通常會由 Visual Studio for Mac 執行。

## <a name="reference"></a>參考資料

請參閱 [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) 手冊頁面，以取得其他命令列選項的詳細資料。



## <a name="related-links"></a>相關連結

- [mtouch(1)](http://iosapi.xamarin.com/?link=man%3amtouch(1))
