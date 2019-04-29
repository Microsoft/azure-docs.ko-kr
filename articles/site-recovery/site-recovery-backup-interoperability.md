---
title: Azure Site Recovery를 사용 하 여 Azure backup에 대 한 지원 | Microsoft Docs
description: 어떻게 Azure Site Recovery 및 Azure Backup과 함께 사용할 수 있는 개요를 제공 합니다.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035778"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Site Recovery를 사용 하 여 Azure backup에 대 한 지원

이 문서에 사용 하기 위한 지원이 요약 되어 있습니다.는 [Site Recovery 서비스](site-recovery-overview.md) 함께 합니다 [Azure Backup 서비스](https://docs.microsoft.com/azure/backup/backup-overview)합니다.

**작업** | **Site Recovery 지원** | **세부 정보**
--- | --- | ---
**서비스를 함께 배포** | 지원됨 | 서비스 상호 운용 가능한 고 함께 구성할 수 있습니다.
**파일 백업/복원** | 지원됨 | 백업 및 복제는 VM에 대해 사용 하도록 설정 하 고 백업 하는 경우 문제가 발생 하지 않습니다 원본 측 Vm 또는 Vm 그룹의 파일을 복원 합니다. 복제는 복제 상태 변경 없이 정상적으로 계속 됩니다.
**디스크 백업/복원** | 현재 지원 되지 않습니다 | 백업 된 디스크를 복원 하는 경우 사용 하지 않도록 설정 하 고 다시 VM에 대 한 복제를 다시 사용 하도록 설정 해야 합니다.
**VM 백업/복원** | 현재 지원 되지 않습니다 | 를 백업 또는 VM 또는 Vm 그룹을 복원 하는 경우 사용 하지 않도록 설정 하 고 VM에 대 한 복제를 다시 사용 하도록 설정 해야 합니다.  


