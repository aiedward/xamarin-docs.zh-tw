---
title: ''
description: 較舊 Xamarin.Forms 的解決方案（或在 macOS 上建立的方案）將不會有通用 Windows 平臺專案，因此本文將說明如何將新的 UWP 專案新增至現有的 Xamarin.Forms 方案。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 87ad78e97046eef7fd6c2e062fa9f84d92c11b38
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132206"
---
# <a name="setup-windows-projects"></a>設定 Windows 專案

_將新的 Windows 專案加入至現有的 Xamarin.Forms 方案_

較舊 Xamarin.Forms 的解決方案（或在 macOS 上建立的方案）將不會有通用 Windows 平臺（UWP）應用程式專案。 因此，您必須手動新增 UWP 專案來建立 Windows 10 （UWP）應用程式。

## <a name="add-a-universal-windows-platform-app"></a>新增通用 Windows 平臺應用程式

建議使用**Windows 10**上的**Visual Studio 2019**來建立 UWP 應用程式。 如需通用 Windows 平臺的詳細資訊，請參閱[通用 Windows 平臺簡介](/windows/uwp/get-started/universal-application-platform-guide/)。

UWP 在 Xamarin.Forms 2.1 和更新版本中提供，以及 Xamarin.Forms 。Xamarin.Forms2.2 和更新版本支援對應。

如需有用的秘訣，請參閱<a href="#troubleshooting">疑難排解</a>一節。

遵循這些指示，新增將在 Windows 10 手機、平板電腦和桌上型電腦上執行的 UWP 應用程式：

 sha-1. 以滑鼠右鍵按一下方案，然後選取 [**新增] > 新專案 ...** ]，然後新增 [**空白應用程式（通用 Windows）** ] 專案：

  ![](universal-images/add-wu.png "Add New Project Dialog")

 2. 在 [**新增通用 Windows 平臺專案**] 對話方塊中，選取應用程式將在其上執行的 Windows 10 最低和目標版本：

  ![](universal-images/target-version.png "New Universal Windows Platform Project Dialog")

 第. 以滑鼠右鍵按一下 UWP 專案，然後選取 [**管理 NuGet 套件 ...** ] 並新增 **Xamarin.Forms** 套件。 確定方案中的其他專案也會更新為相同版本的 Xamarin.Forms 套件。

 4gb. 請確定新的 UWP 專案會建立在**組建 > Configuration Manager** ] 視窗中（這可能不會在預設情況下發生）。 針對通用專案，勾選 [**組建**] 和 [**部署**] 方塊：

  [![](universal-images/configuration-sml.png "Configuration Manager Window")](universal-images/configuration.png#lightbox "Configuration Manager Window")

 第. 以滑鼠右鍵按一下專案，然後選取 [**加入 > 參考**]，並建立 Xamarin.Forms 應用程式專案（.NET Standard 或共用專案）的參考。

  ![](universal-images/addref-sml.png "Reference Manager Dialog")

 7. 在 UWP 專案中，編輯**App.xaml.cs**以在 `Init` 52 行的方法內包含方法呼叫 `OnLaunched` ：

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 utf-7. 在 UWP 專案中，藉由移除包含在元素內的來編輯**MainPage。** `Grid` `Page`

 8. 在**MainPage**中，新增 `xmlns` 的新專案 `Xamarin.Forms.Platform.UWP` ：

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9. 在**MainPage**中，將根項目變更 `<Page` 為 `<forms:WindowsPage` ：

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 十大. 在 UWP 專案中，編輯**MainPage.xaml.cs**以移除 `: Page` 類別名稱的繼承規範（因為 `WindowsPage` 在上一個步驟中所做的變更，現在會繼承自）：

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 英寸. 在**MainPage.xaml.cs**中，于函式中新增 `LoadApplication` 呼叫 `MainPage` 以啟動 Xamarin.Forms 應用程式：

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

> [!NOTE]
> 方法的引數 `LoadApplication` 是 `Xamarin.Forms.Application` .net standard 專案中定義的實例。

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

12. 新增任何本機資源（例如 影像檔案），從所需的現有平臺專案。

## <a name="troubleshooting"></a>疑難排解

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>使用「使用 .NET Native 工具鏈編譯」時的「目標調用例外狀況」

如果您的 UWP 應用程式參考多個元件（例如協力廠商控制程式庫），或您的應用程式本身分割為多個程式庫，則 Xamarin.Forms 可能無法從這些元件載入物件（例如自訂轉譯器）。

當您在專案的 [**屬性] > [組建 > 一般**] 視窗中使用 [**以 .NET Native 編譯] 工具鏈**時，可能會發生這種情況。

您可以在 App.xaml.cs 中使用 UWP 特定的呼叫多載 `Forms.Init` （如下列**App.xaml.cs**程式碼所示，將取代為您的程式 `ClassInOtherAssembly` 代碼所參考的實體類別）來修正此問題：

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

透過直接參考或 NuGet，為您在方案總管中新增做為參考的每個元件新增一個專案。

#### <a name="dependency-services-and-net-native-compilation"></a>相依性服務和 .NET Native 編譯

使用 .NET Native 編譯的發行組建可能無法解析在主要應用程式可執行檔（例如個別的專案或程式庫）外部定義的相依性服務。

使用 `DependencyService.Register<T>()` 方法來手動註冊相依性服務類別。 根據上述範例，新增 register 方法，如下所示：

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
