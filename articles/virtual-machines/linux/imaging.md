---
title: Azure용 Linux 이미지 만들기 개요
description: Linux VM 이미지를 가져오거나 Azure에서 사용할 새 이미지를 만드는 방법입니다.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 781cc10895f3a77afe71d508c1194b425010ec41
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89319545"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Azure에서 Linux 이미지 가져오기 및 만들기

이 개요에서는 이미징에 대한 기본 개념 및 Azure에서 Linux 이미지를 성공적으로 빌드하고 사용하는 방법에 대해 설명합니다. 사용자 지정 이미지를 Azure로 가져오기 전에 사용 가능한 유형과 옵션에 대해 알고 있어야 합니다.

이 문서에서는 이미지 결정 사항 및 요구 사항, 주요 개념에 대해 설명하므로 이를 따를 수 있으며 사용자 지정 이미지를 사양에 맞게 만들 수 있습니다.

## <a name="difference-between-managed-disks-and-images"></a>관리 디스크와 이미지의 차이점


Azure를 통해 VHD를 플랫폼으로 가져오거나, [Managed Disk](../faq-for-disks.md#managed-disks)로 사용하거나, 이미지의 원본으로 사용할 수 있습니다. 

Azure 관리 디스크는 단일 VHD입니다. 기존 VHD를 가져와서 관리 디스크를 만들거나 처음부터 빈 관리 디스크를 만들 수 있습니다. 디스크를 VM에 연결하여 관리 디스크에서 VM을 만들 수 있지만 VHD는 하나의 VM에서만 사용할 수 있습니다. OS 속성은 수정할 수 없습니다. Azure는 VM을 켜고 해당 디스크를 사용하여 시작하려고 합니다. 

Azure 이미지는 여러 OS 디스크와 데이터 디스크로 구성될 수 있습니다. 관리형 이미지를 사용하여 VM을 만드는 경우 플랫폼에서 이미지의 복사본을 만들고 이를 사용하여 VM을 만들므로 관리형 이미지는 동일한 이미지를 여러 VM에 다시 사용할 수 있도록 지원합니다. 또한 Azure는 [Shared Image Gallery](shared-image-galleries.md)를 통해 글로벌 복제 및 버전 관리와 같은 이미지에 대한 고급 관리 기능을 제공합니다. 



## <a name="generalized-and-specialized"></a>일반화 및 특수화

Azure는 일반화 및 특수화의 두 가지 기본 이미지 유형을 제공합니다. 일반화 및 특수화라는 용어는 원래 Windows 용어이며 Azure로 마이그레이션되었습니다. 이러한 유형은 플랫폼에서 VM을 켤 때 이 VM을 처리하는 방법을 정의합니다. 두 가지 유형에는 모두 장단점 및 필수 구성 요소가 있습니다. 시작하기 전에 필요한 이미지 유형을 알고 있어야 합니다. 아래에는 선택해야 하는 시나리오와 유형이 요약되어 있습니다.

| 시나리오      | 이미지 형식  | 스토리지 옵션 |
| ------------- |:-------------:| :-------------:| 
| 여러 VM에서 사용하도록 구성할 수 있는 이미지를 만들고, 호스트 이름을 설정하며, 관리 사용자를 추가하고, 처음 부팅하는 동안 다른 작업을 수행할 수 있습니다. | 일반화 | Shared Image Gallery 또는 독립 실행형 관리형 이미지 |
| VM 스냅샷 또는 백업에서 이미지를 만듭니다. | 특수화 |Shared Image Gallery 또는 관리 디스크 |
| 여러 VM을 만들도록 구성할 필요가 없는 이미지를 빠르게 만듭니다. |특수화 |공유 이미지 갤러리 |


### <a name="generalized-images"></a>일반화된 이미지

일반화된 이미지는 처음 부팅할 때 설치를 완료해야 하는 이미지입니다. 예를 들어 처음 부팅할 때 호스트 이름, 관리 사용자 및 기타 VM 특정 구성을 설정합니다. 이는 이미지를 여러 번 다시 사용하려는 경우와 만드는 중에 매개 변수를 전달하려는 경우에 유용합니다. Azure 에이전트가 일반화된 이미지에 포함되는 경우 에이전트에서 매개 변수를 처리하고 초기 구성이 완료되었다는 신호를 플랫폼에 다시 보냅니다. 이 프로세스를 [프로비저닝](https://docs.microsoft.com/azure/virtual-machines/linux/provisioning)이라고 합니다. 

프로비저닝하려면 이미지에 구축 프로그램이 포함되어 있어야 합니다. 다음 두 가지 구축 프로그램이 있습니다.
- [Azure Linux 에이전트](../extensions/agent-linux.md)
- [cloud-init](./using-cloud-init.md)

이러한 프로그램은 이미지를 만들기 위한 [필수 구성 요소](./create-upload-generic.md)입니다.


### <a name="specialized-images"></a>특수화된 이미지
완전히 구성되어 있으며 VM 및 특수 매개 변수가 필요하지 않은 이미지입니다. 플랫폼에서 VM을 켜기만 하면 됩니다. 동일한 VNET에서 DNS 충돌을 방지하기 위해 VM 내에서 고유성(예: 호스트 이름 설정)을 처리해야 합니다. 

이러한 이미지에는 프로비저닝 에이전트가 필요하지 않지만, 확장 처리 기능을 사용할 수도 있습니다. Linux 에이전트는 설치할 수 있지만 프로비저닝 옵션을 사용하지 않도록 설정할 수 있습니다. 프로비저닝 에이전트가 필요하지 않더라도 이미지에서 Azure 이미지에 대한 [필수 구성 요소](./create-upload-generic.md)를 충족해야 합니다.


## <a name="image-storage-options"></a>이미지 스토리지 옵션
Linux 이미지를 가져오는 경우 다음 두 가지 옵션을 사용할 수 있습니다.

- 개발 및 테스트 환경에서 간단한 VM을 만들기 위한 관리형 이미지
- 이미지를 규모에 맞게 만들고 공유하기 위한 [Shared Image Gallery](shared-image-galleries.md)


### <a name="managed-images"></a>관리형 이미지

관리형 이미지를 사용하여 여러 VM을 만들 수 있지만 많은 제한이 있습니다. 관리형 이미지는 일반화된 원본(VM 또는 VHD)에서만 만들 수 있습니다. 동일한 지역에서 VM을 만드는 데만 사용할 수 있으며, 구독 및 테넌트 간에 공유할 수 없습니다.

관리형 이미지는 단일 지역 및 구독 내에서 사용하기 위해 몇 개의 간단한 일반화된 이미지가 필요한 개발 및 테스트 환경에 사용할 수 있습니다. 

### <a name="azure-shared-image-gallery-sig"></a>Azure SIG(Shared Image Gallery)

[Shared Image Gallery](shared-image-galleries.md)는 이미지를 규모에 맞게 만들고, 관리하고, 공유하는 데 사용하는 것이 좋습니다. Shared Image Gallery를 사용하면 이미지에 대한 구조와 구성을 구축할 수 있습니다.  

- 일반화된 이미지 및 특수화된 이미지 모두 지원
- 1세대 및 2세대 이미지 모두 지원
- 글로벌 이미지 복제
- 보다 쉽게 관리할 수 있도록 이미지 버전 관리 및 그룹화
- 가용성 영역을 지원하는 지역의 ZRS(영역 중복 스토리지)가 포함된 고가용성 이미지. ZRS는 영역 장애 발생 시 보다 나은 복원력을 제공합니다.
- RBAC를 사용하여 구독 간에, 심지어 AD(Active Directory) 테넌트 간에 공유
- 각 지역에서 이미지 복제본으로 배포 스케일링

대략적으로 SIG를 만들고 다음을 구성합니다.
- 이미지 정의 - 이미지 그룹이 포함되는 컨테이너
- 이미지 버전 - 실제 이미지



## <a name="hyper-v-generation"></a>Hyper-V 세대

Azure는 Hyper-V Gen1(1세대) 및 Gen2(2세대)를 지원하고, Gen2는 최신 세대이며 Gen1을 통해 추가 기능을 제공합니다. 예를 들어 메모리 증가, Intel SGX(Software Guard Extensions) 및 vPMEM(가상화된 영구 메모리) 등이 있습니다. 온-프레미스에서 실행되는 2세대 VM에는 아직 Azure에서 지원되지 않는 몇 가지 기능이 있습니다. 자세한 내용은 특징 및 기능 섹션을 참조하세요. 자세한 내용은 이 [문서](../windows/generation-2.md)를 참조하세요. 추가 기능이 필요한 경우 Gen2 이미지를 만듭니다.

사용자 고유의 이미지를 만들어야 하는 경우에도 해당 이미지에서 [이미지 필수 구성 요소](./create-upload-generic.md)를 충족하는지 확인하고 Azure에 업로드합니다. 배포 관련 요구 사항:


- [CentOS 기반 배포](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar 컨테이너 Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 및 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>다음 단계

[Shared Image Gallery](tutorial-custom-images.md)를 만드는 방법에 대해 알아봅니다.
