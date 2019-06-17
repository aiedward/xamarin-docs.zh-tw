---
title: 新的參考計數在 Xamarin.iOS 中的系統
description: 本文件說明 Xamarin 的增強的參考計數系統中，依預設，所有的 Xamarin.iOS 應用程式中啟用。
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: 8c7b1a88284156cb5d4261f18d5659ed66dfaf64
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037156"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>新的參考計數在 Xamarin.iOS 中的系統

Xamarin.iOS 9.2.1 導入了增強的參考計數所有的應用程式的系統預設值。 它可用來消除許多難以追蹤並修正舊版 Xamarin.iOS 中的記憶體問題。

## <a name="enabling-the-new-reference-counting-system"></a>啟用新的參考計數系統

截至 Xamarin 9.2.1 啟用新的參考計數系統**所有**預設的應用程式。

如果您正在開發的現有應用程式，您可以檢查.csproj 檔案，以確保所有發生次數`MtouchUseRefCounting`設定為`true`，例如下列：

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

如果設定為`false`您的應用程式不會使用新的工具。

### <a name="using-older-versions-of-xamarin"></a>使用舊版 Xamarin

Xamarin.iOS 7.2.1 和上述功能增強我們新的參考計數系統的預覽。

**傳統的 API:**

若要啟用這個新參考計數 」 的系統，請檢查**使用參考計數擴充功能**中找到的核取方塊**進階**您的專案 索引標籤**iOS 組建 選項**如下所示： 

[![](newrefcount-images/image1.png "啟用新的參考計數系統")](newrefcount-images/image1.png#lightbox)

請注意，這些選項中已移除較新版本的 Visual Studio for mac。

 **[統一的 API:](~/cross-platform/macios/unified/index.md)**

 新的參考計數擴充功能需要統一的 API，並應該依預設啟用。 IDE 的較舊版本不能自動檢查此值，您可能要加上核取它自己。

    
> [!IMPORTANT]
> 較早版本的這項功能已經周圍 MonoTouch 5.2，但卻是只適用於**sgen**為實驗性的預覽。 這個新的增強版本現已也供**Boehm**記憶體回收行程。


過去曾經發生過兩種類型的受管理的 Xamarin.iOS 物件： 這些只是一個包裝函式的原生的物件 （對等物件） 以及擴充，或合併 （衍生物件） 的新功能，通常就是藉由保留額外記憶體中狀態。 原本可能我們可以增強具有狀態的對等物件 (例如藉由加入C#事件處理常式)，但我們讓移未參考，然後再收集到的物件。 這在稍後可能會造成當機 (例如如果 OBJECTIVE-C 執行階段回撥到 managed 物件)。

新的系統會自動升級為它們所儲存的任何額外資訊時，由執行階段的物件的對等物件。

這解決了各種損毀發生在這種情況下：

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

參考計數擴充功能沒有此程式碼會損毀因為`cell`變成可回收，因此其`TouchDown`委派，這會轉譯為懸空的指標。

參考計數擴充功能可確保受管理的物件會保持運作，並避免其集合中，提供原生物件保留的原生程式碼。

新的系統也不需要*大部分*私用支援欄位繫結-這是預設方法，以維持執行個體中使用。 受管理的連結器會聰明，足以移除所有那些*不需要*欄位從 應用程式使用的新參考計數擴充功能。

這表示每個受管理的物件執行個體使用較少比之前的記憶體。 它也可以解決其中一些支援的欄位會保存已不再需要由 OBJECTIVE-C 執行階段，因此很難回收一些記憶體的參考相關的問題。
