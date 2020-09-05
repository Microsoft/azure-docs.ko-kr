---
title: Azure AD Connect 및 Connect Health란? | Microsoft Docs
description: Azure AD로 온-프레미스 환경을 동기화하고 모니터링하는 데 사용된 도구를 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02a30aa4d553910c52a7dde89719450baf9037ce
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89278839"
---
# <a name="what-is-azure-ad-connect"></a>Azure AD Connect란?

Azure AD Connect는 하이브리드 ID 목표를 충족하고 달성하도록 설계된 Microsoft 도구입니다.  다음과 같은 기능을 제공합니다.
     
- [암호 해시 동기화](whatis-phs.md) - 사용자 온-프레미스 AD 암호의 해시를 Azure AD와 동기화하는 로그인 메서드입니다.
- [통과 인증](how-to-connect-pta.md) - 사용자가 온-프레미스 및 클라우드에서 동일한 암호를 사용할 수 있지만 페더레이션 환경의 추가 인프라가 필요하지 않은 로그인 메서드입니다
- [페더레이션 통합](how-to-connect-fed-whatis.md) - 페더레이션은 Azure AD Connect의 선택적 부분이며 온-프레미스 AD FS 인프라를 사용하여 하이브리드 환경을 구성하는 데 사용할 수 있습니다. 또한 인증서 갱신 및 추가 AD FS 서버 배포와 같은 AD FS 관리 기능을 제공합니다.
- [동기화](how-to-connect-sync-whatis.md) - 사용자, 그룹 및 기타 개체를 생성합니다.  뿐만 아니라 온-프레미스 사용자 및 그룹의 ID 정보가 클라우드와 일치하도록 합니다.  이 동기화에는 암호 해시도 포함되어 있습니다.
- [상태 모니터링]() - Azure AD Connect Health는 강력한 모니터링을 제공하고 Azure Portal에서 중앙 위치를 제공하여 이 작업을 볼 수 있습니다. 


![Azure AD Connect의 정의](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Azure AD Connect Health란?

Azure AD(Azure Active Directory) Connect Health에서는 온-프레미스 ID 인프라에 대한 강력한 모니터링을 제공합니다. 이 기능을 사용하면 Office 365 및 Microsoft Online Services에 대한 신뢰할 수 있는 연결을 유지 관리할 수 있습니다.  핵심 ID 구성 요소에 대한 모니터링 기능을 통해 이러한 신뢰성을 보장합니다. 또한 이러한 구성 요소에 대한 핵심 데이터 요소에 쉽게 액세스할 수 있습니다.

정보는 [Azure AD Connect Health 포털](https://aka.ms/aadconnecthealth)에 표시됩니다. Azure AD Connect Health 포털을 사용하여 경고, 성능 모니터링, 사용량 현황 분석, 기타 정보를 확인합니다. Azure AD Connect Health는 핵심 ID 구성 요소의 상태를 한 곳에서 볼 수 있게 해주는 단일 렌즈의 역할을 합니다.

![Azure AD Connect Health 정의](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Azure AD Connect를 사용하는 이유
Azure AD와 온-프레미스 디렉터리를 통합하면 온-프레미스 및 클라우드 리소스 모두에 액세스하기 위한 일반적인 ID를 제공하므로 사용자가 더 생산성을 높일 수 있습니다. 사용자와 조직은 다음과 같은 이점을 얻을 수 있습니다.

* 사용자는 단일 ID를 사용하여 온-프레미스 애플리케이션 및 Office 365와 같은 클라우드 서비스에 액세스할 수 있습니다.
* 동기화 및 로그인에 대해 간편한 배포 환경을 제공하는 단일 도구입니다.
* 시나리오에 대한 최신 기능을 제공합니다. Azure AD Connect는 DirSync 및 Azure AD Sync와 같은 이전 버전의 ID 통합 도구를 대체합니다. 자세한 내용은 [하이브리드 ID 디렉터리 통합 도구 비교](plan-hybrid-identity-design-considerations-tools-comparison.md)를 참조하세요.

## <a name="why-use-azure-ad-connect-health"></a>Azure AD Connect Health를 사용하는 이유
Azure AD를 사용하면 클라우드 및 온-프레미스 리소스 모두에 액세스할 수 있는 공통 ID가 제공되므로 사용자의 생산성이 향상됩니다. 사용자가 이러한 리소스에 액세스할 수 있게 되면 환경을 신뢰할 수 있는지에 대한 문제가 발생합니다.  Azure AD Connect Health를 사용하면 온-프레미스 ID 인프라를 모니터링하고 파악할 수 있으므로 이 환경의 신뢰성을 보장합니다. 각 온-프레미스 ID 서버에 에이전트를 설치하는 것만큼 간단합니다.

AD FS용 Azure AD Connect Health는 Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 및 Windows Server 2016에서 AD FS 2.0을 지원합니다. 또한 엑스트라넷 액세스에 대한 인증 지원을 제공하는 AD FS 프록시 또는 웹 애플리케이션 프록시 서버의 모니터링을 지원합니다. AD FS에 대한 Azure AD Connect Health는 Health 에이전트를 쉽고 빠르게 설치하며 주요 기능 집합을 제공합니다.

주요 이점 및 모범 사례:

|주요 이점|모범 사례|
|-----|-----|
|향상된 보안|[엑스트라넷 잠금 추세](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[실패한 로그인 보고서](how-to-connect-health-adfs-risky-ip.md)</br>[개인 정보 보호 준수](reference-connect-health-user-privacy.md)|
|[모든 심각한 ADFS 시스템 문제](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)에 대한 알림 받기|서버 구성 및 가용성</br>[성능 및 연결](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>정기적인 유지 관리|
|쉬운 배포 및 관리|[빠른 에이전트 설치](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>최신 에이전트 자동 업그레이드</br>몇 분 안에 포털에서 사용 가능한 데이터|
다양한 [사용량 메트릭](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|상위 애플리케이션 사용량</br>네트워크 위치 및 TCP 연결</br>서버당 토큰 요청|
|멋진 사용자 환경|Azure Portal에서 대시보드 방식</br>[이메일 경고](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Azure AD Connect 사용을 위한 라이선스 요구 사항

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Azure AD Connect Health 사용을 위한 라이선스 요구 사항
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)
- [Azure AD Connect Health 에이전트 설치](how-to-connect-health-agent-install.md)