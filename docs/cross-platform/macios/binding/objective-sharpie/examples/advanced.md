---
title: Advanced （手動）真實世界範例
description: 本檔說明如何使用 xcodebuild 的輸出作為目標 Sharpie 的輸入，讓您深入瞭解目標 Sharpie 在幕後的作用。
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 23ca9c3fe36a65aefb17f10fd3e680937c36acc0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016259"
---
# <a name="advanced-manual-real-world-example"></a>Advanced （手動）真實世界範例

**這個範例使用[來自 Facebook 的 POP 程式庫](https://github.com/facebook/pop)。**

本節涵蓋更先進的系結方法，我們將使用 Apple 的 `xcodebuild` 工具先建立 POP 專案，然後手動推算目標 Sharpie 的輸入。 這基本上涵蓋了目標 Sharpie 在上一節中的作用。

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

因為 POP 程式庫有 Xcode 專案（`pop.xcodeproj`），所以我們可以直接使用 `xcodebuild` 來建立 POP。 這個進程可能會產生目標 Sharpie 可能需要剖析的標頭檔。 這就是在系結之前建立的原因很重要。 透過 `xcodebuild` 來建立時，請務必傳遞您要傳遞至目標 Sharpie 的相同 SDK 識別碼和架構（而且請記住，目標 Sharpie 3.0 通常可以為您執行此動作！）：

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

主控台中將會有許多組建資訊輸出，做為 `xcodebuild`的一部分。 如上所示，我們可以看到已執行 "CpHeader" 目標，其中標頭檔已複製到組建輸出目錄。 這通常是這種情況，讓系結變得更容易：在原生程式庫的組建中，標頭檔通常會複製到「公開」的可取用位置，讓剖析作業更容易進行系結。 在此情況下，我們知道 POP 的標頭檔位於 `build/Headers` 目錄中。

我們現在已準備好系結 POP。 我們知道，我們想要使用 `arm64` 架構建立 SDK `iphoneos8.1`，而且我們關心的標頭檔是在 POP git 結帳的 `build/Headers` 中。 如果我們查看 `build/Headers` 目錄，我們會看到一些標頭檔：

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

如果我們查看 `POP.h`，我們可以看到它是程式庫的主要最上層標頭檔，`#import`其他檔案。 因此，我們只需要將 `POP.h` 傳遞至目標 Sharpie，clang 就會在幕後執行其餘工作：

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

您會發現我們已將 `-scope build/Headers` 引數傳遞至目標 Sharpie。 因為 C 和目標 C 程式庫必須 `#import` 或 `#include` 其他的標頭檔，這些是程式庫的執行詳細資料，而不是您想要系結的 API，所以 `-scope` 引數會指示目標 Sharpie 忽略檔案中未定義的任何 API在 `-scope` 目錄中。

您會發現，`-scope` 引數通常是對完全實作為程式庫而言是選擇性的，但是明確提供它並不會有任何傷害。

此外，我們也指定 `-c -Ibuild/headers`。 首先，`-c` 引數會指示目標 Sharpie 停止解讀命令列引數，並將任何後續的引數_直接傳遞給 clang 編譯器_。 因此，`-Ibuild/Headers` 是 clang 編譯器引數，會指示 clang 搜尋包含在 `build/Headers`之下的，也就是 POP 標頭的上線位置。 如果沒有這個引數，clang 就不知道要在哪裡找出 `POP.h` `#import`的位置。 _幾乎所有的「問題」，使用目標 Sharpie 包括向下找出要傳遞至 clang 的內容_。

### <a name="completing-the-binding"></a>正在完成系結

目標 Sharpie 現在已產生 `Binding/ApiDefinitions.cs` 和 `Binding/StructsAndEnums.cs` 檔案。

這些是目標 Sharpie 在系結上的基本第一次傳遞，在少數情況下，您可能只需要這麼做。 如上所述，開發人員通常需要在目標 Sharpie 完成後手動修改產生的檔案，以修正工具無法自動處理的[任何問題](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)。

更新完成之後，您現在可以將這兩個檔案新增至 Visual Studio for Mac 中的系結專案，或直接傳遞至 `btouch` 或 `bmac` 工具來產生最終的系結。

如需系結程式的詳細說明，請參閱我們的[完整逐步解說指示](~/ios/platform/binding-objective-c/walkthrough.md)。
