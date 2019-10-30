---
title: Xamarin. iOS 9-疑難排解
description: 本文提供各種在 Xamarin 中使用 iOS 9 的疑難排解秘訣。 秘訣涵蓋 XML 剖析、模擬器、版面配置條件約束、網路問題，以及許多其他主題。
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 437698fcda6e85090cd7bdce90959300436e0bc2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031750"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin. iOS 9-疑難排解

_本文提供數個在 Xamarin iOS 應用程式中使用 iOS 9 的疑難排解秘訣。_

## <a name="there-was-a-problem-parsing-the-xml"></a>剖析 XML 時發生問題

Xamarin iOS 設計工具尚不支援 Xcode 7 功能。 嘗試使用新的 iOS 9 （Xcode 7）設計工具專案（例如 System.windows.forms.toolstrip.stackview）時，在設計工具中無法載入分鏡腳本，並出現「_剖析 XML 時發生問題_」。

iOS Designer 對 Xcode 7 功能的支援是以即將推出的週期6功能版本為目標。 週期6的預覽版本目前適用于 Alpha 色板，而且對新的 Xcode 7 功能的支援有限。

Visual Studio for Mac 的部分因應措施：以滑鼠右鍵按一下腳本，然後選擇 [**開啟方式**] > **Xcode Interface Builder**。

## <a name="where-are-the-ios-8-simulators"></a>IOS 8 模擬器在哪裡？

如果您已安裝 Xcode 7 （或更新版本），則預設會自動將所有 iOS 8 模擬器取代為 iOS 9 模擬器。 如果您仍然需要在 iOS 8 上進行測試，您可以啟動 Xcode，然後下載並安裝 iOS 8 模擬器。

在 [Xcode] 中，依序選取 [ **Xcode** ] 功能表和 [**喜好**設定] > **下載**：

[![](troubleshooting-images/ios8.png "iOS 8 Simulators Downloads")](troubleshooting-images/ios8.png#lightbox)

按一下 [**勾選並立即安裝**] 按鈕，以重新安裝 iOS 8 模擬器。

## <a name="layout-constraint-with-leftright-attribute-errors"></a>具有左/右屬性錯誤的版面配置條件約束

在 iOS 8 （和之前的版本）中，分鏡腳本中的 UI 元素可以混合使用**右** & **Left** **屬性（** `NSLayoutAttributeRight` & `NSLayoutAttributeLeft`） &  **，並在**相同的版面配置。

如果在 iOS 9 中執行相同的腳本，它會產生下列格式的例外狀況：

> 因為無法攔截的例外狀況 ' NSInvalidArgumentException ' 而終止應用程式，原因： ' * * * + [NSLayoutConstraint constraintWithItem： attribute： relatedBy： toItem： attribute：乘數：常數：]：不能在前置/尾端之間建立條件約束屬性和右/左屬性。 同時使用開頭/尾端，或兩者皆非。 '

iOS 9 會強制執行配置，以使用**右邊** & **Left** _或_**前置** & **尾端**屬性，但*不*能同時使用兩者。 若要修正此問題，請變更所有版面配置條件約束，以在您的分鏡腳本檔案中使用相同的屬性集。

如需詳細資訊，請參閱[iOS 9 條件約束錯誤](https://stackoverflow.com/questions/32692841/ios-9-constraint-error)Stack Overflow 討論。

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>錯誤 ITMS-90535：未預期的 CFBundleExecutable 金鑰

在切換至 iOS 9 之後，應用程式會使用在 iOS 8 （或更早版本）上編譯並執行的協力廠商元件（尤其是我們現有的 Google Maps 元件），在嘗試將新的組建提交至 iTunes Connect 時，您可以在下列表單中取得錯誤：

> 錯誤 ITMS-90535：未預期的 CFBundleExecutable 金鑰。 ' 承載/app-name. app/component. 配套 ' 的配套未包含配套可執行檔 。

此問題通常可以藉由在專案中尋找指定的組合來解決，如同錯誤訊息所建議-藉由移除 `CFBundleExecutable` 金鑰來編輯配套中的 `Info.plist`。 `CFBundlePackageType` 索引鍵也應該設定為 `BNDL`。

進行這些變更之後，請清除並重建整個專案。 在進行這些變更之後，您應該能夠提交至 iTunes Connect 而不會發生問題。

如需詳細資訊，請參閱這[Stack Overflow](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key)討論。

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake 失敗（-9824）錯誤

嘗試直接或從 iOS 9 中的 web view 連線到網際網路時，您可能會收到下列格式的錯誤：

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

或在表單中：

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

在 iOS9 中，應用程式傳輸安全性（ATS）會在網際網路資源（例如應用程式的後端伺服器）和您的應用程式之間強制執行安全的連線。 此外，ATS 需要使用 `HTTPS` 通訊協定和高階 API 通訊，透過具有轉寄密碼的 TLS 1.2 版進行加密。

由於預設會在針對 iOS 9 和 OS X 10.11 （El Capitan）建立的應用程式中啟用 ATS，因此使用 `NSURLConnection`、`CFURL` 或 `NSURLSession` 的所有連線都會受到 ATS 的安全性需求。 如果您的連線不符合這些需求，則會失敗並產生例外狀況。

如需如何解決此問題的相關資訊，請參閱我們的[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)指南中的選擇[退出 ATS](~/ios/app-fundamentals/ats.md)一節。

## <a name="my-existing-apps-dont-run-on-ios-9"></a>我現有的應用程式不會在 iOS 9 上執行

如需重新建立及重新部署現有應用程式以在 iOS 9 上執行的指示，請參閱我們的[iOS 9 相容性資訊](~/ios/platform/introduction-to-ios9/ios9.md)。

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell 中的 ContentView 是 Null。

**原因：** 在 iOS 9 中，由於 iOS 9 中的行為變更是[UICollectionView 檔的狀態](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)，因此現在需要 `initWithFrame:` 的函式。 如果您註冊了指定識別碼的類別，而且必須建立新的資料格，則現在會藉由呼叫其 `initWithFrame:` 方法來初始化資料格。

**修正：** 新增 `initWithFrame:` 的函數，如下所示：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例： [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)、 [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>從 Xib/筆尖載入視圖時，UIView 無法從當中初始化

**原因：** `initWithCoder:` 的函式是從 Interface Builder Xib 檔載入視圖時所呼叫的函式。 如果此函式不是匯出的，非受控碼就無法呼叫其受管理的版本。 先前（例如 在 iOS 8 中）已叫用 `IntPtr` 的「檢查程式」來初始化 view。

**修正：** 建立並匯出 `initWithCoder:` 的函式，如下所示：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例：[聊天](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Dyld 訊息：沒有名稱為的快取映射 。

在記錄檔中，您可能會遇到下列資訊的損毀：

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 這是 Apple 原生連結器中的錯誤（bug），這會在公開公用架構（在 iOS 7 中設為公用的 JavaScriptCore，在其為私用架構之前），而且應用程式的部署目標是在架構為私用時，用於 iOS 版本。 在此情況下，Apple 的連結器會與架構的私用版本（而不是公用版本）連結。

**修正：** 這適用于 iOS 9，但有一個簡單的因應措施，您可以在此同時套用：僅以您專案中較新的 iOS 版本為目標（在此案例中，您可以試用 iOS 7）。 其他架構可能會展示類似的問題，例如，WebKit 架構在 iOS 8 中是公開的，因此以 iOS 7 為目標會導致此錯誤; 您應該以 iOS 8 為目標，以在您的應用程式中使用 WebKit）。

## <a name="untrusted-enterprise-developer"></a>不受信任的企業開發人員

嘗試在實際的 iOS 硬體上執行您的 Xamarin iOS 應用程式 iOS 9 版本時，您可能會收到一則訊息，指出您的開發人員帳戶在裝置上未受信任。 例如:

[![](troubleshooting-images/untrusted01.png "Untrusted Enterprise Developer alert")](troubleshooting-images/untrusted01.png#lightbox)

若要解決此問題，請執行下列動作：

1. 在開發 Mac 上啟動 Xcode （最新的搶鮮版）。
2. 從 [**視窗]** 功能表中選取 [**裝置**] 以開啟 [裝置] 視窗： 

    [![](troubleshooting-images/untrusted02.png "The Devices Window")](troubleshooting-images/untrusted02.png#lightbox)
3. 在 [**裝置**] 側邊面板中，選取您的裝置，按一下滑鼠右鍵，然後選取 [顯示布建**設定檔 ...** ]： 

    [![](troubleshooting-images/untrusted03.png "SShow Provisioning Profiles")](troubleshooting-images/untrusted03.png#lightbox)
4. 選取目前裝置上的每個布建設定檔，然後按一下 [ **-** ] 按鈕將其刪除： 

    [![](troubleshooting-images/untrusted04.png "Deleting a provisioning profile")](troubleshooting-images/untrusted04.png#lightbox)
5. 從 [ **Xcode** ] 功能表中，選取 [**喜好**設定] 和 [**帳戶**]： 

    [![](troubleshooting-images/untrusted05.png "Xcode account preferences")](troubleshooting-images/untrusted05.png#lightbox)
6. 按一下 [ **View Details ...** ] 按鈕，然後按一下 [**全部下載**] 按鈕： 

    [![](troubleshooting-images/untrusted06.png "Download all profiles")](troubleshooting-images/untrusted06.png#lightbox)
7. 當清單完成更新時，請按一下 [**完成**] 按鈕，然後關閉 [喜好設定] 視窗。
8. 移除您嘗試從 iOS 裝置測試的現有 Xamarin iOS 應用程式版本。
9. 回到 Visual Studio for Mac，執行乾淨的組建，然後嘗試在裝置上重新執行應用程式。

在 Xcode 所載入的新布建設定檔之前，您可能必須停止並重新啟動 Visual Studio for Mac。 您也可能需要調整您的 Xamarin iOS 應用程式的 IOS 套件組合**簽署**選項，以選取新的布建設定檔。

## <a name="launch-screen-issues"></a>啟動畫面問題

iOS 9 現在會強制執行啟動畫面需求，因此不會再重複使用相同的啟動映射來支援不同的介面方向。 如需詳細資訊，請參閱 Apple 的[UILanchImage 參考](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28)。

（選擇性）您可以使用分鏡腳本檔案來呈現應用程式的啟動畫面，而不是使用一組 **.png**影像檔。 這現在是 Apple 用來呈現啟動畫面的慣用方式。 如需詳細資訊，請參閱我們的整合分鏡腳本指南[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)。

最後，您的應用程式必須使用分鏡腳本檔案作為其啟動畫面，並支援所有四個介面方向（直向、向下垂直、向左和向右），以考慮在滑過的面板或分割視圖模式中執行。 若要深入瞭解 iOS 9 的新多工作業能力，請參閱我們[的多工（iPad](~/ios/platform/multitasking.md) ）指南。

## <a name="nsinternalinconsistencyexception-exception"></a>NSInternalInconsistencyException 例外狀況

在編譯和執行適用于 iOS 9 的現有 Xamarin iOS 應用程式時，您可能會收到下列格式的錯誤：

> 已擲回目標-C 例外狀況。  名稱： NSInternalInconsistencyException 原因：應用程式視窗在應用程式啟動結束時，應該會有根視圖控制器

發生這種錯誤的原因是，應用程式視窗預期會有一個根視圖控制器，而您的現有應用程式則不會出現。

此問題至少有兩個可能的因應措施：

1. 將應用程式更新為使用分鏡腳本檔案，而不是 `xib` 檔案，以定義其使用者介面。 這種情況需要很多時間，視您應用程式的大小而定，並瞭解如何使用 iOS 設計工具（或 Xcode 的 Interface Builder）來配置分鏡腳本。 如需詳細資訊，請參閱我們的整合分鏡指令檔[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)。
2. 在 `AppDelegate` 類別的 `FinishedLaunching` 方法中，設定 `RootViewController` 應用程式視窗的屬性，以指向應用程式 UI 中的視圖控制器。

## <a name="when-to-initialize-views-and-view-controllers"></a>初始化 Views 和 View 控制器的時機

使用 Xamarin 時，您可以在函式內建立 View 或 View Controller 初始化，而這些函式會在某些專案公開至 managed 程式碼時呼叫，但是會中斷 iOS 設計。

一般來說，您不應該將任何可從函式呼叫目標-C 程式碼的專案初始化，因為您無法確定何時呼叫它。 這也表示有更好的位置（其他 .ctor）或呼叫覆寫（如目標-C 沒有事件），這應該會執行此初始化。

## <a name="related-links"></a>相關連結

- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [將您的 Xamarin iOS 應用程式更新為 iOS9 （影片）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
