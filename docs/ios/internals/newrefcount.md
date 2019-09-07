---
title: Xamarin 中的新參考計數系統
description: 本檔說明 Xamarin 的增強型參照計數系統，預設會在所有的 Xamarin iOS 應用程式中啟用。
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 56e35662230a3c529eb48a0ae742c2b063c1ac10
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753349"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>Xamarin 中的新參考計數系統

根據預設，9.2.1 會將增強的參考計數系統引進到所有應用程式。 它可以用來消除許多難以在舊版 Xamarin 中追蹤和修正的記憶體問題。

## <a name="enabling-the-new-reference-counting-system"></a>啟用新的參考計數系統

從 Xamarin 9.2.1 開始，預設會為**所有**應用程式啟用新的 ref 計數系統。

如果您正在開發現有的應用程式，您可以檢查 .csproj 檔案，以確保所有出現的`MtouchUseRefCounting`都已設定為`true`，如下所示：

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

如果設定為， `false`您的應用程式將不會使用新的工具。

### <a name="using-older-versions-of-xamarin"></a>使用舊版 Xamarin

7\.2.1 和更新版本提供新的參考計算系統的增強預覽功能。

**Classic API：**

若要啟用這個新的參考計數系統，請核取您專案的**IOS 組建選項**之 [ **Advanced** ] 索引標籤中的 [**使用參考計數延伸**] 核取方塊，如下所示： 

[![](newrefcount-images/image1.png "啟用新的參考計數系統")](newrefcount-images/image1.png#lightbox)

請注意，這些選項已在較新版本的 Visual Studio for Mac 中移除。

 **[Unified API：](~/cross-platform/macios/unified/index.md)**

 Unified API 需要新的參考計數延伸模組，而且應該預設為啟用。 較舊版本的 IDE 可能不會自動檢查此值，您可能必須自行進行檢查。

> [!IMPORTANT]
> 這項功能的較早版本已存在於 MonoTouch 5.2，但僅供**sgen**作為實驗性預覽之用。 這個新的增強版本現在也適用于**Boehm**垃圾收集行程。

在過去，Xamarin 會管理兩種類型的物件：只是原生物件（對等物件）的包裝函式，以及擴充或併入新功能（衍生物件）的物件，通常是藉由保留額外的記憶體內部狀態。 之前，我們可以使用狀態（例如，藉由新增C#事件處理常式）來擴大對等物件，但我們會讓物件成為未參考，然後再進行收集。 這可能會導致稍後損毀（例如，如果目標-C 執行時間被回呼到 managed 物件）。

當物件儲存任何額外的資訊時，新系統會自動將對等物件升級為由執行時間所管理的物件。

這可解決在這類情況下發生的各種損毀：

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

如果沒有參考計數延伸，此程式碼會`cell`損毀，因為會成為`TouchDown`可回收，而其委派會轉譯成無關聯的指標。

參考計數延伸可確保 managed 物件維持運作狀態，並防止其集合，前提是機器碼會保留原生物件。

新的系統也不再需要系結中使用的*大部分*私用支援欄位，這是維持實例運作的預設方法。 受管理的連結器非常聰明，可以使用新的參考計數延伸模組，從應用程式中移除所有不*需要*的欄位。

這表示每個受管理物件實例所耗用的記憶體比以前少。 它也會解決相關的問題，其中某些支援欄位會保留目標-C 執行時間不再需要的參考，因此很難回收一些記憶體。
