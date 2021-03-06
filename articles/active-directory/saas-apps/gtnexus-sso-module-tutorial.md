---
title: 教學課程：Azure Active Directory 與 GTNexus SSO 系統整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 GTNexus SSO 系統之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e677a161-1662-4eb3-b48a-b2835470b59e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd5f32fd43d75ee91460a5d94ec4bcd57edb0769
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57876505"
---
# <a name="tutorial-azure-active-directory-integration-with-gtnexus-sso-system"></a>教學課程：Azure Active Directory 與 GTNexus SSO 系統整合

在本教學課程中，您會了解如何整合 GTNexus SSO 系統與 Azure Active Directory (Azure AD)。
GTNexus SSO 系統與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 GTNexus SSO 系統的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 GTNexus SSO 系統 (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 GTNexus SSO 系統與 Azure AD 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 GTNexus SSO 系統單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* GTNexus SSO System 支援由 **IDP** 起始的 SSO

## <a name="adding-gtnexus-sso-system-from-the-gallery"></a>從資源庫新增 GTNexus SSO 系統

若要設定將 GTNexus SSO 系統整合到 Azure AD 中，您需要從資源庫將 GTNexus SSO 系統新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 GTNexus SSO 系統，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]，然後選取 [所有應用程式] 選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **GTNexus SSO 系統**，從結果面板中選取 [GTNexus SSO 系統]，然後按一下 [新增] 按鈕以新增應用程式。

     ![結果清單中的 GTNexus SSO 系統](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 GTNexus SSO System 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 GTNexus SSO 系統中相關使用者之間的連結關聯性。

若要設定及測試與 GTNexus SSO System 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 GTNexus SSO 系統單一登入](#configure-gtnexus-sso-system-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 GTNexus SSO 系統試使用者](#create-gtnexus-sso-system-test-user)** - 讓 GTNexus SSO 系統中對應的 Britta Simon 連結到使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)**，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 GTNexus SSO System 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [GTNexus SSO 系統] 應用程式整合頁面上，選取 [單一登入]。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 對話方塊中，選取 [SAML/WS-Fed] 模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 如果您有**服務提供者中繼資料檔案**，請在 [基本 SAML 設定] 區段上執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]。

    ![映像](common/upload-metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]。

    ![映像](common/browse-upload-metadata.png)

    c. 當中繼資料檔案成功上傳後，會自動在 [GTNexus SSO 系統] 區段文字方塊中填入 [識別碼] 及 [回覆 URL] 值：

    ![映像](common/idp-intiated.png)

    > [!Note]
    > 如果 [識別碼] 和 [回覆 URL] 值未自動填入，請根據您的需求手動填入這些值。

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="configure-gtnexus-sso-system-single-sign-on"></a>設定 GTNexus SSO 系統單一登入

若要在 **GTNexus SSO 系統**端設定單一登入，您必須將已下載的 [同盟中繼資料 XML] 傳送給 [GTNexus SSO 系統支援小組](mailto:support@gtnexus.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱] 欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱] 欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增] 。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 GTNexus SSO 系統的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [GTNexus SSO 系統]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [GTNexus SSO 系統]。

    ![應用程式清單中的 [GTNexus SSO System] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者] 按鈕，然後在 [新增指派] 對話方塊中，選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 對話方塊的 [使用者] 清單中，選取 [Britta Simon]，然後按一下畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色] 對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取] 按鈕。

7. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

### <a name="create-gtnexus-sso-system-test-user"></a>建立 GTNexus SSO 系統測試使用者

在本節中，您會在 GTNexus SSO 系統中建立名為 Britta Simon 的使用者。 與 [GTNexus SSO 系統支援小組](mailto:support@gtnexus.com) 合作，在 GTNexus SSO 系統平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [GTNexus SSO 系統] 圖格時，應該會自動登入您已設定 SSO 的 GTNexus SSO 系統。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
