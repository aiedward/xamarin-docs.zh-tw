---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: 搭配 Xamarin 使用C++ C/程式庫
description: Visual Studio for Mac 可用來使用 Xamarin 和C++ C#，將跨平臺 C/程式碼建立及整合到 Android 和 iOS 的行動裝置應用程式中。 本文說明如何在 Xamarin 應用程式中設定和C++ debug 專案。
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 42a59570d727657b2f3c23bd9d1f37e1205717d0
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842811"
---
# <a name="use-cc-libraries-with-xamarin"></a>搭配 Xamarin 使用C++ C/程式庫

## <a name="overview"></a>總覽

Xamarin 可讓開發人員使用 Visual Studio 建立跨平臺原生行動應用程式。 一般來說， C#系結是用來向開發人員公開現有的平臺元件。 不過，有些時候 Xamarin 應用程式需要使用現有的程式碼基底。 有時候小組不會有時間、預算或資源，無法將大型、經過妥善測試且高度優化的程式碼基底C#移植至。

[適用C++于跨平臺行動裝置開發的視覺效果](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development)可C++讓C# C/和程式碼建立為相同解決方案的一部分，提供許多優點，包括統一的偵錯工具體驗。 Microsoft 以這種方式C++使用了 C/和 Xamarin 來提供應用程式，例如[Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw)和[Pix 攝影機](https://www.microsoft.com/microsoftpix)。

不過，在某些情況下，需要（或需求）讓現有的 C/C++工具和進程保持原狀，並讓程式庫程式碼與應用程式分離，將程式庫視為類似于協力廠商元件。 在這些情況下，挑戰不只會將相關的成員公開C#到，而是以相依性的方式管理程式庫。 當然，此程式盡可能自動化。  

這篇文章概述此案例的高階方法，並逐步解說一個簡單的範例。

## <a name="background"></a>背景

C/C++被視為跨平臺語言，但必須特別小心，以確保原始程式碼確實是跨平臺的，只使用所有目標編譯器的 C/C++支援，而且只包含小或無條件包含的平臺或編譯器特定的程式碼。

最後，程式碼必須在所有目標平臺上順利編譯和執行，因此，這會匯總到目標平臺（和編譯器）的共同點。 這些問題仍然可能是因為編譯器之間的微小差異，而在每個目標平臺上進行徹底測試（最好是自動化）變得越來越重要。  

## <a name="high-level-approach"></a>高階方法

下圖所示的四階段方法，是用來將 CC++ /原始程式碼轉換成跨平臺 Xamarin 程式庫（透過 NuGet 共用），然後在 Xamarin 應用程式中使用。

![搭配使用 C/C++與 Xamarin 的高階方法](images/cpp-steps.jpg)

4個階段為：

1. 將 C/C++ source 程式碼編譯成平臺特定的原生程式庫。
2. 使用 Visual Studio 方案包裝原生程式庫。
3. 封裝和推送適用于 .NET 包裝函式的 NuGet 套件。
4. 使用 Xamarin 應用程式的 NuGet 套件。

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>第1階段：將 C/C++原始程式碼編譯成平臺特定的原生程式庫

這個階段的目標是要建立可由C#包裝函式呼叫的原生程式庫。 視您的情況而定，這不一定相關。 在這個常見案例中，可以帶入的許多工具和程式已超出本文的範圍。 主要考慮是讓 C/C++ codebase 與任何原生包裝函式程式碼、足夠的單元測試和組建自動化保持同步。 

逐步解說中的程式庫是使用 Visual Studio Code 搭配隨附的 shell 腳本所建立。 此逐步解說的擴充版本可在行動[CAT GitHub 存放庫](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin)中找到，其深入探討此部分的範例。 在此情況下，原生程式庫被視為協力廠商相依性，不過這個階段會針對內容進行說明。

為了簡單起見，本逐步解說僅以架構的子集為目標。 針對 iOS，它會使用 lipo 公用程式，從個別架構特定的二進位檔建立單一的 fat 二進位檔。 Android 會搭配使用動態二進位檔。因此，延伸模組和 iOS 會使用靜態的 fat 二進位檔，副檔名為.。 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>階段2：使用 Visual Studio 方案包裝原生程式庫

下一個階段是要包裝原生程式庫，以便從 .NET 輕鬆地使用它們。 這是利用具有四個專案的 Visual Studio 方案來完成。 共用專案包含通用程式碼。 以每個 Xamarin、Xamarin 和 .NET Standard 為目標的專案，可讓您以平臺中立的方式來參考程式庫。

包裝函式會使用 Paul Betts 所述的「[bait 和切換技巧](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)」。 這不是唯一的方法，但它可讓您輕鬆地參考程式庫，並避免在取用應用程式本身內明確管理平臺特定的實作為需求。 這個技巧基本上是要確保目標（.NET Standard、Android、iOS）共用相同的命名空間、元件名稱和類別結構。 因為 NuGet 一律會偏好平臺特定程式庫，所以在執行時間永遠不會使用 .NET Standard 版本。

在此步驟中，大部分的工作將著重于使用 P/Invoke 呼叫原生程式庫方法，以及管理基礎物件的參考。 其目標是要向取用者公開程式庫的功能，同時將任何複雜度抽象化。 Xamarin 的開發人員不需要對非受控程式庫的內部工作提供知識。 它應該會想要使用任何其他 managed C#程式庫。

最後，此階段的輸出是一組 .NET 程式庫（每個目標一個），還有一個 nuspec 檔，其中包含在下一個步驟中建立封裝所需的資訊。

**第3階段：封裝和推送適用于 .NET 包裝函式的 NuGet 套件**

第三個階段是使用上一個步驟中的組建構件來建立 NuGet 套件。 此步驟的結果是可以從 Xamarin 應用程式使用的 NuGet 套件。 本逐步解說會使用本機目錄作為 NuGet 摘要。 在生產環境中，此步驟應將封裝發佈至公用或私用 NuGet 摘要，而且應該完全自動化。

**第4階段：從 Xamarin 應用程式使用 NuGet 套件**

最後一個步驟是參考並使用來自 Xamarin 應用程式的 NuGet 套件。 這需要在 Visual Studio 中設定 NuGet 摘要，才能使用在上一個步驟中定義的摘要。

設定摘要之後，必須從跨平臺的 Xamarin 應用程式中的每個專案參考該套件。 「Bait 和切換技巧」提供了相同的介面，因此可以使用在單一位置定義的程式碼來呼叫原生程式庫功能。

原始程式碼存放庫包含[進一步閱讀的清單](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)，其中包含如何在 Azure DevOps 上設定私用 NuGet 摘要的文章，以及如何將套件推送至該摘要。 雖然需要比本機目錄更多的安裝時間，但這種類型的摘要在小組開發環境中是較好的。

## <a name="walk-through"></a>逐步解說

所提供的步驟是**Visual Studio for Mac**特有的，但結構也適用于**Visual Studio 2017** 。

### <a name="prerequisites"></a>Prerequisites

為了跟著做，開發人員將需要：

- [NuGet 命令列（CLI）](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [ *Mac 的*Visual Studio](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> 需要有效的[**Apple 開發人員帳戶**](https://developer.apple.com/)，才能將應用程式部署到 iPhone。

## <a name="creating-the-native-libraries-stage-1"></a>建立原生程式庫（階段1）

原生程式庫功能是以[逐步解說：建立和使用靜態程式庫（C++）](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017)中的範例為基礎。

此逐步解說會略過第一個階段，建立原生程式庫，因為此程式庫是在此案例中提供為協力廠商相依性。 先行編譯的原生程式庫會連同[範例程式碼](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin)一起包含，或可直接[下載](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts)。

### <a name="working-with-the-native-library"></a>使用原生程式庫

原始的*MathFuncsLib*範例包含名為 `MyMathFuncs` 的單一類別，並具有下列定義：

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

額外的類別會定義可讓 .NET 取用者建立、處置和與基礎原生 `MyMathFuncs` 類別互動的包裝函式。

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

這會是[Xamarin](https://visualstudio.microsoft.com/xamarin/)端所使用的包裝函式。

## <a name="wrapping-the-native-library-stage-2"></a>包裝原生程式庫（第2階段）

此階段需要[上一節](#creating-the-native-libraries-stage-1)中所述的先行[編譯程式庫](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts)。

### <a name="creating-the-visual-studio-solution"></a>建立 Visual Studio 解決方案

1. 在**Visual Studio for Mac**中，按一下 [**新增專案**] （從 [*歡迎使用] 頁面*）或 [**新增方案**] （從 [檔案 *] 功能表）* 。
2. 從 [**新增專案**] 視窗中，選擇 [**共用專案**] （從多*平臺 > 程式庫*），然後按 **[下一步]** 。
3. 更新下欄欄位，然後按一下 [**建立**]：

    - **專案名稱：** MathFuncs。共用  
    - **解決方案名稱：** MathFuncs  
    - **位置：** 使用預設的儲存位置（或選擇替代方案）   
    - 在**方案目錄中建立專案：** 將此設為 checked
4. 在 [**方案總管**] 中，按兩下 [ **MathFuncs** ] 專案，然後流覽至 [**主要設定**]。
5. 移除 **。** 從**預設命名空間**共用，讓它設定為 [僅**MathFuncs** ]，然後按一下 **[確定]** 。
6. 開啟**MyClass.cs** （由範本建立），然後將類別和檔案名重新命名為**MyMathFuncsWrapper** ，並將命名空間變更為**MathFuncs**。
7. **控制 + 按一下**[方案**MathFuncs**]，然後從 **[新增] 功能表選擇**[**加入新專案**]。
8. 從 [**新增專案**] 視窗中，選擇 [ **.NET Standard 程式庫**] （從多*平臺 > 程式庫*），然後按 **[下一步]** 。
9. 選擇 [ **.NET Standard 2.0** ]，然後按 **[下一步]** 。
10. 更新下欄欄位，然後按一下 [**建立**]：

    - **專案名稱：** MathFuncs。標準  
    - **位置：** 使用與共享專案相同的儲存位置   

11. 在 [**方案總管**] 中，按兩下 [ **MathFuncs** ] 專案。
12. 流覽至 [**主要設定**]，然後將 [**預設命名空間**] 更新為**MathFuncs**。
13. 流覽至 [**輸出**設定]，然後將 [**元件名稱**] 更新為**MathFuncs**。
14. 流覽至 [**編譯器**設定]，將設定**變更為 [** **發行**]，只將 [ **Debug] 資訊**設為 [**符號**] 然後按一下 **[確定]** 。
15. 刪除從專案**啟動的 Class1.cs/Getting** （如果其中一個已納入為範本的一部分）。
16. **控制 + 按一下**[專案相依性 **/參考]** 資料夾，然後選擇 [**編輯參考**]。
17. 從 [**專案**] 索引標籤中選取 [ **MathFuncs** ]，然後按一下 **[確定**]。
18. 使用下列設定重複步驟7-17 （忽略步驟9）：

    | **專案名稱**  | **範本名稱**   | **[新增專案] 功能表**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs Android | 類別庫       | Android > 程式庫      |
    | MathFuncs iOS     | 系結程式庫     | iOS > 程式庫          |

19. 在 [**方案總管**] 中，按兩下 [ **MathFuncs** ] 專案，然後流覽至 [**編譯器**設定]。

20. **將設定設**為**Debug**，編輯**定義符號**以包含**Android;** 。

21. **將設定**變更為 [**發行**]，然後編輯 [**定義符號**] 也包含**Android;** 。

22. 針對**MathFuncs**重複步驟19-20，編輯**定義符號**以包含**IOS** ，而不是**Android;** 在這兩種情況下。

23. 在**發行**設定（**CONTROL + COMMAND + B**）中建立解決方案，並驗證所有三個輸出元件（Android、iOS、.NET Standard）（在個別的專案 bin 資料夾中）是否共用相同的名稱**MathFuncs**。

在這個階段，解決方案應該有三個目標，一個適用于 Android、iOS 和 .NET Standard 的 su，以及三個目標各自參考的共用專案。 這些應該設定為使用相同的預設命名空間和具有相同名稱的輸出元件。 這對於先前所述的「bait 和切換」方法是必要的。

### <a name="adding-the-native-libraries"></a>新增原生程式庫

將原生程式庫新增至包裝函式解決方案的程式，在 Android 和 iOS 之間稍有不同。

#### <a name="native-references-for-mathfuncsandroid"></a>MathFuncs 的原生參考

1. **控制並按一下**[ **MathFuncs** ] 專案，然後從 [**新增**] 功能表中，**選擇 [命名**它**的**程式庫]。

2. 針對每**個 ABI** （應用程式二進位介面），**控制和按一下**[程式庫] 資料夾，然後從 [**加入**] 功能表中選擇 [**新增資料夾**]，在個別**ABI**之後將其命名為。 在此情況下：

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > 如需更詳細的總覽，請參閱[NDK 開發人員指南](https://developer.android.com/ndk/guides/)中的[架構和 cpu](https://developer.android.com/ndk/guides/arch)主題，特別是在[應用程式套件中定址機器碼](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages)的一節。

3. 確認資料夾結構：  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. 根據下列對應 **，將對應的**加入至每個**ABI**資料夾的程式庫：

    **arm64-arm64-v8a：** Lib/Android/arm64

    **armeabi-armeabi-v7a：** Lib/Android/arm  

    **x86：** 程式庫/Android/x86

    **x86_64：** 程式庫/Android/x86_64

    > [!NOTE]
    > 若要新增檔案，請在代表個別**ABI**的資料夾上按下**ctrl**鍵，然後從 [**新增**] 功能表選擇 [**加入**檔案]。 選擇適當的程式庫（從**PrecompiledLibs**目錄），然後按一下 [**開啟**]，再按一下 **[確定]** ，將檔案*複製到目錄*。

5. 針對每個 **.** 檔案， **ctrl + 按一下**，然後從 [**組建動作**] 功能表中選擇 [ **EmbeddedNativeLibrary** ] 選項。

現在， **[** 程式庫] 資料夾應該會顯示如下：

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

#### <a name="native-references-for-mathfuncsios"></a>MathFuncs 的原生參考

1. **控制 + 按一下**[ **MathFuncs** ] 專案，然後從 [**新增**] 功能表中選擇 [**加入原生參考**]。 
2. 選擇 [ **libMathFuncs** ] 程式庫（從 [ **PrecompiledLibs** ] 目錄底下的 [程式庫/ios]），然後按一下 [**開啟**] 
3. **控制 + 按一下** **libMathFuncs**檔案（在 [**原生參考**] 資料夾內，然後從功能表中選擇 [**屬性**] 選項  
4. 設定**原生參考**屬性，以便在**properties** Pad 中核取（顯示刻度圖示）：

    - 強制載入
    - 均C++
    - 智慧連結

    > [!NOTE]
    > 使用系結程式庫專案類型和[原生參考](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references)內嵌靜態程式庫，並讓它與參考它的 Xamarin iOS 應用程式自動連結（即使是透過 NuGet 套件所包含）。

5. 開啟**ApiDefinition.cs**，刪除樣板化的批註程式碼（只保留 `MathFuncs` 的命名空間），然後針對**Structs.cs**執行相同的步驟 

    > [!NOTE]
    > 系結程式庫專案需要這些檔案（使用*ObjCBindingApiDefinition*和*ObjCBindingCoreSource*組建動作）才能建立。 不過，我們將撰寫程式碼，以呼叫原生程式庫（在這些檔案之外），這種方式可使用標準 P/Invoke 在 Android 和 iOS 程式庫目標之間共用。

### <a name="writing-the-managed-library-code"></a>撰寫 managed 程式庫程式碼

現在，撰寫程式C#代碼來呼叫原生程式庫。 其目標是要隱藏任何基礎複雜度。 取用者應該不需要具備原生程式庫內部或 P/Invoke 概念的任何操作知識。  

#### <a name="creating-a-safehandle"></a>建立 SafeHandle

1. **控制 + 按一下**[ **MathFuncs** ] 專案，然後從 [**新增**] 功能表選擇 [**加入**檔案]。 
2. 從 [**新增**檔案] 視窗中選擇 [**空白類別**]，將其命名為**MyMathFuncsSafeHandle** ，然後按一下 [**新增**]
3. 執行**MyMathFuncsSafeHandle**類別：

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
    > [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2)是在 managed 程式碼中使用非受控資源的慣用方式。 這會將許多與重要結束和物件生命週期相關的重複使用程式碼抽象化出來。 這個控制碼的擁有者之後可以將它視為任何其他受控資源，而不需要執行完整的可[處置模式](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)。 

#### <a name="creating-the-internal-wrapper-class"></a>建立內部包裝函式類別

1. 開啟**MyMathFuncsWrapper.cs**，將它變更為內部靜態類別

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. 在相同的檔案中，將下列條件陳述式加入至類別：

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > 這會根據是否針對**Android**或**iOS**建立程式庫來設定**DllName**常數值。 這是為了解決每個個別平臺所使用的不同命名慣例，以及在此情況下所使用的程式庫類型。 Android 使用動態連結程式庫，因此需要包含副檔名的檔案名。 若為 iOS，則需要 ' *__Internal*'，因為我們使用的是靜態程式庫。

3. 在**MyMathFuncsWrapper.cs**檔案的頂端，新增 system.runtime.interopservices.outattribute 的參考 **。**

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. 新增包裝函式方法，以處理**MyMathFuncs**類別的建立和處置：

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > 我們會將常數**DllName**傳入**DllImport**屬性，以及明確告知 .net 執行時間要在該程式庫中呼叫的函式名稱的**EntryPoint** 。 就技術上而言，如果我們的受控方法名稱與非受控方法相同，就不需要提供**EntryPoint**值。 如果未提供，則會使用 managed 方法名稱來做為進入**點**。 不過，最好是明確的。  

5. 新增包裝函式方法，讓我們使用下列程式碼來處理**MyMathFuncs**類別：

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
    > 在此範例中，我們會使用簡單類型做為參數。 因為在此情況下，封送處理是位複製，所以不需要在我們的部分進行額外的工作。 另請注意，使用**MyMathFuncsSafeHandle**類別，而不是標準**IntPtr**。 **IntPtr**會在封送處理常式中自動對應到**SafeHandle** 。

6. 確認完成的**MyMathFuncsWrapper**類別顯示如下：

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

#### <a name="completing-the-mymathfuncssafehandle-class"></a>正在完成 MyMathFuncsSafeHandle 類別

1. 開啟**MyMathFuncsSafeHandle**類別，流覽至**ReleaseHandle**方法中的預留位置**TODO**批註：

    ```csharp
    // TODO: Release the handle here
    ```

1. 取代**待辦事項**行：

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>撰寫 MyMathFuncs 類別

既然包裝函式已完成，請建立 MyMathFuncs 類別來管理非受控C++ MyMathFuncs 物件的參考。  

1. **控制 + 按一下**[ **MathFuncs** ] 專案，然後從 [**新增**] 功能表選擇 [**加入**檔案]。 
2. 從 [**新增**檔案] 視窗中選擇 [**空白類別**]，將其命名為**MyMathFuncs** ，然後按一下 [**新增**]
3. 將下列成員新增至**MyMathFuncs**類別：

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. 執行類別的函式，以便在具現化類別時，建立並儲存原生**MyMathFuncs**物件的控制碼：

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. 使用下列程式碼來執行**IDisposable**介面：

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

6. 使用**MyMathFuncsWrapper**類別執行**MyMathFuncs**方法，藉由傳入我們儲存到基礎未受管理物件的指標，在幕後執行實際工作。 程式碼應如下所示：

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

若要透過 NuGet 封裝和散發程式庫，解決方案需要**nuspec**檔案。 這會識別每個支援的平臺會包含哪些產生的元件。

1. **控制 + 按一下**[方案**MathFuncs**]，然後從 [**新增**] 功能表中選擇 [**新增方案資料夾**]，命名為**SolutionItems**。
2. **控制 + 按一下**[ **SolutionItems** ] 資料夾，然後從 [**加入**] 功能表選擇 [**新增**檔案]。
3. 從 [**新增**檔案] 視窗中選擇 [**空的 XML**檔案]，將它命名為**MathFuncs. Nuspec** ，然後按一下 [**新增**]。
4. 使用要對**NuGet**取用者顯示的基本套件中繼資料來更新**MathFuncs。 nuspec** 。 例如:

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
    > 如需此資訊清單所使用之架構的詳細資訊，請參閱[nuspec 參考](https://docs.microsoft.com/nuget/reference/nuspec)檔。

5. 新增 `<files>` 元素做為 `<package>` 專案的子系（以下 `<metadata>`），並以個別的 `<file>` 元素識別每個檔案：

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > 當套件安裝到專案中，且有多個相同名稱指定的元件時，NuGet 將會有效地選擇最特定平臺的元件。

6. 新增**Android**元件的 `<file>` 元素：

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. 新增**iOS**元件的 `<file>` 元素：

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. 為**netstandard 2.0**元件新增 `<file>` 元素：

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. 確認**nuspec**資訊清單：

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
    > 此檔案會指定**發行**組建中的元件輸出路徑，因此請務必使用該設定來建立解決方案。

此時，解決方案會包含3個 .NET 元件和支援的**nuspec**資訊清單。

## <a name="distributing-the-net-wrapper-with-nuget"></a>使用 NuGet 散發 .NET 包裝函式

下一個步驟是封裝和散發 NuGet 套件，讓應用程式可以輕鬆地使用它並作為相依性來管理。 包裝和耗用量全都可以在單一解決方案中完成，但可透過 NuGet 輔助來散佈程式庫，讓我們可以獨立管理這些基本代碼。

### <a name="preparing-a-local-packages-directory"></a>準備本機封裝目錄

NuGet 摘要最簡單的形式是本機目錄：

1. 在搜尋**工具**中，流覽至方便的目錄。 例如， **/Users**。
2. 從 [檔案 **] 功能表中選擇 [** **新增資料夾**]，提供有意義的名稱，例如**本機-nuget-feed**。

### <a name="creating-the-package"></a>建立封裝

1. 將**組建**設定設為 [**發行**]，然後使用**命令 + B**執行組建。
2. 開啟 [**終端**機]，並將目錄變更為包含**nuspec**檔案的資料夾。
3. 在**終端**機中，使用在[上一個步驟](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed)中建立的資料夾（也就是**本機-nuget**）來執行**nuget pack**命令，以指定**Nuspec**檔案、**版本**（例如1.0.0）和**OutputDirectory** 。 例如:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **確認**已在**本機-nuget**摘要目錄中建立**MathFuncs nupkg** 。

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>選擇性搭配 Azure DevOps 使用私用 NuGet 摘要

在[Azure DevOps 中開始使用 NuGet 套件](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package)中會說明更健全的技術，其中說明如何建立私用摘要，並將套件（在上一個步驟中產生）推送至該摘要。

理想的情況是讓此工作流程完全自動化，例如使用[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts)。 如需詳細資訊，請參閱[開始使用 Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts)。

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>從 Xamarin 應用程式使用 .NET 包裝函式

若要完成此逐步解說，請建立**Xamarin**應用程式，以取用剛發佈至本機**NuGet**摘要的套件。

### <a name="creating-the-xamarinforms-project"></a>建立**Xamarin. Forms**專案

1. 開啟**Visual Studio for Mac**的新實例。 這可以從**終端**機執行：

    ```bash
    open -n -a "Visual Studio"
    ```

2. 在**Visual Studio for Mac**中，按一下 [**新增專案**] （從 [*歡迎使用] 頁面*）或 [**新增方案**] （從 [檔案 *] 功能表）* 。
3. 從 [**新增專案**] 視窗中選擇 [**空白表單應用程式**] （從多*平臺 > 應用程式*），然後按 **[下一步]** 。
4. 更新下欄欄位，然後按 **[下一步]** ：

    - **應用程式名稱：** MathFuncsApp.
    - **組織識別碼：** 使用反向命名空間，例如， _com. {your_org}_ 。
    - **目標平臺：** 使用預設值（Android 和 iOS 目標）。
    - **共用的程式碼：** 將此項設定為 .NET Standard （可能是「共用程式庫」解決方案，但超出本逐步解說的範圍）。

5. 更新下欄欄位，然後按一下 [**建立**]：

    - **專案名稱：** MathFuncsApp.
    - **解決方案名稱：** MathFuncsApp.  
    - **位置：** 使用預設的 [儲存位置] （或選擇替代方案）。

6. 在**方案總管**中，**控制項 + 按一下**目標（**MathFuncsApp**或**MathFuncs**）以進行初始測試，然後選擇 [**設定為啟始專案**]。
7. 選擇慣用的**裝置**或模擬器/**模擬器**。 
8. 執行方案（**命令 + RETURN**）以驗證樣板化的**Xamarin**專案是否建立並執行正常。 

    > [!NOTE]
    > **iOS** （尤其是模擬器）往往會有最快速的組建/部署時間。

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>將本機 NuGet 摘要新增至 NuGet 設定

1. 在**Visual Studio**中，選擇 [**喜好**設定] \ （從 [ **Visual Studio** ] 功能表）。
2. 從 [ **NuGet** ] 區段底下選擇 [**來源**]，然後按一下 [**新增**]。
3. 更新下欄欄位，然後按一下 [**新增來源**]：

    - **名稱：** 提供有意義的名稱，例如，本機封裝。  
    - **位置：** 指定在[上一個步驟](#preparing-a-local-packages-directory)中建立的 [**本機-nuget-** 摘要] 資料夾。

    > [!NOTE]
    > 在此情況下，不需要指定使用者**名稱**和**密碼**。 

4. 按一下 [確定]。

### <a name="referencing-the-package"></a>參考套件

針對每個專案（**MathFuncsApp**、 **MathFuncsApp**和**MathFuncsApp**）重複執行下列步驟。

1. **控制並按一下**專案，然後從 [**新增**] 功能表選擇 [**新增 NuGet 套件 ...** ]。
2. 搜尋**MathFuncs**。 
3. 確認套件的**版本**為**1.0.0** ，而其他詳細資料如預期般出現，例如**標題**和**描述**，也就是*MathFuncs*和*範例C++包裝*函式程式庫。 
4. 選取 [ **MathFuncs** ] 套件，然後按一下 [**新增套件**]。

### <a name="using-the-library-functions"></a>使用程式庫函式

現在，參考每個專案中的**MathFuncs**套件，函式可供程式C#代碼使用。

1. 從**MathFuncsApp**通用**Xamarin. Forms**專案（由**MathFuncsApp**和**MathFuncsApp**所參考）中開啟**MainPage.xaml.cs** 。
2. 在檔案的頂端 **，新增** **MathFuncs**的**using**語句：

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. 在 `MainPage` 類別的頂端宣告 `MyMathFuncs` 類別的實例：

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. 覆寫 `ContentPage` 基類中的 `OnAppearing` 和 `OnDisappearing` 方法：

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

5. 更新 `OnAppearing` 方法，以初始化先前宣告的 `myMathFuncs` 變數：

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. 更新 `OnDisappearing` 方法，以在 `myMathFuncs`上呼叫 `Dispose` 方法：

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. 執行名為**TestMathFuncs**的私用方法，如下所示：

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

8. 最後，呼叫 `OnAppearing` 方法結尾處的 `TestMathFuncs`：

    ```csharp
    TestMathFuncs();
    ```

9. 在每個目標平臺上執行應用程式，並驗證 [**應用程式輸出**] 面板中的輸出，如下所示：

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > 如果您在 Android 上測試時遇到「*system.dllnotfoundexception*」，或 iOS 上發生組建錯誤，請務必檢查您所使用之裝置/模擬器/模擬器的 CPU 架構是否與我們選擇支援的子集相容。 

## <a name="summary"></a>總結

本文說明如何建立使用原生程式庫的 Xamarin 應用程式，透過 NuGet 套件散發的通用 .NET 包裝函式。 本逐步解說中提供的範例刻意非常簡單，更容易示範方法。 實際的應用程式必須處理複雜性，例如例外狀況處理、回呼、更複雜類型的封送處理，以及與其他相依性程式庫連結。 主要考慮是程式C++代碼演進的協調流程，並與包裝函式和用戶端應用程式同步處理。 此程式可能會根據其中一個或兩個問題是否為單一小組負責而有所不同。 不論是哪種方式，自動化都是真正的優勢。 以下提供進一步閱讀一些重要概念以及相關下載的資源。 

### <a name="downloads"></a>下載

- [NuGet 命令列（CLI）工具](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>範例

- [使用 Hyperlapse 跨平臺行動開發C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix （C++和 Xamarin）](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Mono San 洛杉磯範例埠](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>進一步閱讀

[與此貼文內容相關的文章](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)