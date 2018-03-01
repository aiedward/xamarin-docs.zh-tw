---
title: "新增 Windows 應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: ADF99B78-F1BC-48DF-9128-01B93C4411C1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 369e8005c1d398c90b14a95ac36fc8659e462fbf
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="adding-a-windows-app"></a>新增 Windows 應用程式


1. **以滑鼠右鍵按一下方案 > 新增 > 新的專案...**並加入**空白應用程式 (Windows)**

 ![](tablet-images/add-wu.png "加入新的專案 對話方塊")

2. **以滑鼠右鍵按一下專案 > 管理 NuGet 封裝...**並加入**Xamarin.Forms**封裝。

3. **以滑鼠右鍵按一下專案 > 新增 > 參考**並建立共用 Xamarin.Forms 應用程式專案的專案參考。

  ![](tablet-images/addref.png "參考管理員 對話方塊")

4. 編輯**App.xaml.cs**包含`Init()`方法呼叫內`OnLaunched`大約 65 第方法

```csharp
// add this line
Xamarin.Forms.Forms.Init (e); // requires LaunchActivatedEventArgs
// above this existing line
if (e.PreviousExecutionState == ApplicationExecutionState.Terminated) {}
```

 5 . 編輯**MainPage.xaml** -變更根項目`<Page`至`<forms:WindowsPage`*和*定義`xmlns:forms`它所使用：

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
...
</forms:WindowsPage>
```


 6 . 編輯**MainPage.xaml.cs**移除`: Page`繼承的類別名稱的規範。

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 7 . 仍在**MainPage.xaml.cs**，新增`LoadApplication`呼叫中`MainPage`建構函式 （大約第 28） 啟動 Xamarin.Forms 應用程式：

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

8 . 按兩下**Package.appxmanifest**設定通常會需要這些功能：

  功能設定：

  * 網際網路 (用戶端)
  * 位置

9 . 最後，會加入任何本機資源 （例如。 影像檔） 從現有的平台專案所需。

