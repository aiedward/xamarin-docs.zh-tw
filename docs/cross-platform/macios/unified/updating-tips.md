---
title: 將程式碼更新至 Unified API 的祕訣
description: 本檔討論在將應用程式更新為使用 Xamarin 的 Unified API 時, 常見的錯誤和各種秘訣很有用。
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: 844730d2ace717b951df2d80b2add6d1094fe997
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226105"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>將程式碼更新至 Unified API 的祕訣

將舊版 Xamarin 方案更新為 Unified API 時, 可能會遇到下列錯誤。

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>NSInvalidArgumentException 找不到分鏡腳本錯誤

使用自動遷移工具將您的專案轉換成統一的 Api 之後, 目前的 Visual Studio for Mac 版本可能發生[錯誤](https://bugzilla.xamarin.com/show_bug.cgi?id=25569)。 更新之後, 如果您收到下列格式的錯誤訊息:

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

若要解決這個問題, 您可以執行下列動作, 找出下列組建目標檔案:

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

在此檔案中, 您需要尋找下列目標宣告:

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

並在`DependsOnTargets="_CollectBundleResources"`其中加入屬性。 與下列類似：

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

儲存檔案、重新開機 Visual Studio for Mac, 並對專案進行乾淨的 & 重建。 此問題的修正應該很快就會由 Xamarin 發行。

## <a name="useful-tips"></a>有用的秘訣

使用遷移工具之後, 您可能仍然會遇到一些需要手動介入的編譯器錯誤。
某些可能需要手動修正的事項包括:

- 比較`enum`可能`(int)`需要轉換。

- `NSDictionary.IntValue`現在會傳回`Int32Value`,而可以改用。 `nint`

- `nfloat`無法`nint` 標記`const`和類型;`static readonly nint`是合理的替代方法。

- 直接在`MonoTouch.`命名空間中使用的專案, 現在通常是`ObjCRuntime.`在命名空間中 (例如: `MonoTouch.Constants.Version`現在`ObjCRuntime.Constants.Version`是)。

- 序列化物件的程式碼可能會在嘗試序列化`nint`和`nfloat`類型時中斷。 在遷移之後, 請務必檢查您的序列化程式碼是否如預期般運作。

- 有時候, 自動化工具會在條件`#if #else`式編譯器指示詞內遺漏程式碼。 在此情況下, 您必須手動進行修正 (請參閱下面的常見錯誤)。

- 使用`[Export]`手動匯出的方法可能不會由遷移工具自動修正, 例如, 在此程式碼 snippert 中, 您必須手動將傳回`nfloat`類型更新為:

  ```csharp
  [Export("tableView:heightForRowAtIndexPath:")]
  public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
  ```

- Unified API 不會提供 NSDate 與 .NET DateTime 之間的隱含轉換, 因為它不是不失真的轉換。 若要避免在轉換`DateTimeKind.Unspecified`成`NSDate`之前, `DateTime`將 .net 轉換成本機或 UTC 的相關錯誤。

- 目標-C 類別方法現在會產生為 Unified API 中的擴充方法。 例如, 先前使用`UIView.DrawString`的程式碼現在會在 Unified API 中參考。 `NSString.DrawString`

- 使用 AVFoundation 類別`VideoSettings`搭配的程式碼應該會變更`WeakVideoSettings`為使用屬性。 這需要`Dictionary`, 其可做為設定類別上的屬性, 例如:

  ```csharp
  vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
  ```

- NSObject `.ctor(IntPtr)`的函式已從公用變更為受保護 ([以防止不當使用](~/cross-platform/macios/unified/overview.md#NSObject_ctor))。

- `NSAction`已[取代](~/cross-platform/macios/unified/overview.md#NSAction)為標準 .net `Action`。 某些簡單的`Action<T>`(單一參數) 委派也已由取代。

最後, 請參閱[傳統的 Unified API 差異](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md), 以在程式碼中查詢 api 的變更。 搜尋[此頁面](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)將有助於尋找傳統 api, 以及它們已更新到的內容。

> [!NOTE]
> 在`MonoTouch.Dialog`遷移之後, 命名空間會維持不變。 如果您的程式碼使用**MonoTouch** , 您應該繼續使用該命名空間,*不*會`MonoTouch.Dialog`變更`Dialog`為!

## <a name="common-compiler-errors"></a>常見編譯器錯誤

以下列出常見錯誤的其他範例, 以及解決方案:

**錯誤 CS0012:類型 ' MonoTouch. UIKit. UIView ' 定義于未參考的元件中。**

補丁這通常表示專案會參考尚未使用 Unified API 建立的元件或 NuGet 套件。 您應該刪除並重新新增所有元件和 NuGet 套件。 如果這樣做無法修正錯誤, 外部程式庫可能還不支援 Unified API。

**錯誤 MT0034:不能在相同的 Xamarin. iOS 專案中同時包含 ' monotouch ' 和 ' Xamarin ', 而 ' monotouch ' 則是由 ' Xamarin, Version = 0.6.3.0, Culture = 中性, PublicKeyToken = null ' 所參考的 '。**

補丁刪除造成此錯誤的元件, 然後重新加入至專案。

**錯誤 CS0234:命名空間 ' MonoTouch ' 中不存在類型或命名空間名稱 ' Foundation '。您是否遺漏了元件參考？**

補丁Visual Studio for Mac 中的自動化遷移工具*應該*更新所有`MonoTouch.Foundation`的參考`Foundation`, 不過在某些情況下, 這些都需要手動更新。 先前包含`MonoTouch`的其他命名空間`UIKit`(例如) 可能會出現類似的錯誤。

**錯誤 CS0266:無法將類型 ' double ' 隱含轉換為 ' System.object '**

修正: 變更類型, 並將`nfloat`轉換為。 其他具有64位對等的類型 (例如`nint`), 也可能會發生此錯誤。

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**錯誤 CS0266:無法將類型 ' CoreGraphics. Rectanglef ' 隱含轉換為 ' RectangleF '。明確轉換存在 (您是否遺漏了轉換？)**

補丁將`RectangleF`實例變更為`CGRect`to `SizeF` 、 `CGSize`to 和`PointF` to 。`CGPoint` 命名空間`using System.Drawing;`應取代為`using CoreGraphics;` (如果尚未存在的話)。

**錯誤 CS1502:' CoreGraphics. CGCoNtext. SetLineDash (nfloat, nfloat []) ' 的最佳多載方法相符, 有一些不正確引數**

補丁將陣列類型變更`nfloat[]`為, 並`Math.PI`明確轉換。

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**錯誤 CS0115: ' WordsTableSource. RowsInSection (UIKit. UITableView, int) ' 已標示為覆寫, 但找不到適合的方法來覆寫**

補丁將傳回值和參數類型變更為`nint`。 這通常發生在方法覆寫中, 例如`UITableViewSource`上的`RowsInSection`, `NumberOfSections`包括`GetHeightForRow`、 `TitleForHeader`、 `GetViewForHeader`、、等。

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**錯誤 CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)`: 傳回類型必須是 ' nint ' 才能符合覆寫的成員`UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

補丁當傳回類型變更為`nint`時, 將傳回值轉換為。 `nint`

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**錯誤 CS1061:類型 ' CoreGraphics. CGPath ' 不包含 ' AddElipseInRect ' 的定義**

補丁更正為`AddEllipseInRect`的拼寫。 其他名稱變更包括:

- 將 ' Color. 黑色 ' 變更`NSColor.Black`為。
- 將 MapKit ' AddAnnotation ' 變更`AddAnnotations`為。
- 將 AVFoundation ' DataUsingEncoding ' 變更`Encode`為。
- 將 AVFoundation ' AVMetadataObject. TypeQRCode ' 變更`AVMetadataObjectType.QRCode`為。
- 將 AVFoundation ' VideoSettings ' 變更`WeakVideoSettings`為。
- 將 PopViewControllerAnimated 變更`PopViewController`為。
- 將 CoreGraphics ' CGBitmapCoNtext. SetRGBFillColor ' 變更`SetFillColor`為。

**錯誤 CS0546: 無法覆寫, 因為 ' MapKit. MKAnnotation. 座標 ' 沒有可覆寫的 set 存取子 (CS0546)**

透過子類別化 MKAnnotation 建立自訂注釋時, 座標欄位不會有 setter, 只有 getter。

[修正](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

- 加入欄位以追蹤座標
- 在座標屬性的 getter 中傳回此欄位
- 覆寫 SetCoordinate 方法並設定您的欄位
- 使用傳入的座標參數, 在您的 ctor 中呼叫 SetCoordinate

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
- [更新 Xamarin. Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新系結](~/cross-platform/macios/unified/update-binding.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [傳統與 Unified API 差異](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
