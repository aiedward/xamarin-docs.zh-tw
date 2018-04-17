---
title: Google 雲端訊息
description: Google Cloud Messaging (GCM) 是一種服務，可促進行動裝置應用程式與伺服器應用程式之間的傳訊。 本文提供的 GCM 的運作方式概觀，並說明如何設定 Google 服務，因此您的應用程式可以使用 GCM。
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: 29cccf414759a79a8ba74dfc35b7ba9f6a1cc5d6
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="google-cloud-messaging"></a>Google 雲端訊息

_Google Cloud Messaging (GCM) 是一種服務，可促進行動裝置應用程式與伺服器應用程式之間的傳訊。本文提供的 GCM 的運作方式概觀，並說明如何設定 Google 服務，因此您的應用程式可以使用 GCM。_

[![Google Cloud Messaging 標誌](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

本主題提供如何 Google Cloud Messaging 將訊息路由傳送應用程式伺服器，您的應用程式之間的高階概觀，並取得認證，好讓您的應用程式可以使用 GCM 服務提供的逐步程序。

> [!NOTE]
> GCM 已被取代的[Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM)。
> GCM 伺服器和用戶端應用程式開發介面[已被取代](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html)，將無法再使用盡 2019 年 4 月 11。

## <a name="overview"></a>總覽

Google Cloud Messaging (GCM) 是處理傳送、 路由和伺服器應用程式和行動用戶端應用程式之間的訊息佇列服務。 A*用戶端應用程式*裝置上執行的 GCM 啟用應用程式。 *應用程式伺服器*（由您或您的公司所提供） 是用戶端應用程式與通訊透過 GCM GCM 啟用伺服器：

[![GCM 位於用戶端應用程式與應用程式伺服器之間](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

使用 GCM，應用程式伺服器就可以將訊息傳送至單一裝置、 裝置、 群組或主題訂閱的裝置數目。 您的用戶端應用程式可以使用 GCM 訂閱 （例如，若要接收遠端通知） 的應用程式伺服器的下游訊息。 此外，GCM 使得上游訊息傳送至應用程式伺服器的用戶端應用程式。

適用於 GCM 實作應用程式伺服器的詳細資訊，請參閱[GCM 連線伺服器的相關](https://developers.google.com/cloud-messaging/server)。



## <a name="google-cloud-messaging-in-action"></a>Google 雲端訊息的動作

當下游訊息從應用程式伺服器傳送至用戶端應用程式時，應用程式伺服器會傳送訊息以*GCM 連線伺服器*; GCM 連接伺服器，接著，轉寄訊息到正在執行您的用戶端應用程式的裝置。 可以透過 HTTP 傳送訊息或[XMPP](https://developers.google.com/cloud-messaging/ccs) （可延伸的傳訊和目前狀態的通訊協定）。 因為用戶端應用程式不會永遠連接或執行，GCM 連線伺服器儲和儲存訊息，將它們傳送到用戶端應用程式，因為它們重新連線，而且可用。 同樣地，GCM 將會在佇列中上游訊息從用戶端應用程式，應用程式伺服器無法使用應用程式伺服器時。

GCM 使用下列認證來識別應用程式伺服器和用戶端應用程式，並使用這些認證來授權訊息交易 GCM 透過：

-   **API 金鑰** &ndash; *API 金鑰*Google 服務; 可讓您的應用程式伺服器存取GCM 使用此金鑰來驗證您的應用程式伺服器。
    您可以使用 GCM 服務之前，您必須先取得 API 金鑰從[Google 開發人員主控台](https://console.developers.google.com/)藉由建立*專案*。 API 金鑰應該保持安全狀態;如需有關如何保護您的 API 金鑰的詳細資訊，請參閱[安全地使用 API 金鑰的最佳做法](https://support.google.com/cloud/answer/6310037?hl=en)。

-   **寄件者識別碼** &ndash; *寄件者識別碼*授權用戶端應用程式的應用程式伺服器&ndash;它是唯一的編號，可識別允許將訊息傳送至用戶端應用程式的應用程式伺服器。
    寄件者識別碼也是您專案的號碼;當您註冊您的專案時，您可以取得從 Google 開發人員主控台的寄件者識別碼。

-   **註冊語彙基元** &ndash; *註冊語彙基元*是給定的裝置上的用戶端應用程式的 GCM 身分識別。 在執行階段產生註冊憑證&ndash;先向註冊時會 GCM 時在裝置上執行，您的應用程式會收到註冊權杖。 註冊權杖授與 （該特定裝置上執行） 的用戶端應用程式的執行個體，以接收來自 GCM 訊息。

-   **應用程式識別碼**&ndash;用戶端應用程式 （獨立於任何指定的裝置） 註冊以接收來自 GCM 訊息的身分識別。 在 Android 上，應用程式識別碼會記錄中的封裝名稱**AndroidManifest.xml**，例如`com.xamarin.gcmexample`。

[設定註冊 Google Cloud Messaging](#settingup) （稍後在本指南中） 提供建立專案，並產生這些認證的詳細的指示。

下列各節說明透過 GCM 的應用程式伺服器與通訊的用戶端應用程式時，如何使用這些認證。



### <a name="registration-with-gcm"></a>GCM 註冊

在裝置上安裝用戶端應用程式必須先向 GCM 之前訊息發生的情況。 用戶端應用程式必須完成註冊步驟，如下圖所示：

[![應用程式註冊步驟](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  用戶端應用程式會連絡 GCM 來取得寄件者識別碼傳遞至 GCM 註冊語彙基元。

2.  GCM 傳回用戶端應用程式註冊語彙基元。

3.  用戶端應用程式會轉送至應用程式伺服器的註冊權杖。

應用程式伺服器會快取後續的通訊，與用戶端應用程式註冊語彙基元。 （選擇性） 應用程式伺服器可以傳送回用戶端應用程式，以指示註冊語彙基元已收到的通知。 此信號交換發生後，用戶端應用程式可以接收訊息 （或傳送訊息至） 應用程式伺服器。

當用戶端應用程式不再想要從應用程式伺服器接收訊息時，它可以傳送要求到應用程式伺服器，以刪除註冊語彙基元。 如果用戶端應用程式接收 （本文稍後說明） 主題訊息，它可以取消訂閱的主題。
從裝置解除安裝用戶端應用程式時，如果 GCM 偵測此項，並自動通知 要刪除的註冊憑證之應用程式伺服器。

Google[註冊用戶端應用程式](https://developers.google.com/cloud-messaging/registration)說明註冊程序，在更多詳細資料; 它會說明取消註冊及取消訂閱，並說明取消註冊的程序解除安裝用戶端應用程式時。



### <a name="downstream-messaging"></a>下游訊息處理

當應用程式伺服器會將下游訊息傳送至用戶端應用程式時，它會遵循下列圖表中所述的步驟：

[![下游訊息存放區 和 正向的圖表](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  應用程式伺服器至 GCM 傳送訊息。

2.  如果用戶端裝置無法使用，GCM 伺服器會儲存訊息以便稍後傳輸佇列中。

3.  當用戶端裝置可用時，GCM 會傳送訊息至該裝置上的用戶端應用程式。

4.  用戶端應用程式接收來自 GCM 的訊息，並據以處理它。 例如，如果訊息是遠端的通知，就會向使用者。

在此傳訊案例中 （其中的應用程式伺服器會傳送訊息至單一用戶端應用程式），訊息可以是長度最多為 4 kB。

如需詳細資訊 （包括程式碼範例） 接收下游 GCM 訊息在 Android 上的，請參閱[遠端通知](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md)。


#### <a name="topic-messaging"></a>主題的訊息

*主題傳訊*是一種下游傳訊應用程式伺服器到多個訂閱的主題 （例如，天氣預報） 的用戶端應用程式裝置傳送單一訊息的位置。 主題訊息可保留長度，最多為 2 KB 並主題傳訊支援最多 100 萬個訂用帳戶每個應用程式。 如果 GCM 只會用於訊息的主題，用戶端應用程式不需要註冊權杖傳送到應用程式伺服器。 Google[實作主題傳訊](https://developers.google.com/cloud-messaging/topic-messaging)說明如何從應用程式伺服器傳送訊息至訂閱特定主題的多個裝置。



#### <a name="group-messaging"></a>訊息群組

*群組傳訊*是一種下游訊息讓應用程式伺服器會傳送單一訊息到屬於群組 （例如，屬於單一使用者的裝置群組） 的多個用戶端應用程式裝置。 群組訊息可在 iOS 裝置的長度上限為 2 KB，最多 4 KB 的長度，適用於 Android 裝置。 群組是限制為最多 20 個成員。 Google[裝置群組傳訊](https://developers.google.com/cloud-messaging/notifications)說明如何應用程式伺服器時，可以隸屬於某個群組的裝置上執行的多個用戶端應用程式執行個體傳送單一訊息。


### <a name="upstream-messaging"></a>上游傳訊

如果您的用戶端應用程式會連接到伺服器來支援[XMPP](https://developers.google.com/cloud-messaging/ccs)，它可以傳送訊息至應用程式伺服器，如下列圖表所示：

[![上游傳訊的圖表](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  用戶端應用程式傳送訊息至 GCM XMPP 連接伺服器。

2.  應用程式伺服器中斷連線時，如果 GCM 伺服器會儲存訊息在佇列中進行稍後轉送。

3.  應用程式伺服器重新連接時，GCM 轉寄訊息到應用程式伺服器。

4.  應用程式伺服器會剖析訊息驗證的用戶端應用程式的身分識別，然後傳送至 GCM 確認訊息接收 「 通知 」。

5.  應用程式伺服器處理的訊息。

Google[上游訊息](https://developers.google.com/cloud-messaging/ccs#upstream)說明如何建構 JSON 編碼的訊息，並將它們傳送至執行 Google XMPP 型雲端連線伺服器的應用程式伺服器。


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Google Cloud Messaging 設定

您可以在應用程式中使用 GCM 服務之前，您必須先取得 Google 的 GCM 伺服器的存取權的認證。 下列章節說明完成此程序所需的步驟：



### <a name="enable-google-services-for-your-app"></a>啟用您的應用程式的 Google 服務

1.  登入[Google 開發人員主控台](https://developers.google.com/mobile/add?platform=android)與您的 Google 帳戶 （亦即，您 gmail 的地址），並建立新專案。 如果您有現有的專案，選擇您想要成為 GCM 啟用的專案。 在下列範例中，新的專案呼叫**XamarinGCM**建立：

    [![建立 XamarinGCM 專案](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  接下來，輸入您的應用程式的封裝名稱 (在此範例中，封裝名稱是**com.xamarin.gcmexample**) 按一下**繼續選擇瀏覽和設定服務**:

    [![輸入封裝名稱](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    請注意，此封裝的名稱也是您的應用程式的應用程式識別碼。

3.  **選擇和設定服務**區段會列出您可以將它加入至您的應用程式的 Google 服務。 按一下**雲端傳訊**:

    [![選擇雲端訊息](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  接下來，按一下**啟用 GOOGLE CLOUD MESSAGING**:

    [![啟用 Google 雲端訊息](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  A**伺服器 API 金鑰**和**寄件者識別碼**為您的應用程式所產生。 記錄這些值，然後按一下**關閉**:

    [![伺服器 API 金鑰與顯示的寄件者識別碼](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    保護 API 金鑰&ndash;不適合公用用途。 如果遭到入侵的 API 金鑰，未經授權的伺服器無法發佈訊息，用戶端應用程式。
    [安全地使用 API 金鑰的最佳做法](https://support.google.com/cloud/answer/6310037?hl=en)提供實用的方針來保護您的 API 金鑰。



### <a name="view-your-project-settings"></a>檢視您的專案設定

您可以隨時檢視您的專案設定，登入[Google 雲端主控台](https://console.cloud.google.com/)，然後選取您的專案。 例如，您可以檢視**寄件者識別碼**在頁面頂端的功能表提取中選取您的專案 (在此範例中，稱為專案**XamarinGCM**)。 寄件者識別碼是專案中這個螢幕擷取畫面所示 (這裡的寄件者識別碼是**9349932736**):

[![檢視 寄件者識別碼](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

若要檢視**API 金鑰**，按一下  **API Manager** ，然後按一下 **認證**:

[![檢視 API 金鑰](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>進一步閱讀

-   Google[註冊用戶端應用程式](https://developers.google.com/cloud-messaging/registration)描述用戶端註冊程序的詳細資料，並提供有關設定自動重試和保持註冊狀態的同步資訊。

-   [RFC 6120](https://tools.ietf.org/html/rfc6120)和[RFC 6121](https://tools.ietf.org/html/rfc6121)說明，並定義可延伸的傳訊和目前狀態通訊協定 (XMPP)。



## <a name="summary"></a>總結

本文提供的 Google Cloud Messaging (GCM) 的概觀。 它說明可用來識別及授權應用程式伺服器和用戶端應用程式之間的傳訊的各種認證。 它說明最常見的傳訊案例，它的詳細步驟来使用 GCM 服務 GCM 註冊您的應用程式。


## <a name="related-links"></a>相關連結

- [雲端訊息](https://developers.google.com/cloud-messaging/)
