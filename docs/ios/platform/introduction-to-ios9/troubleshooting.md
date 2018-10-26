---
title: Xamarin.iOS 9 – 疑難排解
description: 本文提供使用 Xamarin.iOS 中的 iOS 9 的疑難排解祕訣。 秘訣涵蓋 XML 剖析、 模擬器、 版面配置條件約束、 網路問題，以及許多其他主題。
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 322bb630194f973d37d7ca27a0ca9fe1b548b240
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107207"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin.iOS 9 – 疑難排解

_本文章會提供數個疑難排解提示 iOS 9 的 Xamarin.iOS 應用程式所使用。_

## <a name="there-was-a-problem-parsing-the-xml"></a>發生問題，剖析 XML

Xamarin iOS 設計工具還不支援 Xcode 7 功能。 分鏡腳本將無法使用設計工具中載入 _"時發生問題，剖析 XML"_ 時嘗試使用新的 iOS 9 (Xcode 7) 設計工具的項目，例如 StackView。

iOS 設計工具支援 Xcode 7 功能被針對即將發行的循環 6 功能版本。 循環 6 的預覽版本目前可供 Alpha 色板，且新的 Xcode 7 功能的支援有限。

Visual Studio for Mac 的部分因應措施： 以滑鼠右鍵按一下 分鏡腳本，然後選擇 **開啟** > **Xcode Interface Builder**。

## <a name="where-are-the-ios-8-simulators"></a>在哪裡？ iOS 8 模擬器

如果您已安裝的 Xcode 7 （或更新版本） 會自動取代所有 iOS 8 模擬器 iOS 9 模擬器的預設值。 如果您仍然需要在 iOS 8 上測試，您就可以啟動 Xcode，然後下載並安裝 iOS 8 模擬器。

在 Xcode 中，選取**Xcode**  功能表然後**喜好設定...**  > **下載**:

[![](troubleshooting-images/ios8.png "iOS 8 模擬器下載")](troubleshooting-images/ios8.png#lightbox)

按一下 [**核取並立即安裝**重新安裝 iOS 8 模擬器] 按鈕。

## <a name="layout-constraint-with-leftright-attribute-errors"></a>使用左/右屬性錯誤的版面配置條件約束

在 iOS 8 （與舊版），在分鏡腳本中的 UI 項目可以使用這兩者的混合**右** & **左**屬性 (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) 和**前置** & **尾端**屬性 (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) 在相同的配置。

如果在相同的分鏡腳本執行 iOS 9 中，它會導致例外狀況，請以下列形式：

> 終止應用程式，因為發生無法攔截的例外狀況 'NSInvalidArgumentException'，原因: ' * * * + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: 開頭/結尾之間無法進行的條件約束屬性和向右鍵/向的屬性。 使用前置/尾端的兩者或兩者皆非。 '

iOS 9 強制使用的版面配置**右** & **左**_或_**前置** &  **尾端**屬性，但*不*兩者。 若要修正此問題，變更使用相同的屬性，設定您的分鏡腳本檔內的所有版面配置條件約束。

如需詳細資訊，請參閱[iOS 9 的條件約束錯誤](http://stackoverflow.com/questions/32692841/ios-9-constraint-error)Stack Overflow 討論。

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>錯誤 ITMS-90535： 未預期的 CFBundleExecutable 金鑰

切換至 iOS 9 之後, 從應用程式會使用第 3 的合作對象元件 （特別是我們現有 Google 地圖元件），編譯及執行 ios 8 （或舊版），當嘗試提交新的組建至 iTunes Connect，您可以在表單中收到錯誤：

> 錯誤 ITMS-90535： 非預期的 CFBundleExecutable 索引鍵。 在 'Payload/app-name.app/component.bundle' 組合不包含套件組合的可執行檔...

此問題通常是解決專案中，找出已命名的套件組合即可-就像此錯誤訊息建議-編輯`Info.plist`也就是說組合中藉由移除`CFBundleExecutable`索引鍵。 `CFBundlePackageType`機碼應該設定為`BNDL`以及。

進行這些變更之後，請勿清除並重建整個專案。 您應該能夠進行這些變更之後提交至 iTunes Connect 不會發生問題。

如需詳細資訊，請參閱這[Stack Overflow](http://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key)討論。

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake 失敗 (-9824) 錯誤

當嘗試連線到網際網路，直接或從 web 檢視，在 iOS 9 中，您可能會發生錯誤，在表單中：

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

或在表單：

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

IOS9，App Transport Security (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。 此外，ATS 需要通訊使用`HTTPS`通訊協定和加密正向加密搭配使用 TLS 1.2 版的高階 API 通訊。

因為在建置適用於 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有連線的應用程式預設會啟用 ATS `NSURLConnection`，`CFURL`或`NSURLSession`將受限於 ATS 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

請參閱[Opting 向外延展 ATS](~/ios/app-fundamentals/ats.md)一節我們[App Transport Security](~/ios/app-fundamentals/ats.md)指南以取得有關如何解決此問題的資訊。

## <a name="my-existing-apps-dont-run-on-ios-9"></a>我現有的應用程式未執行 ios 9

請參閱我們[iOS 9 相容性資訊](~/ios/platform/introduction-to-ios9/ios9.md)重建和重新部署現有的應用程式上執行 iOS 9 上的指示。

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView 是建構函式中的 Null

**原因：** 在 iOS 9`initWithFrame:`建構函式現在是必要的因為在為 iOS 9 中的行為變更[UICollectionView 文件狀態](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)。 如果您註冊指定的識別項的類別，必須建立新的儲存格的資料格現在已初始化。 藉由呼叫其`initWithFrame:`方法。

**修正：** 新增`initWithFrame:`建構函式如下：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例： [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)， [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView 無法與 Coder Init Xib/Nib 從載入檢視時

**原因：** `initWithCoder:`建構函式是從介面產生器 Xib 檔案載入檢視時呼叫。 如果這個建構函式不會匯出未受管理的程式碼無法呼叫我們 managed 的版本。 之前 （例如。 在 iOS 8)`IntPtr`建構函式已叫用來初始化檢視。

**修正：** 建立及匯出`initWithCoder:`建構函式如下：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相關的範例：[對談](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Dyld 訊息： 沒有快取映像名稱...

您可能會遇到當機記錄檔中的下列資訊：

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 這是 Apple 的原生連結器，當他們完成的私用的架構公用發生這種情況中的 bug （JavaScriptCore 已公開在 iOS 7，之前它是私用的架構），而應用程式的部署目標為 iOS 版本時framework 是私人的。 在此情況下 Apple 的連結器會使用私用的版本，而不是公用版本的 framework 的連結。

**修正：** 將會解決此問題適用於 iOS 9，但同時套用自行輕鬆因應措施： 只為目標的更新版本的 iOS 版本專案中 （您在此情況下可以嘗試 iOS 7）。 其他架構可能會表現出類似的問題，例如 WebKit 架構已在 iOS 8 中公開 （和以 iOS 7 為目標時，會導致此錯誤，因此您應該為目標 iOS 8 應用程式中使用 WebKit）。

## <a name="untrusted-enterprise-developer"></a>不受信任的企業開發人員

嘗試將在真實 iOS 硬體上執行 Xamarin.iOS 應用程式的 iOS 9 版本時，您可能會收到訊息，指出您的開發人員帳戶不在裝置上受信任。 例如: 

[![](troubleshooting-images/untrusted01.png "不受信任的企業開發人員警示")](troubleshooting-images/untrusted01.png#lightbox)

若要解決此問題，請執行下列作業：

1. 開發 mac 上啟動 Xcode （最新的 beta 版）
2. 選取 **裝置**從**視窗**功能表開啟 裝置 視窗： 

    [![](troubleshooting-images/untrusted02.png "[裝置] 視窗")](troubleshooting-images/untrusted02.png#lightbox)
3. 底下**裝置**側邊面板中，選取您的裝置，以滑鼠右鍵按一下並選取**顯示佈建設定檔...**: 

    [![](troubleshooting-images/untrusted03.png "SShow 佈建設定檔")](troubleshooting-images/untrusted03.png#lightbox)
4. 選取目前的裝置並按一下每個佈建設定檔**-** 按鈕來刪除它： 

    [![](troubleshooting-images/untrusted04.png "正在刪除佈建設定檔")](troubleshooting-images/untrusted04.png#lightbox)
5. 從**Xcode**功能表上，選取**喜好設定...** 並**帳戶**: 

    [![](troubleshooting-images/untrusted05.png "Xcode 帳戶喜好設定")](troubleshooting-images/untrusted05.png#lightbox)
6. 按一下 **檢視詳細資料...** 按鈕，然後按一下 **下載所有**按鈕： 

    [![](troubleshooting-images/untrusted06.png "下載所有設定檔")](troubleshooting-images/untrusted06.png#lightbox)
7. 完成清單已更新，請按一下**完成**按鈕，然後關閉 [喜好設定] 視窗。
8. 移除您嘗試從 iOS 裝置進行測試 Xamarin.iOS 應用程式的現有版本。
9. 返回 Visual Studio for Mac 中，執行乾淨的組建，然後嘗試重新執行在裝置上的 應用程式。

您可能必須停止並重新啟動 Visual Studio for Mac，才能看見由 Xcode 所載入新的佈建設定檔。 您也可能必須調整**iOS 套件組合簽署**您的 Xamarin.iOS 應用程式，以選取新的佈建設定檔的選項。

## <a name="launch-screen-issues"></a>啟動螢幕的問題

iOS 9 現在會強制執行的啟動螢幕的需求，因此可以不再支援不同的介面方向重複使用相同的啟動影像。 請參閱 Apple [UILanchImage 參考](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28)如需詳細資訊。

（選擇性） 您可以使用分鏡腳本檔案來呈現您的應用程式而不是使用一組啟動螢幕 **.png**影像檔。 這是 Apple 的慣用方式來呈現啟動畫面現在。 請參閱我們[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)指南以取得詳細的資訊。

最後，您的應用程式必須使用其啟動螢幕的分鏡腳本檔案，並支援所有的四個介面方向 （直向上下顛倒的直向、 橫向和橫向 Right） 才會被視為對於滑動透過面板中，或在分割檢視模式中執行。 若要深入了解新的多工能力的 iOS 9，請參閱我們[適用於 iPad 的多工](~/ios/platform/multitasking.md)指南。

## <a name="nsinternalinconsistencyexception-exception"></a>NSInternalInconsistencyException 例外狀況

編譯及執行 iOS 9 的現有的 Xamarin.iOS 應用程式時您可能會發生錯誤，在表單中：

> OBJECTIVE-C 擲回例外狀況。  名稱： NSInternalInconsistencyException 原因： windows 應用程式應包含結尾的啟動應用程式的根檢視控制器

這是錯誤因為 Windows 應用程式應包含結尾的啟動應用程式的根檢視控制器，且您現有的應用程式不會引發。

有至少兩個可能的因應措施對於這個問題：

1. 更新應用程式，以使用分鏡腳本檔案，而不是`xib`檔案，用於定義其使用者介面。 這個要求很多時間，取決於您的應用程式的大小和了解如何使用 iOS 設計工具 （或 Xcode 的 Interface Builder） 配置的分鏡腳本。 如需詳細資訊，請參閱我們[統一的分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)文件。
2. 安裝程式`RootViewController`應用程式視窗的屬性中`FinishedLaunching`方法中的`AppDelegate`以指向您的應用程式 UI 中檢視控制器類別。

## <a name="when-to-initialize-views-and-view-controllers"></a>初始化檢視和檢視控制器的時機

與 Xamarin.iOS 就能夠進行檢視或檢視控制器內的項目公開至 managed 程式碼，不過它會破壞 iOS 設計時所呼叫之建構函式的初始化。

一般情況下您應該不初始化任何項目可以回撥 OBJECTIVE-C 程式碼從建構函式因為您不能確定當呼叫它。 這也表示沒有更好的地方 (其他.ctor) 或覆寫 （Objective C 會有任何事件） 的呼叫進行這項初始化的地方。

## <a name="related-links"></a>相關連結

- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [更新您的 Xamarin.iOS 應用程式到 iOS9 （影片）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
