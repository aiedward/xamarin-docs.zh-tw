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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027048"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>調整適用於 Android Designer 的 JAVA 記憶體參數

在為 Android 設計`java`器啟動 進程時使用的預設記憶體參數可能與某些系統配置不相容。

從 Xamarin Studio 5.7.2.7(以及更高版本為 Mac 的可視化工作室)和 Xamarin 3.9.344 的視覺工作室工具開始,這些設置可以按專案進行自定義。

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>新的 Android 設計器屬性與樣本的 Java 選項

以下屬性名稱對應於指示的 java[指令列選項](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **安卓設計者JAVARendererMinMemory-Xms**

- **安卓設計者JAVARendererMax記憶體**-Xmx

- **安卓設計者放大縮小字體功能 放大縮小字體**功能

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中開啟方案。

2. 在解決方案資源管理器中逐個選擇每個 Android 專案,然後單擊每個專案上顯示[所有檔](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90))兩次。 您可以跳過不包含任何`.axml`佈局檔的專案。 此步驟將確保每個項目目錄都包含一個`.csproj.user`檔。

3. 結束 Visual Studio。

4. 從步驟`.csproj.user`2 中查找每個項目的檔。

5. 編輯文字`.csproj.user`編輯器中的每個檔。

6. 在`<PropertyGroup>`元素中添加任何或所有新的 Android 設計器記憶體屬性。 您可以使用現有`<PropertyGroup>`或建立新的。 下面是一個完整的範例`.csproj.user`檔,其中包含設定為其預設值的所有 3 個屬性:

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

7. 保存並關閉所有更新`.csproj.user`的檔。

8. 重新啟動視覺化工作室並重新打開解決方案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Mac 的 Visual Studio 中打開`.userprefs`解決方案,以確保解決方案 目錄包含檔。

2. 退出 Mac 的視覺工作室。

3. 在`.userprefs`解決方案目錄中找到該檔。

4. 在`.userprefs`文字編輯器中編輯檔。

5. 找到具有以下格式的現有 XML 元素。 此元素名稱的最後一部分將與專案名稱匹配:在此示例中為「AndroidApplication1」:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. 如果`<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >`元素不存在,請將其在`<Properties>`封閉 元素中的任何位置創建。 請務必將「Android 應用程式1」替換為專案名稱。

7. 將任何或所有新的 Android 設計器記憶體屬性添加為元素上的屬性。 下面是一個完整的範例`.userprefs`檔,其中包含設定為其預設值的所有 3 個屬性:

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

8. 對解決方案中包含任何`.axml`佈局檔的每個 Android 專案重複步驟 5-7。 (即為每個專案添加一`<MonoDevelop.Ide.ItemProperties.ProjectName>`個元素。

9. 儲存並關閉 `.userprefs` 檔案。

10. 重新啟動 Mac 的可視化工作室並重新打開解決方案。

-----
