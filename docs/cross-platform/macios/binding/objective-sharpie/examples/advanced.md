---
title: 進階 （手動） 的真實世界範例
description: 本文件說明如何使用 xcodebuild 輸出做為目標 Sharpie，提供深入了解目標 Sharpie 沒有實際上的輸入。
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: e820a0c208907a95dda4a50427bb4dac27b88964
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977896"
---
# <a name="advanced-manual-real-world-example"></a>進階 （手動） 的真實世界範例

**這個範例會使用[POP 程式庫，從 Facebook](https://github.com/facebook/pop)。**

本節涵蓋更進階的方法來繫結，我們將在其中使用 Apple 的`xcodebuild`工具第一次建置 POP 專案時，與手動推算目標 Sharpie 的輸入。 這基本上是包含目標 Sharpie 上一節中探討。

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

因為 POP 程式庫有 Xcode 專案 (`pop.xcodeproj`)，我們可以直接使用`xcodebuild`建置 POP。 此程序接著可能會產生目標 Sharpie 可能要剖析的標頭檔。 這就是為什麼建置之前繫結很重要。 透過建置時`xcodebuild`您傳遞相同的 SDK 的識別項和架構，請確定您想要傳遞至目標 Sharpie （請記住，目標 Sharpie 3.0 通常可以為您完成 ！）：

```
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1
 
=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===
 
...
 
CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP
 
...
 
** BUILD SUCCEEDED **
```

做為一部分會有大量的輸出在主控台中的組建資訊`xcodebuild`。 上方顯示，我們可以看到已執行"CpHeader 」 目標，其中的標頭檔會複製到組建輸出目錄。 這通常是如此，並且更輕鬆繫結： 原生程式庫建置的一部分，標頭檔通常會複製到可以讓您更容易剖析繫結的 「 公開 」 可取用位置。 在此情況下，我們知道 POP 的標頭檔位於`build/Headers`目錄。

我們現在已準備好繫結 POP。 我們知道我們想要建置的 SDK`iphoneos8.1`具有`arm64`架構，以及我們所關注的標頭檔位於`build/Headers`下 POP git 簽出。 請看看`build/Headers`目錄下，我們會看到數個標頭檔：

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

如果我們看看`POP.h`，我們可以看到它是程式庫的主要最上層的標頭檔`#import`s 其他檔案。 因為這個緣故，我們只需要傳遞`POP.h`至目標 Sharpie clang 會執行在幕後的其餘部分：

```
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

您會注意到我們傳遞`-scope build/Headers`目標 Sharpie 引數。 因為 C 和 Objective C 程式庫必須`#import`或是`#include`是實作詳細資料的程式庫和 API 您想要繫結時，其他標頭檔`-scope`引數會指示要略過中未定義任何 API 的目標 Sharpie檔案內的某處`-scope`目錄。

您會發現`-scope`引數通常是選擇性的完全實作的程式庫，但還沒有壞處，明確地提供它。

此外，我們指定`-c -Ibuild/headers`。 首先，`-c`引數會指示停止解譯命令列引數，並傳遞任何後續的引數的目標 Sharpie_直接對 clang 編譯器_。 因此， `-Ibuild/Headers` clang 編譯器引數，它會指示要搜尋的 clang 包括下`build/Headers`，也就是 POP 標頭的存放位置。 沒有這個引數，clang 就不知道如何找出檔案，`POP.h`是`#import`結合。 _幾乎所有 「 問題 」 使用目標 Sharpie 歸結為找出要傳遞至 clang_。

### <a name="completing-the-binding"></a>完成繫結

現在已產生目標 Sharpie`Binding/ApiDefinitions.cs`和`Binding/StructsAndEnums.cs`檔案。

這些是目標 Sharpie 基本第一次的傳遞的繫結，以及在少數情況下它可能是您只需要。 如上所述不過，開發人員通常必須手動修改產生的檔案之後若要完成的目標 Sharpie,[修正任何問題](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)，可能不會自動處理此工具。

更新完成後，這兩個檔案現在可以新增至繫結專案在 Visual Studio for Mac，或直接傳遞`btouch`或`bmac`工具來產生最終的繫結。

如需繫結程序的完整描述，請參閱我們[完整逐步解說指示](~/ios/platform/binding-objective-c/walkthrough.md)。
