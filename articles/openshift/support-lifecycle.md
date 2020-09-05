---
title: Azure Red Hat OpenShift 지원 수명 주기
description: Azure Red Hat OpenShift에 대 한 지원 수명 주기 및 지원 되는 버전 이해
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: c066931cece60d14767b86254020ea5ba4bad1be
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854509"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4의 지원 주기

Red Hat는 약 3 개월 마다 Red Hat OCP (OpenShift Container Platform)의 부 버전을 릴리스 합니다. 이러한 릴리스에는 새로운 기능과 향상된 기능이 포함됩니다. 패치 릴리스는 자주 발생 하며 (주로 매주) 부 버전 내에서 중요 한 버그 수정을 위한 것입니다. 이러한 패치 릴리스에는 보안 취약점 또는 주요 버그에 대 한 수정 사항이 포함 될 수 있습니다.

Azure Red Hat OpenShift는 특정 OCP 릴리스를 기반으로 빌드됩니다. 이 문서에서는 Azure Red Hat OpenShift에 대해 지원 되는 OCP 버전 및 업그레이드, 결함 및 지원 정책에 대 한 세부 정보를 다룹니다.

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift 버전

Red Hat OpenShift Container Platform은 의미 체계 버전 관리를 사용 합니다. 의미 체계 버전 관리에서는 서로 다른 버전 번호를 사용 하 여 다른 버전의 버전을 지정 합니다. 다음 표에서는 의미 체계 버전 번호의 여러 부분을 보여 줍니다 .이 경우에는 예제 버전 번호 4.4.11를 사용 합니다.

|주 버전 (x)|부 버전 (y)|패치 (z)|
|-|-|-|
|4|4|11|

버전의 각 번호는 이전 버전과의 호환성을 나타냅니다.

* **주 버전**: 지금은 주 버전 릴리스가 계획 되지 않습니다. 호환 되지 않는 API 변경 또는 이전 버전과의 호환성이 손상 될 때 주 버전은 변경 됩니다.
* **부 버전**: 약 3 개월 마다 출시 됩니다. 부 버전 업그레이드에는 추가 기능, 향상 된 기능, 결함, 제거, 버그 수정, 향상 된 보안 기능 및 기타 개선 사항이 포함 될 수 있습니다.
* **패치**: 일반적으로 매주 또는 필요한 경우에 출시 됩니다. 패치 버전 업그레이드에는 버그 수정, 향상 된 보안 기능 및 기타 향상 된 기능이 포함 될 수 있습니다.

고객은 실행 중인 주 버전의 최신 부 릴리스를 실행 해야 합니다. 예를 들어 프로덕션 클러스터가 4.4에 있고 4.5가 4 시리즈에 대해 일반적으로 사용 가능한 최신 부 버전인 경우, 가능한 한 빨리 4.5로 업그레이드 해야 합니다.

### <a name="upgrade-channels"></a>채널 업그레이드

업그레이드 채널은 사소한 버전의 Red Hat OpenShift Container Platform (OCP)에 연결 됩니다. 예를 들어, OCP 4.4 업그레이드 채널은 4.5 릴리스로 업그레이드 하는 것을 포함 하지 않습니다. 업그레이드 채널은 릴리스 선택만 제어 하 고 클러스터의 버전에는 영향을 주지 않습니다.

Azure Red Hat OpenShift 4는 안정적인 채널만 지원 합니다. 예: 안정적인-4.4.

안정적인-4.5 채널을 사용 하 여 Azure Red Hat OpenShift의 이전 부 버전에서 업그레이드할 수 있습니다. Fast, 시험판 및 후보 채널을 사용 하 여 업그레이드 된 클러스터는 지원 되지 않습니다.

현재 릴리스가 포함 되지 않은 채널로 변경 하는 경우 경고가 표시 되 고 업데이트를 권장 하지 않을 수 있지만 언제 든 지 원래 채널로 다시 변경할 수 있습니다.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Red Hat OpenShift Container Platform 버전 지원 정책

Azure Red Hat OpenShift는 Red Hat OpenShift Container Platform의 GA (일반 공급) 부 버전 두 개를 지원 합니다.
* Azure Red Hat OpenShift (N 이라고 지칭 됨)에서 릴리스된 최신 GA 부 버전
* 이전 부 버전 1 개 (N-1)

안정적인 업그레이드 채널에서 사용할 수 있는 경우 업스트림 OCP에서 사용할 수 있는 최신 부 릴리스 (N + 1, N + 2)도 지원 될 수 있습니다.

중요 패치 업데이트는 Azure Red Hat OpenShift Site 안정성 엔지니어가 자동으로 클러스터에 적용 됩니다. 패치 업데이트를 미리 설치 하려는 고객은 무료로 사용할 수 있습니다.

예를 들어 Azure Red Hat OpenShift가 현재 4.5. z를 도입 하는 경우 다음 버전에 대 한 지원이 제공 됩니다.

|새 부 버전|지원 되는 버전 목록|
|-|-|
|4.5. z|4.5. z, 4.4. z|

". z"는 패치 버전을 나타냅니다. 안정적인 업그레이드 채널에서 사용할 수 있는 경우 고객은 4.6. z로 업그레이드할 수도 있습니다.

새 부 버전이 도입 되 면 가장 오래 된 부 버전이 더 이상 사용 되지 않고 제거 됩니다. 예를 들어 현재 지원 되는 버전 목록은 4.5. z 및 4.4. z입니다. Azure Red Hat OpenShift를 사용할 때 4.6. z 릴리스가 제거 되 고 30 일 이내에 지원 되지 않습니다.

> [!NOTE]
> 고객이 지원 되지 않는 Red Hat OpenShift 버전을 실행 하는 경우 클러스터에 대 한 지원을 요청할 때 업그레이드 하 라는 메시지가 표시 될 수 있습니다. 지원 되지 않는 Red Hat OpenShift 릴리스를 실행 하는 클러스터는 Azure Red Hat OpenShift SLA에서 다루지 않습니다.

## <a name="release-and-deprecation-process"></a>릴리스 및 사용 중단 프로세스

Azure Red Hat OpenShift 릴리스 달력에서 예정 된 버전 릴리스 및 결함를 참조할 수 있습니다.

Red Hat OpenShift Container Platform의 새 부 버전:
* Azure Red Hat OpenShift SRE 팀은 제거 전에 30 일이 지난 후에 [Azure Red Hat Openshift 릴리스 정보](https://github.com/Azure/OpenShift/releases) 에서 새로운 버전 릴리스의 계획 된 날짜 및 해당 하는 이전 버전의 사전 알림을 게시 합니다.
* Azure Red Hat OpenShift SRE 팀은 Azure Red Hat OpenShift 및 포털 액세스 권한이 있는 모든 고객에 게 제공 되는 서비스 상태 알림을 게시 하 고 구독 관리자에 게 예정 된 버전 제거 날짜로 전자 메일을 보냅니다.
* 지원 서비스를 계속 받으려면 지원 되는 부 버전 릴리스로 업그레이드 하기 위해 30 일이 지나면 버전을 제거 해야 합니다.

Red Hat OpenShift Container Platform의 새로운 패치 버전:
* 패치 버전의 긴급 한 특성 때문에 Azure Red Hat OpenShift SRE 팀이 서비스를 사용할 수 있게 되 면이를 서비스에 도입할 수 있습니다.
* 일반적으로 Azure Red Hat OpenShift SRE 팀은 새로운 패치 버전의 설치에 대 한 광범위 한 통신을 수행 하지 않습니다. 그러나 팀은 적시에 지원 하기 위해 사용 가능한 CVE 패치를 지속적으로 모니터링 하 고 유효성을 검사 합니다. 고객 조치가 필요한 경우 팀은 업그레이드에 대 한 정보를 고객에 게 알립니다.

## <a name="supported-versions-policy-exceptions"></a>지원 되는 버전 정책 예외

Azure Red Hat OpenShift SRE 팀은 새로운/기존 버전을 추가 하거나 제거 하는 권리를 보유 하거나, 하나 이상의 중요 한 프로덕션에 영향을 주는 하나 이상의 중요 한 프로덕션 버전을 미리 확인 하지 않고 버그 또는 보안 문제에 영향을 줍니다.

특정 패치 릴리스를 건너뛰거나, 버그 또는 보안 문제의 심각도에 따라 롤아웃이 가속화 될 수 있습니다.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal 및 CLI 버전

포털 또는 Azure CLI를 사용 하 여 Azure Red Hat OpenShift 클러스터를 배포 하는 경우 클러스터는 기본적으로 최신 (N) 부 버전 및 최신 중요 패치로 설정 됩니다. 예를 들어 Azure Red Hat OpenShift가 4.5. z 및 4.4. z를 지 원하는 경우 새 설치의 기본 버전은 4.5. z입니다. 최신 업스트림 OCP 부 버전 (N + 1, N + 2)을 사용 하려는 고객은 언제 든 지 안정적인 업그레이드 채널에서 사용할 수 있는 릴리스로 클러스터를 업그레이드할 수 있습니다.

## <a name="azure-red-hat-openshift-release-calendar"></a>Azure Red Hat OpenShift 릴리스 일정

[이전 Red Hat OpenShift Container Platform (업스트림) 릴리스 내역](https://access.redhat.com/support/policy/updates/openshift/#dates)에 대 한 다음 가이드를 참조 하세요.

|OCP 버전|업스트림 릴리스|Azure Red Hat OpenShift 일반 가용성|수명 종료|
|-|-|-|-|
|4.3|2020년 2월|2020년 5월|2020년 8월|
|4.4.|2020년 5월|2020년 8월|4.6 GA|
|4.5|2020년 7월|10 월 2020|4.7 GA
|4.6|* 초기 Q4, 2020|* 늦은 Q4, 2020|4.8 GA|

\*_보류 중인 업스트림 릴리스 날짜 확인입니다._

## <a name="faq"></a>FAQ

**사용자가 지원 되지 않는 부 버전으로 OpenShift 클러스터를 업그레이드 하면 어떻게 되나요?**

N-2 버전이 나 이전 버전을 사용 하는 경우이는 지원 되지 않으며 업그레이드를 요청 하는 것을 의미 합니다. 버전 N-2에서 N-1로 업그레이드 하는 경우 지원 정책 내에 다시 로그인 됩니다. 예를 들어:
* 가장 오래 전에 지원 되는 Azure Red Hat OpenShift 버전이 4.4. z이 고 사용자가 4.3. z 또는 이전 버전인 경우 지원 외부에 있습니다.
* 4.3에서 4.4. z 이상으로 업그레이드 하는 경우 지원 정책 내에서 다시 시작 됩니다.

클러스터를 이전 버전 또는 롤백으로 되돌리는 것은 지원 되지 않습니다. 최신 버전으로의 업그레이드만 지원 됩니다.

**"지원 외부"는 무엇을 의미 하나요?**

"지원 외부"는 실행 중인 버전이 지원 되는 버전 목록 외부에 있음을 의미 하며, 버전 사용 중단 후 30 일의 유예 기간 내에 있지 않는 한 지원을 요청할 때 지원 되는 버전으로 클러스터를 업그레이드 하 라는 메시지가 표시 될 수 있습니다. 또한 Azure Red Hat OpenShift는 30 일 유예 기간이 끝날 때 지원 되는 버전 목록 외부의 클러스터에 대 한 런타임 또는 SLA를 보장 하지 않습니다.
