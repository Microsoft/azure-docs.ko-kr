---
title: Azure Security Center의 Endpoint protection 권장 사항
description: Endpoint protection 솔루션을 검색 하 고 정상으로 식별 하는 방법입니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 899f4cba31afed812fa1643b925a38812308042e
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552936"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure Security Center의 Endpoint protection 평가 및 권장 사항

Azure Security Center은 [지원 되](security-center-services.md#supported-endpoint-protection-solutions-) 는 버전의 Endpoint protection 솔루션에 대 한 상태 평가를 제공 합니다. 이 문서에서는 다음 두 가지 권장 사항을 생성 하기 위해 Security Center 하는 시나리오를 설명 합니다.

* **가상 컴퓨터에 endpoint protection 솔루션 설치**
* **컴퓨터에서 endpoint protection 상태 문제 해결**

## <a name="windows-defender"></a>Windows Defender

* [Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) 를 실행 하 고 결과를 **amserviceenabled: False로 설정** 하는 경우 **"가상 컴퓨터에 endpoint protection 솔루션 설치"** 를 권장 Security Center

* [Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) 를 실행 하 고 다음 중 하나를 수행 하는 경우 " **컴퓨터에서 endpoint Protection 상태 문제 해결"** 을 권장 Security Center.

  * 다음 속성은 모두 false입니다.

    **AMServiceEnabled**

    **AntispywareEnabled**

    **RealTimeProtectionEnabled**

    **BehaviorMonitorEnabled**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * 다음 속성 중 하나 또는 둘 다가 7 이상인 경우

    **AntispywareSignatureAge**

    **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* **SCEPMpModule ("$env:P Rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1")** 를 가져오고, **amserviceenabled = false** 를 사용 하 여 **MProtComputerStatus** 결과를 실행 하는 경우 **"가상 컴퓨터에 endpoint protection 솔루션 설치"** 를 권장 합니다. Security Center

* **MprotComputerStatus** 실행 되 고 다음 중 하나가 발생할 때 **"컴퓨터에서 endpoint Protection 상태 문제 해결"** 을 권장 Security Center.

    * 다음 속성 중 하나 이상이 false입니다.

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * 다음 서명 업데이트 중 하나 또는 둘 다가 7 보다 크거나 같은 경우 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* 다음 검사 중 하나가 충족 되지 않으면 **"가상 컴퓨터에 endpoint protection 솔루션 설치"** 를 권장 Security Center.
    * **HKLM: \ SOFTWARE\TrendMicro\Deep 보안 에이전트가 있습니다.**
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\설치 폴더가 있습니다.**
    * Dsa_query 파일은 설치 폴더에 있습니다 **.**
    * **구성 요소** 를 사용 하 여 **dsa_query** 결과를 실행 하는 중입니다.

## <a name="symantec-endpoint-protection"></a>Symantec endpoint protection
다음 검사 중 하나가 충족 되지 않으면 **"가상 컴퓨터에 endpoint protection 솔루션 설치"** 를 권장 Security Center.

* **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

또는

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

다음 검사 중 하나라도 충족 되지 않으면 **"컴퓨터에서 endpoint protection 상태 문제 해결"** 을 권장 Security Center.

* Symantec 버전 > = 12: 레지스트리 위치: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-Value" PRODUCTVERSION "를** 확인 합니다.

* 실시간 보호 상태 확인: **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**

* 서명 업데이트 상태 확인: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 일**

* 전체 검사 상태 확인: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 일**

* Symantec 12: **레지스트리 경로 + "CurrentVersion\SharedDefs"-Value "SRTSP"** 의 서명 버전에 대 한 서명 버전 번호를 찾습니다. 

* Symantec 14의 서명 버전 경로: **레지스트리 경로 + "CurrentVersion\SharedDefs\SDSDefs"-값 "SRTSP"**

레지스트리 경로:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Windows 용 McAfee endpoint protection

다음 검사 중 하나가 충족 되지 않으면 **"가상 컴퓨터에 endpoint protection 솔루션 설치"** 를 권장 Security Center.

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** exists

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

다음 검사 중 하나라도 충족 되지 않으면 **"컴퓨터에서 endpoint protection 상태 문제 해결"** 을 권장 Security Center.

* McAfee 버전: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* 서명 버전 찾기: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "dwContentMajorVersion"**

* 서명 찾기 날짜: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" > = 7 일**

* 검색 날짜 찾기: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" > = 7 일**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Linux 위협 방지를 위한 McAfee 엔드포인트 보안 

다음 검사 중 하나가 충족 되지 않으면 **"가상 컴퓨터에 endpoint protection 솔루션 설치"** 를 권장 Security Center.

- 파일 **/opt/isec/ens/threatprevention/bin/isecav** 종료 

- **"/opt/isec/ens/threatprevention/bin/isecav--version"** 출력: **McAfee Name = Mcafee 엔드포인트 보안 Linux 위협 방지 및 mcafee 버전 > = 10**

다음 검사 중 하나라도 충족 되지 않으면 **"컴퓨터에서 endpoint protection 상태 문제 해결"** 을 권장 Security Center.

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 는 **빠른 검색, 전체 검색** 및 두 검색 < = 7 일을 반환 합니다.

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 는 **DAT 및 engine 업데이트 시간** 을 반환 하 고 둘 다 < = 7 일을 반환 합니다.

- **"/opt/isec/ens/threatprevention/bin/isecav--getoasconfig--summary"** 는 **액세스 검사 상태를** 반환 합니다.

## <a name="sophos-antivirus-for-linux"></a>Linux 용 Sophos Antivirus 

다음 검사 중 하나가 충족 되지 않으면 **"가상 컴퓨터에 endpoint protection 솔루션 설치"** 를 권장 Security Center.

- **/Opt/sophos-av/bin/savdstatus** 파일이 종료 되거나 사용자 지정 된 위치 **"readlink $ (savscan)"** 를 검색 합니다.

- **"/opt/sophos-av/bin/savdstatus--version"은** sophos Name = **sophos 바이러스 백신 및 sophos version > = 9를** 반환 합니다.

다음 검사 중 하나라도 충족 되지 않으면 **"컴퓨터에서 endpoint protection 상태 문제 해결"** 을 권장 Security Center.

- **"/opt/sophos-av/bin/savlog--maxage = 7 | grep-i "예약 된 검사입니다.\* 완료 됨 "| 비상-1 "** 은 값을 반환 합니다.

- **"/opt/sophos-av/bin/savlog--maxage = 7 | grep "검사 완료"** | 비상-1 "은 값을 반환 합니다.

- **"/opt/sophos-av/bin/savdstatus--lastupdate"** 는 < = 7 일 이어야 하는 lastupdate를 반환 합니다. 

- **"/opt/sophos-av/bin/savdstatus-v"** 는 **"액세스 시 검색을 실행 중입니다."** 와 같습니다. 

- **"/opt/sophos-av/bin/savconfig Get LiveProtection"** 반환 사용

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

Microsoft 맬웨어 방지 확장 로그는 **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (또는 PaaSAntimalware 방지) \1.5.5.x (버전 #) \CommandExecution.log** 에서 사용할 수 있습니다.

### <a name="support"></a>지원

자세한 내용은 [MSDN azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 azure 전문가에 게 문의 하세요. 또는 Azure 지원 인시던트를 파일에 제공 합니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.