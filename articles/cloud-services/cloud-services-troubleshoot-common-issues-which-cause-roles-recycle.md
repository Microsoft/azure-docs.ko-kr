---
title: 클라우드 서비스 역할 재활용의 일반적인 원인 | Microsoft Docs
description: 재활용이 중요한 가동 중지 시간을 갑작스럽게 발생시킬 수 있는 클라우드 서비스 역할입니다. 역할이 재활용되도록 하는 일반적인 문제는 다음과 같으며 이는 가동 중지를 줄이는 데 도움이 될 수 있습니다.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 2a9214b918883e493ebe5c93fc7f56e7ce9c77ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652216"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>역할을 재활용하게 하는 일반적인 문제
이 문서에서는 배포 문제의 일반적인 몇 가지 원인을 설명하고 이러한 문제를 해결하기 위한 문제 해결 팁을 제공합니다. 애플리케이션에 문제가 있다는 것을 역할 인스턴스가 시작에 실패하거나 초기화, 사용 중, 및 중지 상태를 반복할 경우에 알 수 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>런타임 종속성 누락
애플리케이션의 역할이 .NET Framework 또는 Azure 관리된 라이브러리의 일부가 아닌 어셈블리를 사용하는 경우 애플리케이션 패키지에 해당 어셈블리를 명시적으로 포함해야 합니다. 기타 Microsoft 프레임워크가 기본적으로 Azure에서 사용할 수 없다는 사실을 염두에 두십시오. 역할이 이러한 프레임워크를 사용하는 경우 애플리케이션 패키지에 해당 어셈블리를 추가해야 합니다.

애플리케이션을 작성하고 포장하기 전에 다음을 확인합니다.

* Visual studio를 사용하는 경우 **로컬 복사** 속성이 Azure SDK 또는.NET Framework의 일부가 아닌 프로젝트에서 참조된 각 어셈블리에 대해 **True**로 설정해야 합니다.
* web.config 파일이 컴파일 요소에서 사용되지 않은 어셈블리를 참조하지 않도록 합니다.
* 모든 .cshtml 파일의 **빌드 작업**은 **콘텐츠**로 설정합니다. 이렇게 하면 패키지에서 파일이 올바르게 표시되고 기타 참조된 파일이 표시되도록 합니다.

## <a name="assembly-targets-wrong-platform"></a>어셈블리가 잘못된 플랫폼을 목표로 합니다.
Azure는 64비트 환경입니다. 따라서 32비트 대상에 컴파일된 .NET 어셈블리는 Azure에서 작동하지 않습니다.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>초기화하거나 중지하는 동안 처리되지 않은 예외를 throw하는 역할
[OnStart], [OnStop] 및 [실행] 메서드를 포함하는 [RoleEntryPoint] 클래스의 메서드에서 throw되는 예외는 처리되지 않은 예외입니다. 이러한 메서드에서 처리되지 않은 예외가 발생하면 역할이 재활용됩니다. 역할이 반복적으로 재활용되면 시작하려 할 때마다 처리되지 않은 예외가 throw될 수 있습니다.

## <a name="role-returns-from-run-method"></a>역할은 실행 메서드에서 반환합니다.
[실행] 메서드는 무기한 실행되도록 합니다. 코드가 [실행] 메서드를 재정의하는 경우 무기한 대기해야 합니다. [실행] 메서드가 반환되는 경우 역할은 재활용됩니다.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>잘못된 DiagnosticsConnectionString 설정
애플리케이션이 Azure Diagnostics를 사용하는 경우 서비스 구성 파일은 `DiagnosticsConnectionString` 구성 설정을 지정해야 합니다. 이 설정은 Azure에서 저장소 계정에 HTTPS 연결을 지정해야 합니다.

Azure에 애플리케이션 패키지를 배포하기 전에 `DiagnosticsConnectionString` 설정이 올바른지 확인하려면 다음을 확인합니다.  

* `DiagnosticsConnectionString` 설정이 Azure의 유효한 저장소 계정을 가리킵니다.  
  기본적으로 이 설정은 에뮬레이트된 스토리지 계정을 가리키므로 애플리케이션 패키지를 배포하기 전에 이 설정을 명시적으로 변경해야 합니다. 이 설정을 변경하지 않는 경우 역할 인스턴스가 진단 모니터링을 시작하려고 할 때 예외가 throw됩니다. 역할 인스턴스가 무기한 재활용되게 할 수 있습니다.
* 연결 문자열은 다음과 같은 [형식](../storage/common/storage-configure-connection-string.md)으로 지정해야 합니다. (HTTPS 프로토콜을 지정해야 합니다.) *MyAccountName*을 저장소 계정의 이름으로 바꾸고 *MyAccountKey*를 선택키로 바꿉니다.    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Microsoft Visual Studio용 Azure Tools를 사용하여 애플리케이션을 개발하는 경우 속성 페이지를 사용하여 이 값을 설정할 수 있습니다.

## <a name="exported-certificate-does-not-include-private-key"></a>내보낸 인증서는 개인 키를 포함하지 않습니다.
SSL에서 웹 역할을 실행하려면 내보낸 관리 인증서가 개인 키를 포함해야 합니다. *Windows 인증서 관리자*를 사용하여 인증서를 내보내는 경우 **개인 키 내보내기** 옵션에 대해 **예**를 선택해야 합니다. 인증서는 현재 지원되는 유일한 형식인 PFX 형식으로 내보내야 합니다.

## <a name="next-steps"></a>다음 단계
클라우드 서비스에 대한 [문제해결 문서](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) 를 더 봅니다.

[Kevin Williamson의 블로그 시리즈](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)에서 더 많은 역할 재활용 시나리오를 보세요.

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[실행]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
