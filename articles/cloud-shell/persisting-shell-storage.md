---
title: Azure Cloud Shell에 파일 보관 | Microsoft Docs
description: Azure Cloud Shell에서 파일을 유지하는 방법의 연습입니다.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/20/2019
ms.author: damaerte
ms.openlocfilehash: 0b3b0b2cc97c86fefe37055e0744b747d4f31687
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385559"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Azure Cloud Shell에서 파일 유지
Cloud Shell은 Azure File 스토리지를 활용하여 세션 간에 파일을 유지합니다. 처음 시작 시 Cloud Shell은 세션 간에 파일을 유지하기 위해 새 또는 기존 파일 공유를 연결하도록 요구합니다.

> [!NOTE]
> Bash 및 PowerShell은 동일한 파일 공유를 공유합니다. 하나의 파일 공유는 Cloud Shell에서 자동 탑재와 연결될 수 있습니다.

> [!NOTE]
> Azure storage 방화벽은 cloud shell 저장소 계정에 대해 지원 되지 않습니다.

## <a name="create-new-storage"></a>새 스토리지 만들기

기본 설정을 사용하고 구독만 선택하면 Cloud Shell은 가장 가까운 지원되는 지역에서 사용자를 대신에 3개 리소스를 만듭니다.
* 리소스 그룹: `cloud-shell-storage-<region>`
* Storage 계정: `cs<uniqueGuid>`
* 파일 공유: `cs-<user>-<domain>-com-<uniqueGuid>`

![구독 설정](media/persisting-shell-storage/basic-storage.png)

파일 공유는 `$Home` 디렉터리에서 `clouddrive`로 마운트됩니다. 이것은 일회성 작업이며 파일 공유는 후속 세션에서 자동으로 마운트됩니다. 

파일 공유는 `$Home` 디렉터리에서 데이터를 자동으로 유지하기 위해 만든 5GB 이미지를 포함합니다. 이는 Bash 및 PowerShell 모두에 적용됩니다.

## <a name="use-existing-resources"></a>기존 리소스 사용

고급 옵션을 사용하여 기존 리소스를 연결할 수 있습니다. Cloud Shell 지역을 선택할 때 동일한 지역에 배치된 지원 스토리지 계정을 선택해야 합니다. 예를 들어 할당 된 지역이 미국 서 부 인 경우 미국 서 부에 있는 파일 공유도 연결 해야 합니다.

스토리지 설정 프롬프트가 나타나면 **고급 옵션 표시**를 선택하여 추가 옵션을 봅니다. 채워진 스토리지 옵션은 LRS(로컬 중복 스토리지), GRS(지역 중복 스토리지) 및 ZRS(영역 중복 스토리지) 계정을 필터링합니다. 

> [!NOTE]
> 지원 파일 공유의 복원력을 높이려면 GRS 또는 ZRS 스토리지 계정을 사용하는 것이 좋습니다. 목표 및 가격 기본 설정에 따라 중복성 유형이 달라집니다. [Azure Storage 계정의 복제 옵션에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![리소스 그룹 설정](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>저장소 액세스 보안
보안을 위해 각 사용자는 자신의 스토리지 계정을 프로비전해야 합니다.  RBAC(역할 기반 액세스 제어)의 경우 사용자에게는 스토리지 계정 수준에서 참가자 액세스 권한 이상이 있어야 합니다.

Cloud Shell는 지정 된 구독 내에서 저장소 계정에 Azure 파일 공유를 사용 합니다. 상속 된 권한으로 인해 구독에 대한 충분 한 액세스 권한이 있는 사용자는 구독에 포함 된 모든 저장소 계정 및 파일 공유에 액세스할 수 있습니다.

사용자는 저장소 계정 또는 구독 수준에서 사용 권한을 설정 하 여 파일에 대한 액세스를 잠가야 합니다.

## <a name="supported-storage-regions"></a>지원되는 스토리지 지역
연결된 Azure Storage 계정은 사용자가 마운트하는 Cloud Shell 컴퓨터와 동일한 지역에 있어야 합니다. 현재 영역을 찾으려면 Bash에서 `env`를 실행하여 `ACC_LOCATION` 변수를 찾을 수 있습니다. 파일 공유는 `$Home` 디렉터리를 유지하기 위해 만든 5GB 이미지를 수신합니다.

Cloud Shell 컴퓨터는 아래 하위 지역에 위치합니다.

|영역|지역|
|---|---|
|아메리카|미국 동부, 미국 중남부, 미국 서부|
|유럽|북유럽, 서유럽|
|아시아 태평양|인도 중부, 동남 아시아|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure 리소스 정책으로 리소스 만들기 제한
Cloud Shell에서 생성된 Storage 계정에 `ms-resource-usage:azure-cloud-shell` 태그가 지정됩니다. 사용자가 Cloud Shell에서 스토리지 계정을 만드는 것을 허용하지 않으려면 이 특정 태그로 트리거되는 [태그에 대한 Azure 리소스 정책](../azure-policy/json-samples.md)을 만듭니다.

## <a name="how-cloud-shell-storage-works"></a>Cloud Shell 스토리지 작동 방법 
Cloud Shell은 다음 방법 모두를 통해 파일을 유지합니다. 
* `$Home` 디렉터리 내에 모든 콘텐츠를 유지하기 위해 해당 디렉터리의 디스크 이미지를 만듭니다. 디스크 이미지는 `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`에서 `acc_<User>.img`로 지정된 파일 공유에 저장되고 변경 내용을 자동으로 동기화합니다. 
* 직접 파일 공유의 상호 작용을 위해 `$Home` 디렉터리에서 지정된 파일 공유를 `clouddrive`로 마운트합니다. `/Home/<User>/clouddrive`은 `fileshare.storage.windows.net/fileshare`에 매핑됩니다.
 
> [!NOTE]
> SSH 키와 같이 `$Home` 디렉터리의 모든 파일은 마운트된 파일 공유에 저장된 사용자 디스크 이미지에서 유지됩니다. `$Home` 디렉터리 및 마운트된 파일 공유에서 정보를 유지하는 경우 모범 사례를 적용합니다.

## <a name="clouddrive-commands"></a>clouddrive 명령

### <a name="use-the-clouddrive-command"></a>`clouddrive` 명령 사용
Cloud Shell에서 Cloud Shell에 탑재 된 파일 공유를 수동으로 업데이트할 수 있는 `clouddrive`라는 명령을 실행할 수 있습니다.
![clouddrive 명령 실행](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>목록 `clouddrive`
`clouddrive`로 마운트된 파일 공유를 확인하려면 `df` 명령을 실행합니다. 

clouddrive에 대한 파일 경로는 URL에서 스토리지 계정 이름 및 파일 공유를 표시합니다. 예를 들어 `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>새 clouddrive 탑재

#### <a name="prerequisites-for-manual-mounting"></a>수동 마운트를 위한 필수 구성 요소
`clouddrive mount` 명령을 사용하여 Cloud Shell과 연결된 파일 공유를 업데이트할 수 있습니다.

기존 파일 공유를 탑재할 경우 스토리지 계정은 선택하는 Cloud Shell 영역에 있어야 합니다. `env`를 실행 하 고 `ACC_LOCATION`를 확인 하 여 위치를 검색 합니다.

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` 명령

> [!NOTE]
> 새 파일 공유를 탑재하는 경우 새 사용자 이미지가 `$Home` 디렉터리에 만들어집니다. 이전 `$Home` 이미지는 이전 파일 공유에 보관됩니다.

다음 매개 변수를 사용하여 `clouddrive mount` 명령을 실행합니다.

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

자세한 세부 정보를 보려면 아래와 같이 `clouddrive mount -h`를 실행합니다.

![‘clouddrive mount’ 명령 실행](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>clouddrive 탑재 해제
언제든지 Cloud Shell에 마운트된 파일 공유의 마운트를 해제할 수 있습니다. Azure Cloud Shell에는 사용될 탑재된 파일 공유가 필요하므로 다음 세션에서는 또 다른 파일 공유를 만들고 탑재하라는 메시지가 표시됩니다.

1. `clouddrive unmount`을 실행합니다.
2. 프롬프트 승인 및 확인

파일 공유는 수동으로 삭제하지 않은 한 계속 유지됩니다. Cloud Shell은 후속 세션에서 이 파일 공유를 더 이상 검색하지 않습니다. 자세한 세부 정보를 보려면 아래와 같이 `clouddrive unmount -h`를 실행합니다.

![‘clouddrive unmount’ 명령 실행](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> 이 명령을 실행해도 리소스가 삭제되는 것은 아니지만 Cloud Shell에 매핑된 리소스 그룹, 스토리지 계정 또는 파일 공유를 수동으로 삭제하면 `$Home` 디렉터리 디스크 이미지 및 파일 공유의 모든 파일이 지워집니다. 이 작업은 취소할 수 없습니다.
## <a name="powershell-specific-commands"></a>PowerShell 관련 명령

### <a name="list-clouddrive-azure-file-shares"></a>`clouddrive` Azure 파일 공유 나열
`Get-CloudDrive` cmdlet은 Azure Cloud Shell에서 `clouddrive`에 현재 탑재된 Azure 파일 공유 정보를 검색합니다. <br>
![Get-CloudDrive 실행](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>`clouddrive` 마운트 해제
언제든지 Cloud Shell에 마운트된 Azure 파일 공유의 마운트를 해제할 수 있습니다. Azure 파일 공유가 제거된 경우 다음 세션에서 새 Azure 파일 공유를 만들고 탑재하라는 메시지가 표시됩니다.

`Dismount-CloudDrive` cmdlet은 현재 스토리지 계정에서 Azure 파일 공유를 탑재 해제합니다. `clouddrive`를 탑재 해제하면 현재 세션이 종료됩니다. 사용자에게 다음 세션에서 새 Azure 파일 공유를 만들고 탑재하라는 메시지가 표시됩니다.
![CloudDrive 탑재 해제 실행](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

참고: 파일에서 함수를 정의 하 고 PowerShell cmdlet에서 호출 해야 하는 경우 점 연산자를 포함 해야 합니다. 예: . .\MyFunctions.ps1

## <a name="next-steps"></a>다음 단계
[Cloud Shell 빠른 시작](quickstart.md) <br>
[Microsoft Azure Files 스토리지에 대해 알아보기](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[스토리지 태그에 대해 알아보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
