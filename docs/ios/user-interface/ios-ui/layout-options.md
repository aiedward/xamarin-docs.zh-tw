---
title: 在 Xamarin.iOS 中的版面配置選項
description: 本文件說明在 Xamarin.iOS 中的使用者介面版面配置的不同方式。 它討論自動調整大小和自動版面配置。
ms.prod: xamarin
ms.assetid: D8180FEC-F300-42C0-B029-66803E0C1A5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 1bcced2f43328bf5e7d1ebb171b3c92c9ec22493
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827760"
---
# <a name="layout-options-in-xamarinios"></a>在 Xamarin.iOS 中的版面配置選項

有兩種不同的機制，來檢視已調整大小或旋轉時，控制配置：

-  **自動調整**-在設計工具中的自動調整大小偵測器可用來設定`AutoresizingMask`屬性。 這可讓控制項與其容器邊緣錨定及 （或） 修正其大小。 調整適用於 iOS 的所有版本。 這是詳細說明如下所述
-  **自動配置**– 可讓您細微控制的 UI 控制項的關聯性的 iOS 6 中引進的功能。 它可讓設計介面上的控制項的項目相對於其他元素的位置。 更詳細地討論這個主題[使用 Xamarin iOS 設計工具的自動配置](~/ios/user-interface/designer/designer-auto-layout.md)指南。

## <a name="autosizing"></a>自動調整大小

當使用者調整視窗，例如旋轉裝置時和方向的變更，系統將自動調整大小的檢視，該視窗，根據其自動調整規則。 這些規則可以設定C#使用`AutoresizingMask`屬性`UIView`或是在**Properties Pad**的 「 iOS 設計工具，如下所示：

 [![](layout-options-images/image41.png "Visual Studio for Mac 設計工具")](layout-options-images/image41.png#lightbox)

選取控制項時，這可讓您以手動方式指定的位置和維度的控制項，以及選擇**調整**行為。 以下螢幕擷取畫面所示，我們可以 springs 和 struts 控制項中使用自動調整大小來定義其父代所選的檢視的關聯性：

 [![](layout-options-images/image42.png "Visual Studio for Mac 設計工具")](layout-options-images/image42.png#lightbox)

調整*spring*會導致檢視來調整大小的寬度或高度的父檢視為基礎。 調整*strut*會維護固定的距離本身和其父檢視，在該特定邊緣之間的檢視。

也可以在程式碼中設定這些設定：

```csharp
textfield1.Frame = new RectangleF(15, 277, 79, 27);
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleRightMargin | UIViewAutoresizing.FlexibleBottomMargin;
```


若要測試的自動調整設定，讓不同**支援裝置方向**中專案的選項：

 [![](layout-options-images/image43a.png "自動調整設定")](layout-options-images/image43a.png#lightbox)

在程式碼後置中，我們可以使用下列的程式碼，使水平調整大小的兩個文字控制項：

```csharp
textview1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
textfield1.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
imageview1.AutoresizingMask = UIViewAutoresizing.FlexibleTopMargin | UIViewAutoresizing.FlexibleLeftMargin;
```


我們也可以調整使用設計工具的控制項。 保持而不裁剪的檢視底部的 靠右對齊，圖片將會顯示下面選取 struts:

 [![](layout-options-images/autoresize.png "將自動旋轉")](layout-options-images/autoresize.png#lightbox)

這些螢幕擷取畫面顯示如何調整大小的控制項，或重新定位本身時在螢幕旋轉：

 [![](layout-options-images/image44a.png "將自動旋轉")](layout-options-images/image44a.png#lightbox)

請注意，文字檢視和文字欄位同時自動縮放來保持不變的左緣與右邊界，因為`FlexibleWidth`設定。 影像的上方和左邊界有彈性，這表示它會保留下框線和右邊界 – 映像時保留檢視中螢幕旋轉。 複雜的版面配置通常會需要這些設定每個可見的控制項保持一致的使用者介面，以防止控制項重疊時檢視的範圍變更 （因為旋轉或其他調整大小的事件） 的組合。





## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/monotouch/Controls/)
