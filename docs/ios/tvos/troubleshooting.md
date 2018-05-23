---
title: 疑難排解
description: 本文涵蓋了解 Xamarin 的 tvOS 支援使用時可能遇到的問題。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 86106fa5ca53e93ccffb4dd141914c01ab65a506
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting"></a>疑難排解

_本文涵蓋了解 Xamarin 的 tvOS 支援使用時可能遇到的問題。_

<a name="Known-Issues" />

## <a name="known-issues"></a>已知問題

目前版本的 Xamarin 的 tvOS 支援具有下列已知的問題：

- **連結 mono 架構**– Mono 4.3 Cryptography.ProtectedData 無法解密來自單聲道 4.2 的資料。 如此一來，NuGet 封裝將無法還原錯誤`Data unprotection failed`當設定受保護的 NuGet 來源時。
    - **因應措施**-在 Visual Studio for Mac 必須加回任何 NuGet 封裝來源，再重新嘗試還原封裝的使用密碼驗證。
- **Visual Studio for Mac 以 F # 增益集**– Windows 上建立 F # Android 範本時發生錯誤。 這應該仍然可以正常 mac 上
- **Xamarin.Mac** – 執行 Xamarin.Mac 統一的範本專案目標 Framework 設定為當`Unsupported`，快顯視窗`Could not connect to the debugger`可能會出現。
    - **可能的因應措施**– 我們穩定的通道中可用的連結 Mono 架構版本降級。
- **Visual Studio 的 Xamarin & Xamarin.iOS** – 當 WatchKit 中部署應用程式 Visual studio 中，錯誤`The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist`可能會出現。

請尋找任何 bug 您的報表來[Bugzilla](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

## <a name="troubleshooting"></a>疑難排解

下列各節列出一些使用 tvOS 9 Xamarin.tvOS 與這些問題的解決方案時可能發生的已知的問題：

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>無效的可執行檔的可執行檔不包含 bitcode

嘗試提交至 Apple TV 應用程式市集的 Xamarin.tvOS 應用程式，您可能會收到錯誤訊息格式 _「 無效的可執行檔的可執行檔未包含 bitcode 」_。

若要解決此問題，請執行下列各項：

1. 在 Visual Studio for Mac，以滑鼠右鍵按一下 Xamarin.tvOS 專案檔中**方案總管 中**選取**選項**。
2. 選取**tvOS 組建**，並確定您在內部**發行**組態： 

    [![](troubleshooting-images/ts01.png "選取 tvOS 建置選項")](troubleshooting-images/ts01.png#lightbox)
3. 新增`--bitcode=asmonly`至**其他 mtouch 引數**欄位，然後按一下**確定** 按鈕。
4. 重建您的應用程式中**發行**組態。

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>確認您 tvOS 應用程式包含 Bitcode

若要確認您 Xamarin.tvOS 應用程式的組建包含 Bitcode，開啟 Terminal 應用程式並輸入下列設定：

```csharp
otool -l /path/to/your/tv.app/tv
```

在輸出中，尋找下列：

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr` 和`size`會不同，但是其他欄位應該相同。

您必須確定靜態任何第三方 (`.a`) 所使用的程式庫所建置針對 tvOS 程式庫 （不 iOS 的程式庫），而且它們也包含 bitcode 資訊。

應用程式或程式庫包含有效 bitcode`size`會大於一。 有某些情況下，文件庫可以有 bitcode 標記，但不包含有效 bitcode 其中。 例如: 

**無效的 Bitcode**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**有效 Bitcode** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

請注意的差異`size`之間的兩個程式庫中所列的範例上執行。 必須與 bitcode 啟用從 Xcode 封存建置產生的程式庫 (Xcode 設定`ENABLE_BITCODE`) 做為這個大小問題的解決方案。

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>只包含 arm64 配量的應用程式也必須擁有 「 arm64"UIRequiredDeviceCapabilities Info.plist 中的清單

提交時發行集的 Apple TV 應用程式市集的應用程式，您可能會發生錯誤，在表單中：

_「 只包含 arm64 配量的應用程式必須也有 「 arm64"UIRequiredDeviceCapabilities Info.plist 中的清單中 」_

如果發生這種情況，編輯您`Info.plist`檔案，並確定它有下列機碼：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

重新編譯您的應用程式版本，並重新提交到 iTunes Connect。

### <a name="task-mtouch-execution----failed"></a>工作"MTouch 」 執行-失敗

如果您使用第 3 個合作對象程式庫 （例如 MonoGame)，版本編譯失敗一長串的錯誤訊息結尾`Task "MTouch" execution -- FAILED`，再次嘗試新增`-gcc_flags="-framework OpenAL"`到您**其他觸控引數**:

[![](troubleshooting-images/mtouch01.png "MTouch 執行工作")](troubleshooting-images/mtouch01.png#lightbox)

您也應該包括`--bitcode=asmonly`中**其他觸控引數**，連結器選項設定為**連結所有**執行完整的編譯。

### <a name="itms-90471-error-the-large-icon-is-missing"></a>ITMS 90471 時發生錯誤。 遺漏大圖示

如果您收到訊息，在表單中的 「 ITMS 90471 錯誤。 大型圖示時，缺少 「 嘗試送出的版本中，「 Apple 電視應用程式存放區的 Xamarin.tvOS 應用程式，請參閱下列：

1. 確定您已包含在大圖示資產您`Assets.car`使用您所建立的檔案[應用程式圖示](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons)文件。
2. 請確定您包含`Assets.car`檔案從[使用圖示和映像](~/ios/tvos/app-fundamentals/icons-images.md)最終的應用程式配套中的文件。

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>無效的套件組合 – 支援遊戲控制器的應用程式也必須支援 Apple TV 遠端

或 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>無效的套件組合 – Apple TV GameController 架構的應用程式必須在應用程式 Info.plist 中包含 GCSupportedGameControllers 金鑰

遊戲控制器可以用來強化遊戲及提供了解在遊戲繪圖。 它們也可以用來控制標準 Apple TV 介面，讓使用者不具有遠端與控制器之間切換。

如果您提交的 Apple TV 應用程式市集遊戲控制器支援 Xamarin.tvOS 應用程式，而且您會收到錯誤訊息的形式：


_我們發現您最近的傳遞的 「 應用程式名稱 」 的一個或多個問題。您的傳遞成功，但您可能想要更正您下一次傳遞中的下列問題：_

_無效的套件組合 – 支援遊戲控制器的應用程式也必須支援 Apple TV 遠端。_

或 

_無效的套件組合 – Apple TV GameController 架構的應用程式必須納入應用程式 Info.plist GCSupportedGameControllers 索引鍵。_

解決方法是新增支援 Siri 遠端 (`GCMicroGamepad`) 加入至應用程式`Info.plist`檔案。 已加入微遊戲控制器設定檔為目標 Siri 遠端 apple。 例如，包含下列機碼：

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> 藍芽遊戲控制器可能會讓使用者選擇性購買，您的應用程式不會強制要求使用者購買。 如果您的應用程式支援遊戲控制器就必須也支援使用 Siri 遠端使遊戲能由 Apple TV 的所有使用者。

如需詳細資訊，請參閱我們[使用遊戲控制器](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers)區段我們[Siri 遠端和藍芽控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>不相容的目標 framework:。NetPortable，Version = 4.5 版，設定檔 = Profile78

嘗試將 Xamarin.tvOS 專案包含可攜式類別程式庫 (PCL) 可能會形成中收到一封：

_不相容的目標 framework:。NetPortable，Version = 4.5 版，設定檔 = Profile78_

若要解決此問題，加入名為 XML 檔案` Xamarin.TVOS.xml`具有下列內容：

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

下列路徑：

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

請注意在路徑中的設定檔數目必須符合 PCL 中設定檔的數目。

此檔案，您應該可以成功地將 PCL 檔案新增至 Xamarin.tvOS 專案。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
