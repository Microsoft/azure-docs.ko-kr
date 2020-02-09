---
title: Microsoft Azure VM 아키텍처 워크플로 | Microsoft Docs
description: 이 문서에서는 서비스를 배포할 때 워크플로 프로세스에 대한 개요를 제공 합니다.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162155"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Microsoft Azure 클래식 VM 아키텍처 워크플로 
이 문서에서는 가상 머신과 같은 Azure 리소스를 배포 하거나 업데이트할 때 발생 하는 워크플로 프로세스에 대한 개요를 제공 합니다. 

> [!NOTE]
>Azure에는 리소스를 만들고 사용하기 위한 리소스 관리자 및 클래식. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다.

다음 다이어그램은 Azure 리소스의 아키텍처를 보여 줍니다.

![Azure 워크플로](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>워크플로 기본 사항
   
**A**. RDFE/FFE은 사용자에서 패브릭에 연결 된 통신 경로입니다. RDFE (RedDog 프런트 엔드)은 관리 포털의 프런트 엔드와 Visual Studio, Azure MMC 등의 Service Management API와 같은 공개적으로 노출 된 API입니다.  사용자의 모든 요청은 RDFE를 통과 합니다. FFE (패브릭 프런트 엔드)는 RDFE의 요청을 패브릭 명령으로 변환 하는 계층입니다. RDFE의 모든 요청은 FFE를 통해 패브릭 컨트롤러에 도달 합니다.

**B**. 패브릭 컨트롤러는 데이터 센터의 모든 리소스를 유지 관리 하 고 모니터링 하는 일을 담당 합니다. 게스트 OS 버전, 서비스 패키지, 서비스 구성, 서비스 상태 등의 정보를 전송 하는 패브릭 OS의 패브릭 호스트 에이전트와 통신 합니다.

**C**. 호스트 에이전트는 호스트 OS에 있고 게스트 OS를 설정 하 고 게스트 에이전트와의 통신 (WindowsAzureGuestAgent) 하 여 역할을 의도 된 목표 상태로 업데이트 하 고 게스트 에이전트를 사용 하 여 하트 비트를 확인 해야 합니다. 호스트 에이전트가 10 분 동안 하트 비트 응답을 받지 못하면 호스트 에이전트가 게스트 OS를 다시 시작 합니다.

**C2**. Waappagent.exe는 WindowsAzureGuestAgent를 설치, 구성 및 업데이트 하는 일을 담당 합니다.

**D**.  WindowsAzureGuestAgent은 다음을 담당 합니다.

1. 방화벽, Acl, LocalStorage 리소스, 서비스 패키지 및 구성, 인증서를 포함 하 여 게스트 OS 구성
2. 역할이 실행 될 사용자 계정에 대한 SID를 설정 합니다.
3. 패브릭에 역할 상태를 전달 합니다.
4. WaHostBootstrapper를 시작 하 고이를 모니터링 하 여 역할이 목표 상태에 있는지 확인 합니다.

**E**. WaHostBootstrapper는 다음을 담당 합니다.

1. 역할 구성을 읽고 역할을 구성 하 고 실행 하는 데 필요한 모든 작업 및 프로세스를 시작 합니다.
2. 모든 자식 프로세스 모니터링
3. 역할 호스트 프로세스에 대한 StatusCheck 이벤트를 발생 시킵니다.

**F**. 역할이 전체 IIS 웹 역할로 구성 된 경우 IISConfigurator가 실행 됩니다 (SDK 1.2 HWC 역할에 대해서는 실행 되지 않음). 이 담당 합니다.

1. 표준 IIS 서비스 시작
2. 웹 구성의 재작성 모듈 구성
3. 서비스 모델에서 구성 된 역할에 대한 AppPool 설정
4. DiagnosticStore LocalStorage 폴더를 가리키도록 IIS 로깅 설정
5. 권한 및 Acl 구성
6. 웹 사이트가% roleroot%: \sitesroot\0에 있으며 AppPool은 IIS를 실행 하기 위해이 위치를 가리킵니다. 

**7**. 시작 작업은 역할 모델에 의해 정의 되 고 WaHostBootstrapper에 의해 시작 됩니다. 시작 작업은 백그라운드에서 비동기적으로 실행 되도록 구성할 수 있으며, 호스트 부트스트래퍼는 시작 작업을 시작한 다음 다른 시작 작업을 계속 진행 합니다. 시작 작업은 간단한 (기본) 모드에서 실행 되도록 구성할 수도 있습니다 .이 모드에서는 호스트 부트스트래퍼가 시작 작업의 실행이 완료 될 때까지 대기 하 고, 다음 시작 작업을 계속 하기 전에 성공 (0) 종료 코드를 반환 합니다.

**H**. 이러한 작업은 SDK의 일부 이며 역할의 서비스 정의 (.csdef)에서 플러그 인으로 정의 됩니다. 시작 작업으로 확장 될 때 **DiagnosticsAgent** 및 **RemoteAccessAgent** 는 각각 두 개의 시작 작업, 즉 regular 및 **/blockstartup** 매개 변수를 포함 하는 시작 작업을 정의 한다는 점에서 고유 합니다. 정상적인 시작 작업은 역할 자체가 실행 되는 동안 백그라운드에서 실행 될 수 있도록 백그라운드 시작 작업으로 정의 됩니다. **/Blockstartup** 시작 작업은 WaHostBootstrapper 계속 하기 전에 종료 될 때까지 대기 하도록 간단한 시작 작업으로 정의 됩니다. **/Blockstartup** 작업은 일반 작업의 초기화가 완료 될 때까지 대기한 후 종료 되 고 호스트 부트스트래퍼가 계속 되도록 허용 합니다. 이렇게 하면 역할 프로세스가 시작 되기 전에 진단 및 RDP 액세스를 구성할 수 있습니다 .이 작업은/blockStartup 작업을 통해 수행 됩니다. 이를 통해 호스트 부트스트래퍼가 시작 작업을 완료 한 후에도 진단 및 RDP 액세스를 계속 실행할 수 있습니다 (일반 작업을 통해 수행 됨).

**I**. Wa작업 호스트는 일반 작업자 역할에 대한 표준 호스트 프로세스입니다. 이 호스트 프로세스는 모든 역할의 Dll 및 진입점 코드 (예: OnStart 및 Run)를 호스팅합니다.

**J**. Wawebhost.exe는 SDK 1.2 호환 호스팅 가능 웹 코어 (HWC)를 사용 하도록 구성 된 웹 역할에 대한 표준 호스트 프로세스입니다. 역할은 서비스 정의 (.csdef)에서 요소를 제거 하 여 HWC 모드를 사용 하도록 설정할 수 있습니다. 이 모드에서는 모든 서비스의 코드와 Dll이 Wawebhost.exe 프로세스에서 실행 됩니다. Iis (w3wp.exe)는 사용 되지 않으며 iis는 Wawebhost.exe 내에서 호스트 되기 때문에 IIS 관리자에 구성 된 AppPools 없습니다.

**K**. Waiishost.exe는 전체 IIS를 사용 하는 웹 역할에 대한 역할 진입점 코드의 호스트 프로세스입니다. 이 프로세스는 **Roleentrypoint** 클래스를 사용 하는 첫 번째 DLL을 로드 하 고이 클래스 (OnStart, Run, OnStop)에서 코드를 실행 합니다. Roleenvironment 클래스에서 만든 **Roleenvironment** 이벤트 (예: statuscheck 및 Changed)는이 프로세스에서 발생 합니다.

**L**. W3wp.exe는 역할이 전체 IIS를 사용 하도록 구성 된 경우에 사용 되는 표준 IIS 작업자 프로세스입니다. 그러면 IISConfigurator에서 구성 된 AppPool이 실행 됩니다. 여기에서 만든 RoleEnvironment 이벤트 (예: StatusCheck 및 Changed)는이 프로세스에서 발생 합니다. 두 프로세스 모두에서 이벤트를 구독 하는 경우 RoleEnvironment 이벤트는 두 위치 (Waiishost.exe 및 w3wp.exe)에서 발생 합니다.

## <a name="workflow-processes"></a>워크플로 프로세스

1. 사용자가 ".cspkg" 및 ".cscfg" 파일을 업로드 하 고, 리소스를 중지 하거나 구성 변경을 수행 하는 등의 작업을 수행 하는 등의 요청을 수행 합니다. 이 작업은 Visual Studio 게시 기능과 같이 Service Management API를 사용 하는 도구 또는 Azure Portal을 통해 수행할 수 있습니다. 이 요청은 모든 구독 관련 작업을 수행 하기 위해 RDFE로 이동 하 여 FFE에 요청을 전달 합니다. 이러한 워크플로의 나머지 단계는 새 패키지를 배포 하 고 시작 하는 것입니다.
2. FFE는 올바른 컴퓨터 풀을 찾습니다 (예: 선호도 그룹 또는 지리적 위치 및 패브릭의 입력 (예: 컴퓨터 가용성)). 그리고 해당 컴퓨터 풀의 마스터 패브릭 컨트롤러와 통신 합니다.
3. 패브릭 컨트롤러는 사용 가능한 CPU 코어가 있는 호스트를 찾거나 새 호스트를 회전 합니다. 서비스 패키지와 구성이 호스트에 복사 되 고 패브릭 컨트롤러가 호스트 OS의 호스트 에이전트와 통신 하 여 패키지를 배포 합니다 (Dip, 포트, 게스트 OS 등 구성).
4. 호스트 에이전트는 게스트 OS를 시작 하 고 게스트 에이전트 (WindowsAzureGuestAgent)와 통신 합니다. 호스트는 해당 역할이 목표 상태로 작동 하는지 확인 하기 위해 하트 비트를 게스트에 보냅니다.
5. WindowsAzureGuestAgent는 게스트 OS (방화벽, Acl, LocalStorage 등)를 설정 하 고 새 XML 구성 파일을 c:\Config에 복사한 다음 WaHostBootstrapper 프로세스를 시작 합니다.
6. 전체 IIS 웹 역할의 경우 WaHostBootstrapper IISConfigurator를 시작 하 고 IIS에서 웹 역할에 대한 기존 AppPools을 삭제 하도록 지시 합니다.
7. WaHostBootstrapper E:\RoleModel.xml에서 **시작** 작업을 읽고 시작 작업 실행을 시작 합니다. WaHostBootstrapper는 모든 간단한 시작 작업이 완료 되 고 "성공" 메시지가 반환 될 때까지 대기 합니다.
8. 전체 iis 웹 역할의 경우 wahostbootstrapper는 iis AppPool을 구성 하 고 사이트 `E:\Sitesroot\<index>`를 가리키도록 합니다. 여기서 `<index>` 은 서비스에 대해 정의 된 `<Sites>` 요소 수에 대한 0 기반 인덱스입니다.
9. WaHostBootstrapper는 역할 유형에 따라 호스트 프로세스를 시작 합니다.
    1. **작업자 역할**: Wa의 Host-a가 시작 되었습니다. WaHostBootstrapper는 OnStart () 메서드를 실행 합니다. 반환 된 후 WaHostBootstrapper는 Run () 메서드를 실행 한 다음 역할을 준비 된 것으로 표시 하 고이를 부하 분산 장치 순환에 배치 합니다 (InputEndpoints가 정의 된 경우). 그런 다음 WaHostBootsrapper는 역할 상태를 확인 하는 루프로 이동 합니다.
    1. **SDK 1.2 HWC 웹 역할**: Wawebhost.exe가 시작 되었습니다. WaHostBootstrapper는 OnStart () 메서드를 실행 합니다. 반환 된 후 WaHostBootstrapper Run () 메서드를 실행 한 다음 역할을 준비 된 것으로 표시 하 고 부하 분산 장치 순환에 배치 합니다. Wawebhost.exe는 워밍업 요청을 실행 합니다 (GET/cstr_a_si_init). 모든 웹 요청은 Wawebhost.exe으로 전송 됩니다. 그런 다음 WaHostBootsrapper는 역할 상태를 확인 하는 루프로 이동 합니다.
    1. **전체 IIS 웹 역할**: aIISHost이 시작 되었습니다. WaHostBootstrapper는 OnStart () 메서드를 실행 합니다. 반환 된 후에 Run () 메서드를 실행 한 다음 역할을 준비 된 것으로 표시 하 고 부하 분산 장치 순환에 배치 합니다. 그런 다음 WaHostBootsrapper는 역할 상태를 확인 하는 루프로 이동 합니다.
10. 전체 IIS 웹 역할에 대한 들어오는 웹 요청은 IIS를 트리거하여 w3wp.exe 프로세스를 시작 하 고 요청을 처리 합니다 .이는 온-프레미스 IIS 환경에서와 동일 합니다.

## <a name="log-file-locations"></a>로그 파일 위치

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
이 로그에는 시작, 중지 및 새 구성을 포함 하 여 서비스에 대한 변경 내용이 포함 됩니다. 서비스가 변경 되지 않는 경우이 로그 파일에서 상당한 시간 간격이 표시 될 수 있습니다.
- C:\Logs\WaAppAgent.Log.  
이 로그에는 상태 업데이트 및 하트 비트 알림이 포함 되며 2-3 초 마다 업데이트 됩니다.  이 로그는 인스턴스 상태에 대한 기록 보기를 포함 하 고 인스턴스가 준비 상태가 아닌 경우 사용자에 게 알려줍니다.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS 로그**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows 이벤트 로그**

`D:\Windows\System32\Winevt\Logs`
 



