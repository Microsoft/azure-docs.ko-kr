---
title: Azure Stack Edge & Azure Data Box Gateway 2007 릴리스 정보 | Microsoft Docs
description: Azure Stack Edge 및 2007 릴리스를 실행 하는 Data Box Gateway에 대 한 중요 한 미해결 문제 및 해결 방법을 설명 합니다.
services: databox
author: twooley
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 07/14/2020
ms.author: twooley
ms.openlocfilehash: f9a08cda1466dbe284a0ac3c0e34b109be236883
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783929"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Azure Stack Edge 및 Azure Data Box Gateway 2007 릴리스 정보

다음 릴리스 정보는 Azure Stack Edge 및 Data Box Gateway에 대 한 2007 릴리스에 대 한 중요 한 미해결 문제 및 해결 된 문제를 식별 합니다.

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. Azure Stack Edge/Data Box Gateway를 배포 하기 전에 릴리스 정보에 포함 된 정보를 주의 깊게 검토 하세요.

이 릴리스는 소프트웨어 버전에 해당 합니다.

- **Azure Stack Edge 2007 (1.6.1280.1667)** -KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** -KB 4566550

> [!NOTE]
> 업데이트 2007는 GA (일반 공급) 버전의 소프트웨어를 실행 하는 모든 장치에만 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

이 릴리스에 다음 버그 수정이 포함되어 있습니다.

- **업로드 문제** -이 릴리스에서는 오류로 인해 업로드가 다시 시작 되는 업로드 문제를 해결 합니다. 업로드 완료 속도가 느려질 수 있습니다. 이 문제는 주로 대역폭 제한이 활성 상태일 때 사용 가능한 대역폭을 기준으로 크기가 큰 파일 (특히 제한 되지 않음)으로 구성 된 데이터 집합을 업로드 하는 경우에 발생할 수 있습니다. 이렇게 변경 하면 지정 된 파일에 대 한 업로드를 다시 시작 하기 전에 업로드 완료를 위한 충분 한 기회가 제공 됩니다.

이 릴리스에는 다음과 같은 업데이트도 포함 되어 있습니다.

- Windows VHD의 기본 이미지가 업데이트 되었습니다.
- 5 월 2020 일까 지 릴리스된 모든 누적 Windows 업데이트 및 .NET framework 업데이트가 포함 되어 있습니다.
- 이 릴리스는 Azure Stack에 지 장치에 대 한 IoT Edge 1.0.9.3을 지원 합니다.

## <a name="known-issues-in-this-release"></a>이 릴리스의 알려진 문제

이 릴리스에는 발표 된 새로운 문제가 없습니다. 발표 된 모든 릴리스 문제는 이전 릴리스에서 수행 되었습니다. 알려진 문제 목록을 보려면 [GA 릴리스의 알려진 문제](data-box-gateway-release-notes.md#known-issues-in-ga-release)로 이동 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 배포 준비](data-box-edge-deploy-prep.md)
- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)
