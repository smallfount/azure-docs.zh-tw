---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d6bad1ec584b28fee77bc654eba8a2d0c7b5df30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174042"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>若要在與 StorSimple 裝置管理員服務相同的 Azure 訂用帳戶中新增儲存體帳戶認證

1. 移至您的 StorSimple 裝置管理員服務。 在 [設定]**** 區段中，按一下 [儲存體帳戶認證]****。

    ![儲存體帳戶認證](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. 在 [儲存體帳戶認證]**** 刀鋒視窗上，按一下 [+ 新增]****。

    ![新增儲存體帳戶認證](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. 在 [新增儲存體帳戶認證]**** 刀鋒視窗中，執行下列步驟︰

    1. 當您在與服務相同的 Azure 訂用帳戶中新增儲存體帳戶認證時，請確認已選取 [目前]****。

    2. 從 [儲存體帳戶]**** 下拉式清單中，選取現有的儲存體帳戶。

    3. 根據選取的儲存體帳戶，會顯示 [位置]**** \(呈現灰色且無法在此變更)。

    4. 選擇**啟用 SSL 模式**可為設備和雲之間的網路通信創建安全通道。 只有當您在私人雲端內操作時，才將 [啟用 SSL]**** 停用。

        ![新增 [儲存體帳戶認證] 刀鋒視窗](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. 按一下 [新增]**** 可啟動儲存體帳戶認證的作業建立。 成功建立儲存體帳戶認證之後，您會收到通知。

        ![儲存體帳戶認證的成功通知](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

新建立的儲存體帳戶認證將會顯示在 [儲存體帳戶認證]**** 清單中。

![儲存體帳戶認證的清單](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

