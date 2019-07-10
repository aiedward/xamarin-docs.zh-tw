---
title: 調整適用於 Android Designer 的 JAVA 記憶體參數
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: c42be49aff56453bb1bc3b3c732cad151ccd62e0
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674451"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>調整適用於 Android Designer 的 JAVA 記憶體參數

啟動時會用到的預設記憶體參數`java`處理 Android 設計工具可能與某些系統組態不相容。

開始使用 Xamarin Studio 5.7.2.7 （和更新版本中，Visual Studio for Mac） 和 Visual Studio Tools for Xamarin 3.9.344，這些設定可以針對每個專案進行自訂。

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>新的 Android 設計工具屬性和對應的 Java 選項

下列屬性名稱對應至指定的 java[命令列選項](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  在 Visual Studio 中開啟您的方案。

2.  在 [方案總管] 中選取一個接著一個的每個 Android 專案，然後按一下[顯示所有檔案](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90))兩次上每個專案。 您可以略過不包含任何專案`.axml`版面配置檔。 此步驟可確保每個專案目錄包含`.csproj.user`檔案。

3.  結束 Visual Studio。

4.  找出`.csproj.user`的每個步驟 2 中的專案檔。

5.  編輯每個`.csproj.user`在文字編輯器中的檔案。

6.  新增 any 或 all 中的新 Android 設計工具的記憶體屬性`<PropertyGroup>`項目。 您可以使用現有`<PropertyGroup>`或建立新的帳戶。 以下是完整的範例`.csproj.user`檔案，其中包含所有 3 個屬性設定為其預設值：

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

7.  儲存並關閉所有更新後的`.csproj.user`檔案。

8.  重新啟動 Visual Studio 並重新開啟您的解決方案。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  開啟您在 Visual Studio for Mac，以確保方案目錄中的方案包含`.userprefs`檔案。

2.  結束 Visual Studio for mac。

3.  找出`.userprefs`的方案目錄中的檔案。

4.  編輯`.userprefs`在文字編輯器中的檔案。

5.  找到現有的 XML 項目，其格式如下。 這個項目名稱的最後一個部分會比對您的專案名稱：「 AndroidApplication1 「 在此範例中：

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  如果`<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >`項目不存在時，在封入的任何位置建立它`<Properties>`項目。 請務必以您專案的名稱取代"AndroidApplication1 」。

7.  為元素的屬性，新增任何或所有新的 Android 設計工具的記憶體屬性。 以下是完整的範例`.userprefs`檔案，其中包含所有 3 個屬性設定為其預設值：

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

8.  重複步驟 5-7，每個 Android 專案方案中包含任何`.axml`版面配置檔。 (也就是新增一個`<MonoDevelop.Ide.ItemProperties.ProjectName>`每個專案的項目。)

9.  儲存並關閉`.userprefs`檔案。

10. 重新啟動 Visual Studio for Mac，並重新開啟方案。

-----

