---
title: Azure Security Center의 Vm 및 서버에 대 한 위협 감지 | Microsoft Docs
description: 이 항목에서는 Azure Security Center에서 사용할 수 있는 VM 및 서버 경고를 제공 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a5ed91cef6e49fcb71c35f2262479be45a018651
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754315"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Azure Security Center의 Vm 및 서버에 대 한 위협 감지

이 항목에서는 다음 운영 체제를 사용 하는 Vm 및 서버에서 사용할 수 있는 다양 한 유형의 검색 방법 및 경고를 제공 합니다. 지원 되는 버전 목록은 [Azure Security Center에서 지 원하는 플랫폼 및 기능](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)을 참조 하세요.

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Azure Security Center는 Azure 서비스와 통합 되어 Windows 기반 컴퓨터를 모니터링 하 고 보호 합니다. Security Center은 이러한 모든 서비스의 경고 및 수정 제안을 사용 하기 쉬운 형식으로 제공 합니다.

* **Microsoft defender atp** <a name="windows-atp"></a> -Security Center microsoft defender atp (Advanced Threat protection)와 통합 하 여 클라우드 워크 로드 보호 플랫폼을 확장 합니다. 이는 포괄적인 엔드포인트 검색 및 응답 (EDR) 기능을 제공 합니다.

    > [!NOTE]
    > Microsoft Defender ATP 센서는 Security Center를 사용 하는 Windows server에서 자동으로 사용 하도록 설정 됩니다.

    Microsoft Defender ATP가 위협을 감지 하면 경고를 트리거합니다. 경고는 Security Center 대시보드에 표시 됩니다. 대시보드에서 Microsoft Defender ATP 콘솔로 피벗을 수행 하 고 자세한 조사를 수행 하 여 공격의 범위를 확인할 수 있습니다. Microsoft Defender ATP에 대 한 자세한 내용은 [Microsoft DEFENDER atp 서비스에 서버](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)등록을 참조 하세요.

* **크래시 덤프 분석** <a name="windows-dump"></a> -소프트웨어가 충돌 하는 경우 크래시 덤프는 충돌 시 메모리의 일부를 캡처합니다.

    충돌로 인해 맬웨어가 발생 했거나 맬웨어가 있을 수 있습니다. 보안 제품에서 검색 되지 않도록 하기 위해 다양 한 형태의 맬웨어가 fileless 공격을 사용 하 여 디스크에 기록 하거나 디스크에 기록 하는 소프트웨어 구성 요소를 암호화 하는 것을 방지 합니다. 이러한 유형의 공격은 기존의 디스크 기반 방법을 사용 하 여 감지 하기 어렵습니다.

    그러나 메모리 분석을 사용 하면 이러한 종류의 공격을 감지할 수 있습니다. 크래시 덤프의 메모리를 분석 하 여 공격에 사용 되는 기술을 감지할 수 Security Center. 예를 들어 공격에서 소프트웨어의 취약성을 악용 하 고 기밀 데이터에 액세스 하 고 은밀가 손상 된 컴퓨터 내에서 지속 될 수 있습니다. 이 작업은 호스트에 대 한 성능에 최소한의 영향을 주므로 Security Center 합니다.

    크래시 덤프 분석 경고에 대 한 자세한 내용은 [경고의 참조 테이블](alerts-reference.md#alerts-windows)을 참조 하세요.

* **Fileless 공격 감지** <a name="windows-fileless"></a> -엔드포인트을 대상으로 하는 Fileless 공격이 일반적입니다. 검색을 방지 하기 위해 fileless 공격은 악성 페이로드를 메모리에 삽입 합니다. 공격자 페이로드는 손상 된 프로세스의 메모리 내에 유지 되며 다양 한 악의적인 작업을 수행 합니다.

    Fileless 공격 감지를 통해 자동화 된 메모리 법적 기술은 fileless 공격 도구 키트, 기술 및 동작을 식별 합니다. 이 솔루션은 런타임에 컴퓨터를 정기적으로 검색 하 고 보안에 중요 한 프로세스의 메모리에서 직접 정보를 추출 합니다.

    악용에 대 한 증거, 코드 삽입 및 악성 페이로드의 실행을 찾습니다. Fileless 공격 감지는 경고 심사, 상관 관계 및 다운스트림 응답 시간을 가속화 하기 위한 자세한 보안 경고를 생성 합니다. 이 접근 방식은 이벤트 기반 EDR 솔루션을 보완 하 여 더 큰 검색 검사를 제공 합니다.

    Fileless 공격 감지 경고에 대 한 자세한 내용은 [경고의 참조 테이블](alerts-reference.md#alerts-windows)을 참조 하세요.

> [!NOTE]
> [Azure Security Center 플레이 북: 보안 경고](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)를 다운로드 하 여 Windows 경고를 시뮬레이션할 수 있습니다.

## Linux <a name="linux-machines"></a>

Security Center는 가장 일반적인 Linux 감사 프레임 워크 중 하나인 **auditd**를 사용 하 여 linux 컴퓨터에서 감사 레코드를 수집 합니다. auditd는 주요 커널에 상주 합니다. 

* **Linux auditd 경고 및 Microsoft Monitoring Agent (MMA) 통합** <a name="linux-auditd"></a> -auditd 시스템은 시스템 호출 모니터링을 담당 하는 커널 수준 하위 시스템으로 구성 됩니다. 지정 된 규칙 집합을 기준으로 필터링 하 고이에 대 한 메시지를 소켓에 씁니다. Security Center는 Microsoft Monitoring Agent 내에서 auditd 패키지의 기능을 통합 합니다 (MMA). 이러한 통합을 통해 모든 지원 되는 Linux 배포판에서 사전 요구 사항 없이 auditd 이벤트를 수집할 수 있습니다.  

    auditd 레코드는 Linux MMA 에이전트를 사용 하 여 수집, 보강 및 이벤트로 집계 됩니다. Security Center은 Linux 신호를 사용 하 여 클라우드 및 온-프레미스 Linux 컴퓨터에서 악성 동작을 검색 하는 새로운 분석을 지속적으로 추가 합니다. Windows 기능과 유사 하 게 이러한 분석은 의심 스러운 프로세스, 수상한 로그인 시도, 커널 모듈 로드 및 기타 작업에 걸쳐 있습니다. 이러한 활동은 컴퓨터가 공격을 받고 있거나 위반 되었음을 나타낼 수 있습니다.  

    다음은 공격 수명 주기의 여러 단계에 걸쳐 있는 분석의 몇 가지 예입니다.

    Linux 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-linux)을 참조 하세요.

> [!NOTE]
> [Azure Security Center 플레이 북: linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)검색을 다운로드 하 여 linux 경고를 시뮬레이션할 수 있습니다.

 
 ## <a name="next-steps"></a>다음 단계

예제 및 Security Center 검색에 대 한 자세한 내용은 다음을 참조 하세요.

* [사이버 공격 감지 Azure Security Center 자동화 하는 방법](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [관리 도구를 사용 하 여 취약점을 Azure Security Center 검색 하는 방법](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Azure Security Center 활용 하 여 손상 된 Linux 컴퓨터의 공격을 감지 하는 경우](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Linux에서 새로운 취약점을 검색할 수 있는 Azure Security Center](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)