---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: 將 C/C++函式庫與 Xamarin 一起使用
description: 適用於 Mac 的 Visual Studio 可用於使用 Xamarin 和 C# 建構跨平臺 C/C++ 程式碼並將其整合到適用於 Android 和 iOS 的行動應用程式中。 本文介紹如何在 Xamarin 應用中設置和調試C++專案。
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 42a59570d727657b2f3c23bd9d1f37e1205717d0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73842811"
---
# <a name="use-cc-libraries-with-xamarin"></a>將 C/C++函式庫與 Xamarin 一起使用

## <a name="overview"></a>概觀

Xamarin 使開發人員能夠使用 Visual Studio 創建跨平臺本機行動應用。 通常,C# 繫結定用於向開發人員公開現有平臺元件。 但是,有時 Xamarin 應用需要使用現有的代碼庫。 有時,團隊根本沒有時間、預算或資源將大型、經過良好測試且高度優化的代碼庫移植到 C# 中。

[跨平台行動開發的視覺C++](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development)使 C/C++ 和 C# 代碼能夠作為同一解決方案的一部分建構,提供了許多優勢,包括統一的調試體驗。 微軟已經以這種方式使用C/C++和Xamarin來提供應用程式,如[超延移動](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw)和[皮克斯相機](https://www.microsoft.com/microsoftpix)。

但是,在某些情況下,希望(或要求)保持現有的 C/C++ 工具和進程,並保持庫代碼與應用程式分離,將庫視為與第三方元件類似。 在這些情況下,挑戰不僅在將相關成員暴露給 C# 中,還將庫作為依賴項進行管理。 當然,盡可能自動化此過程。  

這篇文章概述了此方案的高級方法,並介紹了一個簡單的示例。

## <a name="background"></a>背景

C/C++被視為跨平臺語言,但必須非常小心,以確保原始碼確實是跨平臺的,僅使用所有目標編譯器支援的 C/C++,並且包含很少或根本沒有包含有條件的平臺或編譯器特定代碼。

最終,代碼必須在所有目標平臺上成功編譯和運行,因此這歸結為目標平臺(和編譯器)之間的共性。 編譯器之間的細微差異仍可能產生問題,因此每個目標平臺上的徹底測試(最好是自動化)變得越來越重要。  

## <a name="high-level-approach"></a>進階方法

下圖顯示了用於將 C/C++ 原始程式碼轉換為跨平臺 Xamarin 庫的四階段方法,該庫透過 NuGet 共用,然後在 Xamarin.Forms 應用程式中使用。

![Xamarin 的 C/C++的進階方法](images/cpp-steps.jpg)

4個階段是:

1. 將 C/C++原始程式碼編譯為特定於平臺的本機庫。
2. 使用可視化工作室解決方案包裝本機庫。
3. 包裝和推 .NET 包裝的 NuGet 包裝。
4. 使用來自 Xamarin 應用的 NuGet 包。

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>第一階段:將 C/C++原始碼編譯為特定於平臺的本機庫

此階段的目標是創建可以由 C# 包裝器調用的本機庫。 這可能與您的情況無關,也可能不相關。 在此常見場景中可以攜帶的許多工具和流程超出了本文的範圍。 關鍵注意事項是使 C/C++ 代碼庫與任何本機包裝代碼保持同步、足夠的單元測試和生成自動化。 

演練中的庫是使用帶有附帶的 shell 腳本的 Visual Studio Code 創建的。 此演練的擴展版本可以在[移動 CAT GitHub 儲存庫](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin)中找到,該存儲庫將更深入地討論示例的這一部分。 在這種情況下,本機庫被視為第三方依賴項,但此階段為上下文進行了說明。

為簡單起見,演練僅針對體系結構的子集。 對於 iOS,它使用 lipo 實用程式從單個特定於體系結構的二進位檔創建單個胖二進位檔案。 Android 將使用具有 .so 擴展的動態二進位檔案,iOS 將使用具有 .a 擴展的靜態胖二進位檔案。 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>階段 2:使用可視化工作室解決方案包裝本機庫

下一階段是包裝本機庫,以便它們易於從 .NET 使用。 這是通過包含四個專案的可視化工作室解決方案完成的。 共用專案包含公共代碼。 針對 Xamarin.Android、Xamarin.iOS 和 .NET 標準的每個項目允許以與平台無關的方式引用庫。

包裝器使用「[誘餌和開關技巧](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/),」保羅貝茨描述。 這不是唯一的方法,但它便於引用庫,並且無需在使用的應用程式本身中顯式管理特定於平臺的實現。 訣竅實質上是確保目標(.NET 標準、Android、iOS)共用相同的命名空間、程式集名稱和類結構。 由於 NuGet 始終喜歡特定於平臺的庫,因此 .NET 標準版本在運行時從不使用。

此步驟中的大多數工作將側重於使用 P/Invoke 調用本機庫方法並管理對基礎物件的引用。 目標是向消費者公開庫的功能,同時抽象出任何複雜性。 Xamarin.Forms 開發人員不需要瞭解非託管庫的內部工作。 應該覺得他們正在使用任何其他託管 C# 庫。

最終,此階段的輸出是一組 .NET 庫(每個目標一個)以及一個 nuspec 文檔,其中包含在下一步中構建套件所需的資訊。

**階段 3:為 .NET 包裝包裝包裝和推 NuGet 包裝**

第三階段是使用上一步的生成工件創建 NuGet 包。 此步驟的結果是可以從 Xamarin 應用使用的 NuGet 包。 演練使用本地目錄作為 NuGet 源。 在生產中,此步驟應將包發佈到公共或私有 NuGet 源,並且應完全自動化。

**階段 4:使用 Xamarin.Forms 應用的 NuGet 套件**

最後一步是從 Xamarin.Forms 應用引用和使用 NuGet 包。 這需要在 Visual Studio 中配置 NuGet 源才能使用上一步中定義的來源。

配置源後,需要在跨平臺 Xamarin.Forms 應用中的每個專案中引用包。 "誘餌和開關技巧"提供了相同的介面,因此可以使用在單個位置定義的代碼調用本機庫功能。

源代碼儲存庫包含[進一步閱讀的清單](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up),其中包括有關如何在 Azure DevOps 上設置私有 NuGet 源以及如何將包推送到該源的文章。 雖然比本地目錄需要多一點的設置時間,但在團隊開發環境中,這種類型的源更好。

## <a name="walk-through"></a>逐步解說

提供的步驟是特定於**Mac 的 Visual Studio,** 但該結構也適用於 Visual Studio **2017。**

### <a name="prerequisites"></a>Prerequisites

為了跟進,開發人員將需要:

- [NuGet 命令列 (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*適用於 Mac* *的視覺工作室*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> 要將應用部署到 iPhone,需要一個活躍的[**Apple 開發人員帳戶**](https://developer.apple.com/)。

## <a name="creating-the-native-libraries-stage-1"></a>建立本機庫(階段 1)

本機庫功能基於演練中的範例[:創建和使用靜態庫(C++)。](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017)

本演練跳過第一階段,即構建本機庫,因為在此方案中,庫作為第三方依賴項提供。 預編譯的本機庫包含在[範例代碼](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin)旁邊,也可以直接[下載](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts)。

### <a name="working-with-the-native-library"></a>使用本機庫

原始*MathFuncsLib*範例包括一個`MyMathFuncs`呼叫 具有以下定義的類別:

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

另一個類定義包裝函數,允許 .NET 使用者創建、處置和與基礎`MyMathFuncs`本機 類進行交互。

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

這將是這些包裝函數,用於[Xamarin](https://visualstudio.microsoft.com/xamarin/)側。

## <a name="wrapping-the-native-library-stage-2"></a>包裝本機庫(階段 2)

此階段需要[上一節中](#creating-the-native-libraries-stage-1)介紹的預[編譯庫](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts)。

### <a name="creating-the-visual-studio-solution"></a>建立視覺化工作室解決方案

1. 在**Mac 的視覺化工作室中**,按下 **「新專案**」(來自*歡迎頁面*)或 **「新解決方案**」(從 *「檔*」功能表)。
2. 從 **"新專案"** 視窗中,選擇 **「共用專案**」(從*多平臺>庫中*),然後單擊「**下一步**」。
3. 更新以下欄位 **,然後按下:**

    - **專案名稱:** 數學豐克斯.共用  
    - **解決方案名稱:** 數學豐奇  
    - **位置:** 使用預設儲存位置(或選擇替代位置)   
    - **在解決方案目錄中建立專案:** 將此設定為已選取
4. 從**解決方案資源管理員**,按兩下**MathFuncs.共用**專案並瀏覽到**主設定**。
5. 刪除 **。** 從**預設命名空間**共用,以便僅將其設置為**MathFuncs,** 然後單擊 **「確定**」。
6. 開啟**MyClass.cs(** 由樣本建立),然後將類別和檔案名重新命名為**MyMathFuncsWrapper,** 並將命名空間變更為**MathFuncs**。
7. **控制 = 點擊**解決方案**MathFuncs,** 然後從 **"添加"** 選單中選擇 **"添加新專案..."**
8. 在 **"新專案"** 視窗中,選擇 **.NET 標準庫**(從*多平臺>庫*內),然後單擊「**下一步**」。
9. 選擇 **.NET 標準 2.0,** 然後按**一下 「下一步**」 。。
10. 更新以下欄位 **,然後按下:**

    - **專案名稱:** 數學豐克斯.標準  
    - **位置:** 使用與分享項目相同的儲存位置   

11. 從**解決方案資源管理員**,按兩下**MathFuncs.標準**專案。
12. 導覽主**設定**,然後會**預設命名空間**更新為**MathFuncs**。
13. 導覽**輸出**設定,然後將**程式集名稱**更新為**MathFuncs**。
14. 導覽到**編譯器**設定,將 **「設定」** 更改為 **「釋放」**, 將**除錯資訊**設定為 **「僅符號」** 然後按下 **「 確定**」。
15. 從項目中刪除**Class1.cs/Getting 已啟動**(如果其中一個已包含在範本中)。
16. **控制 = 單擊**項目**相依項/引用**資料夾,然後選擇 **「編輯參考**」 。
17. 從「**項目**」選項卡中選擇**MathFuncs.共用**,然後單擊 **「確定**」。
18. 使用以下設定重複步驟 7-17(忽略步驟 9):

    | **專案名稱**  | **樣本名稱**   | **新項目選單**   |
    |-------------------| --------------------| -----------------------|
    | 數學豐奇.安卓 | 類別庫       | Android > 庫      |
    | 數學豐克斯.iOS     | 綁定庫     | iOS >库          |

19. 從**解決方案資源管理員**,按兩下**MathFuncs.Android**專案,然後導航到**編譯器**設置。

20. 設定**設定**為**除錯**,編輯**定義符號**以包含**Android;**。

21. 將**設定**變更為 **「發布**」,然後編輯**定義符號**以包括**Android;**。

22. **對 MathFuncs.iOS**重複步驟 19-20,編輯**定義符號**以包括**iOS;** 而不是**Android;** 在這兩種情況下。

23. 在**發佈**設定 **(CONTROL + COMMAND + B)** 中建構解決方案,並驗證所有三個輸出程式集(安卓、iOS、.NET 標準)(在相應的專案箱資料夾中)共用相同的名稱**MathFuncs.dll**。

在此階段,解決方案應有三個目標,一個用於 Android、iOS 和 .NET 標準,以及三個目標中每個目標都引用的共享專案。 這些應配置為使用相同的預設命名空間和具有相同名稱的輸出程式集。 這是前面提到的「誘餌和開關」 方法所必需的。

### <a name="adding-the-native-libraries"></a>新增本機庫

將本機庫添加到包裝解決方案的過程在 Android 和 iOS 之間略有不同。

#### <a name="native-references-for-mathfuncsandroid"></a>MathFuncs.安卓的本機引用

1. **控制 = 點擊****MathFuncs.Android**專案,然後從 **「新增**」選單中選擇 **「新資料夾**」,將其命名為 **「libs」。。**

2. 對於每個**ABI(** 應用程式二進位介面 **),CONTROL = 單擊** **libs**資料夾,然後從 **「新增**」選單中選擇 **「新資料夾**」,以相應的**ABI**命名。 在此案例中：

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > 有關更詳細的概述,請參閱[NDK 開發人員指南](https://developer.android.com/ndk/guides/)中的[體系結構和 CPU](https://developer.android.com/ndk/guides/arch)主題,特別是有關在[應用包中定址本機代碼](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages)的部分。

3. 驗證資料夾結構:  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. 依以下映射將相應的 **.so**函式庫加入到每個**ABI**資料夾:

    **臂64-v8a:libs/** 安卓/臂64

    **武裝-v7a:** libs/安卓/手臂  

    **x86:** libs/安卓/x86

    **x86_64:** libs/安卓/x86_64

    > [!NOTE]
    > 要添加檔 **,CONTROL = 單擊**表示相應**ABI**的資料夾,然後從 **"添加**"功能表中選擇 **"添加檔..."** 選擇適當的函式庫(從**預寫編譯Libs**目錄),然後按下 **「開啟**」,然後按下「**確定**」 來保留預設選項將*檔案複製到目錄*。

5. 對於每個 **.so**檔案 **,CONTROL + CLICK,** 然後從 **「生成操作」** 選單中選擇 **「嵌入式子庫**」選項。

現在**libs**資料夾應如下所示:

```folders
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86
        - libMathFuncs.so
    - x86_64
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>MathFuncs.iOS 的本機引用

1. **控制 = 點擊** **MathFuncs.iOS**專案,然後從 **「新增」** 選單中選擇 **「添加本機引用**」。。 
2. 選擇**libMathFuncs.a**庫(從**預編譯 Libs**目錄下的 libs/ios)然後單擊 **"打開"** 
3. **控制 = 點擊** **libMathFuncs 檔案**(在**本機參考**資料夾中,然後從選單中選擇 **'屬性**'選項  
4. 設定**這個機參考**屬性,以便在**屬性**鍵盤中選取這些屬性(顯示刻度圖示):

    - 力負載
    - 是C++
    - 智慧連結

    > [!NOTE]
    > 使用綁定庫項目類型以及[本機引用](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references)嵌入靜態庫,並使其能夠自動連結到引用它的 Xamarin.iOS 應用(即使它透過 NuGet 包包含)。

5. 開啟**ApiDefinition.cs,** 刪除樣本化註解程式碼(`MathFuncs`僅保留命名空間),然後為**Structs.cs**執行相同的步驟 

    > [!NOTE]
    > 綁定庫專案需要這些檔案(使用*ObjCBindingApi 定義*和*ObjC BindingCoreSource*生成操作)才能生成。 但是,我們將編寫代碼,以調用我們的本機庫,在這些檔之外,可以使用標準 P/Invoke 在 Android 和 iOS 庫目標之間共用。

### <a name="writing-the-managed-library-code"></a>編寫託管庫碼

現在,編寫 C# 代碼來調用本機庫。 目標是隱藏任何潛在的複雜性。 消費者不需要對本機庫內部或 P/Invoke 概念的任何工作知識。  

#### <a name="creating-a-safehandle"></a>建立安全句柄

1. **控制 = 按一下** **MathFuncs.共用**項目,然後從 **"添加**"菜單中選擇 **"添加檔..."** 
2. 從 **"新檔"** 視窗中選擇**空類**,將其命名為**MyMathFuncsSafeHandle,** 然後單擊"**新建"**
3. 實**作 MyMathFuncsSafeHandle**類別:

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2)是處理託管代碼中非託管資源的首選方法。 這抽象了許多與關鍵定稿和物件生命周期相關的樣板代碼。 此句柄的所有者隨後可以像對待任何其他託管資源一樣對待它,並且不必實現完整的[一次性模式](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)。 

#### <a name="creating-the-internal-wrapper-class"></a>建立內部包裝類別

1. 開啟**MyMathFuncsWrapper.cs,** 將變更為內部靜態類別

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. 在同一檔中,向類新增以下條件語句:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > 這將基於是否為**Android**或**iOS**建構庫來設定**DllName**常量值。 這是為了解決每個平臺使用不同的命名約定,但也解決了本例中使用的庫類型。 Android 正在使用動態庫,因此需要一個檔名,包括擴展名。 對於 iOS,我們需要 *'__Internal',* 因為我們使用的是靜態庫。

3. 在**MyMathFuncsWrapper.cs**檔案頂部新增對**System.Runtime.互通服務的**參考

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. 新增包裝方法以處理**MyMathFuncs**類別的建立和處置:

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > 我們將常量**DllName**傳遞給**DllImport**屬性以及**入口點**,該屬性顯式告訴 .NET 運行時要調用該庫中的函數的名稱。 從技術上講,如果我們的託管方法名稱與非託管方法名稱相同,則我們不需要提供**EntryPoint**值。 如果未提供,則託管方法名稱將改為入口**點**。 但是,最好是顯式的。  

5. 新增包裝方法,使我們能夠使用以下代碼使用**MyMathFuncs**類:

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > 在本示例中,我們使用簡單類型的參數。 由於在這種情況下,編組是一個有點複製,它不需要我們額外的工作。 另請注意**使用 MyMathFuncsSafeHandle**類別,而不是標準的**IntPtr**。 作為編組過程的一部分 **,IntPtr**會自動映射到**SafeHandle。**

6. 驗證已完成的**MyMathFuncsWrapper**類別是否如下所示:

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>完成 MyMathFuncsSafeHandle 課程

1. 打開**MyMathFuncsSafeHandle**類,導航到**釋放處理**方法中的佔位符**TODO**註釋:

    ```csharp
    // TODO: Release the handle here
    ```

1. 更換**TODO**行:

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>編寫 MyMathFuncs 類別

現在包裝器已完成,請創建 MyMathFuncs 類,該類將管理對非託管C++ MyMathFuncs 物件的引用。  

1. **控制 = 按一下** **MathFuncs.共用**項目,然後從 **"添加**"菜單中選擇 **"添加檔..."** 
2. 從 **"新建檔案"** 視窗中選擇**空類**,將其命名為**MyMathFuncs,** 然後單擊"**新建"**
3. 將以下成員加入**MyMathFuncs**類別:

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. 實現類的建構函數,以便在實體化類別時建立句柄並將句柄儲存到本機**MyMathFuncs**物件:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. 使用以下代碼實現**IAI:1:**

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. 使用**MyMathFuncsWrapper**類實現**MyMathFuncs**方法,通過將我們存儲的指標傳遞給基礎非託管物件來執行隱藏罩下的實際工作。 代碼應如下所示:

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>建立 nuspec

為了透過 NuGet 打包和分發庫,解決方案需要一個**nuspec**檔案。 這將確定每個受支援平臺將包含哪些生成的程式集。

1. **控制 = 單擊**解決方案**MathFuncs**,然後從「**新增」功能表中選擇「添加解決方案資料夾**」,將其命名**Add**為 **「解決方案專案**」。
2. **控制 =** 點擊 **"解決方案專案'** 資料夾,然後從 **"添加**"選單中選擇 **"新建檔案..."**
3. 從 **「新檔案」** 視窗中選擇**空 XML 檔**,將其命名為**MathFuncs.nuspec,** 然後單擊「 新建」**。**
4. 使用要向**NuGet**使用者顯示的基本包中資料更新**MathFuncs.nuspec。** 例如：

    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    > 有關用於此清單的架構的進一步詳細資訊,請參閱[nuspec 參考](https://docs.microsoft.com/nuget/reference/nuspec)文檔。

5. 將`<files>`元素加入元素的`<package>`子元素(以下的`<metadata>``<file>`項目識別每個檔案:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > 當包安裝到專案中,並且有多個程式集指定同名時,NuGet 將有效地選擇特定於給定平臺的程式集。

6. 新增`<file>` **Android**程式集的元素:

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. 新增`<file>` **iOS**程式集的元素:

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. 新增`<file>` **net 標準 2.0**程式集的元素:

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. 驗證**nuspec**清單:

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>

        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > 此檔指定**發佈**版本中的程式集輸出路徑,因此請確保使用該配置生成解決方案。

此時,該解決方案包含 3 個 .NET 程式集和支援**的 nuspec**清單。

## <a name="distributing-the-net-wrapper-with-nuget"></a>使用 NuGet 分轉 .NET 包裝

下一步是打包和分發 NuGet 包,以便應用可以輕鬆使用它,並將其作為依賴項進行管理。 包裝和消費都可以在單個解決方案內完成,但透過 NuGet 輔助分離分發庫,使我們能夠獨立管理這些程式碼庫。

### <a name="preparing-a-local-packages-directory"></a>準備本地端目錄

NuGet 來源最簡單的形式是本地目錄:

1. 在**查找器中**,導航到方便的目錄。 例如/**使用者**。
2. 從 **'檔案**' 選單中選擇 **' 新增資料夾**',提供有意義的名稱,如**本地 nuget-feed**。

### <a name="creating-the-package"></a>建立套件

1. 將**生成配置**設定為**發佈**,並使用 COMMAND **+ B**執行生成。
2. 開啟**終端**機並將目錄更改為包含**nuspec**檔案的資料夾。
3. 在**終端**機中,[使用上一步](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed)建立的資料夾(即**本地 nuget-feed)** 執行**nuget pack**命令,指定**nuspec**檔案、**版本**(例如 1.0.0)與**輸出目錄**。 例如：

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **確認**在**本地 nuget 源**目錄中創建了**MathFuncs.1.0.0.nupkg。**

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[選擇性的]將專用 NuGet 源與 Azure DevOps 一起使用

[在 Azure DevOps 中的 NuGet 包入門中](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package)介紹了一種更強大的技術,它演示如何創建私有源並將包(在上一步中生成)推送到該源。

最好讓此工作流完全自動化,例如使用[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts)。 有關詳細資訊,請參閱使用[Azure 管道開始](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts)。

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>使用 Xamarin.Forms 應用的 .NET 包裝器

要完成演練,請建立**Xamarin.Forms**應用以使用剛剛發佈到本地**NuGet**源的包。

### <a name="creating-the-xamarinforms-project"></a>建立**Xamarin.表單**

1. 為 Mac 打開**視覺工作室**的新實例。 這可以從**終端**完成:

    ```bash
    open -n -a "Visual Studio"
    ```

2. 在**Mac 的視覺化工作室中**,按下 **「新專案**」(來自*歡迎頁面*)或 **「新解決方案**」(從 *「檔*」功能表)。
3. 從 **"新項目"** 視窗中,選擇 **"空白表單應用**"(從*多平臺>應用*內),然後按一下「**下一步**」。
4. 更新以下欄位,然後按下 **「下一步**」 :

    - **套用名稱:** 數學Funcs應用程式。
    - **組織識別碼:** 使用反向命名空間,例如 _,com.{your_org}_。
    - **目標平臺:** 使用預設值(Android和iOS目標)。
    - **共用代碼:** 將此設置為 .NET 標準("共用庫"解決方案是可能的,但超出了本演練的範圍)。

5. 更新以下欄位 **,然後按下:**

    - **專案名稱:** 數學Funcs應用程式。
    - **解決方案名稱:** 數學Funcs應用程式。  
    - **位置:** 使用預設保存位置(或選擇替代位置)。

6. 在**解決方案資源管理器****中,CONTROL = 點擊**目標 **(MathFuncsApp.Android**或**MathFuncs.iOS)** 進行初始測試,然後選擇 **「設定為啟動專案**」。
7. 選擇新選**裝置**或**模擬器**/**模擬器**。 
8. 執行解決方案 (**COMMAND = RETURN**), 以驗證範本化**Xamarin.Forms**專案生成和運行正常。 

    > [!NOTE]
    > **iOS(** 特別是模擬器)往往具有最快的構建/部署時間。

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>將本地端 NuGet 源新增到 NuGet 設定

1. 在**視覺工作室中**,選擇 **「首選項**」(從**視覺工作室**功能表中選擇)。
2. 從**NuGet**部分下選擇**源**,然後按一下「**添加**」。
3. 更新以下欄位,然後按下「**新增來源**」 :

    - **名稱:** 提供有意義的名稱,例如本地包。  
    - **位置:** 指定[在上一步驟](#preparing-a-local-packages-directory)中建立的**本地 nuget-feed**資料夾。

    > [!NOTE]
    > 在這種情況下,無需指定**使用者名稱和密碼**。 **Password** 

4. 按一下 [確定]  。

### <a name="referencing-the-package"></a>參考套件

對每個項目重複以下步驟 **(MathFuncsApp、MathFuncsApp.****安卓**和**MathFuncsApp.iOS)。**

1. **控制 + 點擊**項目,然後從 **"添加"** 選單中選擇 **"添加 NuGet 包..."**
2. 搜尋**數學Funcs**。 
3. 驗證套件**的版本**是**1.0.0,** 其他詳細資訊按預期顯示,如**標題**和**說明**,即*MathFuncs*和*範例C++包裝庫*。 
4. 選擇**MathFuncs**包,然後按下 **「添加包**」 。。

### <a name="using-the-library-functions"></a>使用函式庫函式

現在,通過引用每個專案中的**MathFuncs**包,C# 代碼可以使用這些函數。

1. 打開**MainPage.xaml.cs**從**數學FuncsApp**常見的**Xamarin.Forms**專案(引用**數學FuncsApp.安卓**和**MathFuncsApp.iOS)。**
2. 在檔案頂部新增**使用****系統.診斷**與**MathFuncs 的語句**:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. 在`MyMathFuncs``MainPage`類別的頂端的實體:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. 重寫`OnAppearing``OnDisappearing`基類別`ContentPage`的與方法:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. 更新`OnAppearing`方法以初始化以前`myMathFuncs`聲明 的變數:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. 更新方法`OnDisappearing`以呼`Dispose`叫`myMathFuncs`方法 :

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. 實現名為**TestMathFuncs 的**私有方法,如下所示:

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. 最後,在`TestMathFuncs``OnAppearing`方法末尾調用:

    ```csharp
    TestMathFuncs();
    ```

9. 在每個目標平台上運行應用,並驗證**應用程式輸出**板中的輸出如下所示:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > 如果您在 Android 上測試時遇到 *"DLLNotFoundException"* 或在 iOS 上遇到生成錯誤,請務必檢查您正在使用的設備/模擬器/模擬器的 CPU 體系結構是否與我們選擇支援的子集相容。 

## <a name="summary"></a>總結

本文介紹了如何創建 Xamarin.Forms 應用,該應用程式通過通過 NuGet 包分發的通用 .NET 包裝器使用本機庫。 本演練中提供的示例故意非常簡單,可以更輕鬆地演示該方法。 真正的應用程式必須處理複雜性,如異常處理、回調、更複雜的類型的編組以及與其他依賴項庫的連結。 一個關鍵考慮因素是C++代碼的演變過程與包裝器和用戶端應用程式進行協調和同步的過程。 此過程可能因其中一個或兩個問題是單個團隊的責任而異。 無論哪種方式,自動化都是一個真正的好處。 下面是一些資源,提供了有關一些關鍵概念以及相關下載的進一步閱讀。 

### <a name="downloads"></a>下載

- [NuGet 命令列 (CLI) 工具](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>範例

- [超失效跨平台移動開發,C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [微軟皮克斯(C++和薩馬林)](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [單聲道聖洛杉磯樣品埠](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>進階閱讀

[與本帖子內容相關的文章](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)