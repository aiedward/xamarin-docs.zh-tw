---
ms.openlocfilehash: 05f1017f8c4b306996d3e8e165511ff9062a1026
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047483"
---

下列命令列指定解決方案的發行組建**SOLUTION_FILE.sln**適用於 iPhone。 可以透過指定設定的 IPA 位置`IpaPackageDir`命令列上的屬性：

 - 在 Mac 上，使用**xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

**Xbuild**命令通常位於目錄 **/Library/Frameworks/Mono.framework/Commands**。

 - 在 Windows 中，使用**msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**msbuild**不會自動展開`$( )`運算式傳入命令列。 基於這個理由建議時，使用完整路徑`IpaPackageDir`在命令列。


請參閱[適用於 iOS 9.8 更早的版本資訊](https://developer.xamarin.com/releases/ios/xamarin.ios_9/xamarin.ios_9.8/#New_MSBuild_property_IpaPackageDir_to_customize_.ipa_output_location)如需詳細資訊`IpaPackageDir`屬性。
