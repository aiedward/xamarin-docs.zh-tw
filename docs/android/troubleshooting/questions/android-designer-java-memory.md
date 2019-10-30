---
title: 調整適用於 Android Designer 的 JAVA 記憶體參數
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 9c9b9f5a205a2eef7db9f27e8d09b10ce65a4318
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027048"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>調整適用於 Android Designer 的 JAVA 記憶體參數

啟動 Android designer 的 `java` 程式時，所使用的預設記憶體參數可能與某些系統組態不相容。

從 Xamarin Studio 5.7.2.7 （和更新版本、Visual Studio for Mac）和適用于 Xamarin 3.9.344 的 Visual Studio Tools 開始，可以根據每個專案來自訂這些設定。

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>新的 Android 設計工具屬性和對應的 JAVA 選項

下列屬性名稱會對應至指定的 java[命令列選項](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJAVARendererMinMemory** -Xms

- **AndroidDesignerJAVARendererMaxMemory** -Xmx

- **AndroidDesignerJAVARendererPermSize** -XX： MaxPermSize

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中開啟您的方案。

2. 在方案總管中逐一選取每一個 Android 專案，然後按一下每個專案上的 [[顯示所有](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90))檔案] 兩次。 您可以略過不包含任何 `.axml` 設定檔案的專案。 此步驟可確保每個專案目錄都包含一個 `.csproj.user` 檔案。

3. 結束 Visual Studio。

4. 針對步驟2中的每個專案，找出 `.csproj.user` 檔案。

5. 在文字編輯器中編輯每個 `.csproj.user` 檔案。

6. 在 `<PropertyGroup>` 元素內新增任何或所有新的 Android designer 記憶體屬性。 您可以使用現有的 `<PropertyGroup>` 或建立一個新的。 以下是完整的範例 `.csproj.user` 檔案，其中包含設定為預設值的三個屬性：

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

7. 儲存並關閉所有已更新的 `.csproj.user` 檔案。

8. 重新開機 Visual Studio，然後重新開啟您的解決方案。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中開啟您的方案，以確保方案目錄包含 `.userprefs` 檔案。

2. 結束 Visual Studio for Mac。

3. 找出方案目錄中的 `.userprefs` 檔案。

4. 在文字編輯器中編輯 `.userprefs` 檔案。

5. 找出具有下列格式的現有 XML 元素。 此元素名稱的最後部分會符合您的專案名稱：在此範例中為 "AndroidApplication1"：

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. 如果 `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` 專案不存在，請在封入 `<Properties>` 元素內的任何位置建立該元素。 請務必以您的專案名稱取代 "AndroidApplication1"。

7. 新增任何或所有新的 Android designer 記憶體屬性作為元素的屬性。 以下是完整的範例 `.userprefs` 檔案，其中包含設定為預設值的三個屬性：

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

8. 針對方案中包含任何 `.axml` 設定檔案的每個 Android 專案重複步驟5-7。 （也就是為每個專案加入一個 `<MonoDevelop.Ide.ItemProperties.ProjectName>` 元素）。

9. 儲存並關閉 `.userprefs` 檔案。

10. 重新開機 Visual Studio for Mac，然後重新開啟您的解決方案。

-----
