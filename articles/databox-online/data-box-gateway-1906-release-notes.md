---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 릴리스 정보 | Microsoft Docs
description: Azure Data Box Gateway 및 1906 릴리스를 실행 하는 Azure Data Box Edge에 대한 중요 한 미해결 문제 및 해결 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099487"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge 및 Azure Data Box Gateway 1906 릴리스 정보

다음 릴리스 정보는 Azure Data Box Edge 및 Azure Data Box Gateway에 대한 1906 릴리스에 대한 중요 한 미해결 문제 및 해결 된 문제를 식별 합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. Data Box Edge/Data Box Gateway를 배포 하기 전에 릴리스 정보에 포함 된 정보를 주의 깊게 검토 하세요.

이 릴리스는 소프트웨어 버전에 해당 합니다.

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> 업데이트 1906는 GA (일반 공급) 또는 1905 버전의 소프트웨어를 실행 하는 Data Box Edge 장치에만 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

- **복구 키 관리 워크플로의 버그 수정** -이전 릴리스에서는 복구 키가 적용 되지 않은 버그 업데이트로 인해 있었습니다. 이 버그는 이 릴리스에서 수정되었습니다. 이 업데이트를 적용 하는 것이 좋습니다. 복구 키를 사용 하면 장치가 부팅 되지 않는 경우 장치에서 데이터를 복구할 수 있습니다. 자세한 내용은 [Data Box Edge 또는 Data Box Gateway를 배포할 때 복구 키를 저장](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)하는 방법을 참조 하세요.
- FPGA와 관련 된 **FPGA (프로그래밍할 수 있는 게이트 형 게이트 배열) 로깅 개선 사항** -1905 릴리스, 로깅 및 경고 향상이 이루어졌습니다. FPGA와 함께에 지 계산 기능을 사용 하는 경우이는 Data Box Edge에 대한 필수 업데이트가 됩니다. 자세한 내용은 [Data Box Edge에 지 compute를 사용 하 여 데이터를 변환](data-box-edge-deploy-configure-compute-advanced.md)하는 방법을 참조 하세요.

## <a name="known-issues-in-ga-release"></a>GA 릴리스의 알려진 문제

이 릴리스에는 발표 된 새로운 문제가 없습니다. 발표 된 모든 릴리스 문제는 이전 릴리스에서 수행 되었습니다. 알려진 문제 목록을 보려면 [GA 릴리스의 알려진 문제](data-box-gateway-release-notes.md#known-issues-in-ga-release)로 이동 합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)
- [Azure Data Box Edge 배포 준비](data-box-edge-deploy-prep.md)
