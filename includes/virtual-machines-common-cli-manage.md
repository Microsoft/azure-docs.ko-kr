---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: beece95164f0d82b1aa7f22d56f4dce02f4bb38c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387244"
---
Azure CLI를 사용하면 macOS, Linux 및 Windows에서 Azure 리소스를 만들고 관리할 수 있습니다. 이 문서에서는 VM(가상 머신)을 만들고 관리하는 가장 일반적인 몇 가지 명령에 대해 자세히 설명합니다.

이 문서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 브라우저에서 [Cloud Shell](/azure/cloud-shell/quickstart)을 사용할 수도 있습니다.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Azure CLI의 기본 Azure Resource Manager 명령
특정 명령줄 스위치와 옵션에 대해 자세한 도움말은 `az <command> <subcommand> --help`를 입력하여 온라인 명령 도움말과 옵션을 사용할 수 있습니다.

### <a name="create-vms"></a>VM 만들기
| Task | Azure CLI 명령 |
| --- | --- |
| 리소스 그룹 만들기 | `az group create --name myResourceGroup --location eastus` |
| Linux VM 만들기 | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Windows VM 만들기 | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>VM 상태 관리
| Task | Azure CLI 명령 |
| --- | --- |
| VM 시작 | `az vm start --resource-group myResourceGroup --name myVM` |
| VM 중지 | `az vm stop --resource-group myResourceGroup --name myVM` |
| VM 할당 취소 | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| VM 다시 시작 | `az vm restart --resource-group myResourceGroup --name myVM` |
| VM 재배포 | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| VM 삭제 | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>VM 정보 가져오기
| Task | Azure CLI 명령 |
| --- | --- |
| VM 나열 | `az vm list` |
| VM 관련 정보 가져오기 | `az vm show --resource-group myResourceGroup --name myVM` |
| VM 리소스 사용 | `az vm list-usage --location eastus` |
| 모든 사용 가능한 VM 크기 가져오기 | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>디스크 및 이미지
| Task | Azure CLI 명령 |
| --- | --- |
| VM에 데이터 디스크 추가 | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| VM에서 데이터 디스크 제거 | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| 디스크 크기 조정 | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| 디스크 스냅숏 | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| VM 이미지 만들기 | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| 이미지에서 VM 만들기 | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>다음 단계
CLI 명령의 추가 예제는 [Azure CLI를 사용하여 Linux VM 만들기 및 관리](../articles/virtual-machines/linux/tutorial-manage-vm.md) 자습서를 참조하세요.

