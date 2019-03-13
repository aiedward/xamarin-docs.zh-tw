---
title: 連結 Xamarin.iOS 應用程式
description: 本文件描述 Xamarin.iOS 連結器，它可用來消除 Xamarin.iOS 應用程式中未使用的程式碼，以減少其大小。
ms.prod: xamarin
ms.assetid: 3A4B2178-F264-0E93-16D1-8C63C940B2F9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/24/2017
ms.openlocfilehash: 04f92988d4d367abd5e6e864d4450aee2e6c1df2
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233792"
---
# <a name="linking-xamarinios-apps"></a>連結 Xamarin.iOS 應用程式

在建置應用程式時，Visual Studio for Mac 或 Visual Studio 會呼叫一個稱為 **mtouch**、內含受控碼連結器的工具。 此工具可用來從類別庫中移除應用程式未使用的功能。 其目標是要縮減應用程式的大小，只在其中裝載必要的位元。

連結器會使用靜態分析來決定應用程式可能遵循的不同程式碼路徑。 此作業並不輕鬆，因為它必須仔細檢查每個組件，以確保不會移除任何可探索的項目。 根據預設，模擬器組建並不會啟用連結器，以便能在偵錯時加快建置時間。 不過，由於它能產生較小的應用程式，因此能夠加快 AOT 編譯和上傳至裝置的速度，依預設，所有裝置 (發行) 組建都會使用連結器。

連結器是靜態工具，因此無法標記為用於透過反映所呼叫的包含類型和方法，也無法以動態方式具現化。 有幾個選項可解決這項限制。

<a name="Linker_Behavior" />

## <a name="linker-behavior"></a>連結器行為

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

您可以透過 [專案選項] 中的 [連結器行為] 下拉式清單自訂連結程序。 若要存取此清單，請對 iOS 專案按兩下，然後瀏覽至 [iOS 組建] > [連結器選項]，如下所示：

[![](linker-images/image1.png "連結器選項")](linker-images/image1.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

您可以在 Visual Studio 中，透過 [專案屬性] 中的 [連結器行為] 下拉式清單自訂連結程序。

請執行下列動作：

1. 以滑鼠右鍵按一下 [方案總管] 中的**專案名稱**，然後選取 [屬性]：

    ![](linker-images/linking01w.png "以滑鼠右鍵按一下 [方案總管] 中的專案名稱，然後選取 [屬性]")
2. 在 [專案屬性] 中，選取 [iOS 組建]：

    ![](linker-images/linking02w.png "選取 IOS 組建")
3. 遵循下列指示來變更連結選項。

-----

所提供的三種主要選項如下所述：


### <a name="dont-link"></a>不要連結

停用連結可確保不會有修改過的組件。 基於效能考量，這是 IDE 以 iOS 模擬器作為目標時的預設設定。 若為裝置組建，這只應作為每當連結器包含的錯誤會讓應用程式無法執行時的因應措施。 如果應用程式只能在搭配 -nolink 時運作，請提交[錯誤報告](https://github.com/xamarin/xamarin-macios/issues/new)。

此選項對應至使用命令列工具 mtouch 時的 *-nolink* 選項。

<a name="Link_SDK_assemblies_only" />

### <a name="link-sdk-assemblies-only"></a>僅連結 SDK 組件

在此模式中，連結器會讓組件保持原封不動，並且會藉由移除應用程式未使用的所有項目來縮減 SDK 組件 (也就是 Xamarin.iOS 的隨附項目) 的大小。 這是 IDE 以 iOS 裝置作為目標時的預設設定。

這是最簡單的選項，因為不需要變更任何程式碼。 與連結所有項目不同的是，在此模式中連結器無法執行一些最佳化設定，因此您需要在需要連結所有項目的工作以及最終的應用程式大小之間做出取捨。

此選項對應至使用命令列工具 mtouch 時的 -linksdk 選項。

<a name="Link_all_assemblies" />

### <a name="link-all-assemblies"></a>連結所有組件

在連結所有項目時，連結器可以使用其整組最佳化設定讓應用程式盡可能地小。 它會修改使用者程式碼，每當程式碼使用功能的方式無法為連結器的靜態分析所偵測到，就可能導致程式碼損毀。 在這類情況下 (例如 Web 服務、反映或序列化)，應用程式可能需要進行一些調整才能連結所有項目。

此選項對應至使用命令列工具 **mtouch** 時的 -linkall 選項。

<a name="Controlling_the_Linker" />

## <a name="controlling-the-linker"></a>控制連結器

當您使用連結器時，它有時會移除您可能是以動態方式呼叫的程式碼，甚至是間接移除。 為了涵蓋這些案例，連結器提供了一些功能和選項供您加強對其動作的控制力。

<a name="Preserving_Code" />

### <a name="preserving-code"></a>保留程式碼

當您使用連結器時，它有時會移除您可能是以動態方式 (使用 System.Reflection.MemberInfo.Invoke，或藉由使用 `[Export]` 屬性將方法匯出到 Objective-C 再以手動方式叫用選取器) 呼叫的程式碼。

在這些情況下，您可以指示連結器考慮是使用整個類別，還是要藉由在類別層級或成員層級套用 `[Xamarin.iOS.Foundation.Preserve]` 屬性以保留個別成員。 未由應用程式以靜態方式連結的每個成員都可能會移除。 此屬性因此可用來標記未以靜態方式參考，但應用程式仍需要的成員。

例如，若您是以動態方式來具現化類型，您可以保留您類型的預設建構函式。 若您使用的是 XML 序列化，則您可能會想要保留您類型的屬性。

您可以將此屬性套用到某個類型的每個成員，或是套用到類型本身。 若您想要保留整個類型，則可對該類型使用 `[Preserve
(AllMembers = true)]` 語法。

有時候您會想要只有在包含的類型受到保留時，才保留特定的成員。 在這些情況下，請使用 `[Preserve (Conditional=true)]`

若您不想要相依於 Xamarin 程式庫，例如，假設您要建置一個跨平台可攜式類別庫 (PCL) - 您仍可使用此屬性。

若要這樣做，您只需宣告 PreserveAttribute 類別，並在您的程式碼中使用該類別，如下所示：

```csharp
public sealed class PreserveAttribute : System.Attribute {
    public bool AllMembers;
    public bool Conditional;
}
```

其定義所在的命名空間真的不是很重要，連結器會依類型名稱尋找此屬性。

 <a name="Skipping_Assemblies" />

### <a name="skipping-assemblies"></a>略過組件

您可以指定應從連結器程序排除的組件，同時又能夠以一般方式連結其他組件。 如果無法在某些組件上使用 `[Preserve]` (例如，第 3 方程式碼) 或是要作為錯誤的暫時解決方法，這便很實用。

此選項對應至使用命令列工具 mtouch 時的 `--linkskip` 選項。

在使用 [連結所有組件] 選項時，如果您想要告訴連結器略過整個組件，請將下列內容放入最上層組件的 [其他 mtouch 引數] 選項中：

```csharp
--linkskip=NameOfAssemblyToSkipWithoutFileExtension
```

如果您要讓連結器略過多個組件，請納入多個 `linkskip` 引數：

```csharp
--linkskip=NameOfFirstAssembly --linkskip=NameOfSecondAssembly
```

沒有可供使用這個選項的使用者介面，但 Visual Studio for Mac [專案選項] 對話方塊或 Visual Studio [專案屬性] 窗格中的 [其他 mtouch 引數] 文字欄位內會提供此選項。 (例如， *--linkskip=mscorlib* 不會連結 mscorlib.dll，但會連結解決方案中的其他組件)。

<a name="Disabling_Link_Away" />

### <a name="disabling-link-away"></a>停用「連結到別處」

連結器會移除裝置上不太可能用到的程式碼，例如不受支援或不允許。 在極少數情況下，應用程式或程式庫可能會相依於這個 (不論是否運作) 的程式碼。 自 Xamarin.iOS 5.0.1 起，即可指示連結器略過此最佳化。

此選項對應至使用命令列工具 mtouch 時的 -nolinkaway 選項。

沒有可供使用這個選項的使用者介面，但 Visual Studio for Mac [專案選項] 對話方塊或 Visual Studio [專案屬性] 窗格中的 [其他 mtouch 引數] 文字欄位內會提供此選項。 (例如， *--nolinkaway* 不會移除額外的程式碼 (大約 100kb))。

### <a name="marking-your-assembly-as-linker-ready"></a>將組件標示為已可供連結器使用

使用者可以選取只連結 SDK 組件，而不對其程式碼進行任何連結。  這也表示不會連結任何不屬於 Xamarin 核心 SDK 的第三方程式庫。

這種情況常常發生，因為使用者不想要以手動方式在其程式碼中新增 `[Preserve]` 屬性。  副作用是不會連結第三方程式庫，這通常算是不錯的預設值，因為我們無法知道第三方程式庫是否適合搭配連結器使用。

如果您的專案中有程式庫，或者您是可重複使用之程式庫的開發人員，當您想要讓連結器將您的組件視為可連結時，您只需要新增組件層級的屬性 [`LinkerSafe`](xref:Foundation.LinkerSafeAttribute) 即可，如下所示：

```csharp
[assembly:LinkerSafe]
```

您的程式庫實際上不需要為此參考 Xamarin 程式庫。  例如，如果您要建置會在其他平台執行的可攜式類別庫，您還是可以使用 `LinkerSafe` 屬性。
Xamarin 連結器會依名稱查閱 `LinkerSafe` 屬性，而不是依其實際類型。  這意味著您可以撰寫此程式碼，而且它一樣可以運作：

```csharp
[assembly:LinkerSafe]
// ... assembly attribute should be at top, before source
class LinkerSafeAttribute : System.Attribute {}
```

## <a name="custom-linker-configuration"></a>自訂連結器組態

請遵循[建立連結器組態檔的指示](~/cross-platform/deploy-test/linker.md)。


## <a name="related-links"></a>相關連結

- [自訂連結器設定](~/cross-platform/deploy-test/linker.md)
- [在 Mac 上連結](~/mac/deploy-test/linker.md)
- [在 Android 上連結](~/android/deploy-test/linker.md)
