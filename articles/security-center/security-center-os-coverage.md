---
title: Azure Security Center에서 지원하는 기능 및 플랫폼 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 지원하는 기능 및 플랫폼 목록을 제공합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/17/2019
ms.author: monhaber
ms.openlocfilehash: b5eafd15344156965d0a191688f602ffe1b5a498
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703577"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure Security Center에서 지원하는 기능 및 플랫폼

클래식 및 Resource Manager 배포 모델을 둘 다 사용하여 생성된 VM(가상 머신) 및 컴퓨터에 대해 보안 상태 모니터링과 권장 사항이 제공됩니다.

> [!NOTE]
> Azure 리소스의 [클래식 및 리소스 관리자 배포 모델](../azure-classic-rm.md) 에 대해 자세히 알아봅니다.
>
>

## <a name="platforms-that-support-the-data-collection-agent"></a>데이터 수집 에이전트를 지원하는 플랫폼 

이 섹션에서는 Azure Security Center 에이전트를 실행하고 데이터를 수집할 수 있는 플랫폼을 나열합니다.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows 컴퓨터 및 VM에 대해 지원되는 플랫폼
다음 Windows 운영 체제가 지원됩니다.

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Windows Defender ATP와의 통합은 Windows Server 2012 R2 및 Windows Server 2016만 지원합니다.
>
>

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux 컴퓨터 및 VM에 대해 지원되는 플랫폼
다음 Linux 운영 체제가 지원됩니다.

* Ubuntu 버전 12.04 LTS, 14.04 LTS 및 16.04 LTS
* Debian 버전 6, 7, 8 및 9
* CentOS 버전 5, 6 및 7
* RHEL(Red Hat Enterprise Linux) 버전 5, 6 및 7
* SLES(SUSE Linux Enterprise Server) 버전 11 및 12
* Oracle Linux 버전 5, 6 및 7
* Amazon Linux 2012.09~2017
* OpenSSL 1.1.0은 x86_64 플랫폼, 64비트에서만 지원

## <a name="vms-and-cloud-services"></a>VM 및 Cloud Services
클라우드 서비스에서 실행되는 VM도 지원됩니다. 프로덕션 슬롯에서 실행되는 클라우드 서비스 웹 및 작업자 역할만 모니터링됩니다. 클라우드 서비스에 대한 자세한 내용은 [Azure Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요.


## <a name="supported-iaas-features"></a>지원되는 IaaS 기능

> [!div class="mx-tableFixed"]
> 

|서버|Windows||Linux||
|----|----|----|----|----|
|Environment|Azure|비 Azure|Azure|비 Azure|
|VMBA 위협 검색 경고|✔|✔|✔(지원되는 버전에서만)|✔|
|네트워크 기반 위협 탐지 경고|✔|X|✔|X|
|Windows Defender ATP 통합|✔(지원되는 버전에서만)|✔|X|X|
|누락된 패치|✔|✔|✔|✔|
|보안 구성|✔|✔|✔|✔|
|Endpoint Protection|✔|✔|X|X|
|JIT VM 액세스|✔|X|✔|X|
|적응형 애플리케이션 제어|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|디스크 암호화|✔|X|✔|X|
|타사 배포|✔|X|✔|X|
|NSG|✔|X|✔|X|
|Fileless 위협 탐지|✔|✔|X|X|
|네트워크 맵|✔|X|✔|X|
|적응형 네트워크 제어|✔|X|✔|X|


### <a name="supported-endpoint-protection-solutions"></a>지원되는 Endpoint Protection 솔루션

아래 표에는 다음 정보의 행렬이 나와 있습니다.
 - Azure Security Center를 사용하여 각 솔루션을 자동으로 설치할 수 있는지 여부
 - Security Center에서 검색할 수 있는 Endpoint Protection 솔루션. 이러한 Endpoint Protection 솔루션 중 하나가 검색되더라도 Security Center에 솔루션을 설치하는 것이 좋다는 메시지가 표시되지는 않습니다.

| 엔드포인트 보호| 플랫폼 | Security Center 설치 | Security Center 검색 |
|------|------|-----|-----|
| Windows Defender(Microsoft 맬웨어 방지 프로그램)| Windows Server 2016| 아니오, OS에 기본 제공| 예 |
| System Center Endpoint Protection(Microsoft 맬웨어 방지 프로그램) | Windows Server 2012 R2, 2012, 2008 R2(아래 참고 참조) | 확장을 통해 | 예 |
| Trend Micro - 모든 버전 | Windows Server 제품군  | 아닙니다. | 예 |
| Symantec v12.1.1100+| Windows Server 제품군  | 아닙니다. | 예 |
| McAfee v10+ | Windows Server 제품군  | 아닙니다. | 예 |
| Kaspersky| Windows Server 제품군  | 아닙니다. | 아닙니다.  |
| Sophos| Windows Server 제품군  | 아닙니다. | 아닙니다.  |

> [!NOTE]
> - Windows Server 2008 R2 가상 머신에서 SCEP(System Center Endpoint Protection)의 검색을 위해서는 PowerShell 3.0(또는 상위 버전)이 설치된 후 SCEP가 설치되어야 합니다.
>
>

## <a name="supported-paas-features"></a>지원되는 PaaS 기능 


|서비스|권장 사항|위협 감지|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob Storage 계정*|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|VNet|✔| 해당 없음|
|서브넷|✔| 해당 없음|
|NIC|✔| ✔|
|NSG|✔| 해당 없음|
|구독|✔| ✔|

\* 이러한 기능은 현재 공개 미리 보기로 지원됩니다. 



## <a name="next-steps"></a>다음 단계

- [디자인 고려 사항을 계획하고 이해하여 Azure Security Center를 채택](security-center-planning-and-operations-guide.md)하는 방법을 알아봅니다.
- [Security Center에서 가상 머신 동작 분석 및 크래시 덤프 메모리 분석](security-center-alerts-type.md#virtual-machine-behavioral-analysis)에 대해 자세히 알아봅니다.
- [Azure Security Center 사용에 관한 질문과 대답](security-center-faq.md)을 찾아봅니다.
- [Azure 보안 및 규정 준수에 관한 블로그 게시물](https://blogs.msdn.com/b/azuresecurity/)을 찾아봅니다.
