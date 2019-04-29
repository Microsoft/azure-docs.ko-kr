---
title: Windows AWS VM을 Azure로 이동 | Microsoft Docs
description: AWS(Amazon Web Services) EC2 Windows 인스턴스를 Azure 가상 머신으로 이동합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: d6a4c5b2b6d9818dffdb1c1fee8c4c0df7cad77c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456209"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>AWS(Amazon Web Services)에서 Azure 가상 머신으로 Windows VM 이동

워크로드를 호스팅하기 위해 Azure 가상 머신을 평가하는 경우 기존 AWS(Amazon Web Services) EC2 Windows VM 인스턴스를 내보낸 다음 VHD(가상 하드 디스크)를 Azure로 업로드할 수 있습니다. VHD를 업로드하면 VHD에서 Azure로 새 VM을 만들 수 있습니다. 

이 문서에서는 단일 VM을 AWS에서 Azure로 이동하는 방법에 대해 설명합니다. VM을 AWS에서 Azure로 대규모로 이동하려면 [Azure Site Recovery를 사용하여 AWS(Amazon Web Services)의 가상 머신을 Azure로 마이그레이션](../../site-recovery/site-recovery-migrate-aws-to-azure.md)을 참조하세요.

## <a name="prepare-the-vm"></a>VM 준비 
 
일반화된 VHD 및 특수한 VHD 모두를 Azure에 업로드할 수 있습니다. 각 유형은 AWS에서 내보내기 전에 VM을 준비해야 합니다. 

- **일반화된 VHD** - 일반화된 VHD에는 Sysprep을 사용하여 제거된 모든 개인 계정 정보가 포함되어 있습니다. 새 VM을 만드는 이미지로 VHD를 사용하려는 경우 다음을 수행해야 합니다. 
 
    * [Windows VM을 준비합니다](prepare-for-upload-vhd-image.md).  
    * Sysprep을 사용하여 가상 머신을 일반화합니다.  

 
- **특수한 VHD** - 특수한 VHD는 사용자 계정, 애플리케이션 및 원본 VM의 다른 상태 데이터를 유지 관리합니다. 새 VM을 만드는데 VHD를 그대로 사용하려는 경우 다음 단계가 완료되었는지 확인합니다.  
    * [Azure에 업로드할 Windows VHD를 준비합니다](prepare-for-upload-vhd-image.md). Sysprep을 사용하여 VM을 일반화하지 **마십시오**. 
    * 모든 게스트 가상화 도구 및 VM에 설치된 에이전트를 제거합니다(예: VMware 도구). 
    * VM이 DHCP를 통해 해당 IP 주소 및 DNS 설정을 가져오도록 구성되었는지 확인합니다. 이렇게 하면 서버를 시작할 때 VNet 내의 IP 주소를 가져옵니다.  


## <a name="export-and-download-the-vhd"></a>VHD 내보내기 및 다운로드 

EC2 인스턴스를 Amazon S3 버킷의 VHD로 내보냅니다. Amazon 설명서 [VM Import/Export를 사용하여 인스턴스를 VM으로 내보내기](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) 항목의 단계를 수행하고, [create-instance-export-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) 명령을 실행하여 EC2 인스턴스를 VHD 파일로 내보냅니다. 

내보낸 VHD 파일은 지정한 Amazon S3 버킷에 저장됩니다. 기본 구문은 아래에 VHD를 내보내기에 대 한 자리 표시자 텍스트에 대체 하면 \<대괄호 > 정보입니다.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

VHD를 내보냈으면 [S3 버킷에서 개체를 다운로드하려면 어떻게 해야 합니까?(영문)](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html)의 지침에 따라 S3 버킷에서 VHD 파일을 다운로드합니다. 

> [!IMPORTANT]
> AWS에서는 VHD 다운로드에 대한 데이터 전송 요금을 청구합니다. 자세한 내용은 [Amazon S3 요금](https://aws.amazon.com/s3/pricing/)을 참조하세요.


## <a name="next-steps"></a>다음 단계

이제 VHD를 Azure에 업로드하고 새 VM을 만들 수 있습니다. 

- 내보내기 전에 **일반화**하도록 원본에서 Sysprep을 실행한 경우 [일반화된 VHD 업로드 및 이 디스크를 사용하여 Azure에서 새 VM 만들기](upload-generalized-managed.md)를 참조하세요.
- 내보내기 전에 Sysprep을 실행하지 않은 경우 VHD는 **특수한 디스크**로 간주됩니다. [Azure에 특수한 VHD 업로드 및 새 VM 만들기](create-vm-specialized.md)를 참조하세요.

 
