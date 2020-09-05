---
title: Windows Virtual Desktop의 새로운 기능 - Azure
description: Windows Virtual Desktop에 대한 새로운 기능 및 제품 업데이트.
author: Heidilohr
ms.topic: overview
ms.date: 08/07/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 8663fc3f016fadcd4f4c99acd800cd0ccf8844f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003539"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Windows Virtual Desktop의 새로운 기능

Windows Virtual Desktop은 정기적으로 업데이트됩니다. 이 문서에서는 다음에 대해 알아봅니다.

- 최신 업데이트
- 새로운 기능
- 기존 기능의 향상된 기능
- 버그 수정

이 문서는 매월 업데이트됩니다. 새 업데이트를 유지하려면 이 문서를 다시 확인하세요.

## <a name="july-2020"></a>2020년 7월  

7월은 Azure 리소스 관리 통합이 포함된 Windows Virtual Desktop이 일반 공급되었습니다.

이 새 릴리스에서 변경된 내용은 다음과 같습니다. 

- "2019년 가을 릴리스"는 "Windows Virtual Desktop(클래식)"이라고 하며, "2020년 봄 릴리스"는 이제 "Windows Virtual Desktop"입니다. 자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/)을 참조하세요. 

새 기능에 대해 자세히 알아보려면 [이 블로그 게시물](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)을 확인하세요. 

### <a name="autoscaling-tool-update"></a>자동 크기 조정 도구 업데이트

미리 보기 상태에 있던 최신 버전의 자동 크기 조정 도구는 이제 일반 공급됩니다. 이 도구는 Azure 자동화 계정 및 Azure Logic App을 사용하여 호스트 풀 내에서 세션 호스트 VM(가상 머신)을 자동으로 종료하고 다시 시작하여 인프라 비용을 절감합니다. [Azure Automation을 사용하여 세션 호스트 크기 조정](set-up-scaling-script.md)에서 자세히 알아보세요.

### <a name="azure-portal"></a>Azure portal

이제 Windows Virtual Desktop에서 Azure Portal을 사용하여 다음 작업을 수행할 수 있습니다. 

- 개인 데스크톱 세션 호스트에 직접 사용자 할당  
- 호스트 풀의 유효성 검사 환경 설정 변경 

### <a name="diagnostics"></a>진단

Log Analytics 작업 영역에 대해 미리 작성된 새로운 쿼리를 릴리스했습니다. 쿼리에 액세스하려면 **로그**로 이동하고 **범주**에서 **Windows Virtual Desktop**을 선택합니다. [진단 기능에 대해 Log Analytics 사용](diagnostics-log-analytics.md)에서 자세히 알아보세요.

### <a name="update-for-remote-desktop-client-for-android"></a>Android용 원격 데스크톱 클라이언트 업데이트

이제 [Android용 원격 데스크톱 클라이언트](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)에서 Windows Virtual Desktop 연결을 지원합니다. 10.0.7 버전부터 Android 클라이언트는 향상된 사용자 환경을 위한 새 UI를 제공합니다. 또한 클라이언트는 Windows Virtual Desktop 작업 영역을 구독할 때 조건부 액세스를 사용하도록 Android 디바이스에서 Microsoft Authenticator와 통합됩니다.  

이제 이전 버전의 원격 데스크톱 클라이언트를 "원격 데스크톱 8"이라고 합니다. 이전 버전의 클라이언트에 있는 기존 연결은 새 클라이언트로 원활하게 전송됩니다. 새 클라이언트는 iOS 및 macOS 클라이언트와 동일한 기본 RDP 코어 엔진에 다시 작성되었으며, 모든 플랫폼에서 새로운 기능의 빠른 릴리스를 제공합니다. 

### <a name="teams-update"></a>팀 업데이트

Windows Virtual Desktop에 대한 Microsoft Teams에 개선 사항이 있습니다. 가장 중요한 점은 Windows Virtual Desktop은 이제 Windows 데스크톱 클라이언트에 대한 오디오 및 비디오 최적화를 지원한다는 것입니다. 리디렉션은 통화 및 모임에서 오디오 또는 비디오를 사용할 때 사용자 간에 직접 경로를 만들어 대기 시간을 단축시킵니다. 거리를 줄이면 홉 수가 줄어들기 때문에 호출 모양과 소리가 더 부드러워집니다. [Windows Virtual Desktop에서 팀 사용](teams-on-wvd.md)에서 자세히 알아보세요.

## <a name="june-2020"></a>2020년 6월

지난달에는 Azure Resource Manager가 통합된 Windows Virtual Desktop을 미리 보기로 도입되었습니다. 이 업데이트에는 여러 흥미로운 새로운 기능들이 포함되어 있습니다. 이 Windows Virtual Desktop 버전의 새로운 기능은 다음과 같습니다.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>이제 Windows Virtual Desktop이 Azure Resource Manager와 통합되었습니다.

이제 Windows Virtual Desktop이 Azure Resource Manager에 통합되었습니다. 최신 업데이트에서는 이제 모든 Windows Virtual Desktop 개체가 Azure Resource Manager 리소스입니다. 또한 이 업데이트는 Azure RBAC(Azure 역할 기반 액세스 제어)와도 통합됩니다. 자세한 내용은 [Azure Resource Manager란?](../azure-resource-manager/management/overview.md)을 참조하세요.

이러한 변화가 미치는 영향은 다음과 같습니다.

- 이제 Windows Virtual Desktop이 Azure Portal과 통합되었습니다. 즉, 포털에서 직접 모든 항목을 관리할 수 있습니다. PowerShell, 웹앱 또는 타사 도구는 필요하지 않습니다. 시작하려면 [Azure Portal을 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace.md)에 있는 자습서를 확인하세요.

- 이 업데이트 전에는 개별 사용자에게만 RemoteApps 및 데스크톱을 게시할 수 있었습니다. 이제 Azure Resource Manager를 사용하면 Azure Active Directory 그룹에 리소스를 게시할 수 있습니다.

- 이전 버전의 Windows Virtual Desktop에는 테넌트 또는 호스트 풀에 할당할 수 있는 네 가지 기본 제공 관리자 역할이 있습니다. 이러한 역할은 이제 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)에 있습니다. 모든 Windows Virtual Desktop Azure Resource Manager 개체에 이러한 역할을 적용할 수 있으며, 이를 통해 완전하고 다양한 위임 모델을 사용할 수 있습니다.

- 이 업데이트에서는 더 이상 호스트 풀을 확장하기 위해 Azure Marketplace 또는 GitHub 템플릿을 반복적으로 실행할 필요가 없습니다. 호스트 풀을 확장하려면 Azure Portal의 호스트 풀로 이동하고 **+ 추가**를 선택하여 추가 세션 호스트를 배포하기만 하면 됩니다.

- 이제 호스트 풀 배포가 [Azure 공유 이미지 갤러리](../virtual-machines/windows/shared-image-galleries.md)와 완전히 통합되었습니다. 공유 이미지 갤러리는 이미지 버전 관리를 비롯한 VM(가상 머신) 이미지 정의를 저장하는 별도의 Azure 서비스입니다. 또한 글로벌 복제를 사용하여 로컬 배포를 위해 이미지를 복사하여 다른 Azure 지역으로 보낼 수 있습니다.

- PowerShell 또는 진단 서비스 웹앱을 통해 수행되는 함수 모니터링은 이제 Azure Portal의 Log Analytics로 이동되었습니다. 또한 이제 두 가지 옵션을 통해 보고서를 시각화할 수 있습니다. Kusto 쿼리를 실행하고 통합 문서를 사용하여 시각적 보고서를 만들 수 있습니다.

- 더 이상 Windows Virtual Desktop을 사용하도록 Azure AD(Azure Active Directory) 동의를 완료할 필요가 없습니다. 이 업데이트에서 Azure 구독의 Azure AD 테넌트는 사용자를 인증하고 관리자를 위한 Azure RBAC 제어를 제공합니다.


### <a name="powershell-support"></a>PowerShell 지원

이 업데이트를 통해 Azure PowerShell Az 모듈에 새 AzWvd cmdlet을 추가했습니다. 이 새 모듈은 .NET Core에서 실행되는 PowerShell Core에서 지원됩니다.

모듈을 설치하려면 [Windows Virtual Desktop용 PowerShell 모듈 설치](powershell-module.md)에 나온 지침을 따르세요.

[AzWvd PowerShell 참조](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization)에서 사용 가능한 명령 목록을 확인할 수도 있습니다.

새 기능에 대한 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)을 확인하세요.

### <a name="additional-gateways"></a>추가 게이트웨이

연결 대기 시간을 줄이기 위해 새 게이트웨이 클러스터를 남아프리카 공화국에 추가했습니다.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Windows Virtual Desktop(미리 보기)의 Microsoft Teams

Windows Virtual Desktop에 대한 Microsoft Teams에 몇 가지 개선 사항이 있습니다. 가장 중요한 점은 Windows Virtual Desktop은 이제 호출에 대한 오디오 및 시각적 리디렉션을 지원한다는 것입니다. 리디렉션은 오디오 또는 비디오를 사용하여 호출할 때 사용자 간에 직접 경로를 만들어 대기 시간을 단축시킵니다. 거리를 줄이면 홉 수가 줄어들기 때문에 호출 모양과 소리가 더 부드러워집니다.

자세한 내용은 [블로그 게시물](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Microsoft 365 Windows Virtual Desktop 로드맵](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)에서 향후 계획에 대해 알아봅니다.

다음 문서를 확인하여 Windows Virtual Desktop 및 원격 데스크톱 서비스의 클라이언트에 대한 업데이트를 알아봅니다.

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
