---
title: Azure Security Center 준비 로드맵 | Microsoft Docs
description: Azure Security Center에서 시작하기 위한 준비 로드맵을 제공합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: ndicola
editor: ''
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: yurid
ms.openlocfilehash: 7de085766ae700b5c76d9eca49c1127aabdddb9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703407"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure Security Center 준비 로드맵
이 문서에서는 Azure Security Center를 시작하는 데 도움이 되는 준비 로드맵을 제공합니다.

## <a name="understanding-security-center"></a>Security Center 이해
Azure Security Center는 Azure, 온-프레미스 및 기타 클라우드용으로 통합 보안 관리 및 고급 위협 보호 기능을 제공합니다. 

다음 리소스를 사용하여 Security Center를 시작합니다.

문서
* [Azure Security Center 소개](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Azure Security Center 빠른 시작 가이드](https://docs.microsoft.com/azure/security-center/security-center-get-started)

동영상
* [간략한 소개 비디오](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Security Center 예방, 검색 및 응답 기능에 대한 개요](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>계획 및 작업
Security Center를 완벽하게 활용하려면 조직의 여러 개인 또는 팀에서 보안 작업, 모니터링, 거버넌스 및 인시던트 대응 요구 사항에 맞게 서비스를 사용하는 방법에 대해 이해해야 합니다.

다음 리소스를 사용하여 계획 및 작업 프로세스 중에 도움을 받습니다.


문서
* [Azure Security Center 계획 및 작업 가이드](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

비디오
* [Security Center를 사용하여 하이브리드 클라우드 작업 보호](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965)

### <a name="onboarding-computers-to-security-center"></a>Security Center에 컴퓨터 온보딩
Security Center에서는 Security Center 표준 계층이 적용되지 않은 모든 Azure 구독이나 작업 영역을 자동으로 검색합니다. 여기에는 Security Center 무료 계층을 사용하는 Azure 구독과 보안 솔루션이 사용하도록 설정되지 않은 작업 영역이 포함됩니다.

다음 리소스를 사용하여 온보딩 프로세스 중에 도움을 받습니다.

문서
* [보안 개선을 위해 Azure Security Center 표준 계층으로 온보딩](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

비디오
* [Azure Security Center 하이브리드 - 개요](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Security Center를 사용하여 보안 문제 완화
보안 센터는 방화벽 및 엔드포인트 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 실제 위협을 감지하고 가양성을 줄입니다.

다음 리소스를 사용하여 보안 경고를 관리하고 리소스를 보호할 수 있습니다.

문서    
* [Azure Security Center에서 보안 상태 모니터링](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Azure Security Center에서 가상 머신 보호](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-recommendations)
* [Azure Security Center에서 네트워크 보호](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Azure Security Center에서 애플리케이션 보호](https://docs.microsoft.com/azure/security-center/security-center-application-recommendations)
* [Azure Security Center에서 Azure SQL 서비스 및 데이터 보호](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


비디오   
* [Azure Security Center를 사용하여 보안 문제 완화](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>인시던트 대응을 위한 Security Center
비용과 손상을 줄이려면 공격이 발생하기 전에 사고 대응 계획을 적절히 수립하는 것이 중요합니다. Azure Security Center는 사고에 대응하는 여러 단계에서 사용할 수 있습니다.

다음 리소스를 사용하여 Security Center를 인시던트 대응 프로세스에 통합하는 방법을 이해합니다.

동영상  
* [인시던트 대응을 위한 Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [차세대 보안 작업으로 위협에 대한 신속한 대응 및 조사](https://youtu.be/e8iFCz5RM4g)

문서    
* [인시던트 대응에 Azure Security Center 사용](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [보안 플레이 북으로 대응 자동화](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>고급 클라우드 방어

Azure VM은 Security Center의 고급 클라우드 방어 기능을 활용할 수 있습니다. 이러한 기능에는 JIT(Just-In-Time) VM(가상 머신) 액세스 및 적응형 애플리케이션 제어가 포함됩니다.

다음 리소스를 사용하여 Security Center에서 이러한 기능을 사용하는 방법을 알아봅니다.

동영상  
* [Azure Security Center – Just-In-Time VM 액세스](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center - 적응형 애플리케이션 컨트롤](https://youtu.be/wWWekI1Y9ck)

문서    
* [Just-In-Time을 사용하여 가상 머신 액세스 관리](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Azure Security Center의 적응형 애플리케이션 제어](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>실습 활동

* [Security Center 실습 교육](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Security Center의 WAF(웹 애플리케이션 방화벽) 권장 플레이 북](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Security Center 플레이북: 보안 경고](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>추가 리소스
* [Security Center 설명서 페이지](https://docs.microsoft.com/azure/security-center/)
* [Security Center REST API 설명서 페이지](https://msdn.microsoft.com/library/mt704034.aspx)
* [Azure Security Center FAQ(질문과 대답)](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)
* [ID 보안 모범 사례](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices)
* [네트워크 보안 모범 사례](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)
* [PaaS 권장 사항](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [규정 준수](https://www.microsoft.com/en-us/trustcenter/compliance/due-diligence-checklist)
* [하이브리드 클라우드 워크 로드를 보호 하기 위해 log analytics 고객은 Azure Security Center를 이제 사용할 수 있습니다.](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>커뮤니티 리소스

* [Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Security Center 커뮤니티 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



