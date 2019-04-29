---
title: Azure로 VMware VM 재해 복구를 위한 Azure Site Recovery Deployment Planner 정보 | Microsoft Docs
description: Azure로 VMware VM 재해 복구를 위한 Azure Site Recovery Deployment Planner에 대해 알아봅니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mayg
ms.openlocfilehash: 0597f185df35a92696ed9287d23778180319b3de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035795"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>VMware에서 Azure로의 Azure Site Recovery Deployment Planner 정보
이 문서는 VMware에서 Azure로의 프로덕션 배포를 위한 Azure Site Recovery의 Deployment Planner 사용자 가이드입니다.

## <a name="overview"></a>개요

Azure Site Recovery를 사용하여 VMware VM(가상 머신) 보호를 시작하기 전에 원하는 RPO(복구 지점 목표)를 충족하도록 일별 데이터 변경률에 따라 충분한 대역폭을 할당합니다. 적절한 수의 구성 서버와 프로세스 서버를 온-프레미스에 배포해야 합니다.

또한 올바른 유형 및 수의 대상 Azure Storage 계정도 만들어야 합니다. 시간이 지남에 따라 사용량이 증가하므로 원본 프로덕션 서버상의 증가를 고려하여 표준 또는 Premium Storage 계정을 만듭니다. 작업 특성(예: 초당 읽기/쓰기 I/O 작업 수[IOPS] 또는 데이터 변동) 및 Site Recovery에 따라 VM별로 저장소 유형을 선택합니다.

 Site Recovery Deployment Planner는 Hyper-V에서 Azure로 및 VMware에서 Azure로의 재해 복구 시나리오를 위한 명령줄 도구입니다. 이 도구를 통해 VMware VM을 원격으로 프로파일링하여(프로덕션에 전혀 영향을 미치지 않음) 성공적인 복제 및 테스트 장애 조치를 위한 대역폭 및 저장소 요구 사항을 파악할 수 있습니다. Site Recovery 구성 요소를 온-프레미스에 설치하지 않고도 도구를 실행할 수 있습니다. 달성된 처리량에 대한 정확한 결과를 얻으려면 프로덕션 배포의 첫 번째 단계 중 하나로 배포해야 하는 Site Recovery 구성 서버의 최소 요구 사항이 충족되는 Windows Server에서 Deployment Planner를 실행합니다.

이 도구는 다음과 같은 세부 정보를 제공합니다.

**호환성 평가**

* 디스크 수, 디스크 크기, IOPS, 변동 및 부팅 유형(EFI/BIOS) 및 OS 버전에 기반한 VM 적합성 평가

**네트워크 대역폭 요구 사항 및 RPO 평가**

* 델타 복제에 필요한 예상 네트워크 대역폭
* Site Recovery를 통해 온-프레미스에서 Azure로 가져올 수 있는 처리량
* 지정된 시간 내에 초기 복제를 완료하는 데 예상되는 대역폭에 따라 일괄 처리할 VM의 번호
* 지정된 대역폭에서 수행할 수 있는 RPO
* 낮은 대역폭을 프로비전하는 경우 원하는 RPO에 미치는 영향

**Azure 인프라 요구 사항**

* 각 VM에 대한 스토리지 유형(표준 또는 Premium Storage 계정) 요구 사항
* 복제를 위해 설정할 표준 및 Premium Storage 계정의 총 수
* Storage 지침에 따른 스토리지 계정 명명 제안
* 모든 VM에 대한 저장소 계정 배치
* 구독에 대한 테스트 장애 조치 또는 장애 조치 이전에 설정할 Azure 코어의 수
* 각 온-프레미스 VM에 대한 Azure VM 권장 크기

**온-프레미스 인프라 요구 사항**
* 온-프레미스에 배포하는 데 필요한 구성 서버 및 프로세스 서버의 수

**Azure에 대한 예상 재해 복구 비용**
* Azure로 재해 복구하는 데 예상되는 총 비용: 계산, 스토리지, 네트워크 및 Site Recovery 라이선스 비용
* VM별 자세한 비용 분석


>[!IMPORTANT]
>
>시간이 지남에 따라 사용량이 증가할 수 있으므로 앞에서 언급한 모든 도구 계산은 작업 특성에 30% 증가율이 있다고 가정하여 수행됩니다. 또한 계산은 읽기/쓰기 IOPS 및 변동과 같은 모든 프로파일링 메트릭의 95번째 백분위 수 값을 사용합니다. 증가율과 백분위 수 계산은 모두 구성할 수 있습니다. 증가율에 대해 더 자세히 알아보려면 "증가율 고려 사항" 섹션을 참조하세요. 백분위수 값에 대한 자세한 내용은 "계산에 사용되는 백분위수 값" 섹션을 참조하세요.
>

## <a name="support-matrix"></a>지원 매트릭스

| | **VMware에서 Azure로** |**Hyper-V에서 Azure로**|**Azure 간**|**Hyper-V에서 보조 사이트로**|**VMware에서 보조 사이트로**
--|--|--|--|--|--
지원되는 시나리오 |예|예|아닙니다.|예*|아닙니다.
지원되는 버전 | vCenter 6.7, 6.5, 6.0 또는 5.5| Windows Server 2016, Windows Server 2012 R2 | 해당 없음 |Windows Server 2016, Windows Server 2012 R2|해당 없음
지원되는 구성|vCenter, ESXi| Hyper-V 클러스터, Hyper-V 호스트|해당 없음|Hyper-V 클러스터, Hyper-V 호스트|해당 없음|
Site Recovery Deployment Planner의 실행 인스턴스당 프로파일링할 수 있는 서버 수 |한 개(하나의 vCenter Server 또는 하나의 ESXi 서버에 속하는 VM을 한 번에 프로파일링할 수 있습니다.)|여러 개(여러 호스트 또는 호스트 클러스터 간에 VM을 한 번에 프로파일링할 수 있음)| 해당 없음 |여러 개(여러 호스트 또는 호스트 클러스터 간에 VM을 한 번에 프로파일링할 수 있음)| 해당 없음

*이 도구는 주로 Hyper-V에서 Azure로 재해 복구 시나리오용입니다. Hyper-V를 보조 사이트 재해 복구에 사용하는 경우, 원본 쪽 권장 사항(예: 필요한 네트워크 대역폭, 원본 Hyper-V 서버 각각에 필요한 사용 가능한 저장소 공간 및 초기 복제 일괄 처리 번호/일괄 처리 정의)을 파악하는 데만 사용할 수 있습니다. 보고서의 Azure 권장 사항 및 비용은 무시하세요. 또한 Hyper-V에서 보조 사이트로의 재해 복구 시나리오에는 처리량 가져오기 작업을 적용할 수 없습니다.

## <a name="prerequisites"></a>필수 조건
이 도구에는 두 가지 주요 단계, 즉 프로파일링과 보고서 생성 단계가 있습니다. 또한 처리량만 계산하는 세 번째 옵션도 있습니다. 다음 표에는 프로파일링 및 처리량 측정이 시작되는 서버에 대한 요구 사항이 나와 있습니다.

| 서버 요구 사항 | 설명|
|---|---|
|프로파일링 및 처리량 측정| <ul><li>운영 체제: Windows Server 2016 또는 Windows Server 2012 R2<br>(적어도 [구성 서버에 대한 크기 권장 사항](https://aka.ms/asr-v2a-on-prem-components)을 일치하는 것이 이상적)</li><li>머신 구성: 8개 vCPU, 16GB RAM, 300GB HDD</li><li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Visual Studio 2012용 Visual C++ 재배포 가능 패키지](https://aka.ms/vcplusplus-redistributable)</li><li>이 서버에서 Azure에 대한 인터넷 액세스</li><li>Azure Storage 계정</li><li>서버에 대한 관리자 액세스</li><li>최소 100GB의 사용 가능한 디스크 공간(각각 평균 3개의 디스크가 있는 1,000개의 VM 가정하에 30일 동안 프로파일링)</li><li>VMware vCenter 통계 수준 설정은 1 이상일 수 있습니다.</li><li>VCenter 포트 허용(기본값 443): Site Recovery Deployment Planner는 이 포트를 사용하여 vCenter 서버/ESXi 호스트에 연결합니다.</ul></ul>|
| 보고서 생성 | Excel 2013 이상이 설치된 Windows PC 또는 Windows Server<li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Visual Studio 2012용 Visual C++ 재배포 가능 패키지](https://aka.ms/vcplusplus-redistributable)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)은 VM의 최신 VM 구성 정보를 가져오기 위해 보고서 생성 명령에 사용자 옵션을 전달하는 경우에만 필요합니다. Deployment Planner는 vCenter 서버에 연결됩니다. vCenter 포트(기본값 443)를 vCenter 서버에 연결하도록 허용합니다.</li>|
| 사용자 권한 | 프로파일링 중에 VMware vCenter 서버/VMware vSphere ESXi 호스트에 액세스하는 데 사용되는 사용자 계정에 대한 읽기 전용 권한 |

> [!NOTE]
>
>이 도구는 VMDK 및 RDM 디스크를 갖춘 VM만 프로파일링할 수 있습니다. iSCSI 또는 NFS 디스크가 있는 VM은 프로파일링할 수 없습니다. Site Recovery는 VMware 서버용 iSCSI 및 NFS 디스크를 지원합니다. Deployment Planner는 게스트 내부에 있지 않고 vCenter 성능 카운터만 사용하여 프로파일링하므로 이러한 디스크 유형은 도구에서 식별할 수 없습니다.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Deployment Planner 도구 다운로드 및 추출
1. 최신 버전의 [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)를 다운로드합니다.
이 도구는 .zip 폴더에 패키지되어 있습니다. 현재 버전의 도구는 Azure 시나리오를 위해 VMware만 지원합니다.

2. 도구를 실행하려는 Windows Server에 zip 폴더를 복사합니다.
서버가 프로파일링할 VM을 보유하는 vCenter 서버/vSphere ESXi 호스트에 연결할 네트워크 액세스 권한을 가지고 있는 경우 Windows Server 2012 R2에서 도구를 실행할 수 있습니다. 그러나 하드웨어 구성이 [구성 서버 크기 조정 지침](https://aka.ms/asr-v2a-on-prem-components)을 충족하는 서버에서 도구를 실행하는 것이 좋습니다. 이미 Site Recovery 구성 요소를 온-프레미스에 배포한 경우 구성 서버에서 도구를 실행합니다.

    도구를 실행하는 서버에 구성 서버(기본 제공 프로세스 서버가 있는)와 같은 하드웨어 구성이 있는 것이 좋습니다. 그러한 구성이 있으면 도구가 보고하는 달성된 처리량이 Site Recovery에서 복제 중에 달성할 수 있는 실제 처리량과 확실히 일치합니다. 처리량 계산은 서버에서 사용 가능한 네트워크 대역폭 및 서버의 하드웨어 구성(예: CPU 및 저장소)에 따라 달라집니다. 다른 서버에서 도구를 실행하는 경우 해당 서버에서 Azure로의 처리량이 계산됩니다. 또한 서버의 하드웨어 구성은 구성 서버와 다를 수 있으므로 도구가 보고하는 달성된 처리량이 정확하지 않을 수 있습니다.

3. .zip 폴더의 압축을 풉니다.
폴더에 여러 개의 파일과 하위 폴더가 있습니다. 실행 파일은 부모 폴더에 있는 ASRDeploymentPlanner.exe입니다.

    예: .zip 파일을 E:\ 드라이브에 복사하고 압축을 풉니다.
    E:\ASR Deployment Planner_v2.3.zip

    E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="update-to-the-latest-version-of-deployment-planner"></a>최신 버전의 Deployment Planner로 업데이트
이전 버전의 Deployment Planner가 있는 경우 다음 중 하나를 수행합니다.
 * 최신 버전에 프로파일링 수정 프로그램이 없고 현재 버전의 Planner에서 프로파일링을 이미 진행 중인 경우 프로파일링을 계속합니다.
 * 최신 버전에 프로파일링 수정 프로그램이 포함되어 있는 경우 현재 버전에 대한 프로파일링을 중지하고 새 버전으로 프로파일링을 다시 시작하는 것이 좋습니다.


 >[!NOTE]
 >
 >새 버전으로 프로파일링을 시작하는 경우 도구가 기존 파일의 프로파일 데이터를 추가하도록 동일한 출력 디렉터리 경로를 전달합니다. 프로파일링된 데이터의 전체 집합이 보고서를 생성하는 데 사용됩니다. 다른 출력 디렉터리를 전달하는 경우 새 파일이 만들어지고 프로파일링된 이전 데이터는 보고서를 생성하는 데 사용되지 않습니다.
 >
 >새 Deployment Planner 버전 각각은 .zip 파일의 누적 업데이트입니다. 최신 파일을 이전 폴더로 복사할 필요가 없습니다. 새 폴더를 만들어 사용할 수 있습니다.


## <a name="version-history"></a>버전 기록
최신 Site Recovery Deployment Planner 도구 버전은 2.4입니다.
각 업데이트에 추가된 수정 사항은 [Site Recovery Deployment Planner 버전 기록](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
[Site Recovery Deployment Planner 실행](site-recovery-vmware-deployment-planner-run.md)
