---
title: Azure Security Center의 권장 사항 수정 | Microsoft Docs
description: 이 문서에서는 리소스를 보호 하 고 보안 정책을 준수 하는 Azure Security Center의 권장 사항을 수정 하는 방법을 설명 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 325c68e5e4531e5519596bea00c370c26460a8ed
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981904"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Security Center의 권장 사항 수정

권장 사항은 리소스의 보안을 강화 하는 방법에 대 한 제안을 제공 합니다. 권장 구성에서 제공 하는 수정 단계를 수행 하 여 권장 사항을 구현할 수 있습니다.

## 수정 단계<a name="remediation-steps"></a>

모든 권장 사항을 검토 한 후 먼저 수정할 항목을 결정 합니다. [보안 점수 효과](security-center-recommendations.md#monitor-recommendations) 를 사용 하 여 먼저 수행할 작업의 우선 순위를 지정 하는 것이 좋습니다.

1. 목록에서 권장 사항을 클릭 합니다.

1. **수정 단계** 섹션의 지침을 따릅니다. 각 권장 사항에는 고유한 지침 집합이 있습니다. 다음 스크린샷에서는 HTTPS를 통한 트래픽만 허용 하도록 응용 프로그램을 구성 하는 수정 단계를 보여 줍니다.

    ![권장 사항 세부 정보](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 작업이 완료 되 면 재구성에 성공 하면 알려 주는 알림이 표시 됩니다.

## 빠른 수정 재구성<a name="one-click"></a>

빠른 픽스를 사용 하면 여러 리소스에 대 한 권장 사항을 빠르게 수정할 수 있습니다. 특정 권장 사항에 대해서만 사용할 수 있습니다. 빠른 픽스를 사용 하면 수정을 간소화 하 고 보안 점수를 빠르게 늘리고 환경의 보안을 향상 시킬 수 있습니다.

빠른 수정 수정을 구현 하려면:

1. **빠른 수정** 이 있는 권장 사항 목록에서 레이블, 권장 사항을 클릭 합니다.

    [![빠른 수정을 선택 합니다.](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. **비정상 리소스** 탭에서 권장 구성을 구현 하려는 리소스를 선택 하 고 수정 **을 클릭 합니다**.

    > [!NOTE]
    > 나열 된 리소스 중 일부를 수정할 수 있는 적절 한 권한이 없기 때문에 일부 리소스를 사용 하지 못할 수 있습니다.

1. 확인 상자에서 수정 세부 정보 및 의미를 읽습니다.

    ![빠른 수정](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 수정을 클릭 한 후 열리는 **리소스 재구성** 창에서 회색 상자에 의미가 **나열 됩니다.** 빠른 수정 수정을 진행할 때 발생 하는 변경 내용을 나열 합니다.

1. 필요한 경우 관련 매개 변수를 삽입 하 고 수정를 승인 합니다.

    > [!NOTE]
    > 수정이 완료 되 면 **정상 리소스** 탭에서 리소스를 확인 하는 데 몇 분 정도 걸릴 수 있습니다. 수정 작업을 보려면 [활동 로그](#activity-log)를 확인 합니다.

1. 작업이 완료 되 면 재구성에 성공 하면 알려 주는 알림이 표시 됩니다.

## 활동 로그의 빠른 수정 재구성 로깅<a name="activity-log"></a>

수정 작업은 템플릿 배포 또는 REST 패치 API 호출을 사용 하 여 리소스에 대 한 구성을 적용 합니다. 이러한 작업은 [Azure 활동 로그](../azure-resource-manager/management/view-activity-logs.md)에 기록 됩니다.


## <a name="recommendations-with-quick-fix-remediation"></a>빠른 수정 수정을 사용한 권장 사항

|권장|의미상 이러한|
|---|---|
|SQL server에 대 한 감사를 사용 하도록 설정 해야 합니다.|이 작업을 수행 하면 이러한 서버 및 해당 데이터베이스에서 SQL 감사를 사용할 수 있습니다. <br>**참고**: <ul><li>선택한 SQL server의 각 지역에 대해 감사 로그를 저장 하는 저장소 계정이 만들어지고 해당 지역의 모든 서버에서 공유 됩니다.</li><li>적절 한 감사를 보장 하려면 리소스 그룹 또는 저장소 계정을 삭제 하거나 이름을 변경 하지 마십시오.</li></ul>|
|SQL 관리형 인스턴스에서 Advanced Data Security를 사용하도록 설정해야 합니다.|이 작업을 수행 하면 선택한 SQL 관리 되는 인스턴스에서 SQL AD (Advanced Data Security)를 사용할 수 있습니다. <br>**참고**: <ul><li>선택한 SQL 관리 되는 인스턴스의 각 지역 및 리소스 그룹에 대해 검사 결과를 저장 하는 저장소 계정이 만들어지고 해당 지역의 모든 인스턴스에서 공유 됩니다.</li><li> 광고는 SQL 관리 되는 인스턴스당 $15로 청구 됩니다.</li></ul>|
|SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.|이 작업을 수행 하면 선택한 SQL 관리 되는 인스턴스에 대해 SQL 취약성 평가를 사용할 수 있습니다. <br>**참고**:<ul><li>SQL 취약성 평가는 SQL AD (Advanced Data Security) 패키지의 일부입니다. 광고가 아직 설정 되지 않은 경우 관리 되는 인스턴스에서 자동으로 사용 하도록 설정 됩니다.</li><li>선택한 SQL 관리 되는 인스턴스의 각 지역 및 리소스 그룹에 대해 검사 결과를 저장 하는 저장소 계정이 만들어지고 해당 지역의 모든 인스턴스에서 공유 됩니다.</li><li>광고는 SQL server 당 $15로 청구 됩니다.</li></ul>||
|SQL 서버에서 고급 데이터 보안을 사용 하도록 설정 해야 합니다.|이 작업을 수행 하면 선택한 서버 및 해당 데이터베이스에서 AD (Advanced Data Security)를 사용할 수 있습니다. <br>**참고**:<ul><li>선택한 SQL server의 각 지역 및 리소스 그룹에 대해 검사 결과를 저장 하는 저장소 계정이 만들어지고 해당 지역의 모든 서버에서 공유 됩니다. <</li><li>광고는 SQL server 당 $15로 청구 됩니다.</li></ul>||
|SQL server에서 취약성 평가를 사용 하도록 설정 해야 합니다.|이 작업을 수행 하면 선택한 서버와 해당 데이터베이스에 대해 SQL 취약성 평가를 사용할 수 있습니다. <br>**참고**:<ul><li>SQL 취약성 평가는 SQL AD (Advanced Data Security) 패키지의 일부입니다. 광고가 아직 설정 되지 않은 경우 SQL server에서 자동으로 사용 하도록 설정 됩니다.</li><li>선택한 SQL server의 각 지역 및 리소스 그룹에 대해 검사 결과를 저장 하는 저장소 계정이 만들어지고 해당 지역의 모든 인스턴스에서 공유 됩니다.</li><li>광고는 SQL server 당 $15로 청구 됩니다.</li></ul>||
|SQL 데이터베이스에서 투명 한 데이터 암호화를 사용 하도록 설정 해야 합니다.|이 작업을 수행 하면 선택한 데이터베이스에서 TDE (SQL Database 투명한 데이터 암호화)를 사용할 수 있습니다. <br>**참고**: 기본적으로 서비스 관리 tde 키가 사용 됩니다.
|Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.|이 작업은 보안 연결을 통해서만 요청을 허용 하도록 저장소 계정 보안을 업데이트 합니다. (HTTPS). <br>**참고**:<ul><li>HTTP를 사용 하는 모든 요청은 거부 됩니다.</li><li>Azure files 서비스를 사용 하는 경우 암호화 되지 않은 연결이 실패 합니다. 여기에는 SMB 2.1, 암호화 되지 않은 SMB 3.0 및 Linux SMB 클라이언트의 일부 버전을 사용 하는 시나리오가 포함 됩니다. 자세히 알아보세요.</li></ul>|
|웹 애플리케이션에 HTTPS를 통해서만 액세스 가능|이 작업을 수행 하면 선택한 리소스의 모든 트래픽이 HTTP에서 HTTPS로 리디렉션됩니다. <br>**참고**:<ul><li>SSL 인증서가 없는 HTTPS 엔드포인트은 브라우저에서 ' 개인 정보 오류 '로 표시 됩니다. 따라서 사용자 지정 도메인이 있는 사용자는 SSL 인증서를 설정 했는지 확인 해야 합니다.</li><li>App service를 보호 하는 패킷 및 웹 응용 프로그램 방화벽이 HTTPS 세션 전달을 허용 하는지 확인 합니다.</li></ul>|
|함수 앱에 HTTPS를 통해서만 액세스 가능|이 작업을 수행 하면 선택한 리소스의 모든 트래픽이 HTTP에서 HTTPS로 리디렉션됩니다. <br>**참고**:<ul><li>SSL 인증서가 없는 HTTPS 엔드포인트은 브라우저에서 ' 개인 정보 오류 '로 표시 됩니다. 따라서 사용자 지정 도메인이 있는 사용자는 SSL 인증서를 설정 했는지 확인 해야 합니다.</li><li>App service를 보호 하는 패킷 및 웹 응용 프로그램 방화벽이 HTTPS 세션 전달을 허용 하는지 확인 합니다.</li></ul>|
|API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.|이 작업을 수행 하면 선택한 리소스의 모든 트래픽이 HTTP에서 HTTPS로 리디렉션됩니다. <br>**참고**:<ul><li>SSL 인증서가 없는 HTTPS 엔드포인트은 브라우저에서 ' 개인 정보 오류 '로 표시 됩니다. 따라서 사용자 지정 도메인이 있는 사용자는 SSL 인증서를 설정 했는지 확인 해야 합니다.</li><li>App service를 보호 하는 패킷 및 웹 응용 프로그램 방화벽이 HTTPS 세션 전달을 허용 하는지 확인 합니다.</li></ul>|
|웹 애플리케이션에 대해 원격 디버깅을 해제해야 함|이 작업을 수행 하면 원격 디버깅이 사용 되지 않습니다.|
|함수 앱에 대해 원격 디버깅을 해제해야 함|이 작업을 수행 하면 원격 디버깅이 사용 되지 않습니다.|
|API 앱에 대해 원격 디버깅을 해제해야 함|이 작업을 수행 하면 원격 디버깅이 사용 되지 않습니다.|
|CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 함|이 작업은 다른 도메인에서 웹 응용 프로그램에 액세스 하지 못하도록 차단 합니다. 특정 도메인을 허용 하려면 허용 된 원본 필드에 쉼표로 구분 하 여 입력 합니다. <br>**참고**: 필드를 비워 두면 모든 크로스-원본 호출이 차단 됩니다. ' Param 필드 제목: ' 허용 된 원본 '|
|CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용하지 않아야 함|이 작업은 다른 도메인이 함수 응용 프로그램에 액세스 하는 것을 차단 합니다. 특정 도메인을 허용 하려면 허용 된 원본 필드에 쉼표로 구분 하 여 입력 합니다. <br>**참고**: 필드를 비워 두면 모든 크로스-원본 호출이 차단 됩니다. ' Param 필드 제목: ' 허용 된 원본 '|
|CORS에서 모든 리소스가 API 앱에 액세스 하도록 허용 해서는 안 됩니다.|이 작업은 다른 도메인이 API 응용 프로그램에 액세스 하지 못하도록 차단 합니다. 특정 도메인을 허용 하려면 허용 된 원본 필드에 쉼표로 구분 하 여 입력 합니다. <br>**참고**: 필드를 비워 두면 모든 크로스-원본 호출이 차단 됩니다. ' Param 필드 제목: ' 허용 된 원본 '|
|가상 머신에서 모니터링 에이전트를 사용 하도록 설정 해야 합니다.|이 작업은 선택한 가상 컴퓨터에 모니터링 에이전트를 설치 합니다. 에이전트에서 보고할 작업 영역을 선택 합니다.<ul><li>업데이트 정책이 자동으로 설정 된 경우 새 기존 인스턴스에 배포 됩니다.</li><li>업데이트 정책을 수동으로 설정 하 고 기존 인스턴스에 에이전트를 설치 하려는 경우 확인란 옵션을 선택 합니다. [자세히 알아보기](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Key Vault의 진단 로그를 사용하도록 설정해야 합니다.|이 작업을 수행 하면 키 자격 증명 모음에 진단 로그가 사용 됩니다. 진단 로그 및 메트릭은 선택한 작업 영역에 저장 됩니다.|
|Service bus의 진단 로그를 사용 하도록 설정 해야 합니다.|이 작업은 service bus에서 진단 로그를 사용 하도록 설정 합니다. 진단 로그 및 메트릭은 선택한 작업 영역에 저장 됩니다.|

## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center의 권장 사항을 수정 하는 방법을 보여 줍니다. Security Center에 대해 자세히 알아보려면 다음 항목을 참조 하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
