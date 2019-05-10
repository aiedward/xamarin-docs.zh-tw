---
title: Google 雲端通訊
description: Google 雲端通訊 (GCM) 是一項服務，可協助行動裝置應用程式和伺服器應用程式之間通訊。 這篇文章概述 GCM 的運作方式，並說明如何設定 Google 服務，因此您的應用程式可以使用 GCM。
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/02/2019
ms.openlocfilehash: 46de067e52ff141ea30a097562874ff4b2fa1c01
ms.sourcegitcommit: 53480ed32a126f88eec82e8c8ee5ed8d30616c44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65017714"
---
# <a name="google-cloud-messaging"></a>Google 雲端通訊

> [!WARNING]
> Google 已被取代自 2018 年 4 月 10 日起的 GCM。 下列的文件和範例專案可能不會再進行維護。 Google 的 GCM 伺服器和用戶端 Api 將會盡速以 2019 5 月 29 日移除。 Google 建議將 GCM 應用程式移轉至 Firebase 雲端通訊 (FCM)。 如需有關 GCM 與移轉的詳細資訊，請參閱[Google 已被取代 Cloud Messaging](https://developers.google.com/cloud-messaging/)。
>
> 若要開始使用 Firebase 雲端通訊搭配 Xamarin，請參閱[Firebase 雲端通訊](firebase-cloud-messaging.md)。

_Google 雲端通訊 (GCM) 是一項服務，可協助行動裝置應用程式和伺服器應用程式之間通訊。這篇文章概述 GCM 的運作方式，並說明如何設定 Google 服務，因此您的應用程式可以使用 GCM。_

[![Google Cloud Messaging 標誌](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

本主題提供如何 Google Cloud Messaging 路由傳送訊息，您的應用程式和應用程式伺服器之間的高階概觀，並取得認證，好讓您的應用程式可以使用 GCM 服務提供的逐步程序。

## <a name="overview"></a>總覽

Google 雲端通訊 (GCM) 是一項服務會處理傳送、 路由和伺服器應用程式和行動用戶端應用程式之間的訊息排入佇列。 A*用戶端應用程式*是 GCM 功能以裝置執行的應用程式。 *應用程式伺服器*（由您或貴公司提供） 是透過 GCM 用戶端應用程式進行通訊與 GCM 啟用伺服器：

[![用戶端應用程式和應用程式伺服器之間，位於 GCM](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

使用 GCM，應用程式伺服器可以傳送訊息給單一裝置、 裝置、 群組或主題訂閱的裝置數目。 您的用戶端應用程式可以使用 GCM，來訂閱 （例如，若要接收遠端通知） 的應用程式伺服器的下游訊息。 此外，GCM 可讓用戶端應用程式將訊息上游傳送回應用程式伺服器。

如需適用於 GCM 實作應用程式伺服器的資訊，請參閱[關於 GCM 連接伺服器](https://developers.google.com/cloud-messaging/server)。



## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging 動作

當下游的訊息會從應用程式伺服器傳送至用戶端應用程式時，應用程式伺服器會傳送訊息至*GCM 連接伺服器*; GCM 連線伺服器上，依次轉寄訊息到執行您的用戶端應用程式的裝置。 可以透過 HTTP 傳送訊息或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可延伸傳訊和目前狀態通訊協定）。 因為用戶端應用程式不會一直連線或執行，GCM 連接伺服器將加入佇列並將訊息，將它們傳送到用戶端應用程式，因為它們重新連線，而且可用。 同樣地，GCM 將加入佇列應用程式伺服器到用戶端應用程式的上游來自應用程式伺服器無法使用時。

GCM 使用下列認證來識別應用程式伺服器和用戶端應用程式，並使用這些認證來授權透過 GCM 的訊息交易：

-   **API 金鑰** &ndash; *API 金鑰*Google 服務，可讓您的應用程式伺服器存取GCM 會使用此金鑰來驗證您的應用程式伺服器。
    您可以使用 GCM 服務之前，您必須先取得 API 金鑰[Google Developer Console](https://console.developers.google.com/)藉由建立*專案*。 API 金鑰應該受到保護;如需有關如何保護您的 API 金鑰的詳細資訊，請參閱 <<c0> [ 安全地使用 API 金鑰最佳做法](https://support.google.com/cloud/answer/6310037?hl=en)。

-   **寄件者識別碼** &ndash; *寄件者識別碼*授權應用程式伺服器至用戶端應用程式&ndash;它是識別應用程式伺服器，允許將訊息傳送至用戶端應用程式的唯一數字。
    寄件者識別碼也是您的專案號碼;當您註冊您的專案時，您可以取得從 Google 開發人員主控台的寄件者識別碼。

-   **註冊權杖** &ndash; *註冊語彙基元*是指定的裝置上的用戶端應用程式的 GCM 身分識別。 註冊權杖會在執行階段產生&ndash;時它第一次向 GCM 註冊的裝置上執行時，您的應用程式會收到註冊權杖。 註冊權杖，授權 （在該特定裝置上執行） 的用戶端應用程式執行的個體從 GCM 接收訊息。

-   **應用程式識別碼**&ndash;用戶端應用程式 （而不是任何指定的裝置） 註冊從 GCM 接收訊息的身分識別。 在 Android 上，應用程式識別碼會記錄中的封裝名稱**AndroidManifest.xml**，例如`com.xamarin.gcmexample`。

[設定註冊 Google Cloud Messaging](#settingup) （稍後在本指南中） 提供建立專案，並產生這些認證的詳細的指示。

下列各節將說明當用戶端應用程式與透過 GCM 的應用程式伺服器通訊時，如何使用這些認證。



### <a name="registration-with-gcm"></a>向 GCM 註冊

在裝置上安裝用戶端應用程式首先必須向 GCM 註冊，再傳訊進行。 用戶端應用程式必須完成註冊步驟，如下圖所示：

[![應用程式註冊步驟](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  用戶端應用程式會連絡 GCM 取得註冊權杖，將寄件者識別碼傳遞至 GCM。

2.  GCM 註冊權杖傳回給用戶端應用程式。

3.  用戶端應用程式會將轉送至應用程式伺服器的註冊權杖。

應用程式伺服器會快取註冊權杖，供後續使用用戶端應用程式的通訊。 （選擇性） 在應用程式伺服器可以傳送通知給用戶端應用程式，以表示已收到註冊權杖。 此交握會發生之後，用戶端應用程式可以接收訊息 （或將訊息傳送至） 應用程式伺服器。

當用戶端應用程式不再想要從應用程式伺服器接收訊息時，它可以傳送要求到應用程式伺服器，以刪除註冊權杖。 如果用戶端應用程式正在接收主題訊息 （在本文稍後說明），它可以取消訂閱的主題。
從裝置解除安裝用戶端應用程式時，如果 GCM 會偵測到這，並自動通知應用程式伺服器，若要刪除的註冊權杖。

Google[註冊用戶端應用程式](https://developers.google.com/cloud-messaging/registration)說明註冊程序，在更多詳細資料，其中說明取消註冊和取消訂閱，並解除安裝用戶端應用程式時，它會描述的取消註冊程序。



### <a name="downstream-messaging"></a>下游訊息處理

當應用程式伺服器會將下游的訊息傳送至用戶端應用程式時，它會依照下圖中所說明的步驟：

[![下游訊息存放區 和 正向的圖表](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  應用程式伺服器會傳送訊息至 GCM。

2.  如果用戶端裝置無法使用，GCM 伺服器會儲存供稍後傳輸佇列中的訊息。

3.  當用戶端裝置可用時，GCM 就會傳送訊息給該裝置上的用戶端應用程式。

4.  用戶端應用程式從 GCM 接收訊息，並據以處理它。 比方說，如果訊息是遠端的通知，它會顯示給使用者。

在此傳訊案例中 （其中應用程式伺服器的訊息傳送至單一用戶端應用程式），訊息可以是長度為 4 kB。

如需詳細資訊 （包括程式碼範例） 接收在 Android 上的下游 GCM 訊息，請參閱 <<c0> [ 遠端通知](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md)。


#### <a name="topic-messaging"></a>主題傳訊

*主題傳訊*是一種下游傳訊應用程式伺服器到多個訂閱的主題 （例如氣象預報） 的用戶端應用程式裝置傳送單一訊息的位置。 主題的訊息可達 2 KB 的長度，和主題傳訊支援最多 100 萬個訂用帳戶每個應用程式。 如果只會針對訊息的主題使用 GCM，用戶端應用程式不是需要傳送至應用程式伺服器的註冊權杖。 Google[實作主題傳訊](https://developers.google.com/cloud-messaging/topic-messaging)說明如何從應用程式伺服器傳送訊息至訂閱特定主題的多個裝置。



#### <a name="group-messaging"></a>訊息群組

*群組 Messaging*是一種下游傳訊應用程式伺服器用來傳送多個用戶端裝置的應用程式隸屬於某個群組 （例如，隸屬於單一使用者的裝置群組），單一訊息。 群組訊息可達 2 KB 的 iOS 裝置的長度最多 4 KB 的長度，適用於 Android 裝置。 群組是限制為最多 20 個成員。 Google[裝置群組通訊](https://developers.google.com/cloud-messaging/notifications)說明如何應用程式伺服器時，可以隸屬於某個群組的裝置上執行的多個用戶端應用程式執行個體傳送單一訊息。


### <a name="upstream-messaging"></a>上游的傳訊

如果您的用戶端應用程式會連線到支援的伺服器[XMPP](https://developers.google.com/cloud-messaging/ccs)，它可以傳送訊息至應用程式伺服器，如下圖所示：

[![上游傳訊的圖表](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  用戶端應用程式會將訊息傳送至 GCM XMPP 連線伺服器。

2.  中斷連線的應用程式伺服器時，如果 GCM 伺服器會儲存在佇列中以供稍後轉送訊息。

3.  應用程式伺服器重新連線時，GCM 會轉送至應用程式伺服器的訊息。

4.  應用程式伺服器會剖析訊息確認用戶端應用程式的身分識別，則它會 「 通知 」 傳送至 GCM 認可訊息回條。

5.  應用程式伺服器處理的訊息。

Google[上游訊息](https://developers.google.com/cloud-messaging/ccs#upstream)說明如何建構 JSON 編碼的訊息，並將它們傳送到執行 Google 的 XMPP 型雲端連接伺服器的應用程式伺服器。


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Google Cloud Messaging 設定

您可以使用您的應用程式中的 GCM 服務之前，您必須先取得 Google 的 GCM 伺服器的存取權的認證。 下列章節說明完成此程序所需的步驟：



### <a name="enable-google-services-for-your-app"></a>啟用您的應用程式的 Google 服務

1.  登入[Google 開發人員主控台](https://developers.google.com/mobile/add?platform=android)與您的 Google 帳戶 （亦即，您的 gmail 地址），並建立新的專案。 如果您有現有的專案，選擇您想要成為啟用 GCM 的專案。 在下列範例中，呼叫新的專案**XamarinGCM**建立：

    [![建立 XamarinGCM 專案](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  接下來，輸入您的應用程式的套件名稱 (在此範例中，封裝名稱是**com.xamarin.gcmexample**)，按一下 [**繼續] 以選擇並設定服務**:

    [![輸入套件名稱](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    請注意，此封裝名稱也是您的應用程式的應用程式識別碼。

3.  **選擇和設定服務**區段會列出您可以新增到您的應用程式的 Google 服務。 按一下 **雲端傳訊**:

    [![選擇雲端傳訊](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  接下來，按一下**啟用 GOOGLE CLOUD MESSAGING**:

    [![啟用 Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  A**伺服器 API 金鑰**並**寄件者識別碼**專為您的應用程式。 記錄這些值，然後按一下**關閉**:

    [![伺服器 API 金鑰和顯示的寄件者識別碼](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    保護 API 金鑰&ndash;不適用於公開使用。 如果 API 金鑰遭到入侵，未經授權的伺服器無法將訊息發佈到用戶端應用程式。
    [安全地使用 API 金鑰最佳做法](https://support.google.com/cloud/answer/6310037?hl=en)提供有用的指導方針來保護您的 API 金鑰。



### <a name="view-your-project-settings"></a>檢視您的專案設定

您可以隨時檢視您的專案設定，登入[Google 雲端主控台](https://console.cloud.google.com/)，然後選取您的專案。 例如，您可以檢視**寄件者識別碼**下拉功能表頂端的頁面中選取您的專案 (在此範例中的專案稱為**XamarinGCM**)。 寄件者識別碼是此螢幕擷取畫面所示的專案編號 (寄件者識別碼**9349932736**):

[![檢視 寄件者識別碼](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

若要檢視**API 金鑰**，按一下**API 管理員**，然後按一下 **認證**:

[![檢視 API 金鑰](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>進一步閱讀

-   Google[註冊用戶端應用程式](https://developers.google.com/cloud-messaging/registration)描述用戶端註冊程序的詳細資料，並提供設定自動重試，並將註冊狀態保持同步的相關資訊。

-   [RFC 6120](https://tools.ietf.org/html/rfc6120)並[RFC 6121](https://tools.ietf.org/html/rfc6121)說明，並定義可延伸傳訊和目前狀態通訊協定 (protocol，XMPP)。



## <a name="summary"></a>總結

這篇文章提供概觀的 Google 雲端通訊 (GCM)。 它說明用來識別及授權應用程式伺服器和用戶端應用程式之間傳訊的各種認證。 它說明最常見的傳訊案例，以及它的詳細步驟與使用 GCM 服務的 GCM 註冊您的應用程式。


## <a name="related-links"></a>相關連結

- [雲端傳訊](https://developers.google.com/cloud-messaging/)
