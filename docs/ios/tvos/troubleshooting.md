---
title: 針對以 Xamarin 建立的 tvOS 應用程式進行疑難排解
description: 本文提供可協助您在以 Xamarin 建立的 tvOS 應用程式開發期間進行疑難排解的各種秘訣。 它描述已知問題和特定錯誤。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: db630ba7db1532f24fb6e810653427d1678ad5e2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434639"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>針對以 Xamarin 建立的 tvOS 應用程式進行疑難排解

_本文涵蓋您在使用 Xamarin 的 tvOS 支援時可能遇到的問題。_

<a name="Known-Issues"></a>

## <a name="known-issues"></a>已知問題

目前的 Xamarin tvOS 支援版本具有下列已知問題：

- **Mono 架構** – Mono 4.3 密碼編譯。 ProtectedData 無法從 Mono 4.2 解密資料。 如此一來， `Data unprotection failed` 當設定受保護的 NuGet 來源時，NuGet 套件將無法還原，並產生錯誤。
  - 因應**措施–在**Visual Studio for Mac 中，您必須在重新嘗試還原封裝之前，先將使用密碼驗證的任何 NuGet 套件來源新增回來。
- **Visual Studio for Mac w/F # 增益集** -在 Windows 上建立 F # Android 範本時發生錯誤。 這在 Mac 上仍應正常運作。
- **Xamarin** -執行 xamarin 整合範本專案時，如果目標 Framework 設定為 `Unsupported` ，可能會出現快顯視窗 `Could not connect to the debugger` 。
  - **可能** 的因應措施–將穩定通道中可用的 Mono framework 版本降級。
- **Xamarin Visual Studio & xamarin** -在 Visual Studio 中部署 WatchKit 應用程式時， `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` 可能會出現錯誤。

請報告您在 [GitHub](https://github.com/xamarin/xamarin-macios/issues/new)上找到的任何 bug。

## <a name="troubleshooting"></a>疑難排解

下列各節列出使用 tvOS 9 搭配 Xamarin. tvOS 時可能發生的一些已知問題，以及這些問題的解決方案：

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>可執行檔無效-可執行檔不包含 bitcode

當您嘗試將 tvOS 應用程式提交至 Apple TV App Store 時，您可能會收到「 _不正確可執行檔-可執行檔未包含 bitcode_」格式的錯誤訊息。

若要解決此問題，請執行下列動作：

1. 在 Visual Studio for Mac 中，以滑鼠右鍵按一下 **方案總管** 中的 tvOS 專案檔，然後選取 [ **選項**]。
2. 選取 [ **TvOS 組建** ]，並確定您是在 **發行** 設定上： 

    [![選取 tvOS 組建選項](troubleshooting-images/ts01.png)](troubleshooting-images/ts01.png#lightbox)
3. 將加入 `--bitcode=asmonly` [ **其他 mtouch 引數** ] 欄位，然後按一下 [ **確定]** 按鈕。
4. 在 **發行** 設定中重建您的應用程式。

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>確認您的 tvOS 應用程式包含 Bitcode

若要確認您的 tvOS 應用程式組建包含 Bitcode，請開啟終端機應用程式並輸入下列內容：

```csharp
otool -l /path/to/your/tv.app/tv
```

在輸出中，尋找下列各項：

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

`addr` 和 `size` 會不同，但其他欄位應該相同。

您將需要確定您所使用的任何協力廠商靜態 () 程式庫 `.a` 都是針對 tvOS 程式庫所建立， (不是 iOS 程式庫) 以及它們也包含 bitcode 資訊。

如果是包含有效 bitcode 的應用程式或程式庫， `size` 將會大於1。 在某些情況下，程式庫可以有 bitcode 標記，但不包含有效的 bitcode。 例如：

**不正確 Bitcode**

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

請注意在 `size` 上述範例中，這兩個程式庫之間的差異。 您必須從已啟用 bitcode (Xcode) 設定的 Xcode 封存組建產生程式庫， `ENABLE_BITCODE` 做為此大小問題的解決方案。

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>只包含 arm64 配量的應用程式在 UIRequiredDeviceCapabilities 清單中也必須有 "arm64"，其資訊是 plist。

將應用程式提交至 Apple TV App Store 以供發行時，您可能會收到下列格式的錯誤：

_「僅包含 arm64 配量的應用程式在 UIRequiredDeviceCapabilities 的清單中也必須有 "arm64"。_

如果發生這種情況，請編輯您的檔案， `Info.plist` 並確定它具有下列索引鍵：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
  <string>arm64</string>
</array>
```

重新編譯您的應用程式以進行發行，然後重新提交至 iTunes Connect。

### <a name="task-mtouch-execution----failed"></a>工作 "MTouch" 執行--失敗

如果您使用協力廠商程式庫 (例如 MonoGame) ，而您的發行編譯失敗，並出現一系列結尾的錯誤訊息 `Task "MTouch" execution -- FAILED` ，請嘗試新增 `-gcc_flags="-framework OpenAL"` 至 **其他觸控引數**：

[![工作 MTouch 執行](troubleshooting-images/mtouch01.png)](troubleshooting-images/mtouch01.png#lightbox)

您也應該包含 `--bitcode=asmonly` 在 **其他觸控引數**中，將連結器選項設定為 [ **全部連結** ]，並進行乾淨編譯。

### <a name="itms-90471-error-the-large-icon-is-missing"></a>ITMS-90471 錯誤。 缺少大型圖示

如果您收到的訊息格式為 "ITMS-90471 error"。 當您嘗試將 tvOS 應用程式提交至 Apple TV App Store 進行發行時，缺少大型圖示，請檢查下列各項：

1. 確定您已將大型圖示資產包含在您 `Assets.car` 使用 [應用程式圖示](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) 檔建立的檔案中。
2. 確定您 `Assets.car` 在最終的應用程式套件組合中包含了 [使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md) 檔的檔案。

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>套件組合無效–支援遊戲控制器的應用程式也必須支援 Apple TV 遠端

或 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>套件組合無效–使用 GameController 架構的 Apple TV 應用程式必須在應用程式的資訊中包含 GCSupportedGameControllers 金鑰。 plist

遊戲控制器可以用來增強遊戲，並在遊戲中提供深度的意義。 它們也可以用來控制標準 Apple TV 介面，如此使用者就不需要在遠端和控制器之間切換。

如果您要將遊戲控制器支援的 Xamarin. tvOS 應用程式提交至 Apple TV App store，而且您收到的錯誤訊息格式如下：

_我們發現您最近傳遞的「應用程式名稱」有一或多個問題。您的傳遞已成功，但您可能想要在下一次傳遞中更正下列問題：_

_套件組合無效–支援遊戲控制器的應用程式也必須支援 Apple TV 遠端。_

或 

_套件組合無效–使用 GameController 架構的 Apple TV 應用程式必須在應用程式的資訊中包含 GCSupportedGameControllers 金鑰。 plist。_

解決方法是將 Siri 遠端 () 的支援新增 `GCMicroGamepad` 至應用程式的檔案 `Info.plist` 。 Apple 已新增微型遊戲控制器設定檔，以 Siri 遠端為目標。 例如，包含下列索引鍵：

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
> 藍牙遊戲控制器是終端使用者可能進行的選擇性購買，您的應用程式無法強制使用者購買。 如果您的應用程式支援遊戲控制器，則它也必須支援 Siri 遠端，讓所有 Apple TV 使用者都能使用該遊戲。

如需詳細資訊，請參閱我們的[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)檔的「使用[遊戲控制器](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers)」一節。

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>不相容的目標 framework：。NetPortable，Version = v1.0，Profile = Profile78

當您嘗試在 tvOS 專案中包含便攜類別庫 (PCL) 時，您可能會收到下列訊息：

_不相容的目標 framework：。NetPortable，Version = v1.0，Profile = Profile78_

若要解決這個問題，請使用下列內容加入名為的 XML 檔案 `Xamarin.TVOS.xml` ：

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

至下列路徑：

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

請注意，路徑中的設定檔編號必須符合 PCL 的設定檔編號。

有了這個檔案之後，您應該能夠順利將 PCL 檔案新增至 tvOS 專案。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)