---
title: Xamarin 中的版面配置選項
description: 本檔說明在 Xamarin. iOS 中配置使用者介面的不同方式。 其中討論自動調整大小和自動版面配置。
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 6cfb567e6f74228a0b7e9ce017c57436df06cb56
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939096"
---
# <a name="layout-options-in-xamarinios"></a>Xamarin 中的版面配置選項

有兩種不同的機制，可在調整視圖大小或旋轉時控制版面配置：

- **自動調整大小**–設計工具中的自動調整大小偵測器提供設定屬性的方式 `AutoresizingMask` 。 這會讓控制項錨定到其容器的邊緣，並（或）修正其大小。 自動調整大小適用于所有版本的 iOS。 下面將更詳細地說明這一點
- **自動**配置– iOS 6 中引進的一項功能，可讓您更精細地控制 UI 控制項的關聯性。 它會允許控制項相對於設計介面上其他元素的位置。 本主題將在[使用 Xamarin IOS 設計工具的自動版面](~/ios/user-interface/designer/designer-auto-layout.md)配置指南中更詳細地討論。

## <a name="autosizing"></a>自動調整大小

當使用者調整視窗大小時（例如當裝置旋轉且方向變更時），系統會根據其自動調整大小規則，自動調整該視窗內的視圖大小。 您可以使用的 `AutoresizingMask` 屬性 `UIView` ，或在 iOS 設計工具的**Properties Pad**中，以 c # 設定這些規則，如下所示：

 [![Visual Studio for Mac 設計工具](layout-options-images/image41.png)](layout-options-images/image41.png#lightbox)

選取控制項時，這可讓您手動指定控制項的位置和維度，以及選擇**自動調整大小**的行為。 如下列螢幕擷取畫面所示，我們可以使用自動調整大小控制項中的彈簧和 struts，來定義所選的視圖與其父系的關聯性：

 [![Visual Studio for Mac 設計工具](layout-options-images/image42.png)](layout-options-images/image42.png#lightbox)

調整*彈簧*會使視圖根據其父視圖的寬度或高度調整大小。 調整*strut*會讓視圖在該特定邊緣上維持其本身與其父系視圖之間的固定距離。

這些設定也可以在程式碼中設定：

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```

若要測試自動調整大小設定，請在專案的選項中啟用不同的**支援裝置方向**：

 [![自動調整大小設定](layout-options-images/image43a.png)](layout-options-images/image43a.png#lightbox)

在後面的程式碼中，我們可以使用下列程式碼，使兩個文字控制項水準調整大小：

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```

我們也可以使用設計工具來調整控制項。 選取如下所示的 struts，將會使影像保持靠右對齊，而不會被裁剪到視圖的底部：

 [![Autorotation](layout-options-images/autoresize.png)](layout-options-images/autoresize.png#lightbox)

這些螢幕擷取畫面顯示當旋轉螢幕時，控制項的調整大小或重新置放的方式：

 [![Autorotation](layout-options-images/image44a.png)](layout-options-images/image44a.png#lightbox)

請注意，由於設定，[文字] 視圖和 [文字] 欄位都會延展以保留相同的左邊和右邊界 `FlexibleWidth` 。 影像具有上邊界和左邊界彈性，這表示它會保留右下邊界，並在旋轉螢幕時讓影像保持在視野中。 複雜版面配置通常需要在每個可見控制項上組合這些設定，讓使用者介面保持一致，並防止控制項在視圖的界限變更時（由於旋轉或其他調整大小事件）而重迭。

## <a name="related-links"></a>相關連結

- [控制項（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
