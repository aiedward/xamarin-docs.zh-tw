---
ms.openlocfilehash: e383bbccd4e76be8a208f5680e5cf21e45a0dbc3
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511935"
---

下列命令列可指定適用于 iPhone 之方案**SOLUTION_FILE**的發行組建。 您可以藉由在命令列上指定`IpaPackageDir`屬性來設定 .ipa 的位置:

- 在 Mac 上使用**xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

**Xbuild**命令通常位於目錄 **/Library/Frameworks/Mono.framework/Commands**中。

- 在 Windows 上, 使用**msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**msbuild**不會自動展開`$( )`命令列傳入的運算式。 基於這個理由, 建議您在命令列設定`IpaPackageDir`時使用完整路徑。

如需有關`IpaPackageDir`屬性的詳細資料, 請參閱[iOS 9.8 的版本](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location)資訊。
