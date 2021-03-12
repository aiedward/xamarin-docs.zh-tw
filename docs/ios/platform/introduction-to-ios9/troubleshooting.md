---
title: Xamarin. iOS 9 –疑難排解
description: 本文提供在 Xamarin 中使用 iOS 9 的各種疑難排解秘訣。 秘訣涵蓋 XML 剖析、模擬器、配置條件約束、網路問題，以及許多其他主題。
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 37075e485cc74b588b18503cdf8beb2307552f2f
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602303"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin. iOS 9 –疑難排解

_本文提供在 Xamarin iOS 應用程式中使用 iOS 9 的一些疑難排解秘訣。_

## <a name="where-are-the-ios-8-simulators"></a>IOS 8 模擬器在哪裡？

如果您已安裝 Xcode 7 (或更高的) ，它預設會自動將所有 iOS 8 模擬器取代為 iOS 9 模擬器。 如果您仍然需要在 iOS 8 上進行測試，您可以開始 Xcode，然後下載並安裝 iOS 8 模擬器。

在 Xcode 中，選取 [ **Xcode** ] 功能表，然後選取 [**喜好設定 ...**  >  ]**下載**：

[![iOS 8 模擬器下載](troubleshooting-images/ios8.png)](troubleshooting-images/ios8.png#lightbox)

選取 [ **檢查並立即安裝** ] 按鈕，以重新安裝 iOS 8 模擬器。

## <a name="layout-constraint-with-leftright-attribute-errors"></a>具有 Left/Right 屬性錯誤的版面配置條件約束

在 iOS 8 (和先前的) 中，分鏡腳本中的 UI 元素可以混合使用 **右**  &  **左邊** 屬性 (`NSLayoutAttributeRight`  &  `NSLayoutAttributeLeft`) 和 **前置**  &  **尾端** 屬性 (`NSLayoutAttributeLeading`  &  `NSLayoutAttributeTrailing` 在相同配置中) 。

如果在 iOS 9 中執行相同的分鏡腳本，將會產生下列形式的例外狀況：

> 因為未攔截的例外狀況 ' NSInvalidArgumentException ' 而終止應用程式，原因： ' * * * + [NSLayoutConstraint constraintWithItem： attribute： relatedBy： toItem： attribute：乘數：常數：]：不能在前置/尾端屬性與右/左屬性之間進行條件約束。 使用前置/尾端做為或兩者皆非。」

iOS 9 強制進行配置，以使用  &  **左手**_或_**前置**  &  **尾端** 屬性，但 *不* 能同時使用兩者。 若要修正此問題，請將所有版面配置條件約束變更為使用您的分鏡腳本檔案中設定的相同屬性。

如需詳細資訊，請參閱 [iOS 9 條件約束錯誤](https://stackoverflow.com/questions/32692841/ios-9-constraint-error) 堆疊溢位討論。

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>錯誤 ITMS-90535：非預期的 CFBundleExecutable 索引鍵

切換至 iOS 9 之後，從應用程式使用協力廠商元件 (特別是在 iOS 8 (或更早) 版本上編譯和執行的現有 Google Maps 元件) ，您可能會在嘗試將新的組建提交至 iTunes Connect 時收到錯誤，格式如下：

> 錯誤 ITMS-90535：未預期的 CFBundleExecutable 索引鍵。 ' 承載/app-name.exe.config 格式. 應用程式/元件組合 ' 的組合未包含套件組合可執行檔 .。。

這項問題通常可以藉由尋找專案中的命名套件組合來解決，就如同錯誤訊息所建議的方式是藉 `Info.plist` 由移除金鑰來編輯套件組合中的。 `CFBundleExecutable` `CFBundlePackageType`金鑰也應該設定為 `BNDL` 。

進行這些變更之後，請清除並重建整個專案。 進行這些變更後，您可以提交至 iTunes Connect，而不會發生問題。

如需詳細資訊，請參閱此 [堆疊溢](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) 位討論。

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake 失敗 (-9824) 錯誤

當您嘗試直接或從 iOS 9 中的 web 視圖連接到網際網路時，您可能會收到下列格式的錯誤：

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

或格式如下：

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

在 iOS9 中，應用程式傳輸安全性 (ATS) 會強制網際網路資源之間的安全連線 (例如應用程式的後端伺服器) 和您的應用程式。 此外，ATS 需要使用通訊協定進行通訊 `HTTPS` ，而高階 API 通訊必須使用具有轉寄密碼的 TLS 1.2 版進行加密。

由於預設會在針對 iOS 9 和 OS X 10.11 所建立的應用程式中啟用 ATS (El Capitan) ，使用、或的所有連接 `NSURLConnection` `CFURL` `NSURLSession` 都會受到 ATS 安全性需求的要求。 如果您的連線不符合這些需求，則會失敗併發生例外狀況。

如需如何解決此問題的詳細資訊，請參閱我們[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)指南的[ATS](~/ios/app-fundamentals/ats.md)一節。

## <a name="my-existing-apps-dont-run-on-ios-9"></a>我現有的應用程式不會在 iOS 9 上執行

請參閱我們的 [ios 9 相容性資訊](~/ios/platform/introduction-to-ios9/ios9.md) ，以取得有關重建及重新部署您現有應用程式以在 iOS 9 上執行的指示。

<a name="UICollectionViewCell.ContentView-is-null-in-constructors"></a>

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell. ContentView 在函式中為 Null

**原因：** 在 iOS 9 中 `initWithFrame:` ，現在需要有此函式，因為 iOS 9 中的行為變更是 [UICollectionView 檔的狀態](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)。 如果您為指定的識別碼註冊了類別，而且必須建立新的資料格，則現在會藉由呼叫其方法來初始化儲存格 `initWithFrame:` 。

**修正：** 新增如下的函式 `initWithFrame:` ：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例： [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)、 [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib"></a>

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>從 Xib/筆尖載入視圖時，UIView 無法以編碼員初始化

**原因：**`initWithCoder:`從 Interface Builder Xib 檔載入視圖時，會呼叫此函式。 如果未匯出此函式，非受控碼就無法呼叫其受管理的版本。 先前 (例如 在 iOS 8 中) 叫用函式 `IntPtr` 以初始化視圖。

**修正：** 建立和匯出如下的函式 `initWithCoder:` ：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例： [聊天](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Dyld 訊息：沒有名稱為的快取映射 .。。

在記錄中，您可能會遇到下列資訊的損毀：

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 這是 Apple 原生連結器中的錯誤，當使用者在 iOS 7 中將私用架構公開 (JavaScriptCore 成為公用架構) ，而且應用程式的部署目標是在架構為私用時，適用于 iOS 版本。 在此情況下，Apple 的連結器將會連結至私用版本的架構，而不是公用版本。

**修正：** 這將會針對 iOS 9 解決，但您可以在此同時套用自己的因應措施：只要以較新的 iOS 版本為目標， (您就可以在此案例中試用 iOS 7) 。 其他架構可能會出現類似的問題，例如在 iOS 8 中將 WebKit 架構設為公用 (，因此以 iOS 7 為目標會導致此錯誤;您應以 iOS 8 為目標，以) 的應用程式中使用 WebKit。

## <a name="untrusted-enterprise-developer"></a>不受信任的企業開發人員

當您嘗試在實際的 iOS 硬體上執行 Xamarin ios 應用程式的 iOS 9 版本時，您可能會收到訊息，指出您的開發人員帳戶尚未在裝置上受到信任。 例如：

[![不受信任的企業開發人員警示](troubleshooting-images/untrusted01.png)](troubleshooting-images/untrusted01.png#lightbox)

若要解決此問題，請執行下列動作：

1. 開始 Xcode (開發 Mac 上的最新 Beta 版) 。
2. 從 [**視窗]** 功能表中選取 [**裝置**]，以開啟 [裝置] 視窗： 

    [![[裝置] 視窗](troubleshooting-images/untrusted02.png)](troubleshooting-images/untrusted02.png#lightbox)
3. 在 [ **裝置** ] 側邊面板中，選取您的裝置，按一下滑鼠右鍵，然後選取 [顯示布建 **設定檔 ...**： 

    [![SShow 布建設定檔](troubleshooting-images/untrusted03.png)](troubleshooting-images/untrusted03.png#lightbox)
4. 選取目前在裝置上的每個布建設定檔，然後選取 **-** 按鈕以將其刪除： 

    [![刪除布建設定檔](troubleshooting-images/untrusted04.png)](troubleshooting-images/untrusted04.png#lightbox)
5. 從 [ **Xcode** ] 功能表選取 [ **喜好設定 ...** ] 和 [ **帳戶**： 

    [![Xcode 帳戶喜好設定](troubleshooting-images/untrusted05.png)](troubleshooting-images/untrusted05.png#lightbox)
6. 按一下 [ **查看詳細資料** ] 按鈕，然後選取 [ **全部下載** ] 按鈕： 

    [![下載所有設定檔](troubleshooting-images/untrusted06.png)](troubleshooting-images/untrusted06.png#lightbox)
7. 當清單更新完成時，請選取 [ **完成** ] 按鈕，然後關閉 [喜好設定] 視窗。
8. 移除您嘗試從 iOS 裝置測試的現有 Xamarin iOS 應用程式版本。
9. 返回 Visual Studio for Mac，執行乾淨的組建，然後嘗試在裝置上重新執行應用程式。

您可能必須先停止並重新啟動 Visual Studio for Mac，才能看到 Xcode 所載入的新布建設定檔。 您也可能需要調整您的 Xamarin ios 應用程式的 IOS 套件組合 **簽署** 選項，以選取新的布建設定檔。

## <a name="launch-screen-issues"></a>啟動畫面問題

iOS 9 現在會強制啟動畫面需求，如此一來，就無法再重複使用相同的啟動映射來支援不同的介面方向。 如需詳細資訊，請參閱 Apple 的 [UILanchImage 參考](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) 。

（選擇性）您可以使用分鏡腳本檔案來呈現應用程式的啟動畫面，而不是使用一組 **.png** 影像檔案。 這現在是 Apple 用來呈現啟動畫面的慣用方式。 如需詳細資訊，請參閱我們的統一分鏡腳本指南 [簡介](~/ios/user-interface/storyboards/unified-storyboards.md) 。

最後，您的應用程式必須使用分鏡腳本檔案作為啟動畫面，並支援所有四個介面方向 (直向、向下、向下、向左和向右) ，以在 [滑下] 面板或 [分割視圖] 模式中執行。 若要深入瞭解 iOS 9 的新多工功能，請參閱我們 [的多工（適用于 iPad](~/ios/platform/multitasking.md) ）指南。

## <a name="nsinternalinconsistencyexception-exception"></a>NSInternalInconsistencyException 例外狀況

在編譯和執行 iOS 9 的現有 Xamarin iOS 應用程式時，您可能會收到下列格式的錯誤：

> 目標-C 擲回例外狀況。  名稱： NSInternalInconsistencyException 原因：應用程式視窗預期會在應用程式啟動結束時有根視圖控制器

發生此錯誤的原因是應用程式視窗預期在應用程式啟動結束時有根視圖控制器，而且您現有的應用程式不會。

這個問題至少有兩個可能的解決方法：

1. 更新應用程式以使用分鏡腳本檔案（而非檔案） `xib` 來定義其使用者介面。 這需要很長的時間才能更正，視您的應用程式大小以及如何使用 Xcode 的 Interface Builder 來配置分鏡腳本而定。 如需詳細資訊，請參閱整合分鏡指令檔的 [簡介](~/ios/user-interface/storyboards/unified-storyboards.md) 。
2. 在 `RootViewController` 類別的方法中設定應用程式視窗的屬性 `FinishedLaunching` `AppDelegate` ，以指向應用程式 UI 中的 View Controller。

## <a name="when-to-initialize-views-and-view-controllers"></a>初始化視圖和查看控制器的時機

使用 Xamarin 時，可以在函式內初始化 Views 或 View 控制器，在 managed 程式碼中公開某些內容時呼叫，但是會中斷 iOS 設計。

一般而言，您不應該初始化任何可以從函式回呼目標 C 程式碼的作業，因為您無法確定何時會呼叫它。 這也表示有更好的地方 (其他 .ctor) 或呼叫來覆寫 (，因為目標 C 沒有任何事件) 應該在這種情況下進行此初始化。

## <a name="related-links"></a>相關連結

- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [將您的 Xamarin iOS 應用程式更新為 iOS9 (影片) ](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
