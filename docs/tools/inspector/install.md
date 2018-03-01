---
title: "安裝與需求"
ms.topic: article
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/29/2017
ms.openlocfilehash: a587935e35882ed1dc68817fbbe1ae3e91200f29
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="installation-and-requirements"></a>安裝與需求

<script> var inspectorOnLoad = 函式 （） {var primaryTextBase ="Xamarin 活頁簿 （& s） 偵測器 」; var secondaryTextBase = 「 或下載 」; var inspectorDownloadUrlMac ="https://dl.xamarin.com/interactive/XamarinInteractive.pkg"; varinspectorDownloadUrlWin ="https://dl.xamarin.com/interactive/XamarinInteractive.msi";

  var aPrimary = document.getElementById("inspector-download-primary"); var aSecondary = document.getElementById("inspector-download-secondary");

  var aMac = aPrimary; var aWin = aSecondary; var macTextBase = primaryTextBase; var winTextBase = secondaryTextBase;

  if (/win/i.test(navigator.platform.toLowerCase())) { aMac = aSecondary; aWin = aPrimary; macTextBase = secondaryTextBase; winTextBase = primaryTextBase; }

  aMac.href = inspectorDownloadUrlMac; aMac.text = macTextBase + " Mac"; aWin.href = inspectorDownloadUrlWin; aWin.text = winTextBase + " Windows"; };

document.addEventListener("DOMContentLoaded", inspectorOnLoad);
</script>

## <a name="download-and-installation"></a>下載和安裝

<ol>
  <li>下載並安裝<a href="https://dl.xamarin.com/interactive/XamarinInteractive.pkg" id="inspector-download-primary">Xamarin 活頁簿 （& s) 適用於 Mac 的偵測器</a>(<a href="https://dl.xamarin.com/interactive/XamarinInteractive.msi" id="inspector-download-secondary">或下載適用於 Windows</a>)。
  </li>
  <li><a href="~/tools/inspector/inspect.md"> 檢查您的應用程式 ！</a>
    </li>
</ol>

## <a name="requirements"></a>需求

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更新版本
- **Windows** -Windows 7 或更新版本 (使用 Internet Explorer 11 或更高和.NET 4.6.1 或更新版本)

### <a name="supported-ides"></a>支援的 Ide

- 6.2 或更高的 Xamarin Studio
- Visual Studio for Mac Preview 4 或更新版本
- Visual Studio 2015 和 Xamarin 4.3.x 或更新版本
- Visual Studio 2017 搭配 Xamarin 的工作負載

企業客戶可使用即時應用程式檢查。

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>支援的應用程式平台

<table>
<thead>
  <tr>
    <th>應用程式平台</th>
    <th>IDE 支援</th>
    <th>注意</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Mac （整合）</td>
    <td>在 Mac 上才支援</td>
    <td/>
  </tr>
  <tr>
    <td>iOS （整合）</td>
    <td>支援 XS 和 Visual Studio 中</td>
    <td>檢查從 Windows 的 iOS 應用程式需要相同版本的 Mac 組建主機上也會安裝的偵測器。</td>
  </tr>
  <tr>
    <td>Android</td>
    <td>支援 XS 和 Visual Studio 中</td>
    <td>
      <ul>
        <li>必須為目標 Android > = 4.0.3</li>
        <li>必須有 fastdev 啟用</li>
        <li>必須使用 Google、 Visual Studio 或 Xamarin Android 模擬器。 Android 7 模擬器可能不允許在此階段的檢查。</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>WPF</td>
    <td>在 Windows 上的 Visual Studio 中才支援</td>
    <td/>
  </tr>
</tbody>
</table>

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>回報 Bug

應該直接透過 Visual Studio 報告的 bug︰

- **協助 → 傳送意見反應 → 報告問題**

請包含所有的下列資訊：

### <a name="platform-version-information"></a>平台版本資訊

這項資訊是很重要。

Visual Studio For Mac

- **Visual Studio → 顯示詳細資料 → 複製資訊相關的 visual Studio →**
- 貼到 錯誤報告

Xamarin Studio

- **Xamarin Studio → Xamarin Studio → 顯示的相關詳細資料 → 複製資訊**
- 貼到 錯誤報告

Visual Studio

- **協助 > 有關 Visual Studio > 複製資訊**
- 讓我們知道您的作業系統版本和您執行的 32 位元或 64 位元 Windows。

### <a name="log-files"></a>記錄檔

請務必附加 IDE 與 檢查用戶端記錄檔。

偵測器用戶端

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x 也提供功能的能力，從主功能表中選取 [記錄檔中搜尋 (macOS) 或檔案總管] (Windows) 直接：

- **說明 → 顯示記錄檔**

Visual Studio For Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Xamarin Studio

- `~/Library/Logs/XamarinStudio-6.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Visual Studio 內容`Output`窗格也可能會有幫助。

### <a name="project-settings"></a>專案設定

如果您可以將附加`.csproj`對於想要檢查的專案，會是非常有幫助。 這是更為詢問有關個別設定。

也請確認您是在偵錯組態。

### <a name="selected-devices"></a>選取的裝置

適用於 Android 和 iOS，很重要，我們會知道您正在偵錯在當您想要檢查哪些裝置。 我們需要知道：

- 您的 IDE 中所示的裝置名稱
- 您的裝置 OS 版本
- Android： 確認您使用的 x86 模擬器
- Android： 模擬器平台您使用？ Google 模擬器嗎？ Android 的 visual Studio 模擬器嗎？ Xamarin Android Player 嗎？
- 未正確地進行偵錯應用程式會出現和函式中的裝置？
- 裝置是否有網路連線能力 （透過 web 瀏覽器檢查）？

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>解除安裝

### <a name="windows"></a>Windows

根據您如何取得活頁簿 （& s） 偵測器，您可能執行兩個解除安裝程序。 請檢查這兩個完全解除安裝軟體。

#### <a name="visual-studio-installer"></a>Visual Studio 安裝程式

如果您有 Visual Studio 2017，開啟 「 Visual Studio 安裝程式 」，並尋找 「 個別元件 」 中 「 Xamarin 活頁簿 」。 如果已核取，請取消選取，然後按一下 解除安裝的 修改。

#### <a name="system-uninstall"></a>系統解除安裝

如果您已安裝的活頁簿 （& s） 偵測器自行下載安裝程式，就會需要透過解除安裝**應用程式和功能**系統設定 頁面在 Windows 10 上或透過**新增/移除程式**較舊版本的 Windows 控制台中。

> **啟動 → 設定 → 系統 → 應用程式和功能**

![](install-images/windows-remove.png "Xamarin 活頁簿和 [應用程式與功能] 中列出的偵測器")

**您仍應遵循 Visual Studio 安裝程式，以便確定活頁簿的程序 （& s) 偵測器不會不取得重新安裝您不知情的情況下。**

### <a name="macos"></a>macOS

從開始[1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/)，Xamarin 活頁簿 （& s） 偵測器可以從解除安裝終端機執行：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

檔案和目錄，則會移除，並要求確認才能繼續解除安裝程式將會詳細說明。

傳遞`-help`引數`uninstall`更進階案例中的指令碼。

對於較舊版本，您必須手動移除下列項目：

1. 刪除 `"/Applications/Xamarin Workbooks.app"` 上的 Workbooks 應用程式
2. 刪除 `"Applications/Xamarin Inspector.app"` 上的 Inspector 應用程式
2. 刪除增益集：`"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` 和 `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. 刪除這裡的 Inspector 和支援檔：`/Library/Frameworks/Xamarin.Interactive.framework` 和 `/Library/Frameworks/Xamarin.Inspector.framework`

