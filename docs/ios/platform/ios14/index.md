---
title: IOS 14 簡介
description: '本檔提供一些適用于 Xamarin 提供 c # 系結之 iOS 14 Api 的高階描述。'
ms.prod: xamarin
ms.assetid: 4953216e-472b-4484-9c1e-7263ac537f21
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2020
ms.openlocfilehash: e9793617c76813fb68a57213edd8b48529f19ac7
ms.sourcegitcommit: 0c45e3f810947e3d43223aa01bf3e43a0defca65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/21/2020
ms.locfileid: "90843564"
---
# <a name="introduction-to-ios-14"></a>IOS 14 簡介

請遵循下列 [指示](~/ios/platform/ios14/get-started.md) 來開始著手。

## <a name="new-control-uicolorwell"></a>新控制項： UIColorWell

[`UIColorWell`](https://developer.apple.com/documentation/uikit/uicolorwell) 是新的 UIKit 控制項，用來從選取的樣本選取色彩、使用吸管，或手動輸入值。 控制項會顯示迴圈色彩按鈕，以在點擊時啟動強制回應表單。

![UIColorWell](ios14-images/colorwell.png)

```xaml
<ios:UIColorWell
    SelectedColor="{x:Static ios:UIColor.Red}"
    ValueChanged="OnColorChanged" />
```

```csharp
private void OnColorChanged(object sender, EventArgs e)
{
    var colorWell = (UIColorWell)sender; 
    Debug.WriteLine(colorWell.SelectedColor);
}
```

## <a name="modified-controls"></a>修改的控制項

有幾個控制項已收到更新，最值得注意的是：

- [UIBarButtonItem](https://developer.apple.com/documentation/uikit/uibarbuttonitem) 現在可以新增將顯示為 Popover 的 UIMenu。
- [UIDatePicker](https://developer.apple.com/documentation/uikit/uidatepicker) 現在支援多種樣式：自動 (預設) 、Compact、內嵌和滾輪。
- [UISplitViewController](https://developer.apple.com/documentation/uikit/uisplitviewcontroller) 現在支援三個數據行：主要、次要和補充。
 
![發行前 API](~/media/shared/preview.png)

## <a name="embedded-widgetkit-support"></a>Embedded WidgetKit 支援

這一版的 SDK 新增了將以 Swift 撰寫的 WidgetKit 延伸模組內嵌至您的主要 Xamarin iOS 應用程式的支援。 這可讓您立即建立具有 Widget 支援的應用程式。

使用這個方法，您可以建立「混合式」應用程式，使用 SwiftUI 建立 Widget 擴充功能，並將它內嵌在 Xamarin 應用程式中。

利用 WidgetKit 支援需要對專案檔進行一些手動變更。

將如下的區段新增至您的專案：

```xml
<AdditionalAppExtensions Include="$(MSBuildProjectDirectory)/../../native">
     <Name>NativeTodayExtension</Name>
     <BuildOutput Condition="'$(Platform)' == 'iPhone'">build/Debug-iphoneos</BuildOutput>
     <BuildOutput Condition="'$(Platform)' == 'iPhoneSimulator'">build/Debug-iphonesimulator</BuildOutput>
</AdditionalAppExtensions>
```

變更第一個連結所包含的路徑，以指向 Swift UI 延伸模組的組建目錄。

在您的 Xcode 專案中啟用專案相對輸出位置可能會很有説明， (檔→專案設定) 以尋找更簡單的路徑：

![Xcode 設定](ios14-images/xcode-settings.png)

這個 [範例應用程式](https://github.com/chamons/xamarin-ios-swift-extension/blob/master/App/TestApplication/TestApplication.csproj#L143) 會使用 JSON 序列化，將資料從 Xamarin 應用程式傳輸至範例 Widget 以供顯示。

有興趣 WidgetKit 的人會在這裡提供 [意見](https://github.com/xamarin/xamarin-macios/issues/8933)反應。

## <a name="related-links"></a>相關連結

- [Xamarin. iOS 14 版本資訊](/xamarin/ios/release-notes/14/14.0)
- [UIColorWell 檔](https://developer.apple.com/documentation/uikit/uicolorwell)
