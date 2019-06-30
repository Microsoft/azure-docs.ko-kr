---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 5884bb5dc389e6abfd4fa23d28dd71ecd16ff423
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67347116"
---
조직이 클라우드로 마이그레이션하고 배포 일관성을 확인 수 있게 하는 표준화 된 가상 머신 (VM) 이미지입니다. 이미지에는 일반적으로 미리 정의 된 보안 및 구성 설정 및 필수 소프트웨어가 포함 됩니다. 시간, 인프라 및 설치 프로그램을 고유한 이미징 파이프라인을 설정 하려면 필요 하지만 Azure VM 이미지 작성기를 사용 하 여 이미지를 설명 하는 간단한 구성을 제공, 서비스에 제출 및 이미지를 작성 및 배포 합니다.
 
Azure VM 이미지 작성기 (Azure 이미지 작성기)를 사용 하는 Windows 또는 Linux 기반 Azure Marketplace 이미지, 기존 사용자 지정 이미지 또는 Red Hat Enterprise Linux (RHEL) ISO 사용 하 여 시작 하 고 고유한 사용자 지정 추가 시작할 수 있습니다. Image Builder 빌드되므로 [HashiCorp Packer](https://packer.io/), 기존 Packer 셸 프로 비 저 너 스크립트를 가져올 수도 있습니다. 관리 되는 이미지로 (virtual-machines-common-shared-image-galleries.md) Azure 공유 이미지 갤러리 또는 VHD에 호스트 된 이미지를 하려는 지정할 수 있습니다.

> [!IMPORTANT]
> Azure 이미지 작성기는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="preview-features"></a>미리 보기 기능

미리 보기이 기능이 지원 됩니다.

- 회사의 구성을 확인 하 고 최소 보안을 포함 하 고 요구에 맞게 추가로 사용자 지정 하는 부서를 허용 하는 골든 기준 이미지를 생성 합니다.
- 기존 이미지의 패치 이미지 작성기 하면 지속적으로 기존 사용자 지정 이미지를 패치할 수 있습니다.
- Azure 공유 이미지 갤러리와 통합을 사용 하면 버전을 배포 하려면 및 확장성을 전역적으로 이미지 및 이미지 관리 시스템을 제공 합니다.
- 기존 이미지를 사용 하 여 통합 파이프라인, 방금 이미지 작성기를 사용 하 여 파이프라인에서 호출 빌드하거나 간단한 미리 보기 이미지 작성기 Azure DevOps 작업을 사용 합니다.
- 기존 이미지 사용자 지정 파이프라인을 Azure로 마이그레이션하십시오. 이미지를 사용자 지정 하 여 기존 스크립트, 명령 및 프로세스를 사용 합니다.
- Red Hat Bring Your Own Subscription 지원을 사용 합니다. 적합 한, 사용 하지 않는 Red Hat 구독을 사용 하 여 사용에 대 한 Red Hat Enterprise 이미지를 만듭니다.
- VHD 형식의 이미지를 만듭니다.
 

## <a name="regions"></a>Regions
Azure 이미지 작성기 서비스는 이러한 지역에서 미리 보기로 제공 됩니다. 이러한 지역 외부에서 이미지를 배포할 수 있습니다.
- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부 2

## <a name="os-support"></a>OS 지원
AIB은 Azure Marketplace 기본 OS 이미지를 지원 해야 합니다.
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019


## <a name="how-it-works"></a>작동 방법


![Azure 이미지 작성기의 개념적 그리기](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure 이미지 작성기는 Azure 리소스 공급자에서 액세스할 수 있는 완전히 관리 되는 Azure 서비스. Azure 이미지 작성기 프로세스에 세 가지 주요 부분이: 원본, 사용자 지정 및 배포, 서식 파일에 표시 됩니다. 아래 다이어그램에는 해당 속성의 일부를 사용 하 여 구성 요소를 보여 줍니다. 
 


**이미지 작성기 프로세스** 

![Azure 이미지 작성기 프로세스의 개념적 그리기](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. .Json 파일로 이미지 템플릿을 만듭니다. 이.json 파일의 이미지 원본, 사용자 지정 및 배포에 대 한 정보를 포함합니다. 여러 예제에서 가지는 [Azure 이미지 작성기 GitHub 리포지토리](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)합니다.
1. 서비스에 제출, 그러면 이미지 템플릿을 아티팩트 지정 하는 리소스 그룹에 만들어집니다. 이미지 작성기는 백그라운드에서 소스 이미지 또는 ISO 및 필요에 따라 스크립트를 다운로드 합니다. 이러한 형식으로 구독에서 자동으로 만들어지는 별도 리소스 그룹에 저장 됩니다. IT_\<DestinationResourceGroup>_\<TemplateName>. 
1. 이미지 템플릿을 만든 후에 다음 이미지를 빌드할 수 있습니다. 백그라운드에서 이미지 작성기 사용 하 여 템플릿 및 소스 파일을 만듭니다 VM, 네트워크 및 저장소는 IT_\<DestinationResourceGroup > _\<TemplateName > 리소스 그룹입니다.
1. 이미지 만들기의 일환으로, 이미지 작성기는 이미지를 배포 템플릿에 따라 그런 다음 삭제를 IT_의 추가 리소스\<DestinationResourceGroup > _\<TemplateName >에 대해 생성 된 리소스 그룹 프로세스입니다.


## <a name="permissions"></a>권한

관리 되는 이미지 또는 공유 이미지 갤러리에 이미지를 배포 하려면 Azure VM 이미지 작성기를 허용 하려면 "Azure 가상 머신 이미지 작성기" 서비스에 대 한 '참가자' 권한을 제공 해야 합니다 (앱 ID: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) 리소스 그룹입니다. 

기존 사용자 지정 관리 되는 이미지 또는 이미지 버전을 사용 하는 경우 Azure 이미지 작성기 최소한 한 해당 리소스 그룹에 '판독기' 권한이 필요 합니다.

Azure CLI를 사용 하 여 액세스를 할당할 수 있습니다.

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

서비스 계정이 없는 경우 역할 할당을 추가 하는 구독이 리소스 공급자에 아직 등록 하지 않았다면는 의미할 수 있습니다.


## <a name="costs"></a>비용
몇 가지 계산, 네트워킹 및 저장소 비용 만들기, 빌드 및 Azure 이미지 작성기를 사용 하 여 이미지를 저장 하는 경우 발생 합니다. 이러한 비용은 수동으로 사용자 지정 이미지를 만들기에서 발생 하는 비용와 비슷합니다. 리소스에 대 한에 Azure 요금이 청구 됩니다. 

이미지 만들기 프로세스 중 파일이 다운로드 되 고 저장 된 `IT_<DestinationResourceGroup>_<TemplateName>` 작은 저장소 비용이 발생 하는 리소스 그룹입니다. 이 유지 하지 않으려는 f 이미지 빌드 후 이미지 템플릿을 삭제 합니다.
 
이미지 작성기는 VM에 대 한 필요한 네트워킹 및 D1v2 VM 크기와 저장소를 사용 하 여 VM을 만듭니다. 이러한 리소스는 빌드 프로세스의 기간 동안 지속 및 Image Builder에서 이미지 만들기를 완료 한 후 삭제 됩니다. 
 
Azure 이미지 작성기는 네트워크 송신 요금이 발생할 수 있음에 선택한 지역에 이미지를 배포 합니다.
 
## <a name="next-steps"></a>다음 단계 
 
Azure 이미지 작성기를 사용해 구성 문서를 참조 하세요 [Linux](../articles/virtual-machines/linux/image-builder.md) 하거나 [Windows](../articles/virtual-machines/windows/image-builder.md) 이미지입니다.
 
 
