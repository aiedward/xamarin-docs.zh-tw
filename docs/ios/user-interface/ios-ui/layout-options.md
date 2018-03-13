---
title: "配置選項"
ms.topic: article
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: f4917eafff020bb0e2d14a27d3c1a44d1d4087d7
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="layout-options"></a>配置選項

有兩種不同的機制，來檢視已調整大小或旋轉時，控制配置：

-  **自動調整**– 調整設計工具中的偵測器會提供方法來設定`AutoresizingMask`屬性。 這可讓控制項錨定至邊緣與其容器和/或修正其大小。 自動調整大小適用於 iOS 的所有版本。 這是以下更詳細描述
-  **自動版面配置**– 中 UI 控制項的關聯性的精細控制權的 iOS6 所引進的功能。 它會允許在設計介面上的其他項目位置的項目位置的控制項。 更詳細地討論這個主題[Xamarin iOS 設計工具與自動配置](~/ios/user-interface/designer/designer-auto-layout.md)指南。


## <a name="autosizing"></a>自動調整大小

當使用者調整視窗中，例如當裝置旋轉和方向的變更，系統將自動調整大小根據其調整規則該視窗內的檢視。 可以設定這些規則在 C# 中使用`AutoresizingMask`屬性`UIView`或**屬性板**的 ios 設計工具，如下所示：

 [![](layout-options-images/image41.png "Visual Studio for Mac 設計工具")](layout-options-images/image41.png#lightbox)

選取控制項時，這可讓您以手動方式指定的位置和維度的控制項，以及選擇**調整**行為。 以下螢幕擷取畫面所示，我們可以使用 springs 和 struts 調整控制項中選取的檢視關聯性定義其父代：

 [![](layout-options-images/image42.png "Visual Studio for Mac 設計工具")](layout-options-images/image42.png#lightbox)

調整*spring*會造成檢視調整大小的寬度或高度的父檢視為基礎。 調整*strut*會維護固定的距離本身和其父檢視，在該特定邊緣之間的檢視。

也可以在程式碼中設定這些設定：

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


若要測試的自動調整設定，讓不同**支援裝置方向**中專案的選項：

 [![](layout-options-images/image43a.png "自動調整設定")](layout-options-images/image43a.png#lightbox)

在程式碼後置我們可以使用下列程式碼，使水平調整大小的兩個文字控制項：

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


我們也可以調整使用設計工具的控制項。 選取 struts，如展現下方將會導致要保持要裁剪的檢視底部的 靠右對齊的映像：

 [![](layout-options-images/autoresize.png "Autorotation")](layout-options-images/autoresize.png#lightbox)

這些螢幕擷取畫面顯示控制項調整大小或旋轉螢幕時調整本身的方式：

 [![](layout-options-images/image44a.png "Autorotation")](layout-options-images/image44a.png#lightbox)

請注意，文字檢視和文字欄位同時自動縮放以保留相同的保留並因為以下原因，邊界，以滑鼠右鍵`FlexibleWidth`設定。 該映像包含的上方和左方邊界彈性，這表示它會保留的下和右邊界 – 映像時保留檢視中螢幕旋轉。 複雜配置通常會需要這些設定在每一個可見的控制項上保持一致的使用者介面，以及時 （因旋轉或其他調整大小事件），變更檢視的界限重疊時，防止控制項的組合。





## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
