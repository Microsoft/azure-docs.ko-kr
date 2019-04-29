---
title: Azure DevTest Lab 정보 | Microsoft Docs
description: DevTest Lab에서 Azure 가상 머신을 쉽게 만들고 관리하고 모니터링할 수 있는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561458"
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs 정보
Azure DevTest Labs 팀에서 개발자가 효율적으로 자체 관리 가상 머신 (Vm) 및 PaaS 리소스 승인을 기다리지 않고을 수 있습니다.

DevTest Labs 랩으로 구성 된 미리 구성 된 자료 또는 Azure Resource Manager 템플릿을 만듭니다. 모든 필요한 도구 및 소프트웨어 환경을 만드는 데 사용할 수 있는 경우 이러한 몇 시간에서 며칠 몇 분 안에 환경을 만들 수 있습니다.

DevTest Labs를 사용 하 여 다음 작업을 수행 하 여 응용 프로그램의 최신 버전을 테스트할 수 있습니다.

- 재사용 가능 템플릿과 아티팩트를 사용 하 여 Windows 및 Linux 환경을 빠르게 프로 비전 합니다.
- 배포 파이프라인과 DevTest Lab을 쉽게 통합하여 주문형 환경으로 프로비전할 수 있습니다.
- 여러 테스트 에이전트를 프로 비전 하 여 테스트 부하를 수직 확장 및 교육 및 데모용 미리 프로 비전 된 환경을 만듭니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>기능
DevTest Labs Vm을 사용 하는 개발자에 게 다음 기능을 제공 합니다.

- 5 개 미만의 간단한 단계를 수행 하 여 Vm을 신속 하 게 만듭니다.
- 구성, 승인 및 팀 리더 또는 central에서 권한을 부여 하는 VM 기본 항목의 엄선된 된 목록에서 선택 IT.
- 모든 소프트웨어 및 도구가 이미 설치 되어 있는 미리 만든된 사용자 지정 이미지에서 Vm을 만듭니다. 
- 기본적으로 사용자 지정 이미지 Vm을 만들 때 설치 된 소프트웨어의 최신 빌드를 사용 하 여 결합 된 수식에서 Vm을 만듭니다. 
- 프로 비전 하는 후 Vm에 배포 된 확장 된 아티팩트를 설치 합니다.
- 자동 종료를 설정 하 고 Vm에 대 한 일정을 자동으로 시작 합니다.
- 만들기 프로세스를 거치지 않고 미리 생성된 된 VM을 클레임 합니다.

DevTest Labs PaaS 환경을 사용 하는 개발자에 게 다음 기능을 제공 합니다.

- 세 대 보다 적은 간단한 단계를 수행 하 여 PaaS 환경을 빠르게 만들 리소스 관리자를 사용 합니다.
- 큐 레이트 구성 되며 중앙을 팀 리더에 게 권한을 부여 하는 Resource Manager 템플릿 목록에서 선택 IT.
- 랩의 컨텍스트 내에서 Azure를 탐색 하려면 Resource Manager 템플릿을 사용 하 여 빈 리소스 그룹 (샌드박스)을 스핀업 합니다.

DevTest Labs 있습니다 중앙 IT 낭비 제어 리소스에 대 한 비용을 최적화 하 고 다음 작업을 수행 하 여 예산 내에서 유지 합니다.  

- Vm 자동 종료 및 자동 시작 일정을 설정합니다.
- 사용자가 만들 수 있는 Vm 수에 정책을 설정 합니다.
- Vm의 크기 및 사용자가 선택할 수 있는 갤러리 이미지에서 정책을 설정 합니다.
- 비용 추적 및 실습실에 목표를 설정 합니다.
- 필요한 조치를 취할 수 있도록 실습에 대 한 높은 예상된 비용에서 알림을 받기.

DevTest Lab 만들기, 구성 및 클라우드에서 환경을 관리 하는 다음과 같은 이점을 제공 합니다.

## <a name="cost-control-and-governance"></a>비용된 제어 및 거 버 넌 스
DevTest Labs 쉽게 비용을 제어 함으로써 다음 작업을 수행할 수 있습니다.

- [랩 정책을 설정](devtest-lab-get-started-with-lab-policies.md), 사용자 당 또는 랩 당 Vm 수와 같은 합니다. 
- 만들 [자동으로 종료 하는 정책을](devtest-lab-set-lab-policy.md) Vm을 시작 합니다.
- Vm 및 PaaS 리소스에 비용을 추적 내로 유지 하도록 랩 내에서 등록 스핀 [예산의](devtest-lab-configure-cost-management.md)합니다.
- 외부에 리소스를 스핀업 없습니다 있도록 랩의 컨텍스트 내에서 유지 합니다.

## <a name="quickly-get-to-ready-to-test"></a>빠른 테스트 준비
DevTest Labs를 사용 하면 응용 프로그램 개발과 테스트 하는 데 필요한 모든 것을 갖추게 하는 미리 프로 비전 된 환경을 만들 수 있습니다. 방금 [환경을 요청 하 고](devtest-lab-add-claimable-vm.md) 응용 프로그램의 마지막 빌드가 설치 되어 있는 한 시작 작업 합니다. 또는 더 빠르고 간결한 환경 만들기에 대 한 컨테이너를 사용 합니다.

## <a name="create-once-use-everywhere"></a>한 번 만들어서 어디에나 사용
캡처 및 PaaS를 공유 [환경 템플릿](devtest-lab-create-environment-from-arm.md) 하 고 [아티팩트](add-artifact-repository.md) 팀 또는 조직 내에서-소스 제어에서 모든-개발자 만들기 및 테스트 환경을 쉽게 합니다.

## <a name="worry-free-self-service"></a>걱정할 필요가 없는 셀프 서비스
DevTest Labs 사용 하면 개발자와 테스터를 쉽고 빠르게 [IaaS Vm을 만들](devtest-lab-add-vm.md) 하 고 [PaaS 리소스](devtest-lab-create-environment-from-arm.md) 미리 구성 된 리소스 집합을 사용 하 여 합니다.

## <a name="use-iaas-and-paas-resources"></a>IaaS 및 PaaS 리소스를 사용 합니다. 
개발자가 회전 시킬 수 있습니다도 Azure Service Fabric 클러스터와 같은 PaaS 리소스를 SharePoint 팜 및 Azure App Service의 Web Apps 기능 Resource Manager 템플릿을 사용 하 여 합니다. 시작 하려면 PaaS에서 랩에서 템플릿을 사용 합니다 [공용 환경 리포지토리](devtest-lab-configure-use-public-environments.md) 또는 [사용자 고유의 Git 리포지토리에 랩 연결](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). 또한 예산 내로 유지 하도록 이러한 리소스에 비용을 추적할 수 있습니다.

## <a name="integrate-with-your-existing-toolchain"></a>기존 도구 체인과 통합
미리 작성 된 플러그 인 또는 API를 개발/테스트 환경을 프로 비전을 선호 하에서 직접 사용 하 여 [CI (지속적인 통합) 도구](devtest-lab-integrate-ci-cd-vsts.md), IDE (통합 개발 환경)에서 또는 자동화 된 릴리스 파이프라인. 또한 포괄적인 명령줄 도구를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- DevTest Labs에 대 한 자세한 내용은 참조 하세요 [DevTest Labs 개념](devtest-lab-concepts.md)합니다.
- 단계별 지침이 포함 된 연습을 참조 하세요. [자습서: Azure DevTest Labs를 사용 하 여 랩을 설정](tutorial-create-custom-lab.md)합니다.