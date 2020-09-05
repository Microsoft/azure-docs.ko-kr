---
title: 컴퓨팅 노드에 애플리케이션 패키지 배포
description: Azure Batch의 애플리케이션 패키지 기능을 사용하여 Batch 컴퓨팅 노드에 설치할 여러 애플리케이션 및 버전을 간편하게 관리하세요.
ms.topic: how-to
ms.date: 04/26/2019
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: d847dd926d157e455a6ef4e0c58c9dd204a1ecc7
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146473"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>애플리케이션을 배포하여 Batch 애플리케이션 패키지에서 노드 컴퓨팅

Azure Batch의 애플리케이션 패키지 기능은 풀의 컴퓨팅 노드에 태스크 애플리케이션 및 해당 배포를 간편하게 관리할 수 있는 방법을 제공합니다. 애플리케이션 패키지를 사용하여 지원 파일을 비롯하여 태스크에서 실행하는 여러 애플리케이션 버전을 업로드하고 관리할 수 있습니다. 풀의 컴퓨팅 노드에 이러한 애플리케이션을 하나 이상 자동으로 배포할 수 있습니다.

이 문서에서는 Azure portal을 사용하여 애플리케이션 패키지를 업로드하고 관리하는 방법을 알아봅니다. 그런 다음, [Batch .NET][api_net] 라이브러리를 사용하여 풀의 컴퓨팅 노드에 설치하는 방법을 알아봅니다.

> [!NOTE]
> 애플리케이션 패키지는 2017년 7월 5일 이후에 만들어진 모든 Batch 풀에서 지원됩니다. 2016년 3월 10일에서 2017년 7월 5일 사이에 만들어진 Batch 풀에서는 Cloud Service 구성을 사용하여 풀을 만든 경우에만 이러한 패키지가 지원됩니다. 2016년 3월 10일 이전에 만들어진 Batch 풀은 애플리케이션 패키지를 지원하지 않습니다.
>
> 애플리케이션 패키지를 만들고 관리하는 API는 [Batch Management .NET][api_net_mgmt] 라이브러리의 일부입니다. 컴퓨팅 노드에서 애플리케이션 패키지를 설치하는 API는 [Batch .NET][api_net] 라이브러리의 일부입니다. 다른 언어의 경우 상응하는 기능을 Batch API에서 제공합니다. 
>
> 여기서 설명하는 애플리케이션 패키지 기능은 이전 버전의 서비스에서 사용할 수 있는 Batch 앱 기능을 대체합니다.

## <a name="application-package-requirements"></a>애플리케이션 패키지 요구 사항
애플리케이션 패키지를 사용하려면 Batch 계정에 [Azure Storage 계정을 연결](#link-a-storage-account)해야 합니다.

## <a name="about-applications-and-application-packages"></a>애플리케이션 및 애플리케이션 패키지 정보
Azure Batch 내에서 *애플리케이션*은 풀의 컴퓨팅 노드에 자동으로 다운로드할 수 있는 버전이 지정된 이진 파일의 세트를 나타냅니다. *애플리케이션 패키지*는 이러한 이진 파일의 *특정 집합*을 말하며, 지정된 애플리케이션 *버전*을 나타냅니다.

![애플리케이션 및 애플리케이션 패키지에 대한 개략적인 다이어그램][1]

### <a name="applications"></a>애플리케이션
Batch에서 애플리케이션은 하나 이상의 애플리케이션을 포함하거나 애플리케이션에 대한 구성 옵션을 지정합니다. 예를 들어 애플리케이션은 컴퓨팅 노드에 설치할 기본 애플리케이션 패키지 버전, 애플리케이션 패키지를 업데이트할지 아니면 삭제할 것인지를 지정할 수 있습니다.

### <a name="application-packages"></a>애플리케이션 패키지
애플리케이션 패키지는 태스크가 애플리케이션을 실행하는 데 필요한 애플리케이션 이진 파일 및 지원 파일이 포함된 .zip 파일입니다. 각 애플리케이션 패키지는 애플리케이션의 특정 버전을 나타냅니다.

풀 및 태스크 수준에서 애플리케이션 패키지를 지정할 수 있습니다. 풀 또는 태스크를 만들 때 이러한 패키지 및 버전(선택 사항)을 하나 이상 지정할 수 있습니다.

* **풀 애플리케이션 패키지**는 *모든* 노드에 배포됩니다. 애플리케이션은 노드가 풀을 조인하고 재부팅되거나 이미지를 다시 설치할 때 배포됩니다.
  
    풀 애플리케이션 패키지는 풀에 있는 모든 노드가 작업의 태스크를 실행할 때 적합합니다. 풀을 만들 때 하나 이상의 애플리케이션 패키지를 지정할 수 있으며 기존 풀의 패키지를 추가하거나 업데이트할 수 있습니다. 기존 풀의 애플리케이션 패키지를 업데이트하는 경우 새 패키지를 설치하려면 해당 노드를 다시 설치해야 합니다.
* **태스크 애플리케이션 패키지**는 태스크의 명령줄을 실행하기 바로 전에 태스크를 실행하도록 예약된 컴퓨팅 노드에만 배포됩니다. 지정된 애플리케이션 패키지 및 버전이 노드에 이미 있는 경우 다시 배포되지 않으며 기존 패키지가 사용됩니다.
  
    태스크 애플리케이션 패키지는 공유 풀 환경에서 유용하며 여기서는 다른 작업이 하나의 풀에서 실행되고 작업이 완료될 때 풀이 삭제되지 않습니다. 작업에 있는 태스크가 풀에 있는 노드보다 적은 경우 태스크를 실행하는 노드에만 애플리케이션을 배포하므로 태스크 애플리케이션 패키지는 데이터 전송을 최소화할 수 있습니다.
  
    태스크 애플리케이션 패키지를 활용할 수 있는 다른 시나리오는 큰 애플리케이션을 실행하지만 태스크 수는 적은 작업입니다. 예를 들어 전처리 또는 병합 애플리케이션이 대규모인 전처리 단계나 병합 태스크는 태스크 애플리케이션 패키지를 활용할 수 있습니다.

> [!IMPORTANT]
> Batch 계정 내 애플리케이션 및 애플리케이션 패키지 수와 애플리케이션 패키지의 최대 크기에 대한 제한이 있습니다. 이러한 제한에 대한 자세한 내용은 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md) 을 참조하세요.
> 
> 

### <a name="benefits-of-application-packages"></a>애플리케이션 패키지의 이점
애플리케이션 패키지는 Batch 솔루션의 코드를 단순화하고, 태스크에서 실행하는 애플리케이션을 관리하는 데 필요한 오버헤드를 낮출 수 있습니다.

애플리케이션 패키지를 사용하면 풀의 시작 태스크가 노드에 설치할 개별 리소스 파일의 긴 목록을 지정할 필요가 없습니다. Azure Storage 또는 노드에서 애플리케이션 파일의 여러 버전을 수동으로 관리할 필요가 없습니다. Storage 계정의 파일에 대한 액세스 권한을 제공하기 위해 [SAS URL](../storage/common/storage-sas-overview.md) 을 생성할 필요가 없습니다. Batch는 Azure Storage와 함께 백그라운드에서 작동하여 애플리케이션 패키지를 저장하고 컴퓨팅 노드에 배포합니다.

> [!NOTE] 
> 리소스 파일 및 환경 변수를 포함한 시작 태스크의 전체 크기는 32768자 이하여야 합니다. 시작 태스크가 이 제한을 초과하는 경우 애플리케이션 패키지 사용은 또 다른 옵션입니다. 리소스 파일을 포함하는 압축된 보관 파일을 만들어 Blob으로 Azure Storage에 업로드한 다음 시작 태스크의 명령줄에서 압축을 풀 수도 있습니다. 
>
>

## <a name="upload-and-manage-applications"></a>애플리케이션 업로드 및 관리
[Azure Portal][portal] 또는 Batch Management API를 사용하여 Batch 계정에서 애플리케이션 패키지를 관리합니다. 다음 섹션에서는 먼저 Storage 계정을 연결하는 방법을 보여 준 다음 애플리케이션 및 패키지를 추가하고 포털에서 관리하는 방법을 설명합니다.

### <a name="link-a-storage-account"></a>Storage 계정 연결
애플리케이션 패키지를 사용하려면 먼저 [Azure Storage 계정](accounts.md#azure-storage-accounts)을 Batch 계정에 연결해야 합니다. Storage 계정을 아직 구성하지 않은 경우 Batch 계정에서 **애플리케이션** 타일을 처음으로 클릭하면 Azure Portal에서 경고를 표시합니다.



![Azure Portal의 '스토리지 계정이 구성되지 않음' 경고][9]

Batch 서비스는 연결된 Storage 계정을 사용하여 애플리케이션 패키지를 저장합니다. 계정 두 개를 연결한 후에 Batch는 연결된 스토리지 계정에 저장된 패키지를 컴퓨팅 노드에 자동으로 배포할 수 있습니다. Storage 계정을 Batch 계정에 연결하려면 **경고** 창에서 **Storage 계정**을 클릭한 다음, **Storage 계정**을 다시 클릭합니다.

![Azure 포털에서 스토리지 계정 블레이드 선택][10]

*특별히* Batch 계정과 함께 사용할 Storage 계정을 만들었으면 여기에서 선택하는 것이 좋습니다. Storage 계정을 만든 후에 **Storage 계정** 창을 사용하여 Batch 계정에 연결할 수 있습니다.

> [!IMPORTANT] 
> - 현재 [방화벽 규칙](../storage/common/storage-network-security.md)이 구성된 Azure Storage 계정에는 애플리케이션 패키지를 사용할 수 없습니다.
> - **계층 구조 네임스페이스**가 **사용**으로 설정된 Azure Storage 계정은 애플리케이션 패키지에 사용할 수 없습니다.

Batch 서비스는 Azure Storage를 사용하여 애플리케이션 패키지를 블록 Blob으로 저장합니다. 블록 Blob 데이터에 대해서는 [정상적으로 요금이 부과][storage_pricing]되며 각 패킷의 크기는 최대 블록 Blob 크기를 초과할 수 없습니다. 자세한 내용은 [스토리지 계정의 Azure Storage 확장성 및 성능 목표](../storage/blobs/scalability-targets.md)를 참조하세요. 비용을 최소화할 수 있도록 애플리케이션 패키지의 크기와 숫자에 신경 쓰고, 사용하지 않는 패키지를 주기적으로 제거해 주세요.

### <a name="view-current-applications"></a>현재 애플리케이션 보기
Batch 계정의 애플리케이션을 보려면 **Batch 계정**이 표시되는 동안 왼쪽의 **애플리케이션** 메뉴 항목을 클릭합니다.

![애플리케이션 타일][2]

이 메뉴 옵션을 선택하면 **애플리케이션** 창이 열립니다.

![애플리케이션 나열][3]

이 창은 계정의 각 애플리케이션 ID 및 다음 속성을 표시합니다.

* **패키지**: 이 애플리케이션과 연결된 버전의 수입니다.
* **기본 버전**: 풀용 애플리케이션을 지정할 때 버전을 지정하지 않으면 설치되는 애플리케이션 버전입니다. 이 설정은 선택 사항입니다.
* **업데이트 허용**: 패키지 업데이트, 삭제 및 추가 허용 여부를 지정하는 값입니다. 이 값을 **아니요**로 설정하면 애플리케이션에 패키지 업데이트 및 삭제를 사용할 수 없습니다. 새 애플리케이션 패키지 버전만 추가할 수 있습니다. 기본값은 **예**입니다.

컴퓨팅 노드에 애플리케이션 패키지의 파일 구조를 표시하려면 포털에서 Batch 계정으로 이동합니다. Batch 계정에서 **풀**로 이동합니다. 관심 있는 컴퓨팅 노드가 포함된 풀을 선택합니다.

![풀의 노드][13]

풀을 선택한 후에는 애플리케이션 패키지가 설치된 컴퓨팅 노드로 이동합니다. 여기에서 애플리케이션 패키지의 세부 정보는 **애플리케이션** 폴더에 있습니다. 컴퓨팅 노드의 추가 폴더에는 시작 작업, 출력 파일, 오류 출력 등과 같은 다른 파일이 포함되어 있습니다.

![노드에 있는 파일][14]

### <a name="view-application-details"></a>애플리케이션 세부 정보 보기
애플리케이션에 대한 세부 정보를 확인하려면 **애플리케이션** 창에서 애플리케이션을 선택합니다.

![애플리케이션 세부 정보][4]

애플리케이션 세부 정보에서 애플리케이션에 대해 다음 설정을 구성할 수 있습니다.

* **업데이트 허용**: 해당 애플리케이션 패키지를 업데이트하거나 삭제할 수 있는지를 지정합니다. 이 문서에서 아래의 "애플리케이션 패키지 업데이트 또는 삭제"를 참조하세요.
* **기본 버전**: 컴퓨팅 노드에 배포할 기본 애플리케이션 패키지를 지정합니다.
* **표시 이름**: Batch 솔루션이 애플리케이션에 대한 정보를 표시할 때 사용할 수 있는 식별 이름을 지정합니다. Batch를 통해 고객에게 제공하는 서비스의 UI에서 지정하는 이름을 예로 들 수 있습니다.

### <a name="add-a-new-application"></a>새 애플리케이션 추가
새 애플리케이션을 만들려면 애플리케이션 패키지를 추가하고 고유한 새 애플리케이션 ID를 지정합니다. 새 애플리케이션 ID를 사용하여 추가하는 첫 번째 애플리케이션 패키지도 새 애플리케이션을 만듭니다.

**애플리케이션** > **추가**를 클릭합니다.

![Azure portal의 새 애플리케이션 블레이드][5]

**새 애플리케이션** 창은 새 애플리케이션 및 애플리케이션 패키지의 설정을 지정하는 다음 필드를 제공합니다.

**애플리케이션 ID**

이 필드는 새 애플리케이션의 ID를 지정하며, 표준 Azure Batch ID 유효성 검사 규칙을 따릅니다. 애플리케이션 ID를 제공하는 규칙은 다음과 같습니다.

* Windows 노드에서 ID에는 영숫자, 하이픈 및 밑줄의 모든 조합을 사용할 수 있습니다. Linux 노드에서는 영숫자와 밑줄만 허용됩니다.
* 포함할 수 있는 최대 문자는 64자입니다.
* Batch 계정 내에서 고유해야 합니다.
* 대/소문자를 유지하고 대/소문자를 구분하지 않습니다.

**버전**

이 필드는 사용자가 업로드하는 애플리케이션 패키지의 버전을 지정합니다. 버전 문자열은 다음 유효성 검사 규칙을 따릅니다.

* Windows 노드에서 버전 문자열에는 영숫자, 하이픈, 밑줄 및 마침표의 모든 조합을 사용할 수 있습니다. Linux 노드에서는 버전 문자열에 영숫자와 밑줄만 사용할 수 있습니다.
* 포함할 수 있는 최대 문자는 64자입니다.
* 애플리케이션 내에서 고유해야 합니다.
* 대/소문자를 유지하고 대/소문자를 구분하지 않습니다.

**애플리케이션 패키지**

이 필드는 애플리케이션 실행에 필요한 애플리케이션 이진 파일 및 모든 지원 파일을 포함하는 .zip 파일을 지정합니다. **파일 선택** 상자 또는 폴더 아이콘을 클릭하여 애플리케이션의 파일이 포함된 .zip 파일을 찾고 선택합니다.

파일을 선택했으면 **확인** 을 클릭하여 Azure Storage에 업로드를 시작합니다. 업로드 작업이 완료되면 포털에서 알림을 표시합니다. 업로드하는 파일의 크기 및 네트워크 연결 속도에 따라 작업 시간이 달라질 수 있습니다.

> [!WARNING]
> 업로드 작업이 완료되기 전에 **새 애플리케이션** 창을 닫지 않습니다. 만약 닫으면 업로드 프로세스가 중지됩니다.
> 
> 

### <a name="add-a-new-application-package"></a>새 애플리케이션 패키지 추가
기존 애플리케이션에 대한 애플리케이션 패키지 버전을 추가하려면 **애플리케이션** 창에서 애플리케이션을 선택하고, **패키지** > **추가**를 클릭합니다.

![Azure portal의 애플리케이션 패키지 추가 블레이드][8]

여기서 볼 수 있듯이 필드가 **새 애플리케이션** 창의 필드와 일치하지만 **애플리케이션 ID** 상자는 비활성화되어 있습니다. 새 애플리케이션에서 수행한 대로 새 패키지의 **버전**을 지정하고, **애플리케이션 패키지** .zip 파일을 찾은 다음 **확인**을 클릭하여 패키지를 업로드합니다.

### <a name="update-or-delete-an-application-package"></a>애플리케이션 패키지 업데이트 또는 삭제
기존 애플리케이션 패키지를 업데이트하거나 삭제하려면 해당 애플리케이션의 세부 정보를 연 다음, **패키지**를 클릭하고, 수정할 애플리케이션 패키지 행에서 **...(줄임표)** 를 클릭하여 수행할 작업을 선택합니다.

![Azure portal에서 패키지 업데이트 또는 삭제][7]

**Update**

**업데이트**를 클릭하면 **패키지 업데이트** 창이 표시됩니다. 이 창은 **새 애플리케이션 패키지** 창과 비슷하지만, 패키지 선택 필드만 활성화되므로 업로드할 새 ZIP 파일을 지정할 수 있습니다.

![Azure portal의 패키지 업데이트 블레이드][11]

**Delete**

**삭제**를 클릭하면 패키지 버전을 삭제할 것인지 묻는 메시지가 나타나고, Batch가 Azure Storage에서 패키지를 삭제합니다. 애플리케이션의 기본 버전을 삭제하면 해당 애플리케이션의 **기본 버전** 설정이 제거됩니다.

![애플리케이션 삭제][12]

## <a name="install-applications-on-compute-nodes"></a>컴퓨팅 노드에 애플리케이션 설치
Azure Portal을 사용하여 애플리케이션 패키지를 관리하는 방법을 살펴보았으며 애플리케이션 패키지를 컴퓨팅 노드에 배포하고 Batch 작업을 사용하여 실행하는 방법에 대해 설명할 수 있습니다.

### <a name="install-pool-application-packages"></a>풀 애플리케이션 패키지 설치
풀의 모든 컴퓨팅 노드에 애플리케이션 패키지를 설치하려면 풀에 대해 애플리케이션 패키지 *참조*를 하나 이상 지정해야 합니다. 풀에 대해 지정하는 애플리케이션 패키지는 해당 노드가 풀에 조인될 때와 노드가 다시 부팅되거나 다시 이미지화될 때 각 컴퓨팅 노드에 설치됩니다.

Batch .NET에서, 새 풀을 만들 때 또는 기존 풀에 하나 이상의 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]를 지정합니다. [ApplicationPackageReference][net_pkgref] 클래스는 풀의 컴퓨팅 노드에 설치할 애플리케이션 ID 및 버전을 지정합니다.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> 어떤 이유로든 애플리케이션 패키지 배포가 실패하고 Batch 서비스에서 노드를 [사용할 수 없음][net_nodestate]으로 표시하면 해당 노드에서 실행되도록 예약된 작업이 없는 것입니다. 이 경우에 노드를 **다시 시작** 하여 패키지 배포를 다시 시작해야 합니다. 또한 노드를 다시 시작하면 노드에서 작업을 다시 예약할 수 있습니다.
> 
> 

### <a name="install-task-application-packages"></a>태스크 애플리케이션 패키지 설치
풀과 마찬가지로 태스크에 대해 애플리케이션 패키지 *참조*를 지정합니다. 노드에서 실행하도록 태스크가 예약된 경우 태스크의 명령줄이 실행되기 바로 전에 패키지가 다운로드 및 추출됩니다. 지정된 패키지 및 버전이 노드에 이미 설치되어 있는 경우 패키지는 다운로드되지 않으며 기존 패키지가 사용됩니다.

태스크 애플리케이션 패키지를 설치하려면 태스크의 [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref] 속성을 구성합니다.

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>설치된 애플리케이션 실행
풀 또는 태스크에 대해 지정한 패키지가 노드의 `AZ_BATCH_ROOT_DIR` 내에 있는 명명된 디렉터리에 다운로드되어 추출됩니다. 또한 Batch는 명명된 디렉터리에 대한 경로가 포함된 환경 변수를 생성합니다. 태스크 명령줄은 노드에서 애플리케이션을 참조할 때 이 환경 변수를 사용합니다. 

Windows 노드에서는 변수가 다음과 같은 형식입니다.

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux 노드에서는 형식이 약간 다릅니다. 마침표(.), 하이픈(-) 및 숫자 기호(#)가 환경 변수에서 밑줄로 표시됩니다. 또한 애플리케이션 ID의 대/소문자는 유지됩니다. 다음은 그 예입니다.

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` 및 `version`은 배포를 위해 지정한 애플리케이션 및 패키지 버전에 해당하는 값입니다. 예를 들어 Windows 노드에 *blender* 애플리케이션의 2.7 버전을 설치하도록 지정하면 태스크 명령줄은 다음 환경 변수를 사용하여 해당 파일에 액세스합니다.

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux 노드에서는 환경 변수를 다음 형식으로 지정합니다. 마침표(.), 하이픈(-) 및 숫자 기호(#)를 밑줄로 평면화하고 애플리케이션 ID의 대/소문자를 유지합니다.

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

애플리케이션 패키지를 업로드할 때 컴퓨팅 노드에 배포할 기본 버전을 지정할 수 있습니다. 애플리케이션의 기본 버전을 지정하면 애플리케이션 참조 시 버전 접미사를 생략할 수 있습니다. [애플리케이션 업로드 및 관리](#upload-and-manage-applications)에서 표시된 것처럼 Azure Portal의 **애플리케이션** 창에서 기본 애플리케이션 버전을 지정할 수 있습니다.

예를 들어 *blender* 애플리케이션에 대해 기본 버전으로 "2.7"을 설정한 경우 태스크가 다음 환경 변수를 참조하면 Windows 노드에서 버전 2.7을 실행합니다.

`AZ_BATCH_APP_PACKAGE_BLENDER`

다음 코드 조각은 기본 버전의 *blender* 애플리케이션을 실행하는 태스크 명령줄의 예를 보여 줍니다.

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> 컴퓨팅 노드 환경 설정에 대한 자세한 내용은 [태스크에 대한 환경 설정](jobs-and-tasks.md#environment-settings-for-tasks)을 참조하세요. 

## <a name="update-a-pools-application-packages"></a>풀의 애플리케이션 패키지 업데이트
기존 풀이 이미 애플리케이션 패키지를 통해 구성된 경우 해당 풀에 대해 새 패키지를 지정할 수 있습니다. 풀에 대한 새 패키지 참조를 지정하면 다음이 적용됩니다.

* Batch 서비스는 풀에 조인하는 모든 새 노드와 다시 부팅되거나 이미지로 다시 설치되는 기존 노드에 새로 지정된 패키지를 설치합니다.
* 패키지 참조를 업데이트할 때 이미 풀에 있는 Compute 노드는 새 애플리케이션 패키지를 자동으로 설치하지 않습니다. 이러한 컴퓨팅 노드를 재부팅하거나 이미지로 다시 설치하여 새 패키지를 받아야 합니다.
* 새 패키지가 배포될 때 만들어진 환경 변수는 새 애플리케이션 패키지 참조를 반영합니다.

이 예제에서는 기존 풀의 *blender* 애플리케이션 2.7 버전이 [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] 중 하나로 구성되었습니다. 풀의 노드를 2.76b 버전으로 업데이트하려면 새 버전을 사용하여 [ApplicationPackageReference][net_pkgref]를 지정하고 변경 내용을 커밋합니다.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

새 버전이 구성되었으므로 Batch 서비스는 풀에 조인하는 모든 *새* 노드에 2.76b 버전을 설치합니다. *이미* 풀에 있는 노드에 2.76b 버전을 설치하려면 노드를 재부팅하거나 이미지로 다시 설치합니다. 다시 부팅된 노드는 이전 패키지 배포의 파일을 보존합니다.

## <a name="list-the-applications-in-a-batch-account"></a>Batch 계정의 애플리케이션 나열
[ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] 메서드를 사용하여 Batch 계정의 애플리케이션 및 애플리케이션 패키지를 나열할 수 있습니다.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>마무리
애플리케이션 패키지를 사용하면 고객이 작업에 대한 애플리케이션을 선택하고 Batch 지원 서비스를 통해 작업을 처리할 때 사용할 정확한 버전을 지정할 수 있습니다. 또한 고객이 서비스에서 자신의 고유한 애플리케이션을 업로드 및 추적하는 기능을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 또한 [Batch REST API][api_rest]는 애플리케이션 패키지로 작업하도록 지원합니다. 예를 들어 REST API를 사용하여 설치할 패키지를 지정하는 방법에 대한 자세한 내용은 [계정에 풀 추가][rest_add_pool]에서 [applicationPackageReferences][rest_add_pool_with_packages] 요소를 참조하세요. Batch REST API를 사용하여 애플리케이션 정보를 얻는 방법에 대한 자세한 내용은 [애플리케이션][rest_applications]을 참조하세요.
* 프로그래밍 방식으로 [Batch 관리 .NET으로 Azure Batch 계정 및 할당량 관리](batch-management-dotnet.md)를 수행하는 방법을 알아보세요. [Batch 관리.NET][api_net_mgmt] 라이브러리는 Batch 애플리케이션 또는 서비스에 대해 계정 만들기 및 삭제 기능을 활성화할 수 있습니다.

[api_net]: /dotnet/api/overview/azure/batch/client
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_rest]: /rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: /dotnet/api/microsoft.azure.batch.applicationoperations
[net_appops_listappsummaries]: /dotnet/api/microsoft.azure.batch.applicationoperations
[net_cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_cloudpool_pkgref]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_cloudtask_pkgref]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_nodestate]: /dotnet/api/microsoft.azure.batch.computenode
[net_pkgref]: /dotnet/api/microsoft.azure.batch.applicationpackagereference
[portal]: https://portal.azure.com
[rest_applications]: /rest/api/batchservice/application
[rest_add_pool]: /rest/api/batchservice/pool/add
[rest_add_pool_with_packages]: /rest/api/batchservice/pool/add#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "애플리케이션 패키지에 대한 개략적인 다이어그램"
[2]: ./media/batch-application-packages/app_pkg_02.png "Azure portal의 애플리케이션 타일"
[3]: ./media/batch-application-packages/app_pkg_03.png "Azure portal의 애플리케이션 블레이드"
[4]: ./media/batch-application-packages/app_pkg_04.png "Azure portal의 애플리케이션 세부 정보 블레이드"
[5]: ./media/batch-application-packages/app_pkg_05.png "Azure portal의 새 애플리케이션 블레이드"
[7]: ./media/batch-application-packages/app_pkg_07.png "Azure portal의 패키지 업데이트 또는 삭제 드롭다운"
[8]: ./media/batch-application-packages/app_pkg_08.png "Azure portal의 새 애플리케이션 패키지 블레이드"
[9]: ./media/batch-application-packages/app_pkg_09.png "연결된 Storage 계정 없음 경고"
[10]: ./media/batch-application-packages/app_pkg_10.png "포털에서 스토리지 계정 블레이드 선택"
[11]: ./media/batch-application-packages/app_pkg_11.png "Azure portal의 패키지 업데이트 블레이드"
[12]: ./media/batch-application-packages/app_pkg_12.png "Azure portal의 패키지 삭제 확인 대화 상자"
[13]: ./media/batch-application-packages/package-file-structure.png "Azure Portal의 컴퓨팅 노드 정보"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Azure Portal에 표시되는 컴퓨팅 노드의 파일"
