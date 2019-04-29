---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: cbd86571cbdcd600ef3acdea3833568a34657931
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337949"
---
**마지막 문서 업데이트**: 14 년 8 월 2018 오전 10시 PST입니다.

투기적 실행 사이드 채널 공격으로 알려진 공개된 [새로운 종류의 CPU 취약점](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)으로 인해 고객은 명확하게 이해하기 위해 질문하게 되었습니다.  

Microsoft는 모든 클라우드 서비스에서 완화 기능을 배포했습니다. Azure를 실행하고 고객 워크로드를 서로 격리하는 인프라가 보호됩니다. 즉, 동일한 인프라를 사용하는 잠재적 공격자가 이러한 취약점을 사용하여 애플리케이션을 공격할 수 없습니다.

Azure에서는 가능한 경우 [메모리 보존 유지 관리](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot)를 사용하여 고객 영향을 최소화하고 다시 부팅할 필요를 제거합니다. Azure는 호스트에 대한 시스템 전반을 업데이트하고 고객을 보호할 때 이러한 메서드를 계속 활용합니다.

Azure의 모든 측면에 보안을 통합하는 방법에 대한 자세한 정보는 [Azure 보안 설명서](https://docs.microsoft.com/azure/security/) 사이트에서 사용할 수 있습니다. 

> [!NOTE] 
> 이 문서를 처음 게시한 이후 이 취약점 종류의 여러 변형이 공개되었습니다. Microsoft에서는 고객을 보호하고 지침을 제공하는 데 계속 투자하고 있습니다. 계속 추가 수정을 릴리스하므로 이 페이지가 업데이트됩니다. 
> 
> 2018년 8월 14일에 업계에서는 여러 CVE([CVE-2018-3615, CVE-2018-3620 및 CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html))를 할당한 L1TF([L1 Terminal Fault](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018))로 알려진 새로운 투기적 실행 쪽 채널 취약점을 공개했습니다. 이 취약점은 Intel® Core® 프로세서 및 Intel® Xeon® 프로세서에 영향을 줍니다. Microsoft는 고객 간 격리를 강화하는 클라우드 서비스에서 완화를 배포했습니다. L1TF 및 이전 취약점([스펙터 변형 2 CVE-2017-5715 및 멜트다운 변형 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution))으로부터 보호하기 위한 추가 지침은 아래를 참조하세요.
>  






## <a name="keeping-your-operating-systems-up-to-date"></a>운영 체제를 최신 상태로 유지

OS 업데이트가 다른 Azure 고객으로부터 Azure에서 실행되는 애플리케이션을 격리하지 않아도 되지만 항상 소프트웨어를 최신 상태로 유지하는 것이 좋습니다. Windows용 최신 보안 롤업에는 여러 투기적 실행 쪽 채널 취약점에 대한 완화가 포함됩니다. 마찬가지로 Linux 배포판은 이러한 취약점을 해결하기 위해 여러 업데이트를 릴리스했습니다. 운영 체제를 업데이트하기 위해 권장된 조치는 다음과 같습니다.

| 제품 | 권장 작업  |
|----------|---------------------|
| Azure Cloud Services  | [자동 업데이트](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal)를 사용하거나 최신 게스트 OS를 실행합니다. |
| Azure Linux Virtual Machines | 운영 체제 공급자로부터 업데이트를 설치합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [Linux](#linux)를 참조하세요. |
| Azure Windows Virtual Machines  | 최신 보안 롤업을 설치합니다.
| 기타 Azure PaaS 서비스 | 이러한 서비스를 사용하는 고객에게 필요한 작업은 없습니다. Azure에서는 자동으로 OS 버전을 최신 상태로 유지합니다. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>신뢰할 수 없는 코드를 실행하는 경우 추가 지침 

신뢰할 수 없는 사용자가 임의의 코드를 실행하도록 허용하는 고객은 해당 Azure Virtual Machines 또는 Cloud Services 내에서 몇 가지 추가 보안 기능을 구현하려고 할 수 있습니다. 이러한 기능은 몇 가지 투기적 실행 취약점에서 설명하는 프로세스 내 공개 벡터로부터 보호합니다.

추가 보안 기능을 권장하는 예제 시나리오:

- 신뢰할 수 없는 코드를 VM 내에서 실행할 수 있습니다.  
    - *예를 들어 고객 중 한 사람이 애플리케이션 내에서 실행하는 이진 파일 또는 스크립트를 업로드하도록 허용합니다*. 
- 신뢰할 수 없는 사용자가 권한이 낮은 계정을 사용하여 VM에 로그인하도록 허용합니다.   
    - *예를 들어 권한이 낮은 사용자가 원격 데스크톱 또는 SSH를 사용하여 VM 중 하나에 로그인하도록 허용합니다*.  
- 신뢰할 수 없는 사용자가 중첩된 가상화를 통해 구현되는 가상 머신에 액세스하도록 허용합니다.  
    - *예를 들어 Hyper-V 호스트를 제어하지만 신뢰할 수 없는 사용자에게 VM을 할당합니다*. 

신뢰할 수 없는 코드를 포함하는 시나리오를 구현하지 않는 고객은 이러한 추가 보안 기능을 사용할 필요가 없습니다. 

## <a name="enabling-additional-security"></a>추가 보안 사용 

VM 또는 Cloud Service 내에서 추가 보안 기능을 사용할 수 있습니다.

### <a name="windows"></a>Windows 

대상 운영 체제를 최신 상태로 유지하여 이러한 추가 보안 기능을 사용하도록 설정해야 합니다. 다양한 투기적 실행 쪽 채널 완화를 기본적으로 사용하는 반면 여기에 설명된 추가 기능은 수동으로 활성화해야 하며 성능에 영향이 발생할 수 있습니다. 

**1단계**: [Azure 지원에 문의](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) 가상 컴퓨터에 업데이트 노출 펌웨어 (마이크로). 

**2단계**: 커널 가상 주소 숨기기 (KVAS) 및 분기 대상 주입 (BTI) OS 지원을 사용 하도록 설정 합니다. [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 지침에 따라 `Session Manager` 레지스트리 키를 통해 보호를 사용하도록 설정합니다. 다시 부팅해야 합니다. 

**3단계**: 사용 중인 배포용 [중첩 된 가상화](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) (D3 및 E3만): 이러한 지침은 Hyper-v 호스트를 사용 하는 VM 내에서 적용 됩니다. 

1. [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 지침에 따라 `MinVmVersionForCpuBasedMitigations` 레지스트리 키를 통해 보호를 사용하도록 설정합니다.  
 
1. [여기](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)에서 지침에 따라 하이퍼바이저 스케줄러 형식을 **Core**로 설정합니다. 

**4단계**: 지침을 따릅니다 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 보호를 사용 하 여 활성화 되었는지 확인 하는 [SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell 모듈. 

> [!NOTE]
> 이전에 이 모듈을 다운로드한 경우 최신 버전을 설치해야 합니다.
>

모든 VM은 다음을 표시합니다.

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a> 내부 추가 보안 기능 집합을 사용하도록 설정하면 대상 운영 체제를 최신 상태로 유지해야 합니다. 일부 완화는 기본적으로 활성화됩니다. 다음 섹션에서는 기본적으로 해제되어 있고 하드웨어 지원(마이크로코드)을 사용하는 기능을 설명합니다. 이러한 기능을 활성화하면 성능에 영향이 발생할 수 있습니다. 자세한 지침은 운영 체제 공급자의 설명서를 참조합니다.
 
**1단계**: [Azure 지원에 문의](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) 가상 컴퓨터에 업데이트 노출 펌웨어 (마이크로).
 
**2단계**: CVE-2017-5715 (변형 2 스펙터) 운영 체제 공급자의 설명서를 수행 하 여 완화 하기 위해 분기 대상 주입 (BTI) OS 지원을 사용 하도록 설정 합니다. 
 
**3단계**: 운영 체제 공급자의 설명서를 수행 하 여 커널 페이지 테이블 격리 (KPTI) CVE-2017-5754 (변형 3 멜트다운)을 완화 하기 위해 사용 합니다. 
 
자세한 내용은 운영 체제의 공급자에서 사용할 수 있습니다.  
 
- [Redhat 및 CentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [Suse](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [CPU 취약성으로부터 Azure 고객 보호](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)를 참조하세요.
