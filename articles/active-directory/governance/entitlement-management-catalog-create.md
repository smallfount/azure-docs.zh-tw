---
title: 建立&管理授權管理資源目錄 - Azure AD
description: 瞭解如何在 Azure 活動目錄授權管理中創建新的資源和訪問包容器。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffa6f2fbdcde41d81e268779c3a0586bd8310792
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437563"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>在 Azure AD 授權管理中建立及管理資源目錄

## <a name="create-a-catalog"></a>建立目錄

目錄是資源和訪問包的容器。 如果要對相關資源和訪問包進行分組,即可創建目錄。 創建目錄的人將成為第一個目錄擁有者。 目錄擁有者可以添加其他目錄擁有者。

**先決條件角色:** 全域管理員、使用者管理員或目錄建立者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中,按下 **「目錄**」。

    ![Azure 門戶中的權限管理目錄](./media/entitlement-management-catalog-create/catalogs.png)

1. 按下 **「新目錄**」。

1. 輸入目錄的唯一名稱並提供說明。

    使用者將在訪問包的詳細資訊中看到此資訊。

1. 如果希望此目錄中的訪問套件可供使用者在創建後立即請求,請將 **「已啟用」** 設定為「**是**」。

1. 如果要允許所選外部目錄中的使用者能夠請求此目錄中的訪問包,請將**外部使用者的"已啟用"** 設置為 **"是**"。

    ![新的目錄窗格](./media/entitlement-management-shared/new-catalog.png)

1. 按下 **「建立**」 以建立目錄。

### <a name="creating-a-catalog-programmatically"></a>以程式設計方式建立目錄

您還可以使用 Microsoft 圖形創建目錄。  具有委派`EntitlementManagement.ReadWrite.All`權限的應用程式具有適當角色的使用者可以呼叫 API[以建立存取套件目錄](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta)。

## <a name="add-resources-to-a-catalog"></a>新增資源到目錄

要將資源包含在訪問包中,資源必須存在於目錄中。 可以添加的資源類型包括組、應用程式和 SharePoint 連線網站。 這些組可以是雲創建的 Office 365 組或雲端創建的 Azure AD 安全組。 這些應用程式可以是 Azure AD 企業應用程式,包括 SaaS 應用程式和與 Azure AD 聯合的應用程式。 這些網站可以是 SharePoint 線上網站或 SharePoint 線上網站集。

**先決條件角色:** 請參考[資源加入目錄的項目](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左方選單中,按下 **「目錄」 ,** 然後打開要向其添加資源的目錄。

1. 在左側功能表中,按下 **「資源**」。

1. 按下「**添加資源**」。

1. 點選資源類型:**群組與團隊**、**應用程式**或**SharePoint 網站**。

    如果看不到要添加的資源或無法添加資源,請確保具有所需的 Azure AD 目錄角色和授權管理角色。 您可能需要讓具有所需角色的人員將資源添加到目錄中。 有關詳細資訊,請參閱[將資源新增到目錄所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

1. 選擇要添加到目錄的類型中的一個或多個資源。

    ![新增資源到目錄](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 完成後,按一下**添加**。。

    這些資源現在可以包含在目錄中的訪問包中。

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>以程式設計方式將資源加入目錄

您還可以使用 Microsoft 圖形將資源添加到目錄。  具有委派`EntitlementManagement.ReadWrite.All`權限的應用程式具有相應角色的使用者或目錄和資源擁有者可以呼叫 API[來建立存取套件資源請求](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)。

## <a name="remove-resources-from-a-catalog"></a>從目錄中移除資源

可以從目錄中刪除資源。 僅當資源未在目錄的任何訪問包中使用時,才能從目錄中刪除。

**先決條件角色:** 請參考[資源加入目錄的項目](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左方選單中,按下 **「目錄」 ,** 然後開啟要從中刪除資源的目錄。

1. 在左側功能表中,按下 **「資源**」。

1. 選擇要刪除的資源。

1. 按下 **「刪除**」(或單擊省略號 (**...**),然後單擊「**刪除資源**」 。

## <a name="add-additional-catalog-owners"></a>新增其他目錄擁有者

創建目錄的使用者將成為第一個目錄擁有者。 要委派目錄的管理,可以將使用者添加到目錄擁有者角色。 這有助於共用目錄管理職責。 

依以下步驟將使用者分配給目錄擁有者角色:

**先決條件角色:** 全域管理員、使用者管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左方選單中,按下 **「目錄」 ,** 然後開啟要向其添加管理員的目錄。

1. 在左側功能表中,按下 **「角色和管理員**」。。

    ![編目角色和管理員](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 按下「**添加擁有者**」以選擇這些角色的成員。

1. 按下「**選擇」** 以新增這些成員。

## <a name="edit-a-catalog"></a>編輯目錄

您可以編輯目錄的名稱和說明。 使用者在訪問包的詳細資訊中看到此資訊。

**先決條件角色:** 全域管理員、使用者管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左方選單中,按下 **「目錄」 ,** 然後開啟要編輯的目錄。

1. 在目錄的 **「概述」** 頁上,按一下「**編輯**」。

1. 編輯目錄的名稱、說明或啟用的設置。

    ![編輯目錄設定](./media/entitlement-management-shared/catalog-edit.png)

1. 按一下 [儲存]****。

## <a name="delete-a-catalog"></a>刪除目錄

您可以刪除目錄,但前提是它沒有任何訪問包。

**先決條件角色:** 全域管理員、使用者管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左方選單中,按下 **「目錄」 ,** 然後開啟要刪除的目錄。

1. 在目錄的**概述**上,按一下「**刪除**」。。

1. 在出現的訊息方塊中，按一下 [確定]****。

### <a name="deleting-a-catalog-programmatically"></a>以程式設計方式移除目錄

您還可以使用 Microsoft 圖形刪除目錄。  具有委派`EntitlementManagement.ReadWrite.All`權限的應用程式具有適當角色的使用者可以呼叫 API[以刪除存取套件目錄](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta)。

## <a name="next-steps"></a>後續步驟

- [存取治理委派給存取套件管理員](entitlement-management-delegate-managers.md)
