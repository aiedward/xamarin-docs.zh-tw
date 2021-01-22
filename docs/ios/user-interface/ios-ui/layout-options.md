---
title: Xamarin 中的版面配置選項
description: 本檔說明在 Xamarin 中配置使用者介面的不同方式。 它會討論自動調整大小和自動版面配置。
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e320e5e3ad7942f371e01baaade753a2bb35f76e
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697653"
---
# <a name="layout-options-in-xamarinios"></a>Xamarin 中的版面配置選項

當您調整或旋轉視圖時，有兩種不同的機制可控制版面配置：

- **自動調整大小** -設計工具中的自動調整大小偵測器提供設定屬性的方式 `AutoresizingMask` 。 這會讓控制項錨定至其容器的邊緣，並（或）修正其大小。 自動調整大小適用于所有版本的 iOS。 以下會更詳細地說明這一點
- **自動** 配置-在 iOS 6 中引進的一項功能，可讓您更精細地控制 UI 控制項的關聯性。 它可讓您控制專案相對於設計介面上其他元素的位置。 本主題將在  [使用 Xamarin IOS 設計](~/ios/user-interface/designer/designer-auto-layout.md) 工具指南的自動版面配置中更詳細地討論。

## <a name="autosizing"></a>自動調整大小

當使用者調整視窗大小時，例如當裝置旋轉且方向變更時，系統會根據其自動調整大小規則，自動調整該視窗內的視圖大小。 您可以使用中的 `AutoresizingMask` 屬性 `UIView` 或在 iOS 設計工具的 **Properties Pad** 中，以 c # 設定這些規則，如下所示：

 [![螢幕擷取畫面顯示 I O S 設計工具的 Properties Pad。](layout-options-images/image41.png)](layout-options-images/image41.png#lightbox)

選取控制項時，這可讓您手動指定控制項的位置和維度，以及選擇 **自動調整大小** 行為。 如下列螢幕擷取畫面所示，我們可以使用自動調整大小控制項中的彈簧和 struts，來定義所選取的視圖與父系的關聯性：

 [![螢幕擷取畫面顯示 I O S 設計工具 Properties Pad 中的自動調整大小控制項。](layout-options-images/image42.png)](layout-options-images/image42.png#lightbox)

調整 *彈簧* 會使視圖根據其父視圖的寬度或高度調整大小。 調整 *strut* 會讓視圖在該特定邊緣上維持與其父視圖之間的固定距離。

您也可以在程式碼中設定這些設定：

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```

若要測試自動調整大小設定，請在專案選項中啟用不同的 **支援裝置方向** ：

 [![自動調整大小設定](layout-options-images/image43a.png)](layout-options-images/image43a.png#lightbox)

在程式碼後置中，我們可以使用下列程式碼，使兩個文字控制項水準調整大小：

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```

我們也可以使用設計工具來調整控制項。 選取下方的 struts 會導致影像保持靠右對齊，而不會被剪下視圖的底部：

 [![螢幕擷取畫面顯示已選取左側和底部的自動調整大小控制項。 ](layout-options-images/autoresize.png)](layout-options-images/autoresize.png#lightbox)

這些螢幕擷取畫面顯示當旋轉螢幕時，控制項如何自行調整大小或重新置放：

 [![螢幕擷取畫面顯示行動裝置顯示在直向和橫向，並已調整文字和圖形。](layout-options-images/image44a.png)](layout-options-images/image44a.png#lightbox)

請注意，文字視圖和文字欄位都因為設定，而調整為保持相同的左邊和右邊界 `FlexibleWidth` 。 影像的上邊界和左邊界有彈性，這表示它會保留底部和右邊的邊界，而在旋轉螢幕時，讓影像保持在視野中。 複雜的配置通常需要在每個可見控制項上組合這些設定，讓使用者介面保持一致，並防止控制項在視圖的界限變更 (由於旋轉或其他調整大小事件) 。

## <a name="related-links"></a>相關連結

- [控制項 (範例) ](/samples/xamarin/ios-samples/controls)