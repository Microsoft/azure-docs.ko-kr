---
title: Avere vFXT 스토리지 구성 - Azure
description: Azure 용 Avere vFXT에서 클러스터에 대 한 백 엔드 저장소 시스템을 추가 하는 방법에 대해 알아봅니다. 클러스터를 사용 하 여 Azure Blob 컨테이너를 만든 경우 사용할 준비가 된 것입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: c17d3c7cd2cf6fe5bca725cf94344b2d2cb50bf2
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271144"
---
# <a name="configure-storage"></a>스토리지 구성

이 단계에서는 vFXT 클러스터용 백 엔드 스토리지 시스템을 설정합니다.

> [!TIP]
> Avere vFXT 클러스터와 함께 새 Azure Blob 컨테이너를 만든 경우 해당 컨테이너가 이미 구성 되어 사용할 준비가 되었습니다.

클러스터와 함께 새 Blob 컨테이너를 만들지 않았거나 추가 하드웨어 또는 클라우드 기반 스토리지 시스템을 추가하려는 경우 다음 지침을 따릅니다.

다음 두 가지 주요 작업이 있습니다.

1. VFXT 클러스터를 기존 저장소 시스템 또는 Azure Storage 계정 컨테이너에 연결 하는 [core 필터를 만듭니다](#create-a-core-filer).

1. [네임스페이스 접합 만들기](#create-a-junction) - 클라이언트에서 탑재할 경로를 정의합니다.

이러한 단계에서는 Avere 제어판을 사용합니다. 사용 방법을 알아보려면 [vFXT 클러스터에 액세스](avere-vfxt-cluster-gui.md)를 참조하세요.

## <a name="create-a-core-filer"></a>코어 파일러 만들기

"Core 필터"는 백 엔드 저장소 시스템에 대 한 vFXT 용어입니다. 스토리지는 NetApp 또는 Isilon과 같은 하드웨어 NAS 어플라이언스이거나 클라우드 개체 스토리지일 수 있습니다. 핵심 필터에 대 한 자세한 내용은 [Avere 클러스터 설정 가이드](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers)에서 찾을 수 있습니다.

코어 파일러를 추가하려면 다음 두 가지 주요 유형 중 하나의 코어 파일러를 선택합니다.

* [NAS 코어 파일러](#nas-core-filer) - NAS 코어 파일러를 추가하는 방법을 설명합니다
* [Azure Storage cloud core 필터](#azure-blob-storage-cloud-core-filer) -Azure Blob 저장소 컨테이너를 cloud core 필터로 추가 하는 방법을 설명 합니다.

### <a name="nas-core-filer"></a>NAS 코어 파일러

NAS 코어 필터 온-프레미스 NetApp 또는 Isilon 어플라이언스 또는 클라우드의 NAS 끝점이 될 수 있습니다. 스토리지 시스템은 Avere vFXT 클러스터(예: 1GBps ExpressRoute 연결(VPN 아님))에 대한 안정적인 고속 연결이 있어야 하며, 사용되는 NAS 내보내기에 대한 클러스터 루트 액세스 권한을 제공해야 합니다.

NAS core 필터를 추가 하려면 다음 단계를 수행 합니다.

1. Avere 제어판의 위쪽에서 **설정** 탭을 클릭합니다.

1. 왼쪽의 핵심 **필터**  >  **관리 핵심 필터** 를 클릭 합니다.

1. **만들기**를 클릭합니다.

   ![만들기 단추 위로 커서가 있는 새 코어 필터 추가 페이지의 스크린샷](media/avere-vfxt-add-core-filer-start.png)

1. 마법사에서 필수 정보를 입력합니다.

   * 코어 파일러의 이름을 지정합니다.
   * 사용 가능한 경우 FQDN(정규화된 도메인 이름)을 제공합니다. 그렇지 않으면 코어 파일러로 확인되는 IP 주소 또는 호스트 이름을 제공합니다.
   * 목록에서 파일러 종류를 선택합니다. 확실하지 않으면 **기타**를 선택합니다.

     ![코어 파일러 이름과 해당 정규화된 도메인 이름이 있는 새 코어 파일러 추가 페이지의 스크린샷](media/avere-vfxt-add-core-filer.png)
  
   * **다음**을 클릭하고 캐시 정책을 선택합니다.
   * **파일러 추가**를 클릭합니다.
   * 자세한 내용은 Avere 클러스터 설정 가이드의 [새 NAS 코어 파일러 추가](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html)를 참조하세요.

그런 다음, [접합 만들기](#create-a-junction)로 계속 진행합니다.  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob Storage cloud core 필터

VFXT 클러스터의 백 엔드 저장소로 Azure Blob 저장소를 사용 하려면 빈 컨테이너가 핵심 필터로 추가 되어야 합니다.

Blob Storage를 클러스터에 추가하려면 다음 작업이 필요합니다.

* 스토리지 계정 만들기(아래 1단계)
* 빈 Blob 컨테이너 만들기(2-3단계)
* 스토리지 액세스 키를 vFXT 클러스터에 대한 클라우드 자격 증명으로 추가(4-6단계)
* Blob 컨테이너를 vFXT 클러스터용 코어 파일러로 추가(7-9단계)
* 클라이언트에서 코어 파일러를 탑재하는 데 사용하는 네임스페이스 접합 만들기([접합 만들기](#create-a-junction), 하드웨어 및 클라우드 스토리지 모두에 대해 동일함)

> [!TIP]
> Azure 클러스터에 대해 Avere vFXT를 만들 때 새 Blob 컨테이너를 만드는 경우 배포 템플릿은 자동으로 컨테이너를 핵심 필터 구성 합니다. (요청 시 사용할 수 있는 생성 스크립트를 사용 하는 경우에도 마찬가지입니다.) 나중에 핵심 필터을 구성할 필요가 없습니다.
>
> 클러스터 만들기 도구는 다음 구성 작업을 수행 합니다.
>
> * 제공 된 저장소 계정에서 새 Blob 컨테이너를 만듭니다.
> * 컨테이너를 핵심 필터 정의 합니다.
> * 컨테이너에 대 한 네임 스페이스 연결을 만듭니다.
> * 클러스터의 가상 네트워크 내에 저장소 서비스 끝점을 만듭니다.

클러스터를 만든 후에 Blob Storage를 추가하려면 다음 단계를 수행합니다.

1. 다음 설정을 사용하여 범용 V2 스토리지 계정을 만듭니다.

   * **구독** - vFXT 클러스터와 동일
   * **리소스 그룹** - vFXT 클러스터 그룹과 동일(선택 사항)
   * **위치** - vFXT 클러스터와 동일
   * **성능** - 표준(Premium Storage는 지원되지 않음)
   * **계정 종류** - 범용 V2(StorageV2)
   * **복제** - LRS(로컬 중복 스토리지)
   * **액세스 계층** - 핫
   * **보안 전송 필요** - 이 옵션은 사용하지 않도록 설정함(기본값이 아님)
   * **가상 네트워크** -필수 아님

   Azure Portal을 사용하거나 아래의 "Azure에 배포" 단추를 클릭합니다.

   [![저장소 계정 만들기 단추](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. 계정이 만들어지면 스토리지 계정 페이지로 이동합니다.

   ![Azure Portal의 새 스토리지 계정](media/avere-vfxt-new-storage-acct.png)

1. 새 Blob 컨테이너 만들기: 개요 페이지에서 **컨테이너** 를 클릭 한 다음 **+ 컨테이너**를 클릭 합니다. 컨테이너 이름을 사용하고, 액세스가 **프라이빗**으로 설정되어 있는지 확인합니다.

   ![+ 컨테이너 단추가 원으로 표시 되 고 팝업 페이지에서 새 컨테이너를 만드는 저장소 blob 페이지](media/avere-vfxt-new-blob.png)

1. **설정**아래에서 **액세스 키** 를 클릭 하 여 Azure Storage 계정 키를 가져옵니다. 제공 된 키 중 하나를 복사 합니다.

   ![키 복사를 위한 Azure Portal GUI](media/avere-vfxt-copy-storage-key.png)

1. 클러스터에 대한 Avere 제어판을 엽니다. **설정**을 클릭한 다음, 왼쪽 탐색 창에서 **클러스터** > **클라우드 자격 증명**을 차례로 엽니다. [클라우드 자격 증명] 페이지에서 **자격 증명 추가**를 클릭합니다.

   ![[클라우드 자격 증명] 구성 페이지에서 [자격 증명 추가] 단추 클릭](media/avere-vfxt-new-credential-button.png)

1. 다음 정보를 입력하여 클라우드 코어 파일러에 대한 자격 증명을 만듭니다.

   | 필드 | 값 |
   | --- | --- |
   | 자격 증명 이름 | 설명이 포함된 이름 |
   | 서비스 유형 | (액세스 키 Azure Storage 선택) |
   | 테넌트 | 스토리지 계정 이름 |
   | Subscription | 구독 ID |
   | 스토리지 액세스 키 | Azure Storage 계정 키(이전 단계에서 복사됨) |

   **제출**을 클릭합니다.

   ![Avere 제어판에서 완성된 클라우드 자격 증명 양식](media/avere-vfxt-new-credential-submit.png)

1. 다음으로, 코어 파일러를 만듭니다. Avere 컨트롤 패널의 왼쪽에서 **core 필터**  >   **core filers 관리**를 클릭 합니다.

1. **코어 파일러 관리** 설정 페이지에서 **만들기** 단추를 클릭합니다.

1. 마법사를 작성합니다.

   * **클라우드** 파일러 유형을 선택합니다.
   * 새 코어 파일러의 이름을 지정하고, **다음**을 클릭합니다.
   * 기본 캐시 정책을 적용하고 세 번째 페이지로 계속 진행합니다.
   * **서비스 유형**에서 **Azure Storage**를 선택합니다.
   * 이전에 만든 자격 증명을 선택합니다.
   * **버킷 내용**을 **비어 있음**으로 설정합니다.
   * **인증서 확인**을 **사용 안 함**으로 변경합니다.
   * **압축 모드**를 **없음**으로 변경합니다.
   * **다음**을 클릭합니다.
   * 네 번째 페이지에서 **버킷 이름**의 컨테이너 이름을 *storage_account_name*/*container_name*으로 입력합니다.
   * 필요에 따라 **암호화 유형**을 **없음**으로 설정합니다.  Azure Storage는 기본적으로 암호화되어 있습니다.
   * **파일러 추가**를 클릭합니다.

   자세한 내용은 Avere 클러스터 구성 가이드의 [새 클라우드 코어 파일러 추가](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>)를 참조하세요.

페이지가 새로 고쳐지거나 페이지를 새로 고쳐 새 코어 파일러를 표시할 수 있습니다.

다음으로, [접합을 만들어야](#create-a-junction) 합니다.

## <a name="create-a-junction"></a>접합 만들기

접합은 클라이언트에 대해 만드는 경로입니다. 클라이언트에서 경로를 탑재하고 선택한 대상에 도달합니다.

예를 들어 `/vfxt/files`를 만들어 `/vol0/data` NetApp 코어 파일러 내보내기 및 `/project/resources` 하위 디렉터리에 매핑할 수 있습니다.

접합에 대한 자세한 내용은 [Avere 클러스터 구성 가이드의 네임스페이스 섹션](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)에서 찾을 수 있습니다.

Avere 제어판 인터페이스에서 다음 단계를 수행 합니다.

* 왼쪽 위에서 **VServer**  >  **네임 스페이스** 를 클릭 합니다.
* ``/vfxt/data``와 같이 /(슬래시)로 시작하는 네임스페이스 경로를 제공합니다.
* 코어 파일러를 선택합니다.
* 코어 파일러 내보내기를 선택합니다.
* **다음**을 클릭합니다.

  ![접합, 코어 파일러 및 내보내기에 대해 완성된 필드가 있는 "새 접합 추가" 페이지의 스크린샷](media/avere-vfxt-add-junction.png)

몇 초 후에 접합이 표시됩니다. 필요에 따라 추가 접합을 만듭니다.

연결을 만든 후에는 클라이언트에서 네임 스페이스 경로를 사용 하 여 저장소 시스템의 파일에 액세스 합니다.

## <a name="next-steps"></a>다음 단계

* [Avere vFXT 클러스터 탑재](avere-vfxt-mount-clients.md)
* [데이터를 새 Blob 컨테이너로 이동 하는](avere-vfxt-data-ingest.md) 효율적인 방법 알아보기
