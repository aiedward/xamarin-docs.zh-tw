---
title: 調整適用於 Android Designer 的 JAVA 記憶體參數
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 5a692a931bfcdc1e8eee534de3adfff0de688891
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457896"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>調整適用於 Android Designer 的 JAVA 記憶體參數

啟動 Android 設計工具的進程時，所使用的預設記憶體參數 `java` 可能與某些系統設定不相容。

從 Xamarin Studio 5.7.2.7 (和更新版本開始，Visual Studio for Mac Xamarin 3.9.344 的) 和 Visual Studio Tools，這些設定可依每個專案來自訂。

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>新的 Android 設計工具屬性和對應的 JAVA 選項

下列屬性名稱會對應到指定的 java[命令列選項](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)。

- **AndroidDesignerJAVARendererMinMemory** -Xms

- **AndroidDesignerJAVARendererMaxMemory** -Xmx

- **AndroidDesignerJAVARendererPermSize** -XX： MaxPermSize

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中開啟方案。

2. 在方案總管中逐一選取每個 Android 專案，然後按一下每個專案上的 [ [顯示所有](/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) 檔案兩次]。 您可以略過不包含任何版面配置檔案的專案 `.axml` 。 此步驟可確保每個專案目錄都包含一個檔案 `.csproj.user` 。

3. 結束 Visual Studio。

4. `.csproj.user`針對步驟2中的每個專案找出檔案。

5. `.csproj.user`在文字編輯器中編輯每個檔案。

6. 在元素內加入任何或所有新的 Android designer 記憶體屬性 `<PropertyGroup>` 。 您可以使用現有的 `<PropertyGroup>` 或建立一個新的。 以下是完整的範例檔案 `.csproj.user` ，其中包含所有3個屬性都設定為預設值：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7. 儲存並關閉所有已更新的檔案 `.csproj.user` 。

8. 重新開機 Visual Studio，然後重新開啟您的解決方案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中開啟您的方案，以確定方案目錄包含一個檔案 `.userprefs` 。

2. 結束 Visual Studio for Mac。

3. `.userprefs`在方案目錄中找出檔案。

4. `.userprefs`在文字編輯器中編輯檔案。

5. 以下列格式找出現有的 XML 元素。 此元素名稱的最後一個部分將符合您的專案名稱：在此範例中為 "AndroidApplication1"：

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. 如果專案不存在，請在封入專案 `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` 的任何位置建立該元素 `<Properties>` 。 請務必將 "AndroidApplication1" 取代為您的專案名稱。

7. 將任何或所有新的 Android designer 記憶體屬性加入為元素上的屬性。 以下是完整的範例檔案 `.userprefs` ，其中包含所有3個屬性都設定為預設值：

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8. 針對方案中包含任何版面配置檔案的每個 Android 專案，重複步驟 5-7 `.axml` 。  (也是， `<MonoDevelop.Ide.ItemProperties.ProjectName>` 為每個專案新增一個元素。 ) 

9. 儲存並關閉 `.userprefs` 檔案。

10. 重新開機 Visual Studio for Mac，然後重新開啟您的解決方案。

-----