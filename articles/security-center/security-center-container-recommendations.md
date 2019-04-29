---
title: Azure Security Center의 컨테이너 권장 사항 | Microsoft Docs
description: 이 문서에서는 컨테이너를 보호하는 방법에 대한 Azure Security Center 권장 사항을 설명합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 782c769bc7825dc9b6bd3ba3b8e36885bf150eaa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60705295"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Azure Security Center 컨테이너 권장 사항 이해

모놀리식 애플리케이션을 마이그레이션하여 프로덕션 환경의 업무상 중요한 컨테이너화된 클라우드 기본 애플리케이션을 실행할 경우 쉽고 빠른 배포 및 업데이트를 비롯한 컨테이너의 기능을 활용할 수 있습니다. 배포된 컨테이너의 수가 계속 증가하면서 컨테이너의 보안 상태를 파악하고 위협으로부터 보호하기 위해 보안 솔루션을 설정해야 합니다.

Azure Security Center는 컨테이너를 보호하기 위한 다음과 같은 기능을 제공합니다.

- **IaaS Linux 컴퓨터에 호스트된 컨테이너 파악**<br>Azure Security Center의 컨테이너 탭에는 Docker를 사용하여 배포한 모든 가상 머신이 표시됩니다. 가상 머신에서 보안 문제를 탐색할 때 Security Center는 Docker 버전, 호스트에서 실행 중인 이미지 수와 같은 컴퓨터의 컨테이너와 관련된 추가 정보를 제공합니다.

    ![컨테이너 탭](./media/security-center-container-recommendations/docker-recommendation.png)


- **Docker의 CIS 벤치마크에 따른 보안 권장 사항**<br>Security Center는 Docker 구성을 검색하고 평가된 모든 실패 규칙 목록을 제공하여 구성 오류를 표시합니다. Security Center는 이러한 문제를 신속하게 해결하고 시간을 절약하기 위한 지침을 제공합니다. Security Center는 Docker 구성을 지속적으로 평가하고 최신 상태를 제공합니다.

    ![컨테이너 탭](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **실시간 컨테이너 위협 검색**<br> Security Center는 AuditD 구성 요소를 사용하여 Linux 컴퓨터의 컨테이너에 대 한 실시간 위협 검색을 제공합니다. 경고는 호스트의 권한 있는 컨테이너 생성, Docker 컨테이너 내에서 실행 중인 SSH(Secure Shell) 서버의 표시 또는 암호화 마이너 사용과 같은 여러 의심스러운 Docker 활동을 식별합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

    ![컨테이너 탭](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>권장 사항
IaaS Linux 머신에 호스트된 사용 가능한 컨테이너와 해당 Docker 구성의 보안 평가를 이해하는 데 도움이 되는 아래 테이블을 참조하세요.

| 권장 사항 | 설명 | 재구성 |
| --- | --- | --- |
|컨테이너 보안 구성에서 취약성 수정 |구성 모범 사례를 기반으로 컨테이너 보안 구성에서 취약성을 해결합니다.| 컨테이너 보안 구성에서 취약성을 해결하려면<br>1. 실패한 규칙 목록을 검토합니다.<br>2. 지정된 지침에 따라 각 규칙을 수정합니다.|


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure Security Center에서 ID 및 액세스 모니터링](security-center-identity-access.md)
* [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
* [Azure Security Center에서 Azure SQL 서비스 보호](security-center-sql-service-recommendations.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-protection.md)
* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.

