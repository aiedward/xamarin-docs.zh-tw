---
title: "統一的 API 來更新程式碼的秘訣"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: dd44dda0fd52ba1271003b3da9f8d4c756c0625b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>統一的 API 來更新程式碼的秘訣

使用 Visual Studio 中的自動的移轉工具，針對 iOS 和 Mac 的專案參考統一的 API （Xamarin.iOS.dll 或 Xamarin.Mac.dll） 讓您的許多必要的程式碼變更，會將轉換 Mac (請參閱[Xamarin Studio 版本資訊一節 「 傳統統一的 API 程式碼移轉工具 」](http://developer.xamarin.com/releases/studio/xamarin.studio_5.7/xamarin.studio_5.7/)的特定詳細資料)。

# <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>找不到 NSInvalidArgumentException 腳本錯誤

沒有[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569)在目前版本的 Visual Studio for Mac，若要將專案轉換成統一的 Api 使用自動化的移轉工具之後就會發生。 更新之後，如果您在表單中出現錯誤訊息：

```csharp
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...

```

您可以執行下列動作以解決此問題，請找出下列的建置目標檔案：

```csharp
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

此檔案中，您需要尋找下列目標宣告：

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

新增`DependsOnTargets="_CollectBundleResources"`屬性。 與下列類似：

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

儲存檔案、 for Mac、 重新啟動 Visual Studio 和清除和重建您的專案。 應該由 Xamarin 儘速釋放解決這個問題。


# <a name="useful-tips"></a>實用秘訣

使用移轉工具後, 可能仍可使用某些編譯器錯誤，需要手動介入。
可能需要手動修正某些作業，包含：

* 比較`enum`s 可能需要`(int)`轉型。


* `NSDictionary.IntValue` 現在會傳回`nint`，沒有`Int32Value`可以改為使用。


* `nfloat` 和`nint`類型不能標示`const`;  `static readonly nint`是合理的替代方案。


* 若要直接在使用的項目`MonoTouch.`命名空間現在是通常位於`ObjCRuntime.`命名空間 (例如：`MonoTouch.Constants.Version`現在`ObjCRuntime.Constants.Version`)。


* 嘗試序列化時，將物件序列化的程式碼可能會中斷`nint`和`nfloat`型別。 請務必檢查如預期般在移轉之後，序列化程式能夠運作。


* 自動化的工具有時候遺漏內的程式碼`#if #else`條件式編譯器指示詞。 在此情況下，您將需要手動進行修正 （請參閱下方的常見錯誤）。


* 手動匯出的方法使用`[Export]`可能不會自動修正移轉工具，例如在您必須手動更新傳回的型別，以這個程式碼 snippert `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```


 * 統一的 API 不提供 NSDate 和.NET DateTime 之間的隱含轉換，因為它不是不失真的轉換。 若要避免的相關錯誤`DateTimeKind.Unspecified`將轉換.NET`DateTime`為本機或之前轉換成 UTC `NSDate`。


 * Objective C 類別方法現在會產生以統一的 API 中的擴充方法。 例如，程式碼，先前使用`UIView.DrawString`現在會參考`NSString.DrawString`中統一的 API。


 * 使用具有 AVFoundation 類別程式碼`VideoSettings`應該變更為使用`WeakVideoSettings`屬性。 這需要`Dictionary`，這可做為屬性上設定類別，例如：

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```


 * NSObject`.ctor(IntPtr)`建構函式已從變更公開受保護 ([以防止不當使用](~/cross-platform/macios/unified/index.md#NSObject_ctor))。


 * `NSAction` 已[取代](~/cross-platform/macios/unified/index.md#NSAction)與 starndard.NET `Action`。 一些簡單 （單一參數） 委派也已取代為`Action<T>`。


最後，請參閱[傳統 v 統一的 API 差異](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)查閱 api 程式碼中的變更。 搜尋[本頁](http://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)將協助您尋找傳統應用程式開發介面什麼它們已被更新。


**注意：** `MonoTouch.Dialog`命名空間則會在移轉之後仍然相同。 如果您的程式碼使用**MonoTouch.Dialog**應該繼續使用該命名空間-*不*變更`MonoTouch.Dialog`至`Dialog`！


# <a name="common-compiler-errors"></a>常見編譯器錯誤

下面列出常見錯誤的其他範例連同方案一併：

**錯誤 CS0012： 未參考的組件中定義的型別 'MonoTouch.UIKit.UIView'。**

修正： 這通常表示專案所參考的元件或已不使用統一的 API 建置的 NuGet 封裝。 您應該刪除並重新加入所有的元件和 NuGet 套件。 如果這樣無法修正錯誤，外部程式庫可能尚未支援統一的 API。


**錯誤 MT0034： 不能包含 'monotouch.dll' 和 'Xamarin.iOS.dll' 在相同的 Xamarin.iOS 專案-雖然 'monotouch.dll' 正由明確地參考 'Xamarin.iOS.dll' ' Xamarin.Mobile，Version = 0.6.3.0，Culture = neutral，PublicKeyToken = null'。**

修正： 刪除引發此錯誤的元件，再重新加入至專案。


**錯誤 CS0234： 類型或命名空間名稱 'Foundation' 不存在於命名空間 'MonoTouch'。您是否遺漏了組件參考？**

修正: 「 自動的移轉工具在 Visual Studio for Mac*應該*更新所有`MonoTouch.Foundation`參考`Foundation`，不過在某些情況下將必須以手動方式更新。 類似的錯誤可能會出現先前包含在其他命名空間`MonoTouch`，例如`UIKit`。



**錯誤 CS0266： 無法將 'double' 至 'System.float' 類型隱含轉換**

修正： 變更類型，並轉換成`nfloat`。 也會用於 64 位元對等項目與其他類型可能會發生這個錯誤 (例如`nint`，)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```


**錯誤 CS0266： 無法將類型 'CoreGraphics.CGRect' 至 'System.Drawing.RectangleF' 隱含轉換。有明確轉換存在 （您是否遺漏轉型？）**

修正： 變更執行個體，以便`RectangleF`至`CGRect`，`SizeF`至`CGSize`，和`PointF`至`CGPoint`。 命名空間`using System.Drawing;`應該取代成`using CoreGraphics;`（如果尚未存在）。



**錯誤 CS1502： 最多載符合的方法 ' CoreGraphics.CGContext.SetLineDash (System.nfloat、 System.nfloat[])' 有一些無效的引數**

修正： 陣列類型變更為`nfloat[]`和明確轉換`Math.PI`。

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```


**錯誤 CS0115: 'WordsTableSource.RowsInSection （UIKit.UITableView，int）' 標示為覆寫，但若要覆寫找不到合適的方法**

修正： 變更傳回的值和參數類型，以`nint`。 這通常會發生在方法覆寫，例如`UITableViewSource`，包括`RowsInSection`， `NumberOfSections`， `GetHeightForRow`， `TitleForHeader`，`GetViewForHeader`等等。

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```


**錯誤 CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)'**


修正： 時的傳回型別變更為`nint`，傳回值轉換成`nint`。

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```



**錯誤 CS1061： 類型 'CoreGraphics.CGPath' 未包含定義 'AddElipseInRect'**

修正： 更正拼字檢查 以`AddEllipseInRect`。 其他的名稱變更還包括：

* 若要變更 'Color.Black' `NSColor.Black`。
* 若要變更 MapKit 'AddAnnotation' `AddAnnotations`。
* 若要變更 AVFoundation 'DataUsingEncoding' `Encode`。
* 若要變更 AVFoundation 'AVMetadataObject.TypeQRCode' `AVMetadataObjectType.QRCode`。
* 若要變更 AVFoundation 'VideoSettings' `WeakVideoSettings`。
* 變更至 PopViewControllerAnimated `PopViewController`。
* 若要變更 CoreGraphics 'CGBitmapContext.SetRGBFillColor' `SetFillColor`。

**錯誤 CS0546： 無法覆寫，因為 'MapKit.MKAnnotation.Coordinate' 並沒有覆寫的 set 存取子 (CS0546)**

當您建立自訂的註解 MKAnnotation 子類別化時座標欄位有沒有 setter，僅有 getter。

[修正](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* 加入欄位以追蹤的座標
* 傳回此欄位中的座標屬性的 getter
* 覆寫 SetCoordinate 方法，以及設定您的欄位
* 在您使用傳入的座標參數的建構函式中呼叫 SetCoordinate

它看起來應該如下所示：

```csharp
class BasicPinAnnotation : MKAnnotation
{
    private CLLocationCoordinate2D _coordinate;

    public override CLLocationCoordinate2D Coordinate
    {
        get
        {
            return _coordinate;
        }
    }

    public override void SetCoordinate(CLLocationCoordinate2D value)
    {
        _coordinate = value;
    }

    public BasicPinAnnotation (CLLocationCoordinate2D coordinate)
    {
        SetCoordinate(coordinate);
    }
}
```



## <a name="related-links"></a>相關連結

- [更新應用程式](~/cross-platform/macios/unified/updating-apps.md)
- [更新的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin.Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新繫結](~/cross-platform/macios/unified/update-binding.md)
- [使用跨平台應用程式中的原生類型](~/cross-platform/macios/native-types-cross-platform.md)
- [傳統的 vs 統一的 API 差異](http://developer.xamarin.comhttps://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
