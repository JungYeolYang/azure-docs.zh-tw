---
title: 使用 Azure 備份來取代您的磁帶基礎結構
description: 了解 Azure 備份如何提供可讓您在 Azure 中備份和還原資料的磁帶式語意
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2017
ms.author: dacurwin
ms.openlocfilehash: d768f0fae9487a555f6ace12303f8a4bd7cb8bd1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146030"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>將您的長期儲存空間從磁帶移至 Azure 雲端
Azure 备份和 System Center Data Protection Manager 客户可以：

* 依照最適合組織需求的排程備份資料。
* 長期保留備份資料
* 讓 Azure (而非磁帶) 成為長期保留需求的一部分。

此文章說明客戶如何啟用備份和保留原則。 使用磁帶處理長期保留需求的客戶，透過此功能的可用性現在擁有功能強大且可行的替代方案。 最新版的 Azure 備份已啟用此功能 (可從 [這裡](https://aka.ms/azurebackup_agent)取得)。 System Center DPM 客戶至少必須更新為 DPM 2012 R2 UR5，才能搭配使用 DPM 與 Azure 備份服務。

## <a name="what-is-the-backup-schedule"></a>什麼是備份排程？
備份排程會指出備份作業的頻率。 例如，下列畫面中的設定指出備份將於每日下午 6 點和午夜執行。

![每日排程](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

客戶也可以排程每週備份。 例如，下列畫面中的設定指出備份將於每隔兩週的星期日和星期三上午 9:30 和 1:00 執行。

![每週排程](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>保留原則為何？
保留原則會指定備份必須儲存的期間。 除了僅針對所有備份點指定「一般原則」之外，客戶可以指定在進行備份時根據不同的保留原則。 例如，每日取得的備份點 (可充當作業復原點) 可保留 90 天。 在每一季結尾所取得的備份點 (供稽核之用) 可能需要保留較長的時間。

![保留原則](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

此原則中指定的「保留點」總數是 90 (每日保留點) + 40 (每季一點，連續 10 年) = 130。

## <a name="example--putting-both-together"></a>範例 – 將兩者綜合比較
![範例畫面](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **每日保留策略**：每日创建的备份存储 7 天。
2. **每周保留策略**：每天午夜和星期六下午 6 点创建的备份保留 4 周
3. **每月保留策略**：每个月最后一个星期六午夜和下午 6 点创建的备份保留 12 个月
4. **每年保留策略**：每年三月最后一个星期六午夜执行的备份保留 10 年

上圖中計算「保留點」總數 (客戶可從該保留點還原資料) 的方式如下：

* 7 天中每天 2 個保留點 = 14 個復原點
* 4 週中每週 2 個保留點 = 8 個復原點
* 12 個月中每月 2 個保留點 = 24 個復原點
* 10 年中每年 1 個保留點 = 10 個復原點

因此復原點的總數是 56。

> [!NOTE]
> 您可以使用 Azure 備份來建立每個受保護的執行個體的最多 9999 個復原點。 受保護的執行個體係指會備份至 Azure 的電腦、伺服器 (實體或虛擬) 或工作負載。
>

## <a name="advanced-configuration"></a>進階組態
按一下上方畫面中的 [修改]  ，客戶在指定保留排程時擁有更進一步的彈性。

![修改](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>後續步驟
如需有關 Azure Backup 的詳細資訊，請參閱：

* [Azure 備份的簡介](backup-introduction-to-azure-backup.md)
* [試用 Azure 備份](backup-try-azure-backup-in-10-mins.md)
