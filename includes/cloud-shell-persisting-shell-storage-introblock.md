---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: c28441b6fe25b3480a55b79682d5067b19e3023a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60200265"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Azure Cloud Shell에서 파일 유지
Cloud Shell은 Azure File 스토리지를 활용하여 세션 간에 파일을 유지합니다. 처음 시작 시 Cloud Shell은 세션 간에 파일을 유지하기 위해 새 또는 기존 파일 공유를 연결하도록 요구합니다.

> [!NOTE]
> Bash 및 PowerShell은 동일한 파일 공유를 공유합니다. 하나의 파일 공유는 Cloud Shell에서 자동 탑재와 연결될 수 있습니다.

## <a name="create-new-storage"></a>새 저장소 만들기

기본 설정을 사용하고 구독만 선택하면 Cloud Shell은 가장 가까운 지원되는 지역에서 사용자를 대신에 3개 리소스를 만듭니다.
* 리소스 그룹: `cloud-shell-storage-<region>`
* Storage 계정: `cs<uniqueGuid>`
* 파일 공유: `cs-<user>-<domain>-com-<uniqueGuid>`

![구독 설정](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

파일 공유는 `$Home` 디렉터리에서 `clouddrive`로 마운트됩니다. 이것은 일회성 작업이며 파일 공유는 후속 세션에서 자동으로 마운트됩니다. 

> [!NOTE]
> 보안을 위해 각 사용자는 자신의 저장소 계정을 프로비전해야 합니다.  RBAC(역할 기반 액세스 제어)의 경우 사용자에게는 저장소 계정 수준에서 참가자 액세스 권한 이상이 있어야 합니다.

파일 공유는 `$Home` 디렉터리에서 데이터를 자동으로 유지하기 위해 만든 5GB 이미지를 포함합니다. 이는 Bash 및 PowerShell 모두에 적용됩니다.

## <a name="use-existing-resources"></a>기존 리소스 사용

고급 옵션을 사용하여 기존 리소스를 연결할 수 있습니다. Cloud Shell 지역을 선택할 때 동일한 지역에 배치된 지원 스토리지 계정을 선택해야 합니다. 예를 들어 할당된 지역이 미국 서부인 경우 역시 미국 서부에 있는 파일 공유를 연결해야 합니다.

저장소 설정 프롬프트가 나타나면 **고급 옵션 표시**를 선택하여 추가 옵션을 봅니다.  채워진 저장소 옵션은 LRS(로컬 중복 저장소), GRS(지역 중복 저장소) 및 ZRS(영역 중복 저장소) 계정을 필터링합니다. 

> [!NOTE]
> 지원 파일 공유의 복원력을 높이려면 GRS 또는 ZRS 스토리지 계정을 사용하는 것이 좋습니다. 목표 및 가격 기본 설정에 따라 중복성 유형이 달라집니다. [Azure Storage 계정의 복제 옵션에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![리소스 그룹 설정](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>지원되는 저장소 지역
연결된 Azure Storage 계정은 사용자가 마운트하는 Cloud Shell 컴퓨터와 동일한 지역에 있어야 합니다. 현재 영역을 찾으려면 Bash에서 `env`를 실행하여 `ACC_LOCATION` 변수를 찾을 수 있습니다. 파일 공유는 `$Home` 디렉터리를 유지하기 위해 만든 5GB 이미지를 수신합니다.

Cloud Shell 컴퓨터는 아래 하위 지역에 위치합니다.

|영역|지역|
|---|---|
|아메리카|미국 동부, 미국 중남부, 미국 서부|
|유럽|북유럽, 서유럽|
|아시아 태평양|인도 중부, 동남 아시아|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure 리소스 정책으로 리소스 만들기 제한
Cloud Shell에서 생성된 Storage 계정에 `ms-resource-usage:azure-cloud-shell` 태그가 지정됩니다. 사용자가 Cloud Shell에서 저장소 계정을 만드는 것을 허용하지 않으려면 이 특정 태그로 트리거되는 [태그에 대한 Azure 리소스 정책](../articles/azure-policy/json-samples.md)을 만듭니다.
