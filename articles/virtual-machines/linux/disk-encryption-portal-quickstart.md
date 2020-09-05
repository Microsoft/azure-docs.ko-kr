---
title: Azure Portal을 사용하여 Linux VM 만들기 및 암호화
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Linux 가상 머신을 만들고 암호화하는 방법을 배웁니다.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 563b9c03141357eeacf9a7d890c386faae52c6df
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510704"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 가상 머신 만들기 및 암호화

Azure Portal을 통해 Azure VM(가상 머신)을 만들 수 있습니다. Azure Portal은 VM 및 관련 리소스를 만드는 브라우저 기반 사용자 인터페이스를 제공합니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Ubuntu 18.04 LTS를 실행하는 Linux VM(가상 머신)을 배포하고, 암호화 키 스토리지용 키 자격 증명 모음을 만든 다음, VM을 암호화합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
1. 새로 만들기 페이지의 인기에서 **Ubuntu Server 18.04 LTS**를 선택합니다.
1. 기본 탭의 프로젝트 세부 정보에서 올바른 구독이 선택되어 있는지 확인합니다.
1. "리소스 그룹"에 대해 **새로 만들기**를 선택합니다. 이름으로 *myResourceGroup*을 입력하고 **확인**을 선택합니다.
1. **가상 머신 이름**에 대해 *MyVM*을 입력합니다.
1. **지역**에 대해 *(US) 미국 동부*를 선택합니다.
1. **크기**가 *표준 D2s v3*인지 확인합니다.
1. **관리자 계정**에서 **인증 유형**으로 *암호*를 선택합니다. 사용자 이름 및 암호를 입력합니다.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-linux-vm-creation.png" alt-text="Linux VM 만들기 화면":::

    > [!WARNING]
    > "디스크" 탭은 **디스크 옵션**의 "암호화 유형" 필드를 제공합니다. 이 필드는 Azure Disk Encryption이 **아닌** [Managed Disks](managed-disks-overview.md) + CMK에 대한 암호화 옵션을 지정하는 데 사용됩니다.
    >
    > 혼동을 피하기 위해 이 자습서를 완료하는 동안 *디스크* 탭을 완전히 건너뛰는 것이 좋습니다.

1. “관리” 탭을 선택하고 진단 스토리지 계정이 있는지 확인합니다. 스토리지 계정이 없는 경우 *새로 만들기*를 선택하고, 스토리지 계정 이름을 *myStorageAccount*로 지정하고, "확인"을 선택합니다.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="리소스 그룹 만들기 화면":::

1. “검토 + 만들기”를 클릭합니다.
1. **가상 머신 만들기** 페이지에서 만들려는 VM의 세부 정보를 볼 수 있습니다. 준비가 되면 **만들기**를 선택합니다.

VM 배포에는 몇 분 정도 걸립니다. 배포가 완료되면 다음 섹션으로 이동합니다.

## <a name="encrypt-the-virtual-machine"></a>가상 머신 암호화

1. VM 배포가 완료되면 **리소스로 이동**을 선택합니다.
1. 왼쪽 사이드바에서 **디스크**를 선택합니다.
1. 상단 표시줄에서 **추가 설정**을 선택합니다.
1. **암호화 설정** > **암호화할 디스크**에서 **OS 및 데이터 디스크**를 선택합니다.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="디스크 및 암호화 선택":::

1. **암호화 설정**에서 **암호화를 위한 Key Vault 및 키 선택**을 선택합니다.
1. **Azure Key Vault에서 키 선택** 화면에서 **새로 만들기**를 선택합니다.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="디스크 및 암호화 선택":::

1. **Key Vault 및 키**의 왼쪽에서 **키를 선택하려면 클릭**을 선택합니다.
1. **Azure Key Vault에서 키 선택**의 **Key Vault** 필드에서 **새로 만들기**를 선택합니다.
1. **키 자격 증명 모음 만들기** 화면에서 리소스 그룹이 *myResourceGroup*인지 확인하고 키 자격 증명 모음에 이름을 지정합니다.  Azure의 모든 키 자격 증명 모음에는 고유한 이름이 있어야 합니다.
1. **액세스 정책** 탭에서 **볼륨 암호화를 위한 Azure Disk Encryption** 상자를 확인합니다.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="디스크 및 암호화 선택":::

1. **검토 + 만들기**를 선택합니다.  
1. 키 자격 증명 모음이 유효성 검사를 통과한 후 **만들기**를 선택합니다. 그러면 **Azure Key Vault에서 키 선택** 화면으로 돌아갑니다.
1. **키** 필드를 비워 두고 **선택**을 누릅니다.
1. 암호화 화면 위쪽에서 **저장**을 클릭합니다. 팝업에서 VM이 재부팅됨을 경고합니다. **예**를 클릭합니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 가상 머신 및 모든 관련 리소스가 더 이상 필요 없는 경우 삭제해도 됩니다. 이렇게 하려면 가상 머신의 리소스 그룹을 선택하고, 삭제를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 암호화 키에 사용 가능한 Key Vault를 만들고, 가상 머신을 만들고, 암호화에 대해 가상 머신을 활성화했습니다.  

> [!div class="nextstepaction"]
> [Azure Disk Encryption 개요](disk-encryption-overview.md)
