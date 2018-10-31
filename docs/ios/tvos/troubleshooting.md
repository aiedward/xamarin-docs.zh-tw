---
title: 疑難排解使用 Xamarin 建置的 tvOS 應用程式
description: 本文章提供協助以 Xamarin 建置的 tvOS 應用程式開發期間疑難排解祕訣。 它說明已知的問題與特定的錯誤。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 60b54ffdce5f6bda2324c08bd3159eefeb5bc7f8
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50234853"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>疑難排解使用 Xamarin 建置的 tvOS 應用程式

_這篇文章涵蓋了解使用 Xamarin 的 tvOS 支援時可能遇到的問題。_

<a name="Known-Issues" />

## <a name="known-issues"></a>已知問題

Xamarin 的 tvOS 支援目前版本具有下列已知的問題：

- **Mono Framework** – Mono 4.3 Cryptography.ProtectedData 無法解密來自 Mono 4.2 的資料。 如此一來，NuGet 套件將無法還原錯誤`Data unprotection failed`當設定為受保護的 NuGet 來源。
    - **因應措施**-在 Visual Studio for Mac，您必須加回任何 NuGet 套件來源，然後再重新嘗試還原套件的使用密碼驗證。
- **Visual Studio for Mac 中含F#增益集**– 建立時的錯誤F#在 Windows 上的 Android 範本。 這應該仍正常運作。 在 mac 上
- **Xamarin.Mac** – 當執行 Xamarin.Mac 統一的範本專案目標 Framework 設定為`Unsupported`，快顯視窗`Could not connect to the debugger`可能會出現。
    - **可能的因應措施**– 我們穩定通道中，您可以使用 Mono framework 版本降級。
- **Xamarin 的 Visual Studio & Xamarin.iOS** – 當部署 WatchKit 應用程式，在 Visual studio 中，錯誤`The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist`可能會出現。

請尋找任何 bug 您的報表上[GitHub](https://github.com/xamarin/xamarin-macios/issues/new)。

## <a name="troubleshooting"></a>疑難排解

下列各節列出一些使用 tvOS 9 與 Xamarin.tvOS 和這些問題的解決方案時可能發生的已知的問題：

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>無效的可執行檔的可執行檔不包含 bitcode

嘗試將提交至 Apple TV App Store 的 Xamarin.tvOS 應用程式時，您可能會收到一則錯誤訊息格式 _「 無效的可執行檔的可執行檔不包含 bitcode"_。

若要解決此問題，請執行下列作業：

1. 在 Visual Studio for Mac，以滑鼠右鍵按一下 Xamarin.tvOS 專案檔中**方案總管**，然後選取**選項**。
2. 選取  **tvOS 組建**，並確定您位於**發行**組態： 

    [![](troubleshooting-images/ts01.png "選取 tvOS 組建選項")](troubleshooting-images/ts01.png#lightbox)
3. 新增`--bitcode=asmonly`要**其他 mtouch 引數**欄位，然後按一下**確定** 按鈕。
4. 重建您的應用程式中**發行**組態。

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>確認您的 tvOS 應用程式包含 Bitcode

若要確認您 Xamarin.tvOS 應用程式的組建包含 Bitcode，開啟 Terminal 應用程式並輸入下列資訊：

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

`addr` 和`size`會不同，但應該是相同的其他欄位。

您必須確定任何靜態的第三方 (`.a`) 您使用的程式庫所建置 tvOS 程式庫 （不是 iOS 程式庫），它們也包含 bitcode 的資訊。

應用程式或程式庫包含有效的 bitcode`size`會大於 1。 有一些情況中的程式庫可以有 bitcode 標記，但是卻不包含有效的 bitcode。 例如: 

**無效的 Bitcode**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**有效的 Bitcode** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

請注意的差異`size`列出範例中的兩個程式庫之間執行上面。 必須從 Xcode 封存組建產生的程式庫，以啟用 bitcode (Xcode 設定`ENABLE_BITCODE`) 作為此大小問題的解決方案。

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>只包含 arm64 配量的應用程式也必須有"arm64"UIRequiredDeviceCapabilities Info.plist 中的清單中

當提交至 Apple TV App Store 發行集的應用程式，您可能會發生錯誤，在表單中：

_「 只包含 arm64 配量的應用程式必須也有 「 arm64"UIRequiredDeviceCapabilities Info.plist 中的清單中 」_

如果發生這種情況，編輯您`Info.plist`檔案，並確定它具有下列金鑰：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

重新編譯您的應用程式版本並重新提交至 iTunes Connect。

### <a name="task-mtouch-execution----failed"></a>工作"MTouch 」 執行-失敗

如果您使用第 3 的廠商程式庫 （例如 MonoGame) 和發行版本編譯失敗，一長串的錯誤訊息結尾`Task "MTouch" execution -- FAILED`，再次嘗試新增`-gcc_flags="-framework OpenAL"`至您**額外的觸控式的引數**:

[![](troubleshooting-images/mtouch01.png "MTouch 執行工作")](troubleshooting-images/mtouch01.png#lightbox)

您也應該包含`--bitcode=asmonly`中**額外的觸控式的引數**，連結器選項設定為**全部連結**執行完整編譯。

### <a name="itms-90471-error-the-large-icon-is-missing"></a>ITMS 90471 時發生錯誤。 遺漏大圖示

如果您收到訊息，以在表單中的 「 ITMS 90471 錯誤。 大圖示時，缺少 「 嘗試送出的 Xamarin.tvOS 應用程式，以 Apple TV App Store 發行時，請檢查下列項目：

1. 請確定您已包含在大圖示資產您`Assets.car`使用您所建立的檔案[應用程式圖示](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons)文件。
2. 確定您包含`Assets.car`從檔案[使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)您最終的應用程式套件組合中的文件。

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>無效的套件組合 – 支援 遊戲控制器的應用程式也必須支援 Apple TV 遠端

或 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>無效的套件組合 – GameController framework 使用的 Apple TV 應用程式必須包含在應用程式的 Info.plist 中的 GCSupportedGameControllers 金鑰

遊戲控制器可用來增強遊戲，而且提供了解在遊戲中的訓練活動的使用中。 它們也可以用來控制標準的 Apple TV 介面，因此使用者不需要遠端和控制器之間切換。

如果您提交至 Apple TV App store 的遊戲控制器支援 Xamarin.tvOS 應用程式，而且您會收到的錯誤訊息的形式：


_我們發現您最近的傳遞 」 應用程式名稱 」 的一或多個問題。您傳遞成功，但您可能想要更正您下一次傳遞中的下列問題：_

_無效的套件組合 – 支援 遊戲控制器的應用程式也必須支援 Apple TV 遠端。_

或 

_無效的套件組合 – GameController framework 使用的 Apple TV 應用程式都必須包含在應用程式的 Info.plist 中的 GCSupportedGameControllers 金鑰。_

解決方法是新增 Siri 遠端支援 (`GCMicroGamepad`) 到您的應用程式`Info.plist`檔案。 Micro 遊戲控制器設定檔已加入 apple Siri 遠端為目標。 例如，包含下列索引鍵：

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
> 藍芽遊戲控制器可能會讓終端使用者的選選購，您的應用程式不會強制使用者進行購買。 如果您的應用程式支援遊戲控制器就必須也支援 Siri 遠端，以便在遊戲是由 Apple TV 的所有使用者使用。

如需詳細資訊，請參閱我們[使用 遊戲控制器](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers)一節我們[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>不相容的目標 framework:。NetPortable，版本 = v4.5 中，設定檔 = Profile78

嘗試 Xamarin.tvOS 專案中加入可攜式類別庫 (PCL) 時您可能會收到訊息，以形成項目：

_不相容的目標 framework:。NetPortable，版本 = v4.5 中，設定檔 = Profile78_

若要解決此問題，新增 XML 檔案，稱為` Xamarin.TVOS.xml`使用下列內容：

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

在下列路徑：

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

請注意，在路徑中的設定檔數目必須符合 PCL 設定檔數目。

此檔案，您應該能夠成功地將 PCL 檔案加入至 Xamarin.tvOS 專案。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
