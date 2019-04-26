---
title: 將程式碼更新至 Unified API 的祕訣
description: 更新應用程式使用 Xamarin 的統一 API 時，這份文件會討論常見的錯誤以及各種實用的秘訣。
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: a5083e1d31377caece1b8fb4faf33b6e3ff88202
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61211816"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>將程式碼更新至 Unified API 的祕訣

在更新舊的 Xamarin 方案至統一 API 時，可能會發生下列錯誤。

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>找不到 NSInvalidArgumentException 腳本時發生錯誤

沒有[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569)在目前版本的 Visual Studio for Mac 將專案轉換至 Unified Api 使用自動化的移轉工具之後可能發生的。 更新之後，如果您在表單中收到錯誤訊息：

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

您可以執行下列動作以解決此問題，請找出下列的建置目標檔案：

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

此檔案中，您需要尋找下列目標宣告：

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

並新增`DependsOnTargets="_CollectBundleResources"`屬性。 與下列類似：

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

將檔案儲存、 重新 Visual Studio for Mac 中啟動和執行清除及重建您的專案。 應該 xamarin 稍後發行修正此問題。

## <a name="useful-tips"></a>實用的秘訣

使用移轉工具之後, 您可能仍然會取得一些需要手動介入的編譯器錯誤。
可能需要手動修正的事項包括：

* 比較`enum`可能會需要`(int)`轉型。

* `NSDictionary.IntValue` 現在會傳回`nint`，沒有`Int32Value`可以改為使用。

* `nfloat` 並`nint`類型不能標示`const`;  `static readonly nint`是合理的替代方案。

* 要直接在使用的項目`MonoTouch.`命名空間就一般來說，在`ObjCRuntime.`命名空間 (例如：`MonoTouch.Constants.Version`現在`ObjCRuntime.Constants.Version`)。

* 當您嘗試序列化時，將物件序列化的程式碼可能會中斷`nint`和`nfloat`型別。 請務必檢查如預期般在移轉之後，適用於您的序列化程式碼。

* 這個自動化的工具有時遺漏程式碼內`#if #else`條件式編譯器指示詞。 在此情況下，您將需要手動進行修正 （請參閱以下的常見錯誤）。

* 使用手動匯出的方法`[Export]`可能不會自動修正移轉工具，例如在您必須手動更新傳回的型別，以這個程式碼 snippert `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

 * 統一的 API 不提供 NSDate 和.NET DateTime 之間的隱含轉換，因為它不是不失真的轉換。 若要避免錯誤的相關`DateTimeKind.Unspecified`轉換.NET`DateTime`為本機或之前轉換成 UTC `NSDate`。

 * Objective C 分類方法現在會產生做為統一的 API 中的擴充方法。 例如，程式碼，先前使用`UIView.DrawString`現在會參考`NSString.DrawString`Unified API 中。

 * 使用具有 AVFoundation 類別程式碼`VideoSettings`應該會變更為使用`WeakVideoSettings`屬性。 這需要`Dictionary`，這是可設定類別上的屬性，例如：

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

 * NSObject`.ctor(IntPtr)`建構函式已從變更公開給受保護 ([以避免不當使用](~/cross-platform/macios/unified/overview.md#NSObject_ctor))。

 * `NSAction` 已被[取代](~/cross-platform/macios/unified/overview.md#NSAction)starndard.NET 與`Action`。 一些簡單 （單一參數） 的委派也已取代為`Action<T>`。

最後，請參閱[傳統 v 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)查閱 api 在程式碼中的變更。 搜尋[本頁](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)會協助您找出的傳統 Api 和功能他們已更新為。

**注意︰** `MonoTouch.Dialog`命名空間則會在移轉後保持相同。 如果您的程式碼會使用**MonoTouch.Dialog**您應該繼續使用該命名空間-請勿*不*變更`MonoTouch.Dialog`至`Dialog`！

## <a name="common-compiler-errors"></a>常見編譯器錯誤

常見錯誤的其他範例，以下列出隨解決方案：

**錯誤 CS0012:未參考的組件中定義的型別 'MonoTouch.UIKit.UIView'。**

修正：這通常表示此專案參考的元件或尚未建置使用 Unified API 的 NuGet 套件。 您應該刪除並重新加入所有的元件和 NuGet 套件。 如果這樣無法修正錯誤，外部程式庫可能尚未支援統一的 API。

**錯誤 MT0034:不能包含 'monotouch.dll' 和 'Xamarin.iOS.dll' 中相同的 Xamarin.iOS 專案-'monotouch.dll' 所參考時明確地參考 'Xamarin.iOS.dll' ' Xamarin.Mobile，版本 = 0.6.3.0，文化特性 = 中性，PublicKeyToken = null'。**

修正：刪除引發此錯誤的元件，然後重新加入至專案。

**錯誤 CS0234:'Foundation' 不存在於的命名空間 'MonoTouch' 類型或命名空間名稱。您是否遺漏了組件參考？**

修正：在 Visual Studio for Mac 的自動化的移轉工具*應該*更新所有`MonoTouch.Foundation`參考`Foundation`，但在某些情況下將必須以手動方式更新。 針對先前包含在其他命名空間，可能會出現類似的錯誤`MonoTouch`，例如`UIKit`。

**錯誤 CS0266:無法將類型 'double' 到 'System.float' 隱含轉換**

修正： 將類型變更，並轉換成`nfloat`。 這項錯誤也可能會發生其他型別具有 64 位元對等項目 (例如`nint`，)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**錯誤 CS0266:無法隱含地轉換類型 'CoreGraphics.CGRect' 到 'System.Drawing.RectangleF'。存在明確的轉換 （您是否遺漏轉型？）**

修正：變更執行個體`RectangleF`要`CGRect`，`SizeF`要`CGSize`，和`PointF`來`CGPoint`。 命名空間`using System.Drawing;`應該取代成`using CoreGraphics;`（如果尚未存在）。

**錯誤 CS1502:最佳多載符合的方法 ' CoreGraphics.CGContext.SetLineDash (System.nfloat、 System.nfloat[])' 有一些無效的引數**

修正：陣列型別變更`nfloat[]`明確轉型和`Math.PI`。

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**錯誤 CS0115: 'WordsTableSource.RowsInSection （UIKit.UITableView，int）' 會標示為 覆寫，但找到來覆寫任何合適的方法**

修正：變更傳回的值和參數類型，以`nint`。 這通常會發生在方法覆寫，例如`UITableViewSource`，包括`RowsInSection`， `NumberOfSections`， `GetHeightForRow`， `TitleForHeader`，`GetViewForHeader`等等。

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**錯誤 CS0508:`WordsTableSource.NumberOfSections(UIKit.UITableView)': return type must be 'System.nint' to match overridden member `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)'**

修正：若要變更傳回型別時`nint`，將傳回值轉換成`nint`。

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**錯誤 CS1061:類型 'CoreGraphics.CGPath' 未包含 'AddElipseInRect' 定義**

修正：更正拼字檢查 以`AddEllipseInRect`。 其他的名稱變更包括：

* 將 'Color.Black' 變更為`NSColor.Black`。
* 若要變更 MapKit 'AddAnnotation' `AddAnnotations`。
* 若要變更 AVFoundation 'DataUsingEncoding' `Encode`。
* 若要變更 AVFoundation 'AVMetadataObject.TypeQRCode' `AVMetadataObjectType.QRCode`。
* 若要變更 AVFoundation 'VideoSettings' `WeakVideoSettings`。
* 變更至 PopViewControllerAnimated `PopViewController`。
* 若要變更 CoreGraphics 'CGBitmapContext.SetRGBFillColor' `SetFillColor`。

**錯誤 CS0546： 無法覆寫，因為 'MapKit.MKAnnotation.Coordinate' 並沒有可覆寫的 set 存取子 (CS0546)**

當您建立自訂註釋 MKAnnotation 子類別化時座標欄位不具有 setter，只是 getter。

[修正](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* 加入欄位以追蹤的座標
* 傳回此欄位中的座標屬性 getter
* 覆寫 SetCoordinate 方法，以及設定您的欄位
* 在您使用傳入的座標參數的建構函式的呼叫 SetCoordinate

看起來應類似如下：

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
- [更新 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin.Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新繫結](~/cross-platform/macios/unified/update-binding.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
