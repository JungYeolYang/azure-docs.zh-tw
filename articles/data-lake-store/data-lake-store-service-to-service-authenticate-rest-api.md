---
title: 服務對服務驗證：使用 Azure Active Directory 的 REST API 利用 Azure Data Lake 儲存體 Gen1 |Microsoft Docs
description: 了解如何使用 REST API 利用 Azure Active Directory 向 Azure Data Lake Storage Gen1 完成服務對服務驗證
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c48f7d7608b2b70f4ae41e2af5792cff72bb0dd2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60195773"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>使用 REST API 向 Azure Data Lake Storage Gen1 進行服務對服務驗證
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-service-to-service-authenticate-java.md)
> * [使用 .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-service-to-service-authenticate-python.md)
> * [使用 REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

在本文中，您會了解如何使用 REST API 向 Azure Data Lake Storage Gen1 進行服務對服務驗證。 如須使用 REST API 向 Data Lake Storage Gen1 進行使用者驗證，請參閱[使用 REST API 向 Data Lake Storage Gen1 進行終端使用者驗證](data-lake-store-end-user-authenticate-rest-api.md)。

## <a name="prerequisites"></a>必要條件
* **Azure 訂用帳戶**。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **建立 Azure Active Directory "Web" 應用程式**。 您必須已經完成[使用 Azure Active Directory 向 Data Lake Storage Gen1 進行服務對服務驗證](data-lake-store-service-to-service-authenticate-using-active-directory.md)中的步驟。

## <a name="service-to-service-authentication"></a>服務對服務驗證
在此情節中，應用程式會提供自己的認證來執行作業。 為此，您必須發出 POST 要求，如下列程式碼片段所示： 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

這項要求的輸出將包含授權權杖 (在以下的輸出中以 `access-token` 表示)，您接下來將會利用 REST API 呼叫將其傳遞。 在文字檔中儲存驗證權杖；當您向 Data Lake Storage Gen1 進行 REST 呼叫時會需要用到。

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

本文使用 **非互動式** 方法。 如需有關非互動式 (服務對服務呼叫) 的詳細資訊，請參閱 [使用認證進行服務對服務呼叫](https://msdn.microsoft.com/library/azure/dn645543.aspx)。 

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何搭配使用 REST API 與服務對服務驗證向 Data Lake Storage Gen1 進行驗證。 您現在可以查看下列文章，了解如何搭配使用 REST API 與 Data Lake Storage Gen1。

* [使用 REST API 對 Data Lake Storage Gen1 進行帳戶管理作業](data-lake-store-get-started-rest-api.md)
* [使用 REST API 在 Data Lake Storage Gen1 上進行資料作業](data-lake-store-data-operations-rest-api.md)

