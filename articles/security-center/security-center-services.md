---
title: Azure Security Center에서 사용할 수 있는 지원 되는 기능 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 지원 되는 서비스 목록을 제공 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: ebc921e319b5ac16cb39001b5ffba5de48727d84
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181938"
---
# <a name="feature-coverage-for-machines"></a>컴퓨터의 기능 검사

아래 두 탭에는 Windows 및 Linux 가상 컴퓨터 및 서버에서 사용할 수 있는 Azure Security Center 기능이 나와 있습니다.

## <a name="supported-features-for-virtual-machines-and-servers"></a>가상 컴퓨터 및 서버에 대해 지원 되는 기능 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows 컴퓨터**](#tab/features-windows)

|**기능**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**비 Azure 컴퓨터**|**가격**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP 통합](security-center-wdatp.md)|✔</br>(지원 되는 버전)|✔</br>(지원 되는 버전)|✔|표준|
|[가상 컴퓨터 동작 분석 (및 보안 경고)](threat-protection.md)|✔|✔|✔|표준|
|[Fileless 보안 경고](alerts-reference.md#alerts-windows)|✔|✔|✔|표준|
|[네트워크 기반 보안 경고](threat-protection.md#network-layer)|✔|✔|-|표준|
|[Just-in-time VM 액세스](security-center-just-in-time.md)|✔|-|-|표준|
|[기본 취약성 평가](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|표준|
|[파일 무결성 모니터링](security-center-file-integrity-monitoring.md)|✔|✔|✔|표준|
|[적응 애플리케이션 컨트롤](security-center-adaptive-application.md)|✔|-|✔|표준|
|[네트워크 맵](security-center-network-recommendations.md#network-map)|✔|✔|-|표준|
|[적응형 네트워크 강화](security-center-adaptive-network-hardening.md)|✔|-|-|표준|
|[규정 준수 대시보드 & 보고서](security-center-compliance-dashboard.md)|✔|✔|✔|표준|
|Docker에서 호스트 되는 IaaS 컨테이너의 권장 사항 및 위협 방지|-|-|-|표준|
|누락 된 OS 패치 평가|✔|✔|✔|Azure: 무료<br><br>비 Azure: 표준|
|보안 잘못 평가|✔|✔|✔|Azure: 무료<br><br>비 Azure: 표준|
|[Endpoint protection 평가](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: 무료<br><br>비 Azure: 표준|
|디스크 암호화 평가|✔|✔|-|Free|
|타사 취약성 평가|✔|-|-|Free|
|[네트워크 보안 평가](security-center-network-recommendations.md)|✔|✔|-|Free|


### <a name="linux-machines"></a>[**Linux 컴퓨터**](#tab/features-linux)

|**기능**|**Azure Virtual Machines**|**Azure Virtual Machine Scale Sets**|**비 Azure 컴퓨터**|**가격**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender ATP 통합](security-center-wdatp.md)|-|-|-|표준|
|[가상 컴퓨터 동작 분석 (및 보안 경고)](security-center-alerts-iaas.md)|✔</br>(지원 되는 버전)|✔</br>(지원 되는 버전)|✔|표준|
|[Fileless 보안 경고](alerts-reference.md#alerts-windows)|-|-|-|표준|
|[네트워크 기반 보안 경고](threat-protection.md#network-layer)|✔|✔|-|표준|
|[Just-in-time VM 액세스](security-center-just-in-time.md)|✔|-|-|표준|
|[기본 취약성 평가](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|표준|
|[파일 무결성 모니터링](security-center-file-integrity-monitoring.md)|✔|✔|✔|표준|
|[적응 애플리케이션 컨트롤](security-center-adaptive-application.md)|✔|-|✔|표준|
|[네트워크 맵](security-center-network-recommendations.md#network-map)|✔|✔|-|표준|
|[적응형 네트워크 강화](security-center-adaptive-network-hardening.md)|✔|-|-|표준|
|[규정 준수 대시보드 & 보고서](security-center-compliance-dashboard.md)|✔|✔|✔|표준|
|Docker에서 호스트 되는 IaaS 컨테이너의 권장 사항 및 위협 방지|✔|✔|✔|표준|
|누락 된 OS 패치 평가|✔|✔|✔|Azure: 무료<br><br>비 Azure: 표준|
|보안 잘못 평가|✔|✔|✔|Azure: 무료<br><br>비 Azure: 표준|
|[Endpoint protection 평가](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Free|
|디스크 암호화 평가|✔|✔|-|Free|
|타사 취약성 평가|✔|-|-|Free|
|[네트워크 보안 평가](security-center-network-recommendations.md)|✔|✔|-|Free|

--- 


> [!TIP]
>표준 가격 책정 계층 에서만 사용할 수 있는 기능을 시험 하기 위해 무료 계층 사용자는 30 일 평가판으로 등록할 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.


## <a name="supported-endpoint-protection-solutions"></a>지원 되는 endpoint protection 솔루션 <a name="endpoint-supported"></a>

아래 표에는 다음 정보의 행렬이 나와 있습니다.

 - Azure Security Center를 사용하여 각 솔루션을 자동으로 설치할 수 있는지 여부
 - Security Center에서 검색할 수 있는 Endpoint Protection 솔루션. 이 목록에서 endpoint protection 솔루션을 검색 하는 경우 Security Center 설치 하지 않는 것이 좋습니다.

이러한 각 보호에 대 한 권장 사항이 생성 되는 경우에 대 한 자세한 내용은 [Endpoint Protection 평가 및 권장 사항](security-center-endpoint-protection.md)을 참조 하세요.

| Endpoint Protection| 플랫폼 | Security Center 설치 | Security Center 검색 |
|------|------|-----|-----|
| Microsoft Defender 바이러스 백신| Windows Server 2016 이상| 아니오, OS에 기본 제공| 예 |
| System Center Endpoint Protection(Microsoft 맬웨어 방지 프로그램) | Windows Server 2012 R2, 2012, 2008 R2(아래 참고 참조) | 확장을 통해 | 예 |
| 추세 마이크로 – 심층 보안 | Windows Server 제품군  | 예 | 예 |
| Symantec v12.1.1100+| Windows Server 제품군  | 예 | 예 |
| McAfee v10+ | Windows Server 제품군  | 예 | 예 |
| McAfee v10+ | Linux 서버 제품군  | 예 | 예로 **\*** |
| Sophos V9 +| Linux 서버 제품군  | 예 | 예로  **\***  |

 **\*** 현재는 보호 된 구독과 연결 된 Log Analytics 작업 영역 에서만 검사 상태와 지원 데이터를 사용할 수 있습니다. Azure Security Center 포털에는 반영 되지 않습니다.

> [!NOTE]
> Windows Server 2008 R2 가상 머신에서 SCEP (System Center Endpoint Protection)를 검색 하려면 PowerShell (v 3.0 이상) 후 SCEP가 설치 되어 있어야 합니다.


## <a name="next-steps"></a>다음 단계

- [Security Center에서 데이터 및 Log Analytics 에이전트를 수집](security-center-enable-data-collection.md)하는 방법을 알아봅니다.
- [Security Center에서 데이터를 관리 하 고 보호](security-center-data-security.md)하는 방법을 알아봅니다.
- [Security center를 지 원하는 플랫폼](security-center-os-coverage.md)을 검토 합니다.