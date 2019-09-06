---
title: 在 Xamarin 中參考原生程式庫
description: 本檔討論如何將原生 C 程式庫連結至 Xamarin iOS 應用程式。 其中說明如何建立通用原生程式庫，並從C#存取 C 方法。
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/28/2016
ms.openlocfilehash: 75180152c3ed7056102038b9019f8017183c17ee
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279940"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>在 Xamarin 中參考原生程式庫

Xamarin 支援使用原生 C 程式庫和目標 C 程式庫來進行連結。 本檔討論如何連結您的原生 C 程式庫與您的 Xamarin. iOS 專案。 如需針對目標 C 程式庫執行相同作業的詳細資訊，請參閱我們的系結[目標-c 類型](~/ios/platform/binding-objective-c/index.md)檔。

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>建立通用原生程式庫（i386、ARMv7 和 ARM64）

您通常會想要為每個支援的 iOS 開發平臺建立原生程式庫（適用于模擬器的 i386 和適用于裝置本身的 ARMv7/ARM64）。 如果您的文件庫已經有 Xcode 專案，這其實很簡單。

若要建立原生程式庫的 i386 版本，請從終端機執行下列命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

這會導致中的原生靜態程式庫`MyProject.xcodeproj/build/Release-iphonesimulator/`。 複製（或移動）程式庫封存檔案（libMyLibrary），以供稍後使用，並提供唯一名稱（例如**libMyLibrary-i386**），使其不會與您接下來所建立的相同程式庫的 arm64 和 armv7 版本衝突。

若要建立原生程式庫的 ARM64 版本，請執行下列命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

這次，內`MyProject.xcodeproj/build/Release-iphoneos/`建的原生程式庫會位於。 同樣地，請將此檔案複製（或移動）到安全的位置，將它重新命名為**libMyLibrary-arm64**之類的專案，這樣就不會發生衝突。

現在建立 ARMv7 版本的程式庫：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

將產生的程式庫檔案複製（或移動）到您移至其他2個程式庫版本的相同位置，將它重新命名為類似 LibMyLibrary 的專案（例如**armv7**）。

若要建立通用二進位檔，您可以使用`lipo`此工具，如下所示：

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

這會`libMyLibrary.a`建立，這將會是適用于所有 iOS 開發目標的通用（fat）程式庫。


### <a name="missing-required-architecture-i386"></a>缺少必要的架構 i386

當您嘗試在 iOS `does not implement methodSignatureForSelector`模擬器`does not implement doesNotRecognizeSelector`中使用目標 C 程式庫時，如果您在執行時間輸出中收到或訊息，表示您的程式庫可能未針對 i386 架構進行編譯（請參閱[建立通用原生程式庫](#building_native)一節）。

若要檢查指定程式庫所支援的架構，請在終端機中使用下列命令：

```bash
lipo -info /full/path/to/libraryname.a
```

其中`/full/path/to/`是所使用之程式庫的完整路徑， `libraryname.a`而是有問題的程式庫名稱。

如果您有程式庫的來源，如果您想要在 iOS 模擬器上測試您的應用程式，則也需要針對 i386 架構進行編譯和組合。

### <a name="linking-your-library"></a>連結媒體櫃

您使用的任何協力廠商程式庫都必須以靜態方式與您的應用程式連結。 

如果您想要以靜態方式連結您從網際網路或以 Xcode 建立的程式庫「libMyLibrary」，您必須執行一些動作：

- 將程式庫帶入您的專案
- 設定 Xamarin 以程式庫
- 從程式庫存取方法。


若要將連結**庫帶入您的專案**，請從 [方案瀏覽器] 中選取專案，然後按**命令 + Option + a**。 流覽至 libMyLibrary，並將它加入至專案。 出現提示時，請告訴 Visual Studio for Mac 或 Visual Studio 將它複製到專案中。 新增之後，在專案中尋找 libFoo，並以滑鼠右鍵按一下它，並將 [組建]**動作**設定為 [**無**]。

若要將**Xamarin 設定為程式庫**，您可以在最終可執行檔（而不是媒體櫃本身，但最終程式）的專案選項上，新增**IOS 組建**的額外引數（這些是專案選項的一部分）： "-gcc_flags"選項後面接著一個加上引號的字串，其中包含程式所需的所有額外程式庫，例如：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

上述範例會連結**libMyLibrary。**

您可以使用`-gcc_flags`來指定任何一組命令列引數，以傳遞給用來執行可執行檔最後連結的 GCC 編譯器。 例如，此命令列也會參考 CFNetwork 架構：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

如果您的原生C++程式庫包含程式碼，您也必須在「額外引數」中傳遞-.cxx 旗標，讓 Xamarin 知道要使用正確的編譯器。 C++先前的選項如下所示：

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>從 C 存取 C 方法&#35;

IOS 上有兩種可用的原生程式庫：

- 屬於作業系統一部分的共用程式庫。

- 您的應用程式隨附的靜態程式庫。


若要存取其中任一項所定義的方法，您可以使用[Mono 的 P/Invoke 功能](https://www.mono-project.com/docs/advanced/pinvoke/)，這是您在 .net 中使用的相同技術，大致如下：

- 判斷您想要叫用的 C 函數
- 判斷其簽章
- 判斷其所在的媒體櫃
- 撰寫適當的 P/Invoke 宣告

當您使用 P/Invoke 時，您需要指定程式庫的路徑。 使用 iOS 共用程式庫時，您可以硬式編碼路徑，或者可以使用我們在中`Constants`定義的便利性常數，這些常數應該涵蓋 iOS 共用程式庫。

例如，如果您想要從 Apple 的 UIKit 程式庫叫用具有此簽章的 UIRectFrameUsingBlendMode 方法 C：

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

您的 P/Invoke 宣告看起來會像這樣：

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

UIKitLibrary 只是定義為 "/System/Library/Frameworks/UIKit.framework/UIKit" 的常數，而 EntryPoint 可讓我們指定外部名稱（UIRectFramUsingBlendMode），同時在中C#公開不同的名稱，較短的 RectFrameUsingBlendMode。

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>存取 C Dylib 來處理

如果您需要在您的 Xamarin iOS 應用程式中使用 C Dylib，則必須先進行一些額外的設定，才能呼叫`DllImport`屬性。

例如，如果我們有`Animal.dylib`一個`Animal_Version`方法，而我們將在我們的應用程式中呼叫它，我們需要先通知 Xamarin 的程式庫位置，再嘗試使用它。

若要這麼做，請`Main.CS`編輯檔案，使其看起來如下所示：

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

其中`/full/path/to/`是所使用之 Dylib 的完整路徑。 當此程式碼準備就緒之後，我們就可以連結`Animal_Version`到方法，如下所示：

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>靜態程式庫

因為您只能在 iOS 上使用靜態程式庫，所以沒有可連結的外部共用程式庫，因此 DllImport 屬性中的 path 參數必須使用特殊名稱`__Internal` （請注意名稱開頭的雙底線字元），而不是路徑名稱。

這會強制 DllImport 查詢您在目前程式中參考之方法的符號，而不是嘗試從共用程式庫載入。

