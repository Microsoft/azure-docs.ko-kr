---
title: Windows Virtual Desktop이란? - Azure
description: Windows Virtual Desktop의 개요입니다.
author: Heidilohr
ms.topic: overview
ms.date: 08/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cc5ad91c779a3445712db962fb97bab309eda973
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661115"
---
# <a name="what-is-windows-virtual-desktop"></a>Windows Virtual Desktop이란?

Windows Virtual Desktop은 클라우드에서 실행되는 데스크톱 및 앱 가상화 서비스입니다.

Azure에서 Windows Virtual Desktop을 실행하면 다음과 같은 작업이 가능합니다.

* 확장 가능한 완전한 Windows 10을 제공하는 다중 세션 Windows 10 배포 설정
* 엔터프라이즈용 Office 365 앱을 가상화하고 다중 사용자 가상 시나리오에서 실행되도록 최적화
* Windows 7 가상 데스크톱에 무료 기간 연장 보안 업데이트 제공
* 기존 RDS(원격 데스크톱 서비스)와 Windows Server 데스크톱 및 앱을 컴퓨터로 가져오기
* 데스크톱 및 앱 가상화
* 통합 관리 환경을 사용하여 Windows 10/Windows Server/Windows 7 데스크톱 및 앱 관리

## <a name="introductory-video"></a>소개 비디오

Windows Virtual Desktop의 고유한 이유와 이 비디오의 새로운 기능에 대해 알아봅니다.

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Windows Virtual Desktop에 대한 자세한 비디오는 [재생 목록](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)을 참조하세요.

## <a name="key-capabilities"></a>주요 기능

Windows Virtual Desktop을 사용하면 다음과 같이 확장 가능하고 유연한 환경을 설정할 수 있습니다.

* 추가 게이트웨이 서버를 실행할 필요 없이 Azure 구독에 완전한 데스크톱 가상화 환경을 만듭니다.
* 다양한 워크로드를 수용하는 데 필요한 만큼 호스트 풀을 게시합니다.
* Azure 갤러리에서 프로덕션 워크로드 또는 테스트에 사용할 사용자 고유의 이미지를 가져옵니다.
* 풀링된 다중 세션 리소스를 사용하여 비용을 줄입니다. Windows Server의 Windows Virtual Desktop 및 RDSH(원격 데스크톱 세션 호스트) 역할에만 제공되는 새 Windows 10 Enterprise 다중 세션 기능을 사용하면 가상 머신의 수와 OS(운영 체제) 오버헤드를 대폭 줄이면서도 사용자에게 동일한 리소스를 제공할 수 있습니다.
* 개인용(영구) 데스크톱을 통해 개별 소유권을 제공합니다.

다음과 같이 가상 데스크톱을 배포 및 관리할 수 있습니다.

* Azure Portal, Windows Virtual Desktop PowerShell 및 REST 인터페이스를 사용하여 호스트 풀을 구성하고, 앱 그룹을 만들고, 사용자를 할당하고, 리소스를 게시합니다.
* 단일 호스트 풀에서 전체 데스크톱 또는 개별 원격 앱을 게시하고, 여러 사용자 세트에 대한 개별 앱 그룹을 만들고, 심지어 여러 앱 그룹에 사용자를 할당하여 이미지 수를 줄입니다.
* 환경을 관리할 때 기본 제공되는 위임된 액세스를 사용하여 역할을 할당하고 다양한 구성 또는 사용자 오류를 이해할 수 있는 진단 정보를 수집합니다.
* 새 진단 서비스를 사용하여 오류를 해결합니다.
* 인프라가 아닌 이미지와 가상 머신만 관리합니다. 원격 데스크톱 서비스에서 하듯이 원격 데스크톱 역할을 개인적으로 관리할 필요 없이 Azure 구독의 가상 머신만 관리하면 됩니다.

다음과 같이 가상 데스크톱에 사용자를 할당하고 연결할 수도 있습니다.

* 할당된 사용자는 아무 Windows Virtual Desktop 클라이언트를 실행하여 사용자를 게시된 Windows 데스크톱 및 애플리케이션에 연결할 수 있습니다. 사용 중인 디바이스의 네이티브 애플리케이션 또는 Windows Virtual Desktop HTML5 웹 클라이언트를 통해 모든 디바이스에서 연결 가능합니다.
* 역방향 연결을 통해 사용자를 서비스에 안전하게 연결하므로 인바운드 포트를 계속 열어 둘 필요가 없습니다.

## <a name="requirements"></a>요구 사항

Windows Virtual Desktop을 설정하고 Windows 데스크톱 및 애플리케이션에 사용자를 연결하려면 몇 가지 조건을 충족해야 합니다.

다음 운영 체제를 지원하므로 배포하려는 데스크톱 및 앱에 따라 사용자에게 [적절한 라이선스](https://azure.microsoft.com/pricing/details/virtual-desktop/)가 있는지 확인합니다.

|OS|필수 라이선스|
|---|---|
|Windows 10 Enterprise 다중 세션 또는 Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business 프리미엄<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business 프리미엄<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Software Assurance가 포함된 RDS CAL(클라이언트 액세스 라이선스)|

Windows Virtual Desktop을 지원하려면 인프라에 다음과 같은 것들이 필요합니다.

* [Azure Active Directory](/azure/active-directory/).
* Azure Active Directory와 동기화되는 Windows Server Active Directory입니다. Azure AD Connect(하이브리드 조직의 경우) 또는 Azure AD Domain Services(하이브리드 또는 클라우드 조직의 경우)를 사용하여 이를 구성할 수 있습니다.
  * Azure Active Directory와 동기화되는 Windows Server AD. 사용자는 Windows Server AD에서 소싱되고 Windows Virtual Desktop VM은 Windows Server AD 도메인에 조인됩니다.
  * Azure Active Directory와 동기화되는 Windows Server AD. 사용자는 Windows Server AD에서 소싱되고 Windows Virtual Desktop VM은 Azure AD Domain Services 도메인에 조인됩니다.
  * Azure AD Domain Services 도메인. 사용자는 Azure Active Directory에서 소싱되고 Windows Virtual Desktop VM은 Azure AD Domain Services 도메인에 조인됩니다.
* Windows Server Active Directory 또는 Azure AD DS 인스턴스를 포함하거나 이에 연결된 가상 네트워크를 포함하는 동일한 Azure AD 테넌트의 부모가 되는 Azure 구독입니다.

Windows Virtual Desktop에 연결하기 위한 사용자 요구 사항:

* 사용자는 Azure AD에 연결된 것과 동일한 Active Directory에서 소싱되어야 합니다. Windows Virtual Desktop은 B2B 또는 MSA 계정을 지원하지 않습니다.
* Windows Virtual Desktop을 구독하는 데 사용하는 UPN은 VM이 조인된 Active Directory 도메인에 있어야 합니다.

Windows Virtual Desktop에 대해 만드는 Azure 가상 머신은 다음과 같아야 합니다.

* [표준 도메인에 조인](../active-directory-domain-services/active-directory-ds-comparison.md) 또는 [하이브리드 AD에 조인](../active-directory/devices/hybrid-azuread-join-plan.md). 가상 머신이 Azure AD에 조인되면 안 됩니다.
* 다음 [지원되는 OS 이미지](#supported-virtual-machine-os-images) 중 하나를 실행합니다.

>[!NOTE]
>Azure 구독이 필요한 경우에는 [1개월 평가판에 가입](https://azure.microsoft.com/free/)하면 됩니다. Azure 평가판 버전을 사용하는 경우 Windows Server Active Directory와 Azure Active Directory가 동기화 상태를 유지하도록 Azure AD Domain Services를 사용해야 합니다.

Windows Virtual Desktop 배포가 의도한 대로 작동하도록 차단해야 하는 URL 목록은 [Safe URL 목록](safe-url-list.md)을 참조하세요.

Windows Virtual Desktop은 고객이 사용자에게 제공하는 Windows 데스크톱과 앱, 그리고 Microsoft가 Azure에 서비스로 호스팅하는 관리 솔루션으로 구성됩니다. 데스크톱과 앱을 모든 Azure 지역의 VM(가상 머신)에 배포할 수 있으며, 이러한 VM의 관리 솔루션과 데이터는 미국에 상주합니다. 따라서 미국으로 데이터가 전송될 수 있습니다.

최적의 성능을 얻을 수 있도록 네트워크가 다음 요구 사항을 충족하는지 확인합니다.

* 클라이언트의 네트워크와 호스트 풀이 배포된 Azure 지역 간의 RTT(왕복) 대기 시간이 150밀리초 미만이어야 합니다. [경험 예측 도구](https://azure.microsoft.com/services/virtual-desktop/assessment)를 사용하여 연결 상태와 권장 Azure 지역을 확인합니다.
* 데스크톱 및 앱을 호스팅하는 VM을 관리 서비스에 연결할 때 네트워크 트래픽이 국가/지역 경계를 벗어날 수 있습니다.
* 네트워크 성능을 최적화하기 위해 세션 호스트의 VM을 관리 서비스와 동일한 Azure 지역에 배치하는 것이 좋습니다.

## <a name="supported-remote-desktop-clients"></a>지원되는 원격 데스크톱 클라이언트

다음 원격 데스크톱 클라이언트는 Windows Virtual Desktop을 지원합니다.

* [Windows Desktop](connect-windows-7-10.md)
* [Web](connect-web.md)
* [macOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)

> [!IMPORTANT]
> Windows Virtual Desktop은 RADC(RemoteApp 및 데스크톱 연결) 클라이언트 또는 원격 데스크톱 연결(MSTSC) 클라이언트를 지원하지 않습니다.

> [!IMPORTANT]
> Windows Virtual Desktop은 현재 Windows Store에서 원격 데스크톱 클라이언트를 지원하지 않습니다. 이 클라이언트에 대한 지원은 향후 릴리스에 추가될 예정입니다.

클라이언트를 사용하기 위해 차단을 해제해야 하는 URL에 대한 자세한 내용은 [Safe URL 목록](safe-url-list.md)을 참조하세요.

## <a name="supported-virtual-machine-os-images"></a>지원되는 가상 머신 OS 이미지

Windows Virtual Desktop은 다음 x64 운영 체제 이미지를 지원합니다.

* Windows 10 Enterprise 다중 세션, 버전 1809 이상
* Windows 10 Enterprise, 버전 1809 이상
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop은 x86(32비트), Windows 10 Enterprise N 또는 Windows 10 Enterprise KN 운영 체제 이미지를 지원하지 않습니다. 또한 Windows 7은 섹터 크기 제한으로 인해 관리되는 Azure Storage에서 호스팅되는 VHD 또는 VHDX 기반 프로필 솔루션을 지원하지 않습니다.

사용 가능한 자동화 및 배포 옵션은 다음 표에 나와 있는 것처럼 사용자가 선택한 OS 및 버전에 따라 달라집니다.

|운영 체제|Azure 이미지 갤러리|수동 VM 배포|Azure Resource Manager 템플릿 통합|Azure Marketplace에서 호스트 풀 프로비저닝|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 Enterprise(다중 세션) 버전 2004|예|예|예|예|
|Windows 10 Enterprise(다중 세션) 버전 1909|예|예|예|예|
|Windows 10 Enterprise(다중 세션) 버전 1903|예|예|아니요|아니요|
|Windows 10 Enterprise(다중 세션) 버전 1809|예|예|아니요|아니요|
|Windows 7 Enterprise|예|예|아니요|아니요|
|Windows Server 2019|예|예|아니요|예|
|Windows Server 2016|예|예|예|예|
|Windows Server 2012 R2|예|예|아니요|아니요|

## <a name="next-steps"></a>다음 단계

Windows Virtual Desktop(클래식)을 사용하는 경우 [Windows Virtual Desktop에서 테넌트 만들기](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)에서 자습서를 시작할 수 있습니다.

Azure Resource Manager가 통합된 Windows Virtual Desktop을 사용하는 경우 호스트 풀을 대신 만들어야 합니다. 시작하려면 다음 자습서를 시작하세요.

> [!div class="nextstepaction"]
> [Azure Portal로 호스트 풀 만들기](create-host-pools-azure-marketplace.md)
