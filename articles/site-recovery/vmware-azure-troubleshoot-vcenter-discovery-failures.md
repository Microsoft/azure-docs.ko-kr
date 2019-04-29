---
title: Azure Site Recovery를 사용하여 Azure로 VMware VM 재해 복구 시 온-프레미스로 장애 복구(failback) 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 VMware VM 재해 복구하는 동안 발생하는 장애 복구(failback) 및 다시 보호 문제를 해결하는 방법을 설명합니다.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: c598c5e238458c010500579c5371622b85e71de0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565194"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>vCenter 검색 실패 문제 해결

이 문서를 사용 하면 VMware vCenter 검색 오류로 인해 발생 하는 문제를 해결 하도록 도와줍니다.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>MaxSnapShots 속성에서 숫자가 아닌 값

9.20 이전 버전에서 vCenter의 연결을 끊습니다는 속성에 숫자가 아닌 값을 검색할 때 `snapshot.maxSnapShots` VM의 속성입니다.

이 문제는 오류 95126 ID로 식별 됩니다.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
이 문제를 해결하려면

- VM을 식별 하 고 숫자 값 (vCenter의 설정 편집 하는 VM)으로 값을 설정 합니다.

또는

- 9.20 이상 버전으로 구성 서버를 업그레이드 합니다.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>VCenter 연결의 프록시 구성 문제

vCenter 검색 시스템 사용자가 구성한 시스템 기본 프록시 설정을 준수 합니다. DRA 서비스가 OVA 템플릿이나 통합된 설치 관리자를 사용 하 여 구성 서버를 설치 하는 동안 사용자가 제공 하는 프록시 설정을 준수 합니다. 

공용 네트워크;에 통신에 프록시 사용 되는 일반적으로 같은 Azure와 통신 합니다. 프록시 구성 되어 있고 로컬 환경에서 vCenter를 경우 DRA 통신할 수 없습니다.

다음과 같은 경우가이 문제가 발생 하는 경우 발생 합니다.

- VCenter server \<vCenter > 오류 때문에 연결할 수 없습니다. 원격 서버에서 오류를 반환했습니다. 사용할 수 없음 (503) 서버
- VCenter server \<vCenter > 오류 때문에 연결할 수 없습니다. 원격 서버에서 오류를 반환했습니다. 원격 서버에 연결할 수 없습니다.
- VCenter/ESXi 서버에 연결할 수 없습니다.

이 문제를 해결하려면

다운로드 합니다 [PsExec 도구](https://aka.ms/PsExec)합니다. 

PsExec 도구를 사용 하 여 시스템 사용자 컨텍스트에 액세스 하 여 프록시 주소 구성 되어 있는지 여부를 확인 합니다. 다음 절차를 사용 하 여 바이패스 목록에 vCenter를 추가할 수 있습니다.

검색 프록시 구성:

1. PsExec 도구를 사용 하는 시스템 사용자 컨텍스트에서 IE를 엽니다.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. VCenter IP 주소를 사용 하지 않으려면 Internet Explorer에서 프록시 설정을 수정 합니다.
3. Tmanssvc 서비스를 다시 시작 합니다.

DRA 프록시 구성:

1. 명령 프롬프트를 열고 Microsoft Azure Site Recovery Provider 폴더를 엽니다.
 
    **cd C:\Program Files\Microsoft Azure Site Recovery 공급자**

3. 명령 프롬프트에서 다음 명령을 실행 합니다.
   
   **DRCONFIGURATOR 합니다. EXE /AddBypassUrls /configure [vCenter 추가 시 제공 된 서버는 vCenter의 IP 주소/FQDN]**

4. DRA 공급자 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

[VMware VM 재해 복구용 구성 서버 관리](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
