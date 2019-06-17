---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047482"
---
## <a name="firewall-configuration"></a>防火牆組態

為了讓測試，以提交到 Xamarin Test Cloud，提交測試的電腦必須能夠與 Test Cloud 伺服器進行通訊。 防火牆必須設定為允許從位於伺服器的網路流量**testcloud.xamarin.com**連接埠 80 和 443。 此端點由 DNS 和 IP 位址有所變更。 

在某些情況下，測試 （或執行測試的裝置） 必須與受到防火牆保護的 web 伺服器通訊。 在此案例中必須設定防火牆以允許來自下列 IP 位址的流量：

* **195.249.159.238**
* **195.249.159.239**
