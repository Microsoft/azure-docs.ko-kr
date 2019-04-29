---
title: Windows Defender Advanced Threat Protection과 Azure Security Center 통합
description: 이 문서에서는 Azure Security Center와 Windows Defender Advanced Threat Protection의 통합 방법을 소개합니다.
services: security-center
documentationcenter: na
author: barclayn
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: barclayn
ms.openlocfilehash: 75ca1401f72e00454dc3355d31c8b8ea64669a72
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129130"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender Advanced Threat Protection과 Azure Security Center 통합

Azure Security Center를 [Windows Defender ATP(Advanced Threat Protection)](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp)와 통합하면 클라우드 워크로드 보호 플랫폼 제품을 확장할 수 있습니다.
이러한 확장을 통해 제품이 변경되면 포괄적인 EDR(엔드포인트 검색 및 대응) 기능이 제공됩니다. Windows Defender ATP를 통합하면 비정상적인 상태를 정확히 파악할 수 있습니다. 그리고 Azure Security Center에서 모니터링하는 서버 엔드포인트에 대한 고급 공격을 검색하고 적절하게 대응할 수 있습니다.

Azure Security Center 고객은 이제 다음과 같은 Windows Defender ATP 기능을 사용할 수 있습니다.

- **차세대 위반 후 탐지 센서**: Windows 서버용 Windows Defender ATP 센서는 광범위한 동작 신호를 수집합니다.

- **분석 기반 클라우드형 위반 후 탐지**: Windows Defender ATP는 지속적으로 변화하는 위협을 파악할 수 있도록 빠르게 조정되며, 고급 분석 기능과 빅 데이터를 활용합니다. 또한 Windows, Azure, Office 전반에 걸쳐 생성되는 신호를 통해 알 수 없는 위협을 검색하는 Intelligent Security Graph의 기능을 통해 보안을 더욱 강화하며, 신속하게 대응할 수 있도록 조치 가능한 경고를 제공합니다.

- **위협 인텔리전스**: Windows Defender ATP가 공격자 도구, 기술 및 절차를 식별하며 이러한 정보가 검색되면 경고를 생성합니다. 이 과정에서는 Microsoft 위협 확인 담당자와 보안 팀에서 생성하며 파트너가 제공한 정보를 통해 강화된 데이터를 활용합니다.

이제 Azure Security Center에서는 다음과 같은 기능이 제공됩니다.

- **자동 온보딩**: Azure Security Center에 온보딩하는 Windows 서버에는 Windows Defender ATP 센서가 자동으로 사용됩니다.

- **하나의 창에서 경고 확인**: Azure Security Center 콘솔에 Windows Defender ATP 경고가 표시됩니다.

- **세부 머신 조사**: Azure Security Center 고객은 Windows Defender ATP 콘솔에 액세스한 다음 세부 조사를 수행하여 위반 범위를 파악할 수 있습니다.

![경고 목록 및 각 경고 관련 일반 정보가 표시된 Azure Security Center](media/security-center-wdatp/image1.png)

Azure Security Center에서 [경고 조사](security-center-investigation.md)를 진행할 수 있습니다.

![Azure Security Center의 경고 조사 대시보드](media/security-center-wdatp/image2.png)

Windows Defender ATP로 피벗하여 경고를 추가로 조사할 수 있습니다. 그러면 경고 프로세스 트리, 인시던트 그래프 등의 추가 정보를 확인할 수 있습니다. 그리고 이전 기간(최대 6개월 전)의 모든 동작이 표시되는 세부 컴퓨터 타임라인도 확인할 수 있습니다.

![경고 관련 세부 정보가 표시된 Windows Defender ATP 페이지](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>플랫폼 지원

이 기능은 Windows Server 2012 R2 및 Windows Server 2016에서 검색을 지원합니다.

표준 서비스 계층 구독의 서버만 지원됩니다.

## <a name="onboarding-servers-to-security-center"></a>Security Center에 서버 등록 

Security Center에 서버를 등록하려면 Windows Defender ATP 서버 온보딩에서 **Azure Security Center로 이동하여 서버 등록**을 클릭합니다.

1. **온보딩** 블레이드에서 데이터를 저장할 작업 영역을 선택하거나 만듭니다. <br>
2. 일부 작업 영역이 표시되지 않으면 권한이 부족하기 때문일 수 있습니다. 작업 영역이 Azure 보안 표준 계층으로 설정되어 있는지 확인하세요. 자세한 내용은 [보안 강화를 위해 Security Center의 표준 계층으로 업그레이드](security-center-pricing.md)를 참조하세요.
    
3. **서버 추가**를 선택하여 Microsoft Monitoring Agent 설치 방법에 대한 지침을 확인합니다. 

4. 등록한 후에는 **컴퓨팅 및 앱**에서 머신을 모니터링할 수 있습니다.

   ![컴퓨터 등록](media/security-center-wdatp/onboard-computers.png)


## <a name="enable-windows-defender-atp-integration"></a>Windows Defender ATP 통합 사용

Windows Defender ATP 통합이 사용하도록 설정되어 있는지 확인하려면 선택 **Security Center** > **보안 정책** > **구독** > **설정 편집**을 선택합니다.

  ![Azure Security Center 정책 관리](media/security-center-wdatp/policy-management.png)

여기서 현재 사용하도록 설정된 통합을 확인할 수 있습니다.

  ![Windows Defender ATP 통합이 사용하도록 설정된 Azure Security Center 위협 검색 설정 페이지](media/security-center-wdatp/enable-integrations.png)

- Azure Security Center 표준 계층에 서버를 이미 온보딩한 경우에는 추가로 작업을 수행할 필요가 없습니다. 해당 서버는 Windows Defender ATP에 자동 온보딩됩니다. 온보딩은 최대 24시간까지 걸릴 수 있습니다.

- Azure Security Center 표준 계층에 서버를 온보딩하지 않았다면 일반적인 방법으로 Azure Security Center에 서버를 온보딩합니다.

- Windows Defender ATP를 통해 서버를 온보딩한 경우에는 다음을 수행합니다.
  - [서버 컴퓨터를 온보딩하는 방법](https://go.microsoft.com/fwlink/p/?linkid=852906)의 설명서에서 지침을 참조합니다.
  - Azure Security Center에 해당 서버를 온보딩합니다.

## <a name="access-to-the-windows-defender-atp-portal"></a>Windows Defender ATP 포털 액세스

[포털에 대한 사용자 액세스 권한 할당](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection)의 지침을 따릅니다.

## <a name="set-the-firewall-configuration"></a>방화벽 구성 설정

Windows Defender ATP 센서는 시스템 컨텍스트에서 연결하므로, 프록시나 방화벽이 익명 트래픽을 차단하는 경우 익명 트래픽이 허용되도록 설정해야 합니다. [프록시 서버에서 Windows Defender ATP 서비스 URL에 액세스할 수 있도록 설정](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server)의 지침을 따르세요.

## <a name="test-the-feature"></a>기능 테스트

무해한 Windows Defender ATP 테스트 경고를 생성하려면 다음 단계를 수행합니다.

1. 원격 데스크톱을 사용하여 Windows Server 2012 R2 VM 또는 Windows Server 2016 VM에 액세스합니다.  명령 프롬프트 창을 엽니다.

2. 다음 명령을 복사하여 프롬프트에서 실행합니다. 명령 프롬프트 창이 자동으로 닫힙니다.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![위의 명령을 입력한 명령 프롬프트 창](media/security-center-wdatp/image4.jpeg)

3. 명령이 정상적으로 실행되면 Azure Security Center 대시보드와 Windows Defender ATP 포털에 새 경고가 표시됩니다. 이 경고가 표시되려면 몇 분 정도 걸릴 수 있습니다.

4. Security Center에서 경고를 검토하려면 **보안 경고** >  **의심스러운 Powershell 명령줄**로 이동합니다.

5. 조사 창에서 Windows Defender ATP 포털로 이동 하는 링크를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md): Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
- [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md): 권장 사항을 통해 Azure 리소스를 보호하는 방법을 알아봅니다.
- [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
