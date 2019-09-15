---
title: 設定步驟-使用 Apple 登入 Xamarin. 表單
description: 使用 Apple 安裝程式登入會根據您的行動應用程式目標的不同平臺而有所不同。
ms.prod: xamarin
ms.assetid: 8F712802-395B-469B-B5BE-C927AD1A8391
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: c46da6b4ed877cc85f98e6ef0ab2b9a28d811723
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2019
ms.locfileid: "70986006"
---
# <a name="setup-sign-in-with-apple-for-xamarinforms"></a>使用 Apple for Xamarin 的安裝程式登入

![此 API 目前為預覽狀態](~/media/shared/preview.png)

本指南涵蓋了一系列的步驟，讓您設定跨平臺應用程式以使用 Apple 進行登入。 Apple 安裝程式在 Apple 開發人員入口網站中是直接的，但必須執行其他步驟，才能在您的 Android 和 Apple 之間建立安全的關係。 

## <a name="apple-developer-setup"></a>Apple 開發人員設定

在您的應用程式中使用 Apple 登入之前，您必須先在 Apple 開發人員入口網站的 [[憑證]、[識別碼 & 設定檔](https://developer.apple.com/account/resources/)] 區段中解決一些設定步驟。

### <a name="apple-sign-in-domain"></a>Apple 登入網域

在 [*憑證、識別碼 & 設定檔*] 區段的 [[其他](https://developer.apple.com/account/resources/services/list)] 區段中，向 Apple 註冊您的功能變數名稱並加以驗證。

![其他區段](sign-in-images/readme-signin-domain-configure.png)

新增您的網域，然後按一下 [**註冊**]。

![註冊網域表單](sign-in-images/readme-signin-domain-more.png)

> [!NOTE]
> 如果您看到有關您的網域未與 SPF 相容的錯誤，您必須將 SPF DNS TXT 記錄新增至您的網域，並等候它傳播，再繼續進行：SPF TXT 看起來可能像這樣：`v=spf1 a a:myapp.com -all`

接下來，您必須按一下 [**下載**] 來取出`apple-developer-domain-association.txt`該檔案，然後將它上傳至您網域網站`.well-known`的資料夾，以驗證網域的擁有權。

檔案上`.well-known/apple-developer-domain-association.txt`傳並可供連線之後，您可以按一下 [**驗證**]，讓 Apple 驗證您的網域擁有權。

> [!NOTE]
> Apple 會使用`https://`來驗證擁有權。 請確定您已設定 SSL，而且可以透過安全的 URL 存取該檔案。

請先成功完成此程式，再繼續進行。

## <a name="setup-your-app-id"></a>設定您的應用程式識別碼

在 [[識別碼](https://developer.apple.com/account/resources/identifiers/list)] 區段中，建立新的識別碼，然後選擇 [**應用程式**識別碼]。 如果您已經有應用程式識別碼，請選擇改為編輯它。

![建立新的應用程式識別碼](sign-in-images/readme-appid-create.png)

啟用 [**使用 Apple 登入**]。 您很可能會想要使用 [**啟用為主要應用程式識別碼**] 選項。

![啟用使用 Apple 登入](sign-in-images/readme-appid-signin.png)

儲存您的應用程式識別碼變更。

## <a name="create-a-service-id"></a>建立服務識別碼

在 [[識別碼](https://developer.apple.com/account/resources/identifiers/list/serviceId)] 區段中建立新的識別碼，然後選擇 [**服務**識別碼]。

![建立新的服務識別碼](sign-in-images/readme-serviceid-create.png)

為您的服務識別碼提供描述和識別碼。  此識別碼將會是`ServerId`您的。  請務必啟用 [**使用 Apple 登入**]。

在繼續之前，請在啟用的 [_使用 Apple 登入_] 選項旁按一下 [**設定**]。

在 [設定] 面板中，確定已選取正確的 [**主要應用程式識別碼**]。

接下來，選擇您先前設定的**Web 網域**。

最後，新增一或多個傳回**url**。  您`redirect_uri`稍後使用的任何都必須完全依照您的使用方式註冊。  當您輸入時， `http://`請`https://`確定您在 URL 中包含或。

> [!NOTE]
> 基於測試目的，您不能`127.0.0.1`使用`localhost`或，但是您可以`local.test`使用其他網域（例如）。  如果您選擇這樣做，您可以編輯電腦的`hosts`檔案，將此虛構網域解析為本機 IP 位址。

![設定您的 Apple 登入](sign-in-images/readme-serviceid-configure.png)

完成後，請儲存您的變更。

## <a name="create-a-key-for-your-services-id"></a>建立服務識別碼的金鑰

在 [[金鑰](https://developer.apple.com/account/resources/authkeys/list)] 區段中，建立新的**金鑰**。

為您的金鑰命名，並啟用 [**使用 Apple 登入**]。

![建立新的金鑰](sign-in-images/readme-key-create.png)

按一下 [_使用 Apple 登入_] 旁的 [**設定**]。

請確定已選取正確的**主要應用程式識別碼**，然後按一下 [**儲存**]。

按一下 [**繼續**]，然後按 [**註冊**] 來建立新的金鑰。

接下來，您只需要下載您剛產生的金鑰一次。  按一下 [ **下載**]。

![下載金鑰](sign-in-images/readme-key-download.png)

此外，請記下您在此步驟中的**金鑰識別碼**。 這將用於您`KeyId`稍後的。

您將下載`.p8`金鑰檔案。  您可以在 [記事本] 中開啟此檔案，或 VSCode 以查看文字內容。  它們看起來應該像這樣：

```
-----BEGIN PRIVATE KEY-----
MIGTAgEAMBMGBasGSM49AgGFCCqGSM49AwEHBHkwdwIBAQQg3MX8n6VnQ2WzgEy0
Skoz9uOvatLMKTUIPyPCAejzzUCgCgYIKoZIzj0DAQehRANCAARZ0DoM6QPqpJxP
JKSlWz0AohFhYre10EXPkjrih4jTm+b0AeG2BGuoIWd18i8FimGDgK6IzHHPsEqj
DHF5Svq0
-----END PRIVATE KEY-----
```

將此金鑰`P8FileContents`命名為，並將它保存在安全的地方。 將此服務整合到您的行動應用程式時，您將會用到它。

## <a name="summary"></a>總結

本文說明使用 Apple 登入以用於 Xamarin. Forms 應用程式的必要步驟。

## <a name="related-links"></a>相關連結

- [使用 Apple 指導方針登入](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
  