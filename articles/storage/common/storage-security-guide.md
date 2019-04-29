---
title: Azure Storage 보안 가이드 | Microsoft Docs
description: RBAC, Storage 서비스 암호화, 클라이언트 쪽 암호화, SMB 3.0 및 Azure 디스크 암호화를 비롯하여 Azure Storage 보안을 유지하는 다양한 방법을 자세히 설명합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 74be21629a884313e74a824bb34fb5b66f5db61d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729076"
---
# <a name="azure-storage-security-guide"></a>Azure Storage 보안 가이드

Azure Storage는 여러 개발자가 보안 애플리케이션을 빌드하도록 지원하는 포괄적인 보안 기능을 제공합니다.

- Azure Storage에 쓴 모든 데이터는 [SSE(Storage 서비스 암호화)](storage-service-encryption.md)를 사용하여 자동으로 암호화됩니다. 자세한 내용은 [Announcing Default Encryption for Azure Blobs, Files, Table and Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)(Azure Blob, Files, Table 및 Queue Storage에 대한 기본 암호화 발표)를 참조하세요.
- Azure AD(Azure Active Directory) 및 RBAC(역할 기반 액세스 제어)는 다음과 같이 리소스 관리 작업 및 데이터 작업 모두에 대한 Azure Storage에 지원됩니다.   
    - 저장소 계정으로 범위가 지정된 RBAC 역할을 보안 주체에 할당하고 Azure AD를 사용하여 키 관리와 같은 리소스 관리 작업의 권한을 부여할 수 있습니다.
    - Blob 및 큐 데이터 작업에 대 한 azure AD 통합은 지원 됩니다. 구독, 리소스 그룹, 저장소 계정 또는 개별 컨테이너 또는 큐로 범위가 지정된 RBAC 역할을 보안 주체 또는 Azure 리소스의 관리 ID에 할당할 수 있습니다. 자세한 내용은 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](storage-auth-aad.md)을 참조하세요.   
- [클라이언트 쪽 암호화](../storage-client-side-encryption.md), HTTP 또는 SMB 3.0을 사용하여 애플리케이션과 Azure 간에 전송 중인 데이터의 보안을 유지할 수 있습니다.  
- 가상 머신에서 사용되는 OS 및 데이터 디스크는 [Azure Disk Encryption](../../security/azure-security-disk-encryption.md)을 사용하여 암호화될 수 있습니다. 
- [공유 액세스 서명](../storage-dotnet-shared-access-signature-part-1.md)을 사용하여 Azure Storage의 데이터 개체에 대한 위임된 액세스 권한을 부여할 수 있습니다.

이 문서에서는 Azure Storage에서 사용할 수 있는 이러한 각 보안 기능에 대해 간략히 설명합니다. 각 항목을 좀 더 자세히 확인할 수 있도록 각 기능의 세부 정보를 제공하는 문서에 대한 링크가 제공됩니다.

다음은 이 문서에 설명된 항목입니다.

* [관리 평면 보안](#management-plane-security) - Storage 계정 보안 유지

  관리 평면은 저장소 계정 관리에 사용되는 리소스로 구성됩니다. 이 섹션에서는 Azure Resource Manager 배포 모델 및 RBAC(역할 기반 Access Control)를 사용하여 저장소 계정에 대한 액세스를 제어하는 방법을 다룹니다. 또한 저장소 계정 키의 관리와 이러한 키를 다시 생성하는 방법에 대해서도 설명합니다.
* [데이터 평면 보안](#data-plane-security) – 데이터에 대한 액세스 보안 유지

  이 섹션에서는 공유 액세스 서명 및 저장된 액세스 정책을 사용하여 Storage 계정에 있는 Blob, 파일, 큐, 테이블 등의 실제 데이터 개체에 대한 액세스를 허용하는 방법을 살펴볼 것입니다. 서비스 수준 SAS 및 계정 수준 SAS에 대한 설명이 제공됩니다. 또한 특정 IP 주소(또는 IP 주소 범위)에 대한 액세스를 제한하는 방법, 사용되는 프로토콜을 HTTPS로 제한하는 방법 및 만료를 기다리지 않고 공유 액세스 서명을 해지하는 방법도 알아봅니다.
* [전송 중 암호화](#encryption-in-transit)

  이 섹션에서는 Azure Storage 간에 전송하는 데이터를 보호하는 방법을 설명합니다. 그뿐 아니라 권장되는 HTTPS 사용과 Azure 파일 공유를 위해 SMB 3.0에서 사용되는 암호화에 대해 설명합니다. 또한 클라이언트 애플리케이션의 스토리지로 데이터가 전송되기 전에 암호화하고 스토리지 외부로 전송된 후에 암호를 해독할 수 있도록 하는 클라이언트 쪽 암호화도 살펴봅니다.
* [휴지 상태의 암호화](#encryption-at-rest)

  이제 신규 및 기존 스토리지 계정에 대해 자동으로 사용되도록 설정되는 SSE(Storage 서비스 암호화)에 대해 설명합니다. Azure 디스크 암호화를 사용하는 방법을 살펴보고, 디스크 암호화, SSE 및 클라이언트 쪽 암호화의 사례와 기본적인 차이점을 알아봅니다. 미국 정부 컴퓨터의 FIPS 준수에 대해서도 간단히 살펴봅니다.
* [스토리지 분석](#storage-analytics) 을 사용하여 Azure Storage에 대한 액세스 감사

  이 섹션에서는 스토리지 분석 로그에서 요청에 대한 정보를 찾는 방법을 설명합니다. 실제 스토리지 분석 로그 데이터에 대해 살펴보고, 성공 여부에 관계없이 Storage 계정 키 사용, 공유 액세스 서명 사용 또는 익명 방식 중 어떤 방식으로 요청이 수행되는지를 확인하는 방법을 알아봅니다.
* [CORS를 사용하여 브라우저 기반 클라이언트를 사용하도록 설정](#cross-origin-resource-sharing-cors)

  이 섹션에서는 CORS(크로스-원본 자원 공유)를 허용하는 방법을 설명합니다. 도메인 간 액세스와 Azure Storage에 기본 제공된 CORS 기능으로 이러한 액세스 방식을 처리하는 방법을 살펴봅니다.

## <a name="management-plane-security"></a>관리 평면 보안
관리 평면은 저장소 계정 자체에 영향을 주는 작업으로 구성되어 있습니다. 예를 들어 저장소 계정을 생성 또는 삭제하거나, 구독에서 저장소 계정 목록을 가져오거나, 저장소 계정 키를 검색하거나, 저장소 계정 키를 다시 생성할 수 있습니다.

새 저장소 계정을 만들 때 기본 배포 모델 또는 리소스 관리자 배포 모델 중에서 선택합니다. Azure에서 리소스를 만드는 기본 모델에서만 구독 및 저장소 계정에 대한 완전한 액세스를 허용합니다.

이 가이드는 저장소 계정을 만들기 위한 권장 방법인 Resource Manager 모델을 중점적으로 설명합니다. 전체 구독에 대한 액세스를 허용하지 않고 Resource Manager 저장소 계정을 사용하면 RBAC(역할 기반 Access Control)를 사용하여 관리 평면에 대해 보다 명확히 한정된 수준으로 액세스를 제어할 수 있습니다.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>RBAC(역할 기반 Access Control)를 사용하여 저장소 계정의 보안을 유지하는 방법
RBAC의 기능과 사용 방법에 대해 살펴보겠습니다. 각 Azure 구독에는 Azure Active Directory가 있습니다. 해당 디렉터리의 사용자, 그룹 및 애플리케이션에 리소스 관리자 배포 모델을 사용하는 Azure 구독에서 리소스를 관리할 수 있는 권한을 부여할 수 있습니다. 이러한 유형의 보안을 RBAC(역할 기반 액세스 제어)라고 합니다. 이 액세스를 관리하기 위해 [Azure Portal](https://portal.azure.com/), [Azure CLI 도구](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) 또는 [Azure Storage Resource Provider REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx)를 사용할 수 있습니다.

리소스 관리자 모델에서는 Azure Active Directory를 사용하여 리소스 그룹에 저장소 계정을 추가하고 해당 특정 저장소 계정의 관리 평면에 대한 액세스를 제어합니다. 예를 들어 특정 사용자에게는 저장소 계정 키에 액세스할 수 있는 기능을 제공하고 다른 사용자에게는 저장소 계정에 대한 정보는 볼 수 있지만 저장소 계정 키에는 액세스하지 못하게 할 수 있습니다.

#### <a name="granting-access"></a>액세스 권한 부여
올바른 범위에서 사용자, 그룹 및 애플리케이션에 적절한 RBAC 역할을 할당하여 액세스 권한을 부여합니다. 전체 구독에 대한 액세스 권한을 부여하려면 구독 수준에서 역할을 할당합니다. 리소스 그룹 자체에 대한 권한을 부여하여 리소스 그룹의 모든 리소스에 대한 액세스 권한을 부여할 수 있습니다. 또한 저장소 계정 등의 특정 리소스에 특정 역할을 할당할 수도 있습니다.

RBAC를 사용하여 Azure Storage 계정 관리 작업에 액세스하는 작업과 관련해서 알아야 할 주요 사항은 다음과 같습니다.

* 액세스 권한을 할당할 경우 기본적으로 액세스 권한을 부여하려는 계정에 역할을 할당합니다. 계정의 데이터 개체가 아니라 해당 저장소 계정을 관리하는 데 사용되는 작업에 대한 액세스를 제어할 수 있습니다. 예를 들어 Blob Storage의 컨테이너 또는 컨테이너 내의 데이터가 아니라 Storage 계정의 속성(예: 중복)을 검색할 수 있는 권한을 부여할 수 있습니다.
* 누군가에게 저장소 계정의 데이터 개체에 액세스할 수 있는 권한을 부여하려면 저장소 계정 키를 읽을 수 있는 권한을 부여할 수 있으며 해당 사용자는 해당 키를 사용하여 Blob, 큐, 테이블 및 파일에 액세스할 수 있습니다.
* 특정 사용자 계정, 사용자 그룹 또는 특정 애플리케이션에 역할을 할당할 수 있습니다.
* 각 역할에는 작업 목록 및 작업 안 함 목록이 있습니다. 예를 들어 Virtual Machine 참여자 역할에는 저장소 계정 키를 읽을 수 있도록 하는 “listKeys” 작업이 있습니다. 참여자 역할에는 Active Directory의 사용자에 대한 액세스를 업데이트하는 것과 같은 작업을 포함하는 “작업 안 함” 목록이 있습니다.
* 저장소에 대한 역할에는 다음 역할이 포함됩니다(이에 국한되지 않음).

  * 소유자 - 액세스를 제외한 모든 것을 관리할 수 있습니다.
  * 참여자 – 액세스 할당을 제외하고 소유자가 수행할 수 있는 모든 작업을 수행할 수 있습니다. 이 역할을 가진 사람은 저장소 계정 키를 보고 다시 생성할 수 있습니다. 저장소 계정 키가 있으면 데이터 개체에 액세스할 수 있습니다.
  * 읽기 권한자 - 암호를 제외한 저장소 계정에 대한 정보를 볼 수 있습니다. 예를 들어 저장소 계정에 대한 읽기 권한자 권한이 있는 역할을 다른 사람에게 할당하는 경우 해당 사용자는 저장소 계정의 속성을 볼 수 있지만 속성을 변경하거나 저장소 계정 키를 볼 수는 없습니다.
  * Storage 계정 참여자 – Storage 계정을 관리할 수 있고, 구독의 리소스 그룹 및 리소스를 읽을 수 있고, 구독 리소스 그룹 배포를 만들고 관리할 수 있습니다. 저장소 계정 키에도 액세스할 수 있으므로 데이터 평면에도 액세스할 수 있습니다.
  * 사용자 액세스 관리자 - 저장소 계정에 대한 사용자 액세스를 관리할 수 있습니다. 예를 들어 특정 사용자에게 읽기 관리자 액세스 권한을 부여할 수 있습니다.
  * Virtual Machine 참여자 - 가상 컴퓨터를 관리할 수 있으나 해당 컴퓨터가 연결된 저장소 계정은 관리할 수 없습니다. 이 역할이 있으면 저장소 계정 키를 나열할 수 있습니다. 즉, 이 역할을 할당 받은 사용자는 데이터 평면을 업데이트할 수 있습니다.

    사용자가 가상 머신을 만들 수 있으려면 저장소 계정에 해당 VHD 파일을 만들 수 있어야 합니다. 이렇게 하려면 저장소 계정 키를 검색하고 VM을 만드는 API에 전달할 수 있어야 합니다. 따라서 이러한 권한이 있어야 저장소 계정 키를 나열할 수 있습니다.
* 사용자 지정 역할을 정의할 수 있으면 Azure 리소스에 대해 수행할 수 있는 사용 가능한 작업 목록에서 작업 집합을 작성할 수 있습니다.
* 사용자에게 역할을 할당하려면 먼저 Azure Active Directory에서 해당 사용자를 설정해야 합니다.
* PowerShell 또는 Azure CLI를 사용하여 누가 어떤 사용자에게 어떤 종류의 액세스 권한을 어떤 범위까지 부여/해지했는지에 대한 보고서를 만들 수 있습니다.

#### <a name="resources"></a>리소스
* [Azure Active Directory 역할 기반 Access Control](../../role-based-access-control/role-assignments-portal.md)

  이 문서에서는 Azure Active Directory 역할 기반 Access Control 및 작동 방식에 대해 설명합니다.
* [RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)

  이 문서에서는 RBAC에서 사용할 수 있는 기본 제공 역할의 모든 사항을 자세히 설명합니다.
* [리소스 관리자 배포 및 클래식 배포 이해](../../azure-resource-manager/resource-manager-deployment-model.md)

  이 문서에서는 리소스 관리자 배포 및 기존 배포 모델에 대해 설명하고 리소스 관리자를 사용할 때와 리소스 그룹을 사용할 때의 이점에 대해서도 설명합니다. Resource Manager 모델에서 Azure Compute, 네트워크 및 Storage 공급자가 작동하는 방식에 대해 설명합니다.
* [REST API를 사용하여 역할 기반 Access Control 관리](../../role-based-access-control/role-assignments-rest.md)

  이 문서에서는 REST API를 사용하여 RBAC를 관리하는 방법을 보여 줍니다.
* [Azure Storage 리소스 공급자 REST API 참조](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  이 API 참조는 저장소 계정을 프로그래밍 방식으로 관리하는 데 사용할 수 있는 API에 대해 설명합니다.
* [Resource Manager 인증 API를 사용하여 구독에 액세스](../../azure-resource-manager/resource-manager-api-authentication.md)

  이 문서에서는 Resource Manager API를 사용하여 인증을 수행하는 방법을 보여 줍니다.
* [Ignite를 사용한 Microsoft Azure에 대한 역할 기반 Access Control](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  2015 MS Ignite 회의와 관련해서 Channel 9에 게시된 비디오에 대한 링크입니다. 이 세션에서는 Azure의 액세스 관리 및 보고 기능에 대해 논의하고 Azure Active Directory를 사용하여 Azure 구독에 대한 액세스의 보안을 유지하는 모범 사례를 알아봅니다.

### <a name="managing-your-storage-account-keys"></a>Storage 계정 키 관리
Storage 계정 키는 Azure에서 생성되는 512비트 문자열로, Storage 계정에 저장된 데이터 개체(예: Blob, 테이블 내 엔터티, 큐 메시지, Azure 파일 공유의 파일)에 액세스하기 위해 Storage 계정 이름과 함께 사용될 수 있습니다. 저장소 계정 키에 대한 액세스를 제어하면 해당 저장소 계정의 데이터 평면에 대한 액세스도 제어됩니다.

각 저장소 계정에는 [Azure Portal](https://portal.azure.com/) 및 PowerShell cmdlet에서 "키 1"과 "키 2"로 지칭되는 두 개의 키가 있습니다. 이러한 키는 [Azure 포털](https://portal.azure.com/), PowerShell, Azure CLI를 사용하거나 .NET Storage Client Library 또는 Azure Storage Services REST API를 사용하여 프로그래밍 방식으로 다시 생성할 수도 있습니다.

저장소 계정 키를 다시 생성하는 이유는 많습니다.

* 보안상의 이유로 정기적으로 다시 생성할 수 있습니다.
* 누군가가 애플리케이션에 간신히 해킹하여 구성 파일에 하드 코딩되었거나 저장된 키를 검색함으로써 사용자의 스토리지 계정에 대한 모든 권한을 얻게 되면 스토리지 계정 키를 다시 생성해야 합니다.
* 키를 다시 생성하는 또 다른 경우는 팀이 Storage 계정 키를 유지하는 스토리지 탐색기 응용 프로그램을 사용하고 있는데 팀 구성원 중 하나가 팀을 탈퇴하는 경우입니다. 애플리케이션은 계속 작동되고 해당 구성원이 나간 후에도 스토리지 계정에 대한 액세스 권한을 부여합니다. 실제로 이것이 액세스 수준 공유 액세스 서명을 만드는 주요 이유입니다. 이러한 문제를 방지하기 위해 구성 파일에 액세스 키를 저장하는 대신 계정 수준 SAS를 사용할 수 있습니다.

#### <a name="key-regeneration-plan"></a>키 다시 생성 계획
사용 중인 키를 계획 없이 그냥 다시 생성하고 싶지는 않을 것입니다. 이렇게 하면 해당 저장소 계정에 대한 모든 액세스가 차단되어 심각한 업무 중단이 발생할 수 있습니다. 이 때문에 두 개의 키를 준비하는 것입니다. 한 번에 하나의 키를 다시 생성해야 합니다.

키를 다시 생성하기 전에 저장소 계정에 종속되는 모든 애플리케이션의 목록과 Azure에서 사용하는 다른 서비스 목록을 준비해야 합니다. 예를 들어 저장소 계정에 종속된 Azure Media Services를 사용하는 경우 키를 다시 생성한 후 미디어 서비스와 액세스 키를 다시 동기화해야 합니다. 저장소 탐색기와 같은 애플리케이션을 사용하는 경우 해당 애플리케이션에도 새 키를 제공 해야 합니다. 해당 VHD 파일이 저장소 계정에 저장되어 있는 VM의 경우 저장소 계정 키를 다시 생성해도 영향을 받지 않습니다.

Azure 포털에서 키를 다시 생성할 수 있습니다. 키가 다시 생성되면 Storage 서비스에서 동기화되는 데 최대 10분이 걸릴 수 있습니다.

준비가 완료되면 키를 변경하는 방법을 자세히 설명하는 다음의 일반 프로세스를 참조하세요. 이 경우 사용자가 현재 키 1을 사용하고 있으며 키 2를 대신 사용하도록 모든 항목을 변경하려 한다고 가정합니다.

1. 확실한 결과를 얻기 위해 키 2를 다시 생성합니다. 이 작업은 Azure 포털에서 수행할 수 있습니다.
2. 스토리지 키가 저장되는 모든 애플리케이션에서 키 2의 새 값을 사용하도록 스토리지 키를 변경합니다. 애플리케이션을 테스트한 후 게시합니다.
3. 모든 애플리케이션과 서비스가 성공적으로 시작되고 실행되면 키 1을 다시 생성합니다. 이렇게 하면 새 키를 명시적으로 부여받지 못한 사람은 누구나 더 이상 저장소 계정에 액세스할 수 없습니다.

현재 키 2를 사용할 경우 동일한 프로세스를 사용할 수 있지만 키 이름을 되돌려야 합니다.

며칠에 걸쳐 마이그레이션을 수행하여 각 애플리케이션이 새 키를 사용하도록 변경한 후 게시할 수 있습니다. 이러한 작업이 모두 완료되면 돌아가서 더 이상 작동하지 않도록 이전 키를 다시 생성해야 합니다.

다른 옵션은 저장소 계정 키를 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에 암호로 추가하고 애플리케이션이 해당 위치에서 키를 검색하도록 하는 것입니다. 그런 다음, 키를 다시 생성하고 Azure Key Vault를 업데이트하면 애플리케이션이 Azure Key Vault에서 자동으로 새 키를 선택하게 되므로 애플리케이션을 다시 배포하지 않아도 됩니다. 필요할 때마다 애플리케이션에서 키를 읽도록 하거나, 메모리에 캐시한 후 사용에 실패할 경우 Azure Key Vault에서 키를 다시 검색할 수 있습니다.

또한 Azure Key Vault를 사용하면 저장소 키에 대한 보안이 한 층 더 강화됩니다. 이 방법을 사용하는 경우 저장소 키가 구성 파일에 하드 코딩되지 않게 되며, 누군가가 특정 권한 없이도 키에 액세스할 수 있는 공간이 없어지는 셈이 됩니다.

Azure Key Vault를 사용하면 Azure Active Directory를 사용하여 키에 대한 액세스를 제어할 수 있다는 또 다른 장점도 있습니다. 즉, Azure Key Vault에서 키를 검색해야 하는 다양한 애플리케이션에 액세스 권한을 부여할 수 있으며, 특별히 권한을 부여 받지 못한 다른 애플리케이션은 키에 액세스할 수 없다는 것을 알 수 있습니다.

> [!NOTE]
> 모든 애플리케이션에서 키 중 하나만 동시에 사용하는 것이 좋습니다. 어떤 경우에는 키 1을 사용하고, 다른 경우에는 키 2를 사용하면 어떤 애플리케이션이 액세스 권한을 상실해야만 키를 순환할 수 있게 됩니다.

#### <a name="resources"></a>리소스

* [Azure Portal에서 저장소 계정 설정 관리](storage-account-manage.md)
* [Azure Storage 리소스 공급자 REST API 참조](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>데이터 평면 보안
데이터 평면 보안은 Azure Storage에 저장된 데이터 개체(Blob, 큐, 테이블 및 파일)의 보안을 유지하는 데 사용되는 방법을 나타냅니다. 데이터가 전송되는 동안 데이터 및 보안을 암호화하는 방법을 살펴보았습니다. 그렇다면 개체에 대한 액세스를 제어하는 방법은 어디에서 확인할까요?

Azure Storage에서 데이터 개체에 대한 액세스를 인증하는 데 다음을 포함한 세 가지 옵션이 있습니다.

- Azure AD를 사용 하 여 컨테이너 및 큐에 대 한 액세스 권한을 부여 합니다. Azure AD는 코드에서 암호를 저장할 필요성 제거를 포함하여 권한 부여에 대한 다른 인증 방법에 비해 여러 가지 이점을 제공합니다. 자세한 내용은 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](storage-auth-aad.md)을 참조하세요. 
- 저장소 계정 키를 사용하여 공유 키를 통해 액세스 권한을 부여합니다. 공유 키를 통한 권한 부여는 애플리케이션에 저장소 계정 키를 저장해야 하므로 가능한 경우 대신 Azure AD를 사용하는 것이 좋습니다.
- 공유 액세스 서명을 사용하여 특정 시간 동안 특정 데이터 개체에 제어된 권한을 부여합니다.

또한 Blob Storage의 경우 Blob을 포함하는 컨테이너에 대한 액세스 수준을 적절히 설정하여 Blob에 대한 공용 액세스를 허용할 수 있습니다. 컨테이너에 대한 액세스를 Blob 또는 컨테이너로 설정하는 경우 해당 컨테이너의 Blob에 대해 공용 읽기 액세스가 허용됩니다. 즉, 해당 컨테이너의 Blob을 가리키는 URL이 있으면 누구나 공유 액세스 서명이나 저장소 계정 키 없이도 브라우저에서 열 수 있습니다.

권한 부여를 통해 액세스를 제한할 뿐 아니라 [방화벽 및 가상 네트워크](storage-network-security.md)를 사용하여 네트워크 규칙에 따라 저장소 계정에 대한 액세스를 제한할 수도 있습니다.  이 방법을 통해 공용 인터넷 트래픽에 대한 액세스를 거부하고 특정 Azure Virtual Network 또는 공용 인터넷 IP 주소 범위에 대한 액세스 권한만 부여할 수 있습니다.

### <a name="storage-account-keys"></a>Storage 계정 키
Storage 계정 키는 Azure에서 생성되는 512비트 문자열로, Storage 계정에 저장된 데이터 개체에 액세스하기 위해 Storage 계정 이름과 함께 사용될 수 있습니다.

예를 들어, Blob을 읽고, 큐에 쓰고, 테이블을 만들고, 파일을 수정할 수 있습니다. Azure 포털을 통해 또는 많은 Storage Explorer 애플리케이션 중 하나를 사용하여 이러한 많은 작업을 수행할 수 있습니다. REST API 또는 Storage 클라이언트 라이브러리 중 하나를 사용하여 이러한 작업을 수행하기 위한 코드를 작성할 수도 있습니다.

[관리 평면 보안](#management-plane-security)에 대한 섹션에 설명된 대로 Azure 구독에 대한 모든 권한을 부여하여 기본 저장소 계정의 저장소 키에 대한 액세스 권한을 부여할 수 있습니다. Azure Resource Manager 모델을 사용하는 저장소 계정의 저장소 키에 대한 액세스는 RBAC(역할 기반 Access Control)를 통해 제어할 수 있습니다.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>공유 액세스 서명 및 저장된 액세스 정책을 사용하여 계정의 개체에 대한 액세스를 위임하는 방법
공유 액세스 서명은 URI에 추가할 수 있는 보안 토큰을 포함하는 문자열로, 저장소 개체에 대한 액세스 권한을 위임하고 사용 권한 및 액세스의 날짜/시간 범위와 같은 제한을 지정할 수 있도록 합니다.

Blob, 컨테이너, 큐 메시지, 파일 및 테이블에 대한 액세스 권한을 부여할 수 있습니다. 테이블의 경우 실제로 사용자가 액세스하도록 하려는 파티션 및 행 키 범위를 지정하여 테이블의 엔터티 범위에 액세스할 수 있는 권한을 부여할 수 있습니다. 예를 들어 주 지역의 파티션 키를 사용하여 데이터를 저장한 경우 누군가에게 캘리포니아의 데이터에 대한 액세스 권한만 부여할 수 있습니다.

또 다른 예로, 웹 애플리케이션에 큐에 항목을 쓸 수 있도록 하는 SAS 토큰을 제공하고, 작업자 역할 애플리케이션에 큐에서 메시지를 가져온 후 처리하기 위한 SAS 토큰을 제공할 수 있습니다. 또는 Blob Storage의 컨테이너에 사진을 업로드하는 데 사용할 수 있는 SAS 토큰을 고객 한 명에게 제공하고 웹 애플리케이션에 해당 사진을 읽기 위한 권한을 부여할 수 있습니다. 두 경우에서 중요한 측면은 각기 다릅니다. 즉, 각 애플리케이션에 해당 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 이러한 작업은 공유 액세스 서명을 사용하여 수행할 수 있습니다.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>공유 액세스 서명을 사용하려는 이유
저장소 계정 키를 제공하는 대신 SAS를 사용하려고 하는 이유는 무엇일까요? 어떤 방법이 훨씬 더 쉬울까요? 저장소 계정 키를 제공하는 것은 저장소 왕국의 키를 공유하는 것과 비슷합니다. 완전한 액세스 권한을 부여하게 됩니다. 누군가가 사용자의 키를 사용하여 전체 음악 라이브러리를 저장소 계정에 업로드할 수 있습니다. 파일을 바이러스에 감염된 버전으로 바꿀 수도 있고 데이터를 훔칠 수도 있습니다. 저장소 계정에 대해 무제한 액세스 권한을 부여하는 일은 가볍게 넘어갈 수 있는 일이 아닙니다.

공유 액세스 서명을 사용하여 제한된 기간 동안만 필요한 사용 권한을 클라이언트에 제공할 수 있습니다. 예를 들어 누군가가 사용자의 계정에 Blob을 업로드하는 경우 해당 사용자에게 Blob을 업로드할 수 있는 충분한 시간(Blob의 크기에 따라 다름) 동안 쓰기 권한을 부여할 수 있습니다. 또한 마음이 바뀌면 부여한 권한을 취소할 수 있습니다.

그뿐 아니라 SAS를 사용하여 수행한 요청이 Azure 외부의 특정 IP 주소 또는 IP 주소 범위로 제한되도록 지정할 수 있습니다. 특정 프로토콜(HTTPS 또는 HTTP/HTTPS)을 사용하여 요청이 수행되도록 요구할 수도 있습니다. 즉, HTTPS 트래픽만 허용하려면 필요한 프로토콜을 HTTPS로만 설정할 수 있으며 이 경우 HTTP 트래픽은 차단됩니다.

#### <a name="definition-of-a-shared-access-signature"></a>공유 액세스 서명의 정의
공유 액세스 서명은 허용되는 액세스에 대한 정보와 액세스가 허용되는 기간을 제공하는

리소스를 가리키는 URL에 추가된 쿼리 매개 변수 집합입니다. 다음은 예제입니다. 이 URI는 5분 동안 Blob에 대한 읽기 권한을 제공합니다. SAS 쿼리 매개 변수는 URL로 인코딩되어야 합니다(예: 콜론(:)의 경우 %3A, 공백의 경우 %20).

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Azure Storage 서비스에서 공유 액세스 서명에 권한을 부여하는 방법
저장소 서비스가 요청을 받으면 입력 쿼리 매개 변수를 수용한 후 호출 프로그램과 동일한 방법을 사용하여 서명을 만듭니다. 그런 후 두 서명을 비교합니다. 두 서명이 일치하면 저장소 서비스는 저장소 서비스 버전이 유효한지 확인하고, 현재 날짜 및 시간이 지정된 기간 내에 속하는지 확인하고, 요청된 액세스가 수행된 요청과 일치할 수 있습니다.

예를 들어 위의 URL이 Blob 대신 파일을 가리킨다면 공유 액세스 서명은 Blob에 대한 것이라고 지정하고 있으므로 이 요청은 실패할 것입니다. 호출되는 REST 명령이 Blob을 업데이트하는 명령이라면 공유 액세스 서명은 읽기 액세스만 허용된다고 지정하고 있으므로 실패할 것입니다.

#### <a name="types-of-shared-access-signatures"></a>공유 액세스 서명의 유형
* 서비스 수준 SAS를 사용하여 저장소 계정의 특정 리소스에 액세스할 수 있습니다. 이에 대한 몇 가지 예로는 컨테이너의 Blob 목록 검색, Blob 다운로드, 테이블의 엔터티 업데이트, 큐에 메시지 추가, 파일 공유에 파일 업로드 등이 있습니다.
* 서비스 수준 SAS를 사용하여 서비스 수준 SAS가 사용될 수 있는 모든 항목에 액세스할 수 있습니다. 또한 컨테이너, 테이블, 큐 및 파일 공유를 만드는 능력과 같이 서비스 수준 SAS를 통해서는 허용되지 않는 리소스에 대한 옵션이 제공될 수 있습니다. 한 번에 여러 서비스에 대한 액세스를 지정할 수도 있습니다. 예를 들어 누군가에게 사용자의 저장소 계정에 있는 Blob 및 파일에 대한 액세스 권한을 부여할 수도 있습니다.

#### <a name="creating-a-sas-uri"></a>SAS URI 만들기
1. 필요할 때마다 모든 쿼리 매개 변수를 정의하여 URI를 만들 수 있습니다.

   이 방식은 유연한 방법이지만 매번 비슷한 매개 변수의 논리적 집합을 사용한다면 저장된 액세스 정책을 사용하는 것이 더 좋습니다.
2. 전체 컨테이너, 파일 공유, 테이블 또는 큐에 대해 저장된 액세스 정책을 만들 수 있습니다. 그런 다음 이 정책을 만드는 SAS URI의 기반으로 사용할 수 있습니다. 저장된 액세스 정책을 기준으로 하는 사용 권한은 쉽게 해지할 수 있습니다. 각 컨테이너, 큐, 테이블 또는 파일 공유에 대해 최대 5개의 정책을 정의할 수 있습니다.

   예를 들어 많은 사람들이 특정 컨테이너의 Blob을 읽을 수 있게 하려면 다른 설정은 항상 동일하게 유지하고 “읽기 액세스 부여”라는 저장된 액세스 정책을 만들 수 있습니다. 그런 다음 해당 저장된 액세스 정책의 설정을 사용하고 만료 날짜/시간을 지정하여 SAS URI를 만들 수 있습니다. 이렇게 하면 매번 모든 쿼리 매개 변수를 지정할 필요가 없습니다.

#### <a name="revocation"></a>해지
SAS가 손상되었거나, 회사 보안 또는 규정 준수 요구 때문에 변경하려는 경우를 가정해 봅니다. 해당 SAS를 사용하여 리소스에 대한 액세스를 해지하려면 어떻게 할까요? SAS URI를 만든 방법에 따라 다릅니다.

임시 URI를 사용하는 경우 세 가지 옵션이 있습니다. 만료 기간이 짧은 정책을 사용하여 SAS 토큰을 발급하고 해당 SAS가 만료되기를 기다릴 수 있습니다. 리소스의 이름을 변경하거나 삭제할 수 있습니다(토큰 범위가 단일 개체로 지정되었다고 가정). 저장소 계정 키를 변경할 수 있습니다. 이 마지막 옵션은 해당 저장소 계정을 사용하는 서비스 수에 따라 지대한 영향을 미칠 수 있으므로 계획 없이 수행할 수 있는 단순한 작업은 아닐 것입니다.

저장된 액세스 정책에서 파생된 SAS를 사용하는 경우 저장된 액세스 정책을 취소하여 액세스 권한을 제거할 수 있습니다. 즉, 이미 만료된 상황이므로 그냥 변경하거나 함께 제거할 수 있습니다. 이러한 작업은 즉시 적용되며 해당 저장된 액세스 정책을 사용하여 만든 모든 SAS가 무효화됩니다. 저장된 액세스 정책을 업데이트하거나 제거하면 SAS를 통해 해당 특정 컨테이너, 파일 공유, 테이블 또는 큐에 액세스하는 사용자에게 영향을 줄 수 있지만 이전 SAS가 무효화될 때 클라이언트가 새 SAS를 요청하도록 구성된 경우에는 정상적으로 작동합니다.

저장된 액세스 정책에서 파생된 SAS를 사용하면 해당 SAS를 즉시 해지할 수 있으므로 가능한 경우 항상 저장된 액세스 정책을 사용하는 것이 바람직합니다.

#### <a name="resources"></a>리소스
공유 액세스 서명 및 저장된 액세스 정책 사용에 대한 자세한 내용과 예제를 보려면 다음 문서를 참조하세요.

* 다음은 참조 문서입니다.

  * [서비스 SAS(영문)](https://msdn.microsoft.com/library/dn140256.aspx)

    이 문서에서는 Blob, 큐 메시지, 테이블 범위 및 파일에서 서비스 수준 SAS를 사용하는 예제를 제공합니다.
  * [서비스 SAS 생성(영문)](https://msdn.microsoft.com/library/dn140255.aspx)
  * [계정 SAS 생성(영문)](https://msdn.microsoft.com/library/mt584140.aspx)
* .NET 클라이언트 라이브러리를 사용하여 공유 액세스 서명 및 저장된 액세스 정책을 만드는 방법에 대한 자습서입니다.

  * [SAS(공유 액세스 서명) 사용](../storage-dotnet-shared-access-signature-part-1.md)
  * [공유 액세스 서명 2부: Blob Service를 사용하여 SAS 만들기 및 사용](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    이 문서에서는 SAS 모델에 대한 설명, 공유 액세스 서명의 예, SAS에 대한 권장 모범 사용 사례를 제공합니다. 부여된 사용 권한을 해지하는 방법도 설명합니다.

* Authentication

  * [Azure Storage 서비스에 대한 인증](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* 공유 액세스 서명 시작 자습서

  * [SAS 시작 자습서(영문)](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>전송 중 암호화
### <a name="transport-level-encryption--using-https"></a>전송 수준 암호화 - HTTPS 사용
Azure Storage 데이터의 보안을 유지하기 위해 수행해야 하는 또 다른 단계는 클라이언트와 Azure Storage 간에 데이터를 암호화하는 것입니다. 첫 번째 권장 지침은 항상 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 프로토콜을 사용하여 공용 인터넷에서의 통신 보안을 유지하는 것입니다.

보안 통신 채널을 포함하려면 REST API를 호출하거나 저장소의 개체를 액세스할 때는 항상 HTTPS를 사용해야 합니다. 또한 Azure Storage 개체에 대한 액세스를 위임하는 데 사용할 수 있는 **공유 액세스 서명**에는 공유 액세스 서명을 사용할 때 HTTPS 프로토콜만 사용할 수 있도록 지정하여 SAS 토큰이 있는 링크를 보내는 누구든지 적절한 프로토콜을 사용할 수 있도록 하는 옵션이 포함되어 있습니다.

저장소 계정에 대해 [보안 전송 필요](../storage-require-secure-transfer.md)를 설정하여 저장소 계정의 개체에 액세스하도록 REST API를 호출할 때 HTTPS 사용을 적용할 수 있습니다. 설정된 경우 HTTP를 사용한 연결은 거부됩니다.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>전송 중에 Azure 파일 공유에 암호화 사용
[Azure Files](../files/storage-files-introduction.md)는 파일 REST API를 사용하는 경우 HTTPS와 SMB 3.0을 통해 암호화를 지원합니다. Azure 지역 외부에서 탑재하는 경우 Azure 파일 공유는 암호화가 항상 필요한 온-프레미스 또는 다른 Azure 지역과 같은 SMB 3.0에 위치합니다. SMB 2.1은 암호화를 지원하지 않으므로 기본적으로 연결은 Azure의 동일한 지역 내에서만 허용되지만 암호화를 사용하는 SMB 3.0은 저장소 계정에 대해 [보안 전송을 요구](../storage-require-secure-transfer.md)하여 적용될 수 있습니다.

암호화를 사용하는 SMB 3.0은 SMB 2.1만을 지원하는 Windows 7 및 Windows Server 2008 R2를 제외하고 [지원되는 모든 Windows 및 Windows Server 운영 체제](../files/storage-how-to-use-files-windows.md)에서 사용 가능합니다. SMB 3.0은 [macOS](../files/storage-how-to-use-files-mac.md) 및 Linux 커널 4.11 이상을 사용하는 [Linux](../files/storage-how-to-use-files-linux.md)의 배포판에서도 지원됩니다. SMB 3.0에 대한 암호화 지원은 여러 Linux 배포판으로 이전 버전의 Linux 커널로 백포팅되었습니다. [SMB 클라이언트 요구 사항 이해](../files/storage-how-to-use-files-linux.md#smb-client-reqs)를 참조하세요.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>클라이언트 쪽 암호화를 사용하여 저장소에 전송하는 데이터 보안 유지
데이터가 클라이언트 애플리케이션 및 스토리지 간에 전송되는 동안 보안을 유지하는 데 도움이 되는 또 다른 옵션은 클라이언트 쪽 암호화입니다. 데이터는 Azure Storage로 전송되기 전에 암호화됩니다. Azure Storage에서 데이터를 검색하는 경우 클라이언트 쪽에서 수신된 후에 데이터 암호가 해독됩니다. 데이터가 네트워크를 통해 이동되지만 데이터 무결성에 영향을 미치는 네트워크 오류를 줄이는 데 도움이 되는 HTTPS를 사용하는 것도 좋습니다.

클라이언트 쪽 암호화 또한 데이터가 암호화된 형태로 저장되므로 미사용 데이터를 암호화하는 방법입니다. 이 내용은 [휴지 상태의 암호화](#encryption-at-rest)에 대한 섹션에서 좀 더 자세히 알아보겠습니다.

## <a name="encryption-at-rest"></a>휴지 상태의 암호화
휴지 상태의 암호화를 제공하는 세 가지 Azure 기능이 있습니다. Azure 디스크 암호화는 IaaS Virtual Machines에서 OS 및 데이터 디스크를 암호화하는 데 사용합니다. 클라이언트 쪽 암호화와 SSE는 Azure Storage의 데이터를 암호화하는 데 사용됩니다. 

클라이언트 쪽 암호화를 사용하여 전송 중인 데이터를 암호화할 수 있지만(암호화된 형태로 저장소에 저장되기도 함) 전송 중에 HTTPS를 사용하고 저장될 때 데이터가 자동으로 암호화되는 방법을 원할 수 있습니다. 이 작업은 Azure 디스크 암호화와 SSE의 두 가지 방법으로 수행할 수 있습니다. 하나는 VM에 사용되는 OS 및 데이터 디스크에 있는 데이터를 직접 암호화하는 데 사용되고, 다른 하나는 Azure Blob Storage에 기록되는 데이터를 암호화하는 데 사용됩니다.

### <a name="storage-service-encryption-sse"></a>SSE(Storage 서비스 암호화)

SSE는 모든 저장소 계정에 대해 사용되도록 설정되며 사용되지 않도록 설정할 수 없습니다. SSE는 Azure Storage에 쓸 때 자동으로 데이터를 암호화합니다. Azure Storage에서 데이터를 읽을 때 데이터가 반환되기 전에 Azure Storage에서 암호가 해독됩니다. SSE를 사용하여 코드를 수정하거나 애플리케이션에 코드를 추가하지 않고도 데이터를 보호할 수 있습니다.

Microsoft 관리 키 또는 자체 사용자 지정 키를 사용할 수 있습니다. Microsoft에서는 관리 키를 생성하고 내부 Microsoft 정책에 정의된 대로 키의 정기적인 순환뿐 아니라 보안 저장도 처리합니다. 사용자 지정 키 사용에 대한 자세한 내용은 [Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화](storage-service-encryption-customer-managed-keys.md)를 참조하세요.

SSE는 모든 성능 계층(표준 및 프리미엄), 모든 배포 모델(Azure Resource Manager 및 클래식) 및 모든 Azure Storage 서비스(Blob, 큐, 테이블 및 파일)의 데이터를 자동으로 암호화합니다. 

### <a name="client-side-encryption"></a>클라이언트 쪽 암호화
전송 중인 데이터 암호화를 설명하면서 클라이언트 쪽 암호화를 언급했습니다. 이 기능을 사용하면 Azure Storage에 쓸 데이터를 네트워크를 통해 보내기 전에 클라이언트 애플리케이션에서 프로그래밍 방식으로 암호화하고, Azure Storage에서 검색한 후에 프로그래밍 방식으로 데이터의 암호를 해독할 수 있습니다.

이 기능은 전송 중에 데이터를 암호화하지만 휴지 상태의 암호화 기능은 제공하지 않습니다. 데이터가 전송 중에 암호화되지만, 여전히 HTTPS를 사용하여 데이터의 무결성에 영향을 주는 네트워크 오류 완화에 도움이 되는 기본 제공 데이터 무결성 확인 기능을 활용하는 것이 좋습니다.

이 기능은 Blob을 저장하고 Blob을 검색하는 웹 애플리케이션이 있을 때 애플리케이션 및 데이터를 가능한 한 안전하게 유지하려는 경우 등에 사용할 수 있습니다. 이 경우 클라이언트 쪽 암호화를 사용할 수 있습니다. 클라이언트와 Azure Blob 서비스 간의 트래픽에는 암호화된 리소스가 포함되어 있으며 누구도 전송 중인 데이터를 해석하여 개인 Blob으로 다시 구성할 수 없습니다.

클라이언트 쪽 암호화가 Java 및 .NET 저장소 클라이언트 라이브러리에 기본적으로 제공되어 있으며, 여기에서 Azure Key Vault API가 사용하므로 구현하기가 쉽습니다. 데이터 암호화 및 해독 프로세스는 봉투(envelope) 기법을 사용하고 암호화에 사용되는 메타데이터를 각 저장소 개체에 저장합니다. 예를 들어 Blob의 경우 Blob 메타데이터에 저장하고, 큐의 경우에는 각 큐 메시지에 추가됩니다.

암호화 자체를 위해 자체 암호화 키를 생성하고 관리할 수 있습니다. Azure Storage 클라이언트 라이브러리에 의해 생성된 키를 사용하거나 Azure Key Vault에서 키를 생성하도록 할 수 있습니다. 온-프레미스 키 저장소에 암호화 키를 저장하거나 Azure Key Vault에 저장할 수 있습니다. Azure Key Vault를 사용하면 Azure Active Directory를 사용하여 특정 사용자에게 Azure Key Vault의 암호에 대한 액세스 권한을 부여할 수 있습니다. 즉, 특정한 사용자만이 Azure Key Vault를 읽고 클라이언트 쪽 암호화에 사용 중인 키를 검색할 수 있습니다.

#### <a name="resources"></a>리소스
* [Microsoft Azure Storage에서 Azure Key Vault를 사용하여 Blob 암호화 및 해독](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  이 문서에서는 PowerShell을 사용하여 KEK를 만든 후 자격 증명 모음에 저장하는 방법을 비롯하여 Azure Key Vault와 클라이언트 쪽 암호화를 사용하는 방법을 보여 줍니다.
* [Microsoft Azure Storage용 클라이언트 쪽 암호화 및 Azure Key Vault](../storage-client-side-encryption.md)

  이 문서에서는 클라이언트 쪽 암호화에 대해 설명하고, 저장소 클라이언트 라이브러리를 사용하여 4개의 저장소 서비스에서 리소스를 암호화하고 해독하는 예를 제공합니다. 또한 Azure Key Vault에 대해서도 설명합니다.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Azure 디스크 암호화를 사용하여 가상 머신에 사용되는 디스크 암호화
Azure Disk Encryption을 사용 하면 OS 디스크 및 IaaS Virtual Machine에서 사용 되는 데이터 디스크를 암호화할 수 있습니다. Windows의 경우 업계 표준의 BitLocker 암호화 기술을 사용하여 드라이브가 암호화됩니다. Linux의 경우 DM-Crypt 기술을 사용하여 디스크가 암호화됩니다. 이 기술은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어 및 관리할 수 있도록 합니다.

이 솔루션은 Microsoft Azure에서 사용되도록 설정될 경우 IaaS VM에 대해 다음 시나리오를 지원합니다.

* Azure Key Vault와 통합
* 표준 계층 VM: [A, D, DS, G, GS 및 기타 시리즈 IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Windows 및 Linux IaaS VM에서 암호화 사용
* Windows IaaS VM에 대한 OS 및 데이터 드라이브에서 암호화 사용 안 함
* Linux IaaS VM에 대한 데이터 드라이브에서 암호화 사용 안 함
* Windows 클라이언트 OS를 실행하는 IaaS VM에서 암호화 사용
* 탑재 경로가 있는 볼륨에서 암호화 사용
* mdadm을 사용하여 디스크 스트라이프(RAID)로 구성된 Linux VM에서 암호화 사용
* 데이터 디스크에 대해 LVM을 사용하여 Linux VM에서 암호화 사용
* 저장소 공간을 사용하여 구성된 Windows VM에서 암호화 사용
* 모든 Azure 공용 지역 지원됨

이 솔루션은 릴리스에서 다음 시나리오, 기능 및 기술을 지원하지 않습니다.

* 기본 계층 IaaS VM
* Linux IaaS VM에 대한 OS 드라이브에서 암호화 사용 안 함
* 클래식 VM 만들기 방법을 사용하여 만든 IaaS VM
* 온-프레미스 키 관리 서비스와의 통합
* Azure 파일(공유 파일 시스템), NFS(네트워크 파일 시스템), 동적 볼륨, 소프트웨어 기반 RAID 시스템으로 구성된 Windows VM


> [!NOTE]
> Linux OS 디스크 암호화는 현재 Linux 배포 버전 RHEL 7.2, CentOS 7.2n, Ubuntu 16.04에서 지원됩니다.
>
>

이 기능은 가상 머신 디스크에 있는 모든 데이터가 휴지 상태로 Azure Storage에 암호화되도록 합니다.

#### <a name="resources"></a>리소스
* [Windows 및 Linux IaaS VM용 Azure 디스크 암호화](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Azure 디스크 암호화, SSE 및 클라이언트 쪽 암호화의 비교

#### <a name="iaas-vms-and-their-vhd-files"></a>IaaS VM 및 해당 VHD 파일

IaaS VM에서 사용되는 데이터 디스크의 경우 Azure Disk Encryption이 권장됩니다. Azure Marketplace에서 이미지를 사용하여 관리되지 않는 디스크가 있는 VM을 만드는 경우 Azure는 Azure Storage의 Storage 계정에 대해 이미지의 [단순 복사](https://en.wikipedia.org/wiki/Object_copying)를 수행하며, SSE가 사용되도록 설정되어 있더라도 암호화되지 않습니다. VM을 만들고 이미지 업데이트를 시작한 후에는 SSE가 데이터 암호화를 시작합니다. 따라서 완전히 암호화하려는 경우 Azure Marketplace의 이미지에서 만든 관리되지 않는 디스크가 있는 VM에 대해 Azure 디스크 암호화를 사용하는 것이 좋습니다. Managed Disks가 있는 VM을 만들 경우 SSE는 플랫폼 관리 키를 사용하여 기본적으로 모든 데이터를 암호화합니다. 

온-프레미스에서 Azure로 미리 암호화된 VM을 가져오는 경우 Azure Key Vault에 암호화 키를 업로드할 수 있으며 사용하던 해당 VM에 대한 암호화를 온-프레미스에서 계속 사용할 수 있습니다. 이 시나리오를 위해 Azure 디스크 암호화가 사용되도록 설정됩니다.

온-프레미스의 암호화되지 않은 VHD가 있는 경우 갤러리에 사용자 지정 이미지로 업로드한 후 여기에서 VM을 프로비전할 수 있습니다. Resource Manager 템플릿을 사용하여 이 작업을 수행하는 경우 VM을 부팅할 때 Azure 디스크 암호화를 켜도록 요청할 수 있습니다.

데이터 디스크를 추가하고 VM에 탑재하는 경우 해당 데이터 디스크에 대해 Azure 디스크 암호화를 켤 수 있습니다. 그러면 먼저 로컬에서 해당 데이터 디스크가 암호화되고, 클래식 배포 모델 계층은 저장소 콘텐츠가 암호화되도록 저장소에 대해 지연 쓰기를 수행합니다.

#### <a name="client-side-encryption"></a>클라이언트 쪽 암호화
클라이언트 쪽 암호화는 전송 전에 데이터를 암호화하고 미사용 데이터를 암호화하기 때문에 가장 안전한 데이터 암호화 방법입니다.  그러나 원치 않게, 저장소를 사용하여 애플리케이션에 코드를 추가해야 합니다. 이 경우 HTTPS를 사용하여 전송 중인 데이터를 보호할 수 있습니다. 데이터가 Azure Storage에 도달하면 SSE를 통해 암호화됩니다.

클라이언트 쪽 암호화를 사용하여 테이블 엔터티, 큐 메시지 및 Blob을 암호화할 수 있습니다. 

클라이언트 쪽 암호화는 전적으로 애플리케이션에 의해 관리됩니다. 이 방법은 가장 안전한 방법이지만 애플리케이션을 프로그래밍 방식으로 변경하고 키 관리 프로세스를 배치해야 합니다. 전송 중에 추가 보안을 유지하려고 하며 저장된 데이터를 암호화하고 싶으면 이 방법을 사용합니다.

클라이언트 쪽 암호화는 클라이언트에서 더 많은 부하를 발생하므로, 확장성 계획을 세울 때, 특히 대량의 데이터를 암호화하고 전송하는 경우에 이러한 사실을 고려해야 합니다.

#### <a name="storage-service-encryption-sse"></a>SSE(Storage 서비스 암호화)

SSE는 Azure Storage에 의해 관리됩니다. SSE는 전송 중인 데이터에 대한 보안을 제공하지 않지만 Azure Storage에 기록된 데이터는 암호화합니다. SSE는 Azure Storage 성능에 영향을 주지 않습니다.

SSE를 사용하여 모든 종류의 저장소 계정 데이터를 암호화할 수 있습니다(블록 Blob, 추가 Blob, 페이지 Blob, 테이블 데이터, 큐 데이터 및 파일).

새 가상 머신을 만들기 위한 기준으로 사용하는 VHD 파일의 보관 또는 라이브러리가 있는 경우 새 저장소 계정을 만든 다음, 해당 계정에 VHD 파일을 업로드할 수 있습니다. 이러한 VHD 파일은 Azure Storage에 의해 암호화됩니다.

VM의 디스크에 대해 Azure Disk Encryption가 사용되도록 설정되면 새로 쓴 데이터가 SSE 및 Azure Disk Encryption 둘 다에 의해 암호화됩니다.

## <a name="storage-analytics"></a>저장소 분석
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>저장소 분석을 사용하여 인증 유형 모니터링
각 Storage 계정에 대해 Azure Storage 분석을 사용하도록 설정하여 로깅을 수행하고 메트릭 데이터를 저장할 수 있습니다. 이 기능은 저장소 계정의 성능 메트릭을 확인하려고 하거나 성능 문제 때문에 저장소 계정 문제를 해결해야 할 때 사용하면 유용한 도구입니다.

스토리지 분석 로그에서 볼 수 있는 또 다른 데이터는 누군가가 스토리지에 액세스할 때 사용하는 인증 방법입니다. 예를 들어, Blob Storage를 사용하는 경우 공유 액세스 서명을 사용했는지 아니면 Storage 계정 키를 사용했는지, 액세스하는 Blob이 공용인지를 확인할 수 있습니다.

저장소에 대한 액세스를 엄격히 관리하는 경우에는 이 방법이 도움이 될 수 있습니다. 예를 들어, Blob Storage에서 모든 컨테이너를 개인으로 설정하고 애플리케이션 전체에서 SAS 서비스를 사용을 구현할 수 있습니다. 그런 다음, 로그를 주기적으로 확인하여 스토리지 계정 키를 사용하여 Blob에 액세스하는지(보안 위반을 의미할 수 있음) 또는 Blob이 공용이지만 공용이면 안 되는지 알아볼 수 있습니다.

#### <a name="what-do-the-logs-look-like"></a>로그는 어떤 형태로 표시되나요?
Azure 포털을 통해 저장소 계정 메트릭 및 로깅을 사용하도록 설정하면 분석 데이터가 빠르게 누적되기 시작합니다. 각 서비스에 대한 로깅과 메트릭은 별개입니다. 로깅은 해당 저장소 계정에 작업이 있을 때만 기록되지만, 메트릭은 사용자가 구성한 방식에 따라 분, 시간 또는 일 단위로 기록됩니다.

로그는 스토리지 계정에서 $logs라는 컨테이너의 블록 Blob에 저장됩니다. 이 컨테이너는 저장소 분석이 사용되도록 설정되면 자동으로 만들어집니다. 이 컨테이너가 만들어지면 해당 내용은 삭제할 수 있지만 컨테이너 자체는 삭제할 수 없습니다.

$Logs 컨테이너 아래에는 각 서비스에 대한 폴더가 있으며 년/월/일/시간에 대한 하위 폴더가 있습니다. 시간 아래에서 로그 번호가 매겨집니다. 디렉터리 구조는 다음과 같습니다.

![로그 파일 보기](./media/storage-security-guide/image1.png)

Azure Storage에 대한 모든 요청이 기록됩니다. 다음은 처음 몇 개의 필드를 보여 주는 로그 파일의 스냅숏입니다.

![로그 파일 스냅숏](./media/storage-security-guide/image2.png)

이 스냅숏을 보면 로그를 사용하여 스토리지 계정에 대한 모든 종류의 호출을 추적할 수 있다는 것을 알 수 있습니다.

#### <a name="what-are-all-of-those-fields-for"></a>이러한 모든 필드의 용도는 무엇인가요?
아래의 리소스에 나열되는 문서는 로그의 많은 필드와 해당 용도를 목록으로 제공합니다. 필드 목록을 순서대로 나타내면 다음과 같습니다.

![로그 파일의 필드 스냅숏](./media/storage-security-guide/image3.png)

GetBlob에 대한 항목과 권한 부여 방법에 관심이 있으므로 operation-type이 “Get-Blob”인 항목을 찾은 다음, request-status(4번째</sup> 열) 및 authorization-type(8번째</sup> 열)을 확인해야 합니다.

예를 들어 위 목록의 처음 몇 행에서 request-status는 “Success”이고 authorization-type은 “authenticated”입니다. 즉, 저장소 계정 키를 사용하여 요청에 권한이 부여됩니다.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>내 Blob에 대한 액세스 권한은 어떻게 부여되나요?
우리는 다음 세 가지 경우에 관심이 있습니다.

1. Blob이 공용이고 공유 액세스 서명 없이 URL을 사용하여 액세스됩니다. 이 경우 request-status는 “AnonymousSuccess”이고 authorization-type은 “anonymous”입니다.

   1.0;2015-11-17T02:01:29.0488963Z;GetBlob;**AnonymousSuccess**;200;124;37;**anonymous**;;mystorage…
2. Blob이 개인이고 공유 액세스 서명과 함께 사용되었습니다. 이 경우 request-status는 “SASSuccess”이고 authorization-type은 “sas”입니다.

   1.0;2015-11-16T18:30:05.6556115Z;GetBlob;**SASSuccess**;200;416;64;**sas**;;mystorage…
3. Blob이 개인이고 액세스하는 데 저장소 키가 사용됩니다. 이 경우 request-status는 “**성공**”이고 authorization-type은 “**인증됨**”입니다.

   1.0;2015-11-16T18:32:24.3174537Z;GetBlob;**Success**;206;59;22;**authenticated**;mystorage…

Microsoft Message Analyzer를 사용하여 이러한 로그를 보고 및 분석할 수 있습니다. 여기에는 검색 및 필터링 기능이 포함되어 있습니다. 예를 들어 GetBlob의 인스턴스를 검색하여 예상대로 사용되고 있는지 알아볼 수 있습니다. 즉, 누군가가 사용자의 저장소 계정에 부적절하게 액세스하고 있지 않은지 확인할 수 있습니다.

#### <a name="resources"></a>리소스
* [저장소 분석](../storage-analytics.md)

  이 문서는 저장소 분석 기능과 이 기능을 사용하도록 설정하는 방법을 간단히 설명합니다.
* [저장소 분석 로그 형식](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  이 문서에서는 저장소 분석 로그 형식을 설명하고, 요청에 사용되는 인증 유형을 나타내는 authentication-type을 비롯하여 사용 가능한 필드를 자세히 소개합니다.
* [Azure Portal에서 Storage 계정 모니터링](../storage-monitor-storage-account.md)

  이 문서에서는 메트릭의 모니터링 및 저장소 계정에 대한 로깅을 구성하는 방법을 보여 줍니다.
* [Azure Storage 메트릭 및 로깅, AzCopy 및 Message Analyzer를 사용한 종단 간 문제 해결](../storage-e2e-troubleshooting.md)

  이 문서에서는 저장소 분석 사용과 관련된 문제 해결 방법과 Microsoft 메시지 분석기 사용 방법을 보여 줍니다.
* [Microsoft Message Analyzer 운영 가이드](https://technet.microsoft.com/library/jj649776.aspx)

  이 문서는 Microsoft Message Analyzer에 대한 참조로, 자습서, 빠른 시작 및 기능 요약에 대한 링크를 포함합니다.

## <a name="cross-origin-resource-sharing-cors"></a>CORS(크로스-원본 자원 공유)
### <a name="cross-domain-access-of-resources"></a>리소스의 도메인 간 액세스
한 도메인에서 실행되는 웹 브라우저가 다른 도메인의 리소스에 대해 HTTP 요청을 수행하는 경우를 크로스-원본 HTTP 요청이라고 합니다. 예를 들어 contoso.com에서 제공되는 HTML 페이지에서 fabrikam.blob.core.windows.net에서 호스트되는 jpeg를 요청한다고 가정합니다. 보안상의 이유로 브라우저는 JavaScript와 같은 스크립트 내에서 시작된 크로스-원본 HTTP 요청을 제한합니다. 즉, contoso.com의 웹 페이지에 있는 일부 JavaScript 코드가 fabrikam.blob.core.windows.net의 해당 jpeg를 요청하면 브라우저는 해당 요청을 허용하지 않습니다.

이것은 Azure Storage와 어떤 관계가 있을까요? Fabrikam이라는 Storage 계정을 사용하여 Blob Storage에 JSON 또는 XML 데이터 파일과 같은 정적 자산을 저장하는 경우 자산에 대한 도메인은 fabrikam.blob.core.windows.net이 되므로 두 도메인이 달라집니다. 따라서 contoso.com 웹 애플리케이션은 JavaScript를 사용하여 도메인에 액세스할 수 없게 됩니다. Table Storage와 같이, JavaScript 클라이언트에서 처리할 JSON 데이터를 반환하는 Azure Storage 서비스 중 하나를 호출하려는 경우에도 마찬가지입니다.

#### <a name="possible-solutions"></a>가능한 해결 방법
이 문제를 해결하는 한 가지 방법은 “storage.contoso.com”과 같은 사용자 지정 도메인을 fabrikam.blob.core.windows.net에 할당하는 것입니다. 문제는 하나의 저장소 계정에 해당 사용자 지정 도메인만 할당할 수 있다는 것입니다. 자산이 여러 저장소 계정에 저장되어 있다면 어떻게 될까요?

이 문제를 해결하는 또 다른 방법은 웹 애플리케이션이 스토리지 호출에 대한 프록시로 작동하도록 하는 것입니다. 즉, Blob Storage에 파일을 업로드하는 경우 웹 애플리케이션은 파일을 로컬로 쓴 다음 Blob Storage에 복사하거나, 모두 메모리로 읽어온 다음 Blob Storage에 쓰게 됩니다. 또는 파일을 로컬로 업로드한 후 Blob Storage에 쓰는 전용 웹 애플리케이션(예: 웹 API)을 작성할 수 있습니다. 어떤 방법을 사용하든 확장성 요구를 결정할 때는 해당 기능을 고려해야 합니다.

#### <a name="how-can-cors-help"></a>CORS는 어떻게 도움이 될까요?
Azure Storage를 사용하면 CORS(크로스 원본 자원 공유)를 사용하도록 설정할 수 있습니다. 각 저장소 계정에 대해 해당 저장소 계정의 리소스에 액세스할 수 있는 도메인을 지정할 수 있습니다. 예를 들어 위에 설명된 경우에, fabrikam.blob.core.windows.net 저장소 계정에 대해 CORS를 사용하도록 설정한 후 contoso.com에 대한 액세스를 허용하도록 구성할 수 있습니다. 그러면 웹 애플리케이션 contoso.com에서 fabrikam.blob.core.windows.net의 리소스에 직접 액세스할 수 있습니다.

한 가지 주의할 점은 CORS가 액세스를 허용하지만 저장소 리소스의 모든 비공용 액세스에 필요한 인증은 제공하지 않는다는 것입니다. 즉, 공용이거나 해당 권한을 부여하는 공유 액세스 서명을 포함하는 경우에만 Blob에 액세스할 수 있습니다. 테이블, 큐 및 파일의 경우에는 공용 액세스가 없으므로 SAS가 필요합니다.

기본적으로 CORS는 모든 서비스에서 사용되지 않도록 설정되어 있습니다. REST API 또는 저장소 클라이언트 라이브러리를 사용하여 서비스 정책을 설정하는 방법 중 하나를 호출하도록 CORS를 설정할 수 있습니다. 이렇게 할 때 XML에 있는 CORS 규칙을 포함합니다. 다음은 저장소 계정의 Blob service에 대해 서비스 속성 설정 작업을 사용하여 설정된 CORS 규칙의 예입니다. Azure Storage에 대해 Storage 클라이언트 라이브러리 또는 REST API를 사용하여 해당 작업을 수행할 수 있습니다.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

각 행의 의미는 다음과 같습니다.

* **AllowedOrigins** 저장소 서비스에서 데이터를 요청하고 수신할 수 있는 일치하지 않는 도메인을 나타냅니다. 이것은 contoso.com과 fabrikam.com 둘 다 특정 Storage 계정에 대한 Blob Storage에서 데이터를 요청할 수 있음을 의미합니다. 이것을 와일드카드(\*)로 설정하여 모든 도메인이 요청에 액세스하도록 허용할 수도 있습니다.
* **AllowedMethods** 요청을 수행할 때 사용할 수 있는 메서드(HTTP 요청 동사)의 목록입니다. 이 예제에서는 PUT 및 GET만 허용됩니다. 이것을 와일드카드(\*)로 설정하여 모든 메서드가 사용되도록 할 수 있습니다.
* **AllowedHeaders** 요청을 수행할 때 원본 도메인이 지정할 수 있는 요청 헤더입니다. 이 예제에서는 x-ms-meta-data, x-ms-meta-target 및 x-ms-meta-abc로 시작하는 모든 메타데이터 헤더를 지정할 수 있습니다. 와일드카드 문자(\*)는 지정한 접두사로 시작하는 모든 헤더가 허용됨을 나타냅니다.
* **ExposedHeaders** 브라우저에서 요청 발급자에게 노출해야 하는 응답 헤더를 나타냅니다. 이 예제에서는 "x-ms-meta-"로 시작하는 모든 헤더가 노출됩니다.
* **MaxAgeInSeconds** : 브라우저가 실행 전 OPTIONS 요청을 캐시하는 최대 시간입니다. 실행 전 요청에 대한 자세한 내용은 아래의 첫 번째 문서를 참조하세요.

#### <a name="resources"></a>리소스
CORS 및 이를 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 리소스를 확인하세요.

* [Azure.com의 Azure Storage 서비스에 대한 CORS(크로스-원본 자원 공유) 지원(영문)](../storage-cors-support.md)

  이 문서에서는 CORS와 다른 저장소 서비스에 대한 규칙 설정하는 방법을 대략적으로 설명합니다.
* [MSDN의 Azure Storage 서비스에 대한 CORS(크로스-원본 자원 공유) 지원(영문)](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Azure Storage 서비스의 CORS 지원에 대한 참조 설명서입니다. 여기에는 각 저장소 서비스에 적용되는 문서에 대한 링크와 예제가 포함되어 있고 CORS 파일의 각 요소에 대한 설명도 수록되어 있습니다.
* [Microsoft Azure Storage: CORS 소개](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  CORS를 소개하고 사용 방법을 보여 주는 초기 블로그 문서로 연결되는 링크입니다.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Azure Storage 보안에 대한 FAQ(질문과 대답)
1. **HTTPS 프로토콜을 사용할 수 없는 경우 Azure Storage와 주고받는 Blob의 무결성을 확인하려면 어떻게 하나요?**

   어떤 이유로든 HTTPS 대신 HTTP를 사용해야 하며 블록 Blob을 사용하고 있는 경우 MD5 확인을 사용하면 전송되는 Blob의 무결성을 확인하는 데 도움이 될 수 있습니다. 이렇게 하면 네트워크/전송 계층 오류로부터 보호되지만 항상 중간 공격을 막을 수 있는 것은 아닙니다.

   전송 수준 보안을 제공하는 HTTPS를 사용할 수 있는 경우 MD5 확인을 사용하는 것은 중복된 작업으로 불필요합니다.

   자세한 내용은 [Azure Blob MD5 개요](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx)를 참조하세요.
2. **미국 정부의 FIPS 준수**

   미국에서 FIPS(Federal Information Processing Standard)는 중요한 데이터의 보호를 위해 미국 연방 정부 컴퓨터 시스템에서 사용하도록 승인되는 암호화 알고리즘을 정의합니다. Windows 서버 또는 데스크톱에서 FIPS 모드를 사용하도록 설정하면 FIPS에서 유효하다고 확인된 암호화 알고리즘만 사용하도록 OS에 지시됩니다. 비규격 알고리즘을 사용하는 애플리케이션은 중단됩니다. .NET Framework 버전 4.5.2 이상을 사용할 경우 애플리케이션은 컴퓨터가 FIPS 모드일 때 FIPS 준수 알고리즘을 사용하도록 암호화 알고리즘을 자동으로 전환합니다.

   FIPS 모드를 사용하도록 설정할지 여부는 각 고객이 결정할 수 있습니다. 기본적으로 FIPS 모드 사용 설정과 관련해서 정부 규제를 따르지 않는 고객을 강제할 수 있는 이유는 없습니다.

### <a name="resources"></a>리소스
* [“FIPS 모드”를 더 이상 권장하지 않는 이유](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  이 블로그 문서에서는 FIPS의 개요를 제공하고 기본적으로 FIPS 모드를 사용하지 않는 이유를 설명합니다.
* [FIPS 140 유효성 검사(영문)](https://technet.microsoft.com/library/cc750357.aspx)

  이 문서에서는 Microsoft 제품 및 암호화 모듈이 미국 연방 정부의 FIPS 표준을 준수하는 방법에 대한 정보를 제공합니다.
* [Windows XP 이상 버전의 Windows에서 "시스템 암호화: 암호화, 해시 및 서명에 FIPS 호환 알고리즘 사용" 보안 설정의 영향](https://support.microsoft.com/kb/811833)

  이 문서에서는 이전 Windows 컴퓨터에서 FIPS 모드를 사용하는 경우에 대해 설명합니다.
