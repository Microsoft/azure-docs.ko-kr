---
title: Hyper-V VM과 Azure 간 재해 복구를 위한 Azure Site Recovery Deployment Planner 정보 | Microsoft Docs
description: Hyper-V VM과 Azure 간 재해 복구를 위한 Azure Site Recovery Deployment Planner에 대해 알아봅니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/18/2019
ms.author: mayg
ms.openlocfilehash: aecbe666814a9ae80611de7798b4ab21fe15e219
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946497"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Hyper-V와 Azure 간 재해 복구를 위한 Azure Site Recovery Deployment Planner 정보

이 문서는 Hyper-V에서 Azure로 프로덕션 배포에 대한 Azure Site Recovery Deployment Planner 사용자 가이드입니다.

Site Recovery를 사용하여 Hyper-V VM(가상 머신) 보호를 시작하기 전에, 원하는 RPO(복구 지점 목표)를 충족하도록 일일 데이터 변경률을 기반으로 충분한 대역폭을 할당하고, 온-프레미스 Hyper-V 저장소의 각 볼륨에 사용 가능한 저장소 공간을 충분히 할당합니다.

또한 올바른 유형 및 수의 Azure Storage 계정을 만들어야 합니다. 시간이 지남에 따라 사용량이 증가하므로 원본 프로덕션 서버상의 증가를 고려하여 표준 또는 Premium Storage 계정을 만듭니다. 워크로드 특성(예: 읽기/쓰기 IOPS(초당 I/O 작업 수) 또는 데이터 변동) 및 Azure Site Recovery 제한을 기반으로 VM별 저장소 유형을 선택합니다. 

Azure Site Recovery Deployment Planner는 Hyper-V에서 Azure로 및 VMware에서 Azure로의 재해 복구 시나리오 모두에 대한 명령줄 도구입니다. 이 도구를 사용하여 여러 Hyper-V 호스트에 있는 Hyper-V VM을 원격으로 프로파일링하여(프로덕션에 전혀 영향을 미치지 않음) 성공적인 복제 및 테스트 장애 조치(failover)/장애 조치(failover)를 위한 Azure 저장소 요구 사항 및 대역폭을 파악할 수 있습니다. Azure Site Recovery 구성 요소를 온-프레미스에 설치하지 않고도 도구를 실행할 수 있습니다. 단, 달성된 처리량 결과를 정확히 얻으려면 하드웨어 구성이 Azure로 재해 복구 보호에 사용할 Hyper-V 서버 중 하나와 동일한 Windows Server에서 Planner를 실행하는 것이 좋습니다. 

이 도구는 다음과 같은 세부 정보를 제공합니다.

**호환성 평가**

* VM 적합성 평가(디스크 수, 디스크 크기, IOPS, 변동률 및 몇 가지 VM 특성 기준)

**네트워크 대역폭 요구 사항 및 RPO 평가**

* 델타 복제에 필요한 예상 네트워크 대역폭
* Azure Site Recovery를 통해 온-프레미스에서 Azure로 가져올 수 있는 처리량
* 지정된 대역폭에서 수행할 수 있는 RPO
* 낮은 대역폭을 프로비전하는 경우 원하는 RPO에 미치는 영향

    
**Azure 인프라 요구 사항**

* 각 VM에 대한 스토리지 유형(표준 또는 Premium Storage 계정) 요구 사항
* 복제를 위해 설정해야 할 표준 및 Premium Storage 계정의 총 수
* Azure Storage 지침에 따른 스토리지 계정 명명 제안
* 모든 VM에 대한 저장소 계정 위치
* 테스트 장애 조치 또는 구독에 대한 장애 조치 이전에 설정해야 할 Azure 코어의 수
* 각 온-프레미스 VM에 대한 Azure VM 권장 크기

**온-프레미스 인프라 요구 사항**
* 성공적인 초기 복제 및 델타 복제를 위해 Hyper-V 스토리지의 각 볼륨에 필요한 사용 가능한 스토리지 공간이며, VM 복제로 인해 프로덕션 애플리케이션에 바람직하지 않은 가동 중지 시간이 발생하지 않도록 합니다.
* Hyper-V 복제에 설정할 최대 복사 빈도

**초기 복제 일괄 처리 지침** 
* 보호에 사용할 VM 일괄 처리 수
* 각 일괄 처리에 있는 VM 목록
* 각 일괄 처리를 보호할 순서
* 각 일괄 처리에 대한 초기 복제 완료 예상 시간

**Azure로 DR에 대한 예상 비용**
* Azure로 DR에 대한 총 예상 비용: 계산, 스토리지, 네트워크 및 Azure Site Recovery 라이선스 비용
* VM별 자세한 비용 분석



>[!IMPORTANT]
>
>사용량은 시간이 지남에 따라 증가할 가능성이 있으므로 앞의 모든 도구 계산은 워크로드 특성에 30% 증가율을 가정하고 모든 프로파일링 메트릭(읽기/쓰기 IOPS, 변동 등)의 95번째 백분위수 값을 사용하여 수행합니다. 이러한 두 요소(증가율과 백분위수)는 모두 구성할 수 있습니다. 증가율에 대해 더 자세히 알아보려면 "증가율 고려 사항" 섹션을 참조하세요. 백분위수 값에 대해 더 자세히 알아보려면 "계산에 사용한 백분위수 값" 섹션을 참조하세요.
>

## <a name="support-matrix"></a>지원 매트릭스

| | **VMware에서 Azure로** |**Hyper-V에서 Azure로**|**Azure 간**|**Hyper-V에서 보조 사이트로**|**VMware에서 보조 사이트로**
--|--|--|--|--|--
지원되는 시나리오 |예|예|아닙니다.|예*|아닙니다.
지원되는 버전 | vCenter 6.7, 6.5, 6.0 또는 5.5| Windows Server 2016, Windows Server 2012 R2 | 해당 없음 |Windows Server 2016, Windows Server 2012 R2|해당 없음
지원되는 구성|vCenter, ESXi| Hyper-V 클러스터, Hyper-V 호스트|해당 없음|Hyper-V 클러스터, Hyper-V 호스트|해당 없음|
Azure Site Recovery Deployment Planner 실행 인스턴스당 프로파일링할 수 있는 서버 수 |한 개(하나의 vCenter Server 또는 하나의 ESXi 서버에 속하는 VM을 한 번에 프로파일링할 수 있습니다.)|여러 개(여러 호스트 또는 호스트 클러스터의 VM을 한 번에 프로파일링할 수 있습니다.)| 해당 없음 |여러 개(여러 호스트 또는 호스트 클러스터의 VM을 한 번에 프로파일링할 수 있습니다.)| 해당 없음

*이 도구는 주로 Hyper-V에서 Azure로 재해 복구 시나리오용입니다. Hyper-V에서 보조 사이트로 재해 복구에 대하여, 필요한 네트워크 대역폭, 각 원본 Hyper-V 서버에 필요한 사용 가능한 저장소 공간 및 초기 복제 일괄 처리 번호 및 일괄 처리 정의와 같은 원본 측 권장 사항을 이해하는 데에만 사용할 수 있습니다.  보고서의 Azure 권장 사항 및 비용은 무시하세요. 또한 처리량 가져오기 작업은 Hyper-V에서 보조 사이트로 재해 복구 시나리오에는 적용할 수 없습니다.

## <a name="prerequisites"></a>필수 조건
도구에는 VM 목록 가져오기, 프로파일링 및 보고서 생성이라는 Hyper-V의 세 가지 주요 단계가 있습니다. 또한 처리량만 계산하는 네 번째 옵션도 있습니다. 서로 다른 단계가 실행되어야 하는 서버에 대한 요구 사항은 다음 테이블에 있습니다.

| 서버 요구 사항 | 설명 |
|---|---|
|VM 목록 가져오기, 프로파일링 및 처리량 측정 |<ul><li>운영 체제: Microsoft Windows Server 2016 또는 Microsoft Windows Server 2012 R2 </li><li>머신 구성: 8개 vCPU, 16GB RAM, 300GB HDD</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Visual Studio 2012용 Microsoft Visual C++ 재배포 가능 패키지](https://aka.ms/vcplusplus-redistributable)</li><li>이 서버에서 Azure에 대한 인터넷 액세스</li><li>Azure Storage 계정</li><li>서버에 대한 관리자 액세스</li><li>최소 100GB의 사용 가능한 디스크 공간(각각 디스크 3개의 평균을 포함한 VM 1000개를 가정, 30일 동안 프로파일링)</li><li>Azure Site Recovery Deployment Planner 도구를 실행 중인 곳의 VM이 모든 Hyper-V 서버의 TrustedHosts 목록에 반드시 추가되어야 합니다.</li><li>도구를 실행 중인 클라이언트 VM의 TrustedHosts 목록에 프로파일링할 모든 Hyper-V 서버를 추가해야 합니다. [서버를 TrustedHosts 목록에 추가하는 내용을 자세히 알아봅니다](#steps-to-add-servers-into-trustedhosts-list). </li><li> 도구는 PowerShell의 관리자 권한 또는 클라이언트의 명령줄 콘솔에서 실행되어야 합니다.</ul></ul>|
| 보고서 생성 | Microsoft Excel 2013 이상을 포함한 모든 Windows PC 또는 Windows Server |
| 사용자 권한 | VM 목록 가져오기 및 프로파일링 작업을 수행하는 동안 Hyper-V 클러스터/Hyper-V 호스트에 액세스하기 위한 관리자 계정입니다.<br>프로파일링해야 하는 모든 호스트에는 자격 증명(예: 사용자 이름 및 암호)이 동일한 도메인 관리자 계정이 있어야 합니다.
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>서버를 TrustedHosts 목록에 추가하는 단계
1.  도구를 배포할 VM에 TrustedHosts 목록에서 프로파일링할 모든 호스트가 있어야 합니다. 클라이언트를 Trustedhosts 목록에 추가하려면 VM의 관리자 권한 PowerShell에서 다음 명령을 실행합니다. VM은 Windows Server 2012 R2 또는 Windows Server 2016일 수 있습니다. 

            set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate

1.  프로파일링해야 하는 각 Hyper-V 호스트에는 다음이 필요합니다.

    a. TrustedHosts 목록에서 도구를 실행할 VM. Hyper-V의 관리자 권한 PowerShell에서 다음 명령을 실행합니다.

            set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate

    b. PowerShell 원격 기능을 사용할 수 있어야 합니다.

            Enable-PSRemoting -Force

## <a name="download-and-extract-the-deployment-planner-tool"></a>Deployment Planner 도구 다운로드 및 추출

1.  최신 버전의 [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)를 다운로드합니다.
이 도구는 .zip 폴더에 패키지되어 있습니다. 동일한 도구가 VMware에서 Azure로 및 Hyper-V에서 Azure로 재해 복구 시나리오를 모두 지원합니다. 이 도구를 Hyper-V에서 보조 사이트로 재해 복구 시나리오에도 사용할 수 있지만 보고서의 Azure 인프라 권장 사항은 무시합니다.

1.  .zip 폴더를 도구를 실행할 Windows Server에 복사합니다. 도구는 Windows Server 2012 R2 또는 Windows Server 2016에서 실행할 수 있습니다. 프로파일링할 VM을 보유하고 있는 Hyper-V 클러스터 또는 Hyper-V 호스트에 연결하려면 서버에 네트워크 액세스 권한이 있어야 합니다. 보호할 Hyper-V 서버와 도구를 실행할 VM의 하드웨어 구성이 동일한 것이 좋습니다. 이렇게 구성하면 도구가 보고하는 달성된 처리량이 Azure Site Recovery에서 복제 중에 달성할 수 있는 실제 처리량과 확실히 일치합니다. 처리량 계산은 서버의 사용 가능한 네트워크 대역폭 및 서버의 하드웨어 구성(CPU, 저장소 등)에 따라 달라집니다. 처리량은 도구가 Azure로 실행되는 서버에서 계산됩니다. 서버의 하드웨어 구성이 Hyper-V 서버와 다른 경우 도구가 보고하는 처리량이 정확하지 않습니다.
권장되는 VM 구성: 8개 vCPU, 16GB RAM, 300GB HDD.

1.  .zip 폴더의 압축을 풉니다.
폴더에 여러 개의 파일과 하위 폴더가 있습니다. 실행 파일은 부모 폴더에 있는 ASRDeploymentPlanner.exe입니다.

예: .zip 파일을 E:\ 드라이브에 복사하고 압축을 풉니다. E:\ASR Deployment Planner_v2.3.zip

E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>최신 버전의 Deployment Planner로 업데이트
이전 버전의 Deployment Planner가 있는 경우 다음 중 하나를 수행합니다.
 * 최신 버전에 프로파일링 수정 프로그램이 없고 현재 버전의 Planner에서 프로파일링을 이미 진행 중인 경우 프로파일링을 계속합니다.
 * 최신 버전에 프로파일링 수정 프로그램이 있는 경우 현재 버전에서 프로파일링을 중지하고 새 버전으로 프로파일링을 다시 시작하는 것이 좋습니다.


  >[!NOTE]
  >
  >새 버전으로 프로파일링을 시작하는 경우 도구가 기존 파일의 프로파일 데이터를 추가하도록 동일한 출력 디렉터리 경로를 전달합니다. 프로파일링된 데이터의 전체 집합을 사용하여 보고서가 생성됩니다. 다른 출력 디렉터리를 전달하는 경우 새 파일이 생성되고 이전의 프로파일링된 데이터는 보고서 생성 시 사용되지 않습니다.
  >
  >각 새 Deployment Planner는 .zip 파일의 누적 업데이트입니다. 최신 파일을 이전 폴더로 복사할 필요가 없습니다. 새 폴더를 만들어 사용할 수 있습니다.

## <a name="version-history"></a>버전 기록
최신 Azure Site Recovery Deployment Planner 도구 버전은 2.4입니다.
가리킵니다 [Azure Site Recovery Deployment Planner 버전 기록](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) 각 업데이트에 추가 된 수정에 대 한 페이지입니다.


## <a name="next-steps"></a>다음 단계
* [Deployment Planner 실행](site-recovery-hyper-v-deployment-planner-run.md).
