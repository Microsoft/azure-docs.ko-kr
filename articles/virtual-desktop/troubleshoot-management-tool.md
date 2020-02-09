---
title: Windows 가상 데스크톱 관리 도구-Azure
description: Windows 가상 데스크톱 관리 도구를 사용 하 여 문제를 해결 하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
ms.openlocfilehash: dd9fd135536fc2cb222b908f3d61c1d1430e65b0
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905616"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Windows 가상 데스크톱 관리 도구 문제 해결

이 문서에서는 Windows 가상 데스크톱 관리 도구를 배포 하는 동안 발생할 수 있는 문제와 이러한 문제를 해결 하는 방법을 설명 합니다.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>오류: 관리 도구 서비스를 구성 했지만 자동 설치에 실패 함

관리 도구에 대 한 서비스를 성공적으로 설정 했지만 자동화 된 설치에 실패 하는 경우 다음 오류 메시지가 표시 됩니다.

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

이는 일반적으로 다음 두 가지 중 하나를 의미 합니다.

- 사용자는 테넌트 수준의 구독 및 전역 관리자에 대 한 소유자 권한이 있지만 Azure에 로그인 할 수 없습니다.
- 사용자의 계정 설정에서 multi-factor authentication을 사용 하도록 설정 했습니다.

이 문제를 해결 하려면 다음을 수행 합니다.

1. Azure Active Directory 사용자 계정 이름에 대해 만든 사용자에 게 "참가자" 구독 수준이 있는지 확인 합니다.
2. 계정 설정을 확인 하 고 multi-factor authentication이 켜져 있지 않은지 확인 하려면 UPN 계정으로 > <에 로그인 합니다. 설정 되어 있는 경우 해제 합니다.
3. Windows 가상 데스크톱 동의 페이지를 방문 하 여 서버 및 클라이언트 앱이 동의 했는지 확인 합니다.
4. 문제가 계속 되 고 도구를 다시 배포 하는 경우 [관리 도구 배포](manage-resources-using-ui.md) 자습서를 검토 합니다.

## <a name="error-job-with-specified-id-already-exists"></a>오류: 지정한 ID를 가진 작업이 이미 있습니다.

사용자에 게 "지정 된 ID를 가진 작업이 이미 있습니다." 라는 오류 메시지가 표시 되는 경우 템플릿을 배포할 때 "응용 프로그램 이름" 매개 변수에 고유한 이름을 제공 하지 않았기 때문입니다.

이 문제를 해결 하려면 "응용 프로그램 이름" 매개 변수를 입력 하 여 관리 도구를 다시 배포 합니다.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>관리 도구를 열 때 지연 된 동의 확인 프롬프트

관리 도구를 배포 하면 동의 확인 프롬프트가 즉시 열리지 않을 수 있습니다. 즉, Azure 웹 앱 서비스를 로드 하는 데 평소 보다 오래 걸리고 있습니다. Azure 웹에서 로드를 완료 한 후에 프롬프트가 표시 됩니다.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>사용자는 미국 동부 지역에 관리 도구를 배포할 수 없습니다.

고객이 지역을 미국 동부로 설정 하는 경우 관리 도구를 배포할 수 없습니다.

이 문제를 해결 하려면 다른 지역에 관리 도구를 배포 합니다. 다른 지역에 도구를 다시 배포 하면 사용자 환경에 영향을 주지 않아야 합니다.

## <a name="next-steps"></a>다음 단계

- [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)에서 에스컬레이션 트랙에 대해 알아봅니다.
- [원격 데스크톱 서비스에 대 한 ARM 템플릿에서](https://github.com/Azure/RDS-Templates/blob/master/README.md)Windows 가상 데스크톱 도구와 관련 된 문제를 보고 하는 방법에 대해 알아봅니다.
- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대 한 개요는 [문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.
- 관리 도구를 배포 하는 방법에 대 한 자세한 내용은 [관리 도구 배포](manage-resources-using-ui.md)를 참조 하세요.