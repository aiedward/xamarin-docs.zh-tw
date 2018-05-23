---
title: 疑難排解
description: 本文章會提供數個疑難排解提示 iOS 9 Xamarin.iOS 應用程式中使用。
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 1b335fc6b19d87a46059511baf866433691b1b4d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting"></a>疑難排解

_本文章會提供數個疑難排解提示 iOS 9 Xamarin.iOS 應用程式中使用。_

## <a name="there-was-a-problem-parsing-the-xml"></a>剖析 XML 發生問題

Xamarin iOS 設計工具還不支援 Xcode 7 功能。 分鏡腳本將無法使用設計工具中載入 _"時發生問題，剖析 XML 」_ 時嘗試使用新的 iOS 9 (Xcode 7) 例如 StackView 設計工具項目。

iOS Xcode 7 功能的設計工具支援適用於即將推出的循環 6 功能版本。 循環 6 的預覽版本目前用於 Alpha 色板，而新 Xcode 7 功能的支援有限。

Visual Studio for Mac 部分因應措施： 分鏡腳本上按一下滑鼠右鍵，然後選擇 **開啟** > **Xcode 介面產生器**。

## <a name="where-are-the-ios-8-simulators"></a>在哪裡？ iOS 8 模擬器

如果您已經安裝 Xcode 7 （或以上） 將會自動取代所有 iOS 8 模擬器 iOS 9 模擬器預設。 若要在 iOS 8 上進行測試，您可以啟動 Xcode，然後下載並安裝 iOS 8 模擬器。

在 Xcode 中，選取**Xcode**功能表然後**喜好設定...**  > **下載**:

[![](troubleshooting-images/ios8.png "iOS 8 模擬器下載")](troubleshooting-images/ios8.png#lightbox)

按一下**核取並立即安裝** 按鈕，重新安裝 iOS 8 模擬器。

## <a name="layout-constraint-with-leftright-attribute-errors"></a>版面配置與左/右屬性錯誤的條件約束

在 iOS 8 （和之前），在分鏡腳本的 UI 項目可以使用這兩者的混合**右邊** & **左**屬性 (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) 和**前置** & **尾端**屬性 (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) 在相同的配置。

如果在相同的分鏡腳本執行 iOS 9 中，將會導致例外狀況，請以下列形式：

> 終止應用程式，因為發生無法攔截的例外狀況 'NSInvalidArgumentException'，原因: ' * * * + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: 前置/結尾之間無法進行的條件約束屬性和右/向左的屬性。 使用前置/結尾為同時或兩者皆非。 '

iOS 9 會強制執行版面配置，以使用**右邊** & **左**_或_**前置** &  **尾端**屬性但*不*兩者。 若要修正這個問題，變更所有配置的條件約束，以使用分鏡腳本檔案內所設定的相同屬性。

如需詳細資訊，請參閱[iOS 9 條件約束錯誤](http://stackoverflow.com/questions/32692841/ios-9-constraint-error)堆疊溢位的討論。

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>錯誤 ITMS 90535： 未預期的 CFBundleExecutable 金鑰

切換至 iOS 9 之後，從應用程式使用第 3 個合作對象 （特別是我們現有 Google 地圖元件） 的元件編譯及執行 ios 8 （或之前版本），當嘗試提交新的組建至 iTunes Connect，您可以在表單中收到錯誤：

> 錯誤 ITMS 90535： 非預期的 CFBundleExecutable 索引鍵。 在 'Payload/app-name.app/component.bundle' 組合不包含組合的可執行檔...

這個問題可以通常解決專案中尋找具名的配套然後-錯誤訊息指出-一樣編輯`Info.plist`的組合中是藉由移除`CFBundleExecutable`索引鍵。 `CFBundlePackageType`金鑰應該設定為`BNDL`以及。

進行這些變更之後，清除並重建整個專案。 您應該能夠進行這些變更後送出至 iTunes Connect 不會發生問題。

如需詳細資訊，請參閱本[堆疊溢位](http://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key)討論。

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake 失敗 (-9824) 錯誤

當嘗試連線到網際網路，直接或從 web 檢視在 iOS 9 中，您可能會發生錯誤，在表單中：

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

或在表單：

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

IOS9，應用程式傳輸安全性 (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。 此外，AT 需要通訊使用`HTTPS`通訊協定和 TLS 1.2 版使用正向加密與加密的高階應用程式開發介面通訊。

因為在建置適用於 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有連接的應用程式預設會啟用 AT `NSURLConnection`，`CFURL`或`NSURLSession`都會受到 AT 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

請參閱[Opting 外 AT](~/ios/app-fundamentals/ats.md)區段我們[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)指南以取得如何解決此問題的資訊。

## <a name="my-existing-apps-dont-run-on-ios-9"></a>我現有的應用程式未執行 ios 9

請參閱我們[iOS 9 相容性資訊](~/ios/platform/introduction-to-ios9/ios9.md)如需重新建置並重新部署現有的應用程式執行 iOS 9 上的指示。

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView 是建構函式中的 Null

**原因：** 在 iOS 9`initWithFrame:`建構函式現在是必要項目，因為在為 iOS 9 中的行為變更[UICollectionView 文件狀態](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)。 如果您註冊指定的識別項的類別，並且必須建立新的儲存格，資料格現在藉由呼叫初始化其`initWithFrame:`方法。

**修正：** 新增`initWithFrame:`建構函式，就像這樣：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相關範例： [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)， [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView 無法初始化與編碼器 Xib/Nib 從載入檢視時

**原因：** `initWithCoder:`建構函式是從介面產生器 Xib 檔案載入檢視時呼叫。 如果這個建構函式不會匯出 unmanaged 程式碼無法呼叫它的我們受管理的版本。 之前 （例如。 在 iOS 8)`IntPtr`建構函式已叫用來初始化檢視。

**修正：** 建立和匯出`initWithCoder:`建構函式，就像這樣：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相關的範例：[交談](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Dyld 訊息： 未快取映像名稱...

您可能會遇到的損毀記錄檔中的下列資訊：

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 這是 Apple 的原生連結器，以致它們公開私用的 framework 中的 bug （JavaScriptCore 已公開在 iOS 7，之前它是私用架構），和應用程式的部署目標是 iOS 版本時架構是私人的。 在此情況下 Apple 的連結器會使用私用的版本，而不是公用版本的 framework 的連結。

**修正：** 這將會解決 ios 9，但沒有簡單的解決方法，您可以同時套用自行： 只為目標的更新版本的 iOS 版本專案中 （您在此情況下可以嘗試 iOS 7）。 其他架構可能會表現出類似的問題，例如 WebKit framework 已公開在 iOS 8 （和目標 iOS 7 時，會導致此錯誤，因此您應將目標設 iOS 應用程式中使用 WebKit 8）。

## <a name="untrusted-enterprise-developer"></a>不受信任的企業開發人員

如果嘗試執行 iOS 9 版 Xamarin.iOS 應用程式的真實 iOS 硬體上，您可能會收到訊息，指出您的開發人員帳戶不在裝置上受信任。 例如: 

[![](troubleshooting-images/untrusted01.png "不受信任的企業開發人員警示")](troubleshooting-images/untrusted01.png#lightbox)

若要解決此問題，請執行下列各項：

1. 開發 mac 上啟動 Xcode （最新的 beta 版）
2. 選取**裝置**從**視窗**功能表開啟 [裝置] 視窗： 

    [![](troubleshooting-images/untrusted02.png "[裝置] 視窗")](troubleshooting-images/untrusted02.png#lightbox)
3. 在下**裝置**端面板中，選取您的裝置，以滑鼠右鍵按一下並選取**顯示佈建的設定檔...**: 

    [![](troubleshooting-images/untrusted03.png "SShow 佈建的設定檔")](troubleshooting-images/untrusted03.png#lightbox)
4. 選取目前的裝置，然後按一下每個佈建設定檔**-** 按鈕來刪除它： 

    [![](troubleshooting-images/untrusted04.png "刪除佈建設定檔")](troubleshooting-images/untrusted04.png#lightbox)
5. 從**Xcode**功能表上，選取**喜好設定...** 和**帳戶**: 

    [![](troubleshooting-images/untrusted05.png "Xcode 帳號喜好設定")](troubleshooting-images/untrusted05.png#lightbox)
6. 按一下**檢視詳細資料...** 按鈕，然後按一下**下載所有**按鈕： 

    [![](troubleshooting-images/untrusted06.png "下載的所有設定檔")](troubleshooting-images/untrusted06.png#lightbox)
7. 當更新完成清單時，按一下**完成**按鈕，然後關閉 [喜好設定] 視窗。
8. 移除您嘗試從 iOS 裝置測試 Xamarin.iOS 應用程式的現有版本。
9. 返回 Visual Studio for Mac、 乾淨的組建，並再試一次重新執行應用程式在裝置上。

您可能必須停止並重新啟動 Visual Studio for Mac，才能看到新的佈建設定檔，載入 Xcode。 您可能也必須調整**iOS 套件組合簽署**Xamarin.iOS 應用程式以選取新的佈建設定檔的選項。

## <a name="launch-screen-issues"></a>啟動螢幕的問題

iOS 9 現在會強制啟動螢幕需求，如此可以不再支援不同的介面方向重複使用相同的啟動映像。 請參閱 Apple [UILanchImage 參考](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28)如需詳細資訊。

或者，您可以使用分鏡腳本檔案，來呈現您的應用程式啟動畫面而不是使用一組 **.png**影像檔。 這是現在 Apple 的慣用方式來顯示啟動螢幕。 請參閱我們[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)指南以取得詳細的資訊。

最後，您的應用程式必須使用其啟動螢幕的分鏡腳本檔案，並支援所有的四個介面方向 （直向、 顛倒直向、 橫向左右橫向） 才會被視為投影片上面板中，或在分割檢視模式中執行。 若要了解有關新的多工能力的 iOS 9 的詳細資訊，請參閱我們[適用於 iPad 的多工](~/ios/platform/multitasking.md)指南。

## <a name="nsinternalinconsistencyexception-exception"></a>NSInternalInconsistencyException 例外狀況

編譯及執行 ios 9 現有 Xamarin.iOS 應用程式時您可能會發生錯誤，在表單中：

> Objective C 擲回例外狀況。  名稱： NSInternalInconsistencyException 原因： windows 應用程式應包含結尾的應用程式啟動在根檢視控制站

這是錯誤因為 Windows 應用程式應包含結尾的應用程式啟動在根檢視控制站，而且您現有的應用程式不會引發。

有此問題在至少兩個可能的解決方法：

1. 更新應用程式可以使用分鏡腳本檔案，而不是`xib`檔案，用於定義其使用者介面。 此一到版面配置的分鏡腳本需要很多時間取決於您的應用程式的大小和如何使用 iOS 設計工具 （或 Xcode 的介面產生器） 的知識。 如需詳細資訊，請參閱我們[簡介統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)文件。
2. 安裝程式`RootViewController`屬性的應用程式視窗中`FinishedLaunching`方法中的`AppDelegate`類別，以指向您的應用程式 UI 中檢視控制站。

## <a name="when-to-initialize-views-and-view-controllers"></a>初始化檢視和檢視控制器的時機

Xamarin.iOS 很可能要檢視或檢視控制器內的項目公開至 managed 程式碼，不過它會破壞 iOS 設計時所呼叫之建構函式的初始化。

一般情況下您應該初始化可以回 Objective C 程式碼從呼叫建構函式不能確定的任何項目時呼叫。 這也表示有更佳的位置 (其他.ctor) 或覆寫 （如 Objective C 沒有任何事件） 的呼叫應該在其中完成這類初始化。



## <a name="related-links"></a>相關連結

- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Xamarin.iOS 應用程式更新至 iOS9 （影片）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
