---
title: Azure 업데이트 관리 업데이트 평가 보기
description: 이 문서에서는 업데이트 배포에 대한 업데이트 평가를 보는 방법을 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310087"
---
# <a name="view-azure-update-management-update-assessments"></a>Azure 업데이트 관리 업데이트 평가 보기

Azure Automation 계정에서 **업데이트 관리** 를 선택 하 여 컴퓨터의 상태를 확인 합니다.

이 보기에서는 컴퓨터, 누락 업데이트, 업데이트 배포 및 예약된 업데이트 배포에 대한 정보를 제공합니다. **준수** 열에서 컴퓨터가 마지막으로 평가 된 시간을 확인할 수 있습니다. **에이전트 준비 상태 업데이트** 열에서 업데이트 에이전트의 상태를 볼 수 있습니다. 문제가 있는 경우 문제를 해결 하는 데 도움이 될 수 있는 문제 해결 설명서로 이동 하려면 링크를 선택 합니다.

컴퓨터, 업데이트 또는 배포에 대한 정보를 반환 하는 로그 검색을 실행 하려면 목록에서 해당 항목을 선택 합니다. 이렇게 하면 선택한 항목에 대한 쿼리가 있는 **로그 검색** 창이 열립니다.

![업데이트 관리 기본 보기](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>누락 업데이트 보기

**누락 업데이트**를 선택하여 컴퓨터에서 누락된 업데이트 목록을 표시합니다. 각 업데이트가 나열되며 선택 가능합니다. 업데이트를 필요로 하는 컴퓨터 수, 운영 체제 세부 정보 및 자세한 정보에 대한 링크가 모두 표시 됩니다. **로그 검색** 창에도 업데이트에 대한 자세한 정보가 표시 됩니다.

![누락 업데이트](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>업데이트 분류

다음 표에는 업데이트 관리에서 지원 되는 업데이트 분류와 각 분류에 대한 정의가 나와 있습니다.

### <a name="windows"></a>Windows

|분류  |Description  |
|---------|---------|
|중요 업데이트     | 보안 관련 중요 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.        |
|업데이트 롤업     | 간편한 배포를 위해 함께 패키지된 핫픽스의 누적 집합입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 애플리케이션에 적용되는 핫픽스의 누적 집합입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일에 대한 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치되어 있는 애플리케이션 또는 파일에 대한 업데이트입니다.        |

### <a name="linux-2"></a>Linux

|분류  |Description  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 기본적으로 중요 하지 않거나 보안 업데이트가 아닌 다른 모든 업데이트.        |

Linux의 경우 평가 데이터를 표시 하는 동안 클라우드의 중요 업데이트와 보안 업데이트를 구분할 수 업데이트 관리. (이 세분성은 클라우드의 데이터 보강 때문에 가능 합니다.) 패치를 위해 업데이트 관리은 컴퓨터에서 사용할 수 있는 분류 데이터를 기반으로 합니다. 다른 배포와 달리 CentOS는 제품의 RTM 버전에서이 정보를 사용할 수 없습니다. 다음 명령에 대한 보안 데이터를 반환 하도록 구성 된 CentOS 컴퓨터가 있는 경우 분류에 따라 업데이트 관리 패치를 적용할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재 CentOS에서 네이티브 분류 데이터 가용성을 사용 하도록 설정 하는 방법이 지원 되지 않습니다. 지금은이 기능을 사용 하도록 설정한 고객에 게 최상의 지원만 제공 됩니다.

Red Hat Enterprise 버전 6에서 업데이트를 분류 하려면 yum-보안 플러그 인을 설치 해야 합니다. Red Hat Enterprise Linux 7에서 플러그 인은 이미 yum 자체의 일부 이므로 아무 것도 설치할 필요가 없습니다. 자세한 내용은 다음 Red Hat [기술](https://access.redhat.com/solutions/10021)항목을 참조 하세요.

## <a name="next-steps"></a>다음 단계

업데이트 평가를 확인 한 후에는 [Azure vm에 대한 업데이트 및 패치 관리](automation-tutorial-update-management.md)의 단계에 따라 업데이트 배포를 예약할 수 있습니다.
