---
title: Azure에서 Linux VHD 다운로드 | Microsoft Docs
description: Azure CLI 및 Azure Portal을 사용하여 Linux VHD를 다운로드합니다.
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
ms.openlocfilehash: f72d49a3ab204ce64eb89d0f05630b640c138e0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61390231"
---
# <a name="download-a-linux-vhd-from-azure"></a>Azure에서 Linux VHD 다운로드

이 문서에서는 Azure CLI 및 Azure Portal을 사용하여 Azure에서 Linux VHD(가상 하드 디스크) 파일을 다운로드하는 방법을 알아봅니다. 

아직 수행하지 않았다면 [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2)를 설치합니다.

## <a name="stop-the-vm"></a>VM을 중지합니다.

실행 중인 VM에 연결된 경우 Azure에서 VHD는 다운로드할 수 없습니다. VHD를 다운로드하려면 VM을 중지해야 합니다. VHD를 [이미지](tutorial-custom-images.md)로 사용하여 새 디스크를 사용하는 다른 VM을 만들려는 경우 파일에 포함된 운영 체제를 프로비전 해제 및 일반화하고 VM을 중지해야 합니다. VHD를 기존 VM의 새 인스턴스에 대한 디스크 또는 데이터 디스크로 사용하려면 VM을 중지하고 할당 취소해야 합니다.

VHD를 다른 VM을 만들기 위한 이미지로 사용하려면 다음 단계를 완료합니다.

1. VM의 SSH, 계정 이름 및 공용 IP 주소를 사용하여 연결하고 프로비전 해제합니다. [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show)를 사용하여 공용 IP 주소를 찾을 수 있습니다. +user 매개 변수는 마지막 프로비전된 사용자 계정을 제거합니다. 계정 자격 증명을 VM에 굽는 경우 이 +user 매개 변수를 그대로 둡니다. 다음 예제는 마지막 프로비전된 사용자 계정을 제거합니다.

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. [az login](https://docs.microsoft.com/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인합니다.
3. VM을 중지 및 할당 취소합니다.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. VM을 일반화합니다. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

VHD를 기존 VM의 새 인스턴스에 대한 디스크 또는 데이터 디스크로 사용하려면 다음 단계를 완료합니다.

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  허브 메뉴에서 **Virtual Machines**를 클릭합니다.
3.  목록에서 VM을 선택합니다.
4.  VM에 대한 블레이드에서 **중지**를 클릭합니다.

    ![VM 중지](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL 생성

VHD 파일을 다운로드하려면 [SAS(공유 액세스 서명)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL을 생성해야 합니다. URL이 생성될 때 만료 시간이 URL에 할당됩니다.

1.  VM에 대한 블레이드 메뉴에서 **디스크**를 클릭합니다.
2.  VM에 대한 운영 체제 디스크를 선택한 다음 **내보내기**를 클릭합니다.
3.  **URL 생성**을 클릭합니다.

    ![URL 생성](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>VHD 다운로드

1.  생성된 URL에서 VHD 파일 다운로드를 클릭합니다.

    ![VHD 다운로드](./media/download-vhd/export-download.png)

2.  다운로드를 시작하려면 브라우저에서 **저장**을 클릭해야 합니다. VHD 파일에 대한 기본 이름은 *abcd*입니다.

    ![브라우저에서 저장 클릭](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 업로드 및 만들기](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 방법을 알아봅니다. 
- [Azure CLI를 사용하여 Azure 디스크를 관리합니다](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

