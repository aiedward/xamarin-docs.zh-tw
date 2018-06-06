---
title: 新的參考計數在 Xamarin.iOS 系統
description: 本文件說明 Xamarin 的增強的參考計數系統中，依預設，所有的 Xamarin.iOS 應用程式中啟用。
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f2e40ca1fdd4a02d62e45004b75f3abefda781a5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786248"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>新的參考計數在 Xamarin.iOS 系統

Xamarin.iOS 9.2.1 導入了增強的參考計數的所有應用程式的系統預設。 它可用來消除許多難以追蹤並修正 Xamarin.iOS 較舊版本中的記憶體問題。

## <a name="enabling-the-new-reference-counting-system"></a>啟用新的參考計數系統

為準，Xamarin 9.2.1 啟用新的 ref 計數系統**所有**預設的應用程式。

如果您正在開發的現有應用程式，您可以檢查，確保.csproj 檔案的所有項目`MtouchUseRefCounting`設為`true`類似如下：

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

如果設定為`false`您的應用程式將不會使用新的工具。

### <a name="using-older-versions-of-xamarin"></a>使用 Xamarin 的舊版本

Xamarin.iOS 7.2.1 和上述功能增強的預覽，我們新的參考計數系統。

**傳統應用程式開發介面：**

若要啟用此新參考計數 」 的系統，請檢查**使用參考計數延伸**核取方塊位於**進階**] 索引標籤的 [您的專案**iOS 建置選項**如下所示： 

[![](newrefcount-images/image1.png "啟用新的參考計數系統")](newrefcount-images/image1.png#lightbox)

請注意，這些選項中已移除較新版本的 Visual Studio for mac。

 **[統一的 API:](~/cross-platform/macios/unified/index.md)**

 新的參考計數延伸模組需要統一的 API，則應啟用預設。 您的 IDE 的舊版本不一定會自動檢查此值，您可能必須在它自己。

    
> [!IMPORTANT]
> 較早版本的這項功能已經周圍 MonoTouch 5.2，但卻是僅供**sgen**實驗的預覽版本。 這個新的增強版本現在也會提供如**Boehm**記憶體回收行程。


在過去已有兩種類型的物件管理 Xamarin.iOS： 而只是原生物件 （對等物件） 以及擴充或加入新功能 （衍生物件）-周圍的包裝函式通常會保留額外記憶體中狀態。 它之前，我們無法擴充具有狀態的對等物件 （例如藉由加入 C# 事件處理常式），不過，我們讓移未參考，然後再收集到的物件。 這在稍後可能會造成損毀 (例如如果 Objective C 執行階段回撥到 managed 物件)。

新的系統會自動升級為物件所管理的執行階段時，它們會儲存任何額外資訊的對等物件。

這樣可以解決這種情況中發生的各種損毀：

```csharp
class MyTableSource : UITableViewSource {
   public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath) {
        var cell = tableView.DequeueReusableCell ("myId");
        if (cell != null)
                return cell;

        cell = new UITableViewCell (UITableViewCellStyle.Default, "myId");
        var txt = new UITextField ();
        txt.TouchDown += delegate { Console.WriteLine ("...."); };
        cell.ContentView.AddSubview (txt);
        return cell;
   }
}
```

不含參考計數副檔名這段程式碼會損毀因為`cell`變成可回收，，因此其`TouchDown`委派，這會轉譯為懸置的指標。

提供原生程式碼不會保留原始物件的參考計數延伸模組可確保受管理的物件會保持運作，並防止它的集合。

新的系統也不再需要*大部分*私用欄位繫結-這是要讓執行個體保持運作的預設方法中使用的備份。 受管理的連結器會聰明，可以移除所有項目*不需要*欄位從應用程式使用新的參考計數的延伸模組。

這表示每個受管理的物件執行個體使用較少比之前的記憶體。 它也可以解決相關的問題，其中某些支援欄位會保存已不需要再 Objective C 執行階段，讓它更難以回收記憶體的參考。
