---
title: "Android 設計工具中調整 Java 記憶體參數"
ms.topic: article
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 354a750fc57fd28754aea902d293e75b65d63997
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Android 設計工具中調整 Java 記憶體參數

啟動時所使用的預設記憶體參數`java`處理 Android 設計工具可能與某些系統組態不相容。

開始使用 Xamarin Studio 5.7.2.7 （和更新版本、 Visual Studio for Mac），Xamarin for Visual Studio 3.9.344 可以自訂這些設定，根據每個專案。

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>新的 Android 設計工具屬性和對應的 Java 選項

下列的屬性名稱對應至指定的 java[命令列選項](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  在 Visual Studio 中開啟您的方案。

2.  在 [方案總管] 中選取一個接著一個的每個 Android 專案，然後按一下[顯示所有檔案](https://msdn.microsoft.com/en-us/library/4afxey9h.aspx)每個專案上的兩倍。 您可以略過不包含任何專案`.axml`版面配置檔案。 這個步驟可確保每個專案目錄包含`.csproj.user`檔案。

3.  結束 Visual Studio。

4.  找出`.csproj.user`的每個步驟 2 中的專案檔。

5.  編輯每`.csproj.user`文字編輯器中的檔案。

6.  新增任一或所有最新 Android 設計工具的記憶體內容內`<PropertyGroup>`項目。 您可以使用現有`<PropertyGroup>`或另外新建一個。 以下是完整的範例`.csproj.user`檔案，其中包含所有 3 屬性設為其預設值：

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

8.  重新啟動 Visual Studio，並重新開啟您的方案。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  開啟您在 Visual Studio for Mac，以確保使用的方案目錄中的方案包含`.userprefs`檔案。

2.  結束 Visual Studio for mac。

3.  找出`.userprefs`的方案目錄中的檔案。

4.  編輯`.userprefs`文字編輯器中的檔案。

5.  找出以下列格式的現有 XML 項目。 這個項目名稱的最後一個部分會比對您的專案名稱:"AndroidApplication1 「 在此範例中：

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  如果`<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >`項目不存在，在封入的任何位置建立它`<Properties>`項目。 請務必以您的專案名稱取代"AndroidApplication1"。

7.  加入做為屬性項目上的任一或所有最新 Android 設計工具的記憶體內容。 以下是完整的範例`.userprefs`檔案，其中包含所有 3 屬性設為其預設值：

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

8.  重複步驟 5-7 中每個 Android 專案方案，其中包含任何`.axml`版面配置檔案。 (也就是新增一個`<MonoDevelop.Ide.ItemProperties.ProjectName>`每個專案的項目。)

9.  儲存並關閉`.userprefs`檔案。

10. 重新啟動 Visual Studio for Mac，並重新開啟您的方案。

-----

