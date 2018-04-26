---
title: 新增 Windows Phone 應用程式
ms.prod: xamarin
ms.assetid: B598FA9D-6818-4CC9-8191-838C156DB9DA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 55bd4bdcfde4c91ad5c9b94bef486207466e135d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="adding-a-windows-phone-app"></a>新增 Windows Phone 應用程式


首先，如果您使用 Xamarin.Forms PCL 範本[更新設定檔](~/xamarin-forms/platform/windows/installation/index.md)，然後依照下列指示：

1. **以滑鼠右鍵按一下方案 > 新增 > 新的專案...**並加入**空白應用程式 (Windows Phone)**

  ![](phone-images/add-wp81.png "加入新的專案 對話方塊")

2. **新建立的專案上按一下滑鼠右鍵 > 管理 NuGet 封裝...**並加入**Xamarin.Forms**封裝。

3. **以滑鼠右鍵按一下專案 > 新增 > 參考**並建立共用 Xamarin.Forms 應用程式專案的專案參考。

  ![](phone-images/addref.png "參考管理員 對話方塊")

4. 編輯**App.xaml.cs**包含`Init()`方法呼叫，在`OnLaunched`列 67 解決方法：

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5 . 編輯**MainPage.xaml** -變更根項目`<Page`至`<forms:WindowsPhonePage`*和*定義`xmlns:forms`它所使用：

```xaml
<forms:WindowsPhonePage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPhonePage>
```

 6 . 編輯**MainPage.xaml.cs**移除`: PhonePage`繼承的類別名稱的規範。

```csharp
public sealed partial class MainPage  // REMOVE ": PhonePage"
```

 7 . 仍在**MainPage.xaml.cs**，新增`LoadApplication`呼叫中`MainPage`建構函式 （大約第 28） 啟動 Xamarin.Forms 應用程式：

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . 按兩下**Package.appxmanifest**設定通常會需要這些功能：

  * 網際網路 (用戶端與伺服器)

9 . 最後，會加入任何本機資源 （例如。 影像檔） 從現有的平台專案所需。

