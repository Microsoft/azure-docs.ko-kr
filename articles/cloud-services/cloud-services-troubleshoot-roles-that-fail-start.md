---
title: 시작에 실패한 역할의 문제 해결 | Microsoft Docs
description: 클라우드 서비스 역할이 시작에 실패한 이유에 대한 몇 가지 일반적인 원인은 다음과 같습니다. 또한 이러한 문제에 대한 솔루션이 제공됩니다.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: d2daae2a3317d3b48748262d87ab8d7f7e13f2b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653389"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>시작에 실패한 클라우드 서비스 역할의 문제 해결
시작에 실패한 Azure Cloud Services 역할에 관련된 일반적인 문제 및 솔루션은 다음과 같습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLL 또는 종속성 누락
응답하지 않는 역할 및 **초기화 중**, **사용 중** 및 **중지** 상태를 반복하는 역할은 누락된 DLL 또는 어셈블리 때문에 발생할 수 있습니다.

누락된 DLL 또는 어셈블리의 증상은 다음과 같을 수 있습니다.

* 역할 인스턴스가 **초기화 중**, **사용 중** 및 **중지** 상태를 반복하고 있습니다.
* 역할 인스턴스가 **준비** 로 이동했지만 웹 애플리케이션을 탐색하면 페이지가 나타나지 않습니다.

이러한 문제를 조사하기 위해 권장되는 여러 가지 방법이 있습니다.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>웹 역할에서 누락된 DLL 문제 진단
웹 역할에 배포된 웹 사이트로 이동하고 브라우저가 다음과 유사한 서버 오류를 표시하는 경우 DLL이 없음을 나타낼 수 있습니다.

!['/' 애플리케이션의 서버 오류.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>사용자 지정 오류를 해제하여 문제 진단
웹 역할에 대해 web.config가 사용자 지정 오류 모드를 꺼짐으로 설정하고 서비스를 다시 배포하도록 구성하여 오류 정보를 더 자세하게 볼 수 있습니다.

원격 데스크톱을 사용하지 않고 오류를 더 자세하게 보려면

1. Microsoft Visual Studio에서 솔루션을 엽니다.
2. **솔루션 탐색기**에서 web.config 파일을 찾아 엽니다.
3. Web.config 파일에서 system.web 섹션을 찾아서 다음 줄을 추가합니다.

    ```xml
    <customErrors mode="Off" />
    ```
4. 파일을 저장합니다.
5. 서비스를 다시 패키지하고 다시 배포합니다.

서비스가 다시 배포되면 누락된 어셈블리나 DLL의 이름으로 오류 메시지가 표시됩니다.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>원격으로 오류를 확인하여 문제 진단
원격 데스크톱을 사용하여 역할에 액세스하고 원격으로 오류 정보를 더 자세하게 볼 수 있습니다. 원격 데스크톱을 사용하여 오류를 보려면 다음 단계를 수행합니다.

1. Azure SDK 1.3 이상이 설치되어야 합니다.
2. Visual Studio를 사용하여 솔루션을 배포하는 동안 원격 데스크톱을 사용하도록 설정합니다. 자세한 내용은 [Visual Studio를 사용하여 Azure Cloud Services에서 역할에 대한 원격 데스크톱 연결 사용](cloud-services-role-enable-remote-desktop-visual-studio.md)을 참조하세요.
3. Microsoft Azure Portal에서 인스턴스 상태가 **준비**로 표시되면 해당 인스턴스에 원격 연결합니다. Cloud Services에서 원격 데스크톱 사용에 대한 자세한 내용은 [역할 인스턴스에 원격 연결](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances)을 참조하세요.
5. 원격 데스크톱을 구성하는 동안 지정한 자격 증명을 사용하여 가상 머신에 로그인합니다.
6. 명령 창을 엽니다.
7. `IPconfig`를 입력합니다.
8. IPV4 주소 값을 적습니다.
9. Internet Explorer를 엽니다.
10. 웹 애플리케이션의 주소 및 이름을 입력합니다. 예: `http://<IPV4 Address>/default.aspx`.

웹 사이트를 탐색하면 이제 더 구체적인 오류 메시지가 반환됩니다.

* '/' 애플리케이션의 서버 오류.
* 설명: 현재 웹 요청을 실행 하는 동안 처리 되지 않은 예외가 발생 했습니다. 오류에 대한 자세한 내용 및 코드에서 어디에 기반하는지는 스택 추적을 검토합니다.
* 예외 정보: System.IO.FIleNotFoundException: 파일 또는 어셈블리를 로드할 수 없습니다 ' Microsoft.WindowsAzure.StorageClient, 버전 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35 =' 또는 해당 종속성 중 하나입니다. 시스템은 지정된 파일을 찾을 수 없습니다.

예를 들면 다음과 같습니다.

!['/' 애플리케이션의 명시적 서버 오류](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>컴퓨팅 에뮬레이터를 사용하여 문제 진단
Microsoft Azure 컴퓨팅 에뮬레이터를 사용하여 누락된 종속성 및 web.config 오류 문제를 진단하고 해결할 수 있습니다.

이 진단 방법을 사용하여 최상의 결과가 발생한 경우 Windows 새로 설치한 컴퓨터 또는 가상 머신을 사용해야 합니다. Azure 환경을 가장 잘 시뮬레이션하려면 Windows Server 2008 R2 x64를 사용해야 합니다.

1. [Azure SDK](https://azure.microsoft.com/downloads/)의 독립 실행형 버전을 설치합니다.
2. 개발 컴퓨터에서 클라우드 서비스 프로젝트를 빌드합니다.
3. Windows Explorer에서 클라우드 서비스 프로젝트의 bin\debug 폴더로 이동합니다.
4. 문제를 디버깅하는 데 사용하는 컴퓨터에 .csx 폴더와.cscfg 파일을 복사합니다.
5. 클린 컴퓨터에서 Azure SDK 명령 프롬프트 창을 열고 `csrun.exe /devstore:start`을 입력합니다.
6. 명령 프롬프트에 `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`을 입력합니다.
7. 역할이 시작될 때 Internet Explorer에서 자세한 오류 정보가 표시됩니다. 또한 표준 Windows 문제해결 도구를 사용하여 추가로 문제를 진단할 수 있습니다.

## <a name="diagnose-issues-by-using-intellitrace"></a>IntelliTrace를 사용하여 문제 진단
.NET Framework 4를 사용하는 작업자 및 웹 역할의 경우 Microsoft Visual Studio Enterprise에서 사용 가능한 [IntelliTrace](/visualstudio/debugger/intellitrace)를 사용할 수 있습니다.

IntelliTrace를 사용하는 서비스를 배포하려면 다음 단계를 수행합니다.

1. Azure SDK 1.3 이상이 설치되었는지 확인합니다.
2. Visual Studio를 사용하여 솔루션을 배포합니다. 배포하는 동안 **.NET 4 역할에 IntelliTrace 사용** 확인란을 선택합니다.
3. 인스턴스가 시작되면 **서버 탐색기**를 엽니다.
4. **Azure\\클라우드 서비스** 노드를 확장하고 배포를 찾습니다.
5. 역할 인스턴스를 확인할 때까지 배포를 확장합니다. 마우스 오른쪽 단추로 인스턴스 중 하나를 클릭합니다.
6. **IntelliTrace 로그 보기**를 선택합니다. **IntelliTrace 요약** 이 열립니다.
7. 요약의 예외 섹션을 찾습니다. 예외가 있는 경우 해당 섹션이 **예외 데이터**로 표시됩니다.
8. **예외 데이터**를 확장하고 다음과 비슷한 **System.IO.FileNotFoundException** 오류를 찾습니다.

![예외 데이터, 파일 또는 어셈블리 누락](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>누락된 DLL 및 어셈블리 주소 지정
누락된 DLL 및 어셈블리 오류에 주소를 지정하려면 다음 단계를 수행합니다.

1. Visual Studio에서 솔루션을 엽니다.
2. **솔루션 탐색기**에서 **참조** 폴더를 엽니다.
3. 오류에서 식별된 어셈블리를 클릭합니다.
4. **속성** 창에서 **복사 로컬 속성**을 찾아 값을 **True**로 설정합니다.
5. 클라우드 서비스를 다시 배포합니다.

모든 오류가 수정되었다고 확인되면 **.NET 4 역할에 IntelliTrace 사용** 확인란을 선택하지 않고 서비스를 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계
클라우드 서비스에 대한 [문제해결 문서](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) 를 더 봅니다.

Azure PaaS 컴퓨터 진단 데이터를 사용하여 클라우드 서비스 역할 문제를 해결하는 방법을 알아보려면 [Kevin Williamson의 블로그 시리즈](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)를 참조하세요.
