---
title: Azure Storage 리소스 공급자를 사용 하 여 관리 리소스에 액세스
description: Azure Storage 리소스 공급자는 Azure Storage에 대 한 관리 리소스에 대 한 액세스를 제공 하는 서비스입니다. Azure Storage 리소스 공급자를 사용 하 여 저장소 계정, 개인 엔드포인트 및 계정 액세스 키와 같은 리소스를 만들고, 업데이트 하 고, 관리 하 고, 삭제할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972356"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Azure Storage 리소스 공급자를 사용 하 여 관리 리소스에 액세스

Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure Storage 리소스 공급자는 Azure Resource Manager를 기반으로 하며 Azure Storage의 관리 리소스에 대 한 액세스를 제공 하는 서비스입니다. Azure Storage 리소스 공급자를 사용 하 여 저장소 계정, 개인 엔드포인트 및 계정 액세스 키와 같은 리소스를 만들고, 업데이트 하 고, 관리 하 고, 삭제할 수 있습니다. Azure Resource Manager에 대 한 자세한 내용은 [Azure Resource Manager 개요](/azure/azure-resource-manager/resource-group-overview)를 참조 하세요.

Azure Storage 리소스 공급자를 사용 하 여 저장소 계정 만들기 또는 삭제, 구독에서 저장소 계정 목록 가져오기 등의 작업을 수행할 수 있습니다. Azure Storage 리소스 공급자에 대 한 요청에 권한을 부여 하려면 Azure Active Directory (Azure AD)를 사용 합니다. 이 문서에서는 관리 리소스에 대 한 사용 권한을 할당 하는 방법을 설명 하 고 Azure Storage 리소스 공급자에 대 한 요청을 수행 하는 방법을 보여 주는 예제를 가리킵니다.

## <a name="management-resources-versus-data-resources"></a>관리 리소스와 데이터 리소스 비교

Microsoft는 Azure Storage 리소스를 사용 하기 위한 두 가지 REST Api를 제공 합니다. 이러한 Api는 Azure Storage에 대해 수행할 수 있는 모든 작업의 기본을 형성 합니다. Azure Storage REST API를 사용 하 여 blob, 큐, 파일 및 테이블 데이터를 비롯 하 여 저장소 계정에서 데이터를 사용할 수 있습니다. Azure Storage 리소스 공급자 REST API를 사용 하 여 저장소 계정 및 관련 리소스를 사용할 수 있습니다.

Blob 데이터를 읽거나 쓰는 요청에는 관리 작업을 수행 하는 요청과 다른 권한이 필요 합니다. RBAC는 두 종류의 리소스에 대 한 권한을 보다 세부적으로 제어할 수 있도록 합니다. 보안 주체에 RBAC 역할을 할당 하는 경우 해당 보안 주체에 부여 되는 사용 권한을 알고 있어야 합니다. 각 기본 제공 RBAC 역할에 연결 된 작업을 설명 하는 자세한 내용은 [Azure 리소스에 대 한 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조 하세요.

Azure Storage는 Azure AD를 사용 하 여 Blob 및 큐 저장소에 대 한 요청에 권한을 부여 합니다. Blob 및 큐 데이터 작업의 RBAC 역할에 대 한 자세한 내용은 [Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](storage-auth-aad.md)를 참조 하세요.

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>RBAC (역할 기반 액세스 제어)를 사용 하 여 관리 권한 할당

모든 Azure 구독에는 사용자, 그룹 및 응용 프로그램을 관리 하는 Azure Active Directory 연결 되어 있습니다. 사용자, 그룹 또는 응용 프로그램을 [Microsoft id 플랫폼](/azure/active-directory/develop/)의 컨텍스트에서 보안 주체 라고도 합니다. RBAC (역할 기반 액세스 제어)를 사용 하 여 Active Directory에 정의 된 보안 주체에 대 한 구독의 리소스에 대 한 액세스 권한을 부여할 수 있습니다.

보안 주체에 RBAC 역할을 할당 하는 경우에는 해당 역할에서 부여 된 사용 권한이 적용 되는 범위도 표시 됩니다. 관리 작업의 경우 구독, 리소스 그룹 또는 저장소 계정의 수준에서 역할을 할당할 수 있습니다. [Azure Portal](https://portal.azure.com/), [Azure CLI 도구](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs)또는 [Azure Storage 리소스 공급자 REST API](/rest/api/storagerp)를 사용 하 여 보안 주체에 RBAC 역할을 할당할 수 있습니다.

RBAC에 대 한 자세한 내용은 [azure 리소스에 대 한 rbac (역할 기반 액세스 제어) 란 무엇 인가요?](../../role-based-access-control/overview.md) 및 [클래식 구독 관리자 역할, azure RBAC 역할 및 azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조 하세요.

### <a name="built-in-roles-for-management-operations"></a>관리 작업의 기본 제공 역할

Azure는 관리 작업을 호출 하는 권한을 부여 하는 기본 제공 역할을 제공 합니다. 또한 Azure Storage는 Azure Storage 리소스 공급자와 함께 사용 하기 위한 기본 제공 역할도 제공 합니다.

저장소 관리 작업을 호출 하는 권한을 부여 하는 기본 제공 역할에는 다음 표에 설명 된 역할이 포함 됩니다.

|    RBAC 역할    |    Description    |    계정 키에 대 한 액세스를 포함 하나요?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **소유자** | 모든 저장소 리소스를 관리 하 고 리소스에 액세스할 수 있습니다.  | 예, 저장소 계정 키를 확인 하 고 다시 생성할 수 있는 권한을 제공 합니다. |
| **기여자**  | 모든 저장소 리소스를 관리할 수 있지만 리소스에 대 한 할당을 관리할 수 없습니다. | 예, 저장소 계정 키를 확인 하 고 다시 생성할 수 있는 권한을 제공 합니다. |
| **판독기** | 저장소 계정에 대 한 정보를 볼 수 있지만 계정 키를 볼 수는 없습니다. | 아닙니다. |
| **Storage 계정 기여자** | 저장소 계정을 관리 하 고, 구독의 리소스 그룹 및 리소스에 대 한 정보를 가져오고, 구독 리소스 그룹 배포를 만들고 관리할 수 있습니다. | 예, 저장소 계정 키를 확인 하 고 다시 생성할 수 있는 권한을 제공 합니다. |
| **사용자 액세스 관리자** | 저장소 계정에 대 한 액세스를 관리할 수 있습니다.   | 예, 보안 주체가 자신 및 다른 사용자에 게 권한을 할당할 수 있습니다. |
| **Virtual Machine 참가자** | 가상 컴퓨터를 관리할 수 있지만 연결 된 저장소 계정은 관리할 수 없습니다.   | 예, 저장소 계정 키를 확인 하 고 다시 생성할 수 있는 권한을 제공 합니다. |

테이블의 세 번째 열은 기본 제공 역할이 **Microsoft. Storage/storageAccounts/listkeys/action**을 지원 하는지 여부를 나타냅니다. 이 작업은 저장소 계정 키를 읽고 다시 생성할 수 있는 권한을 부여 합니다. Azure Storage 관리 리소스에 대 한 액세스 권한은 데이터에 액세스할 수 있는 권한도 포함 하지 않습니다. 그러나 사용자에 게 계정 키에 대 한 액세스 권한이 있으면 계정 키를 사용 하 여 공유 키 권한 부여를 통해 Azure Storage 데이터에 액세스할 수 있습니다.

### <a name="custom-roles-for-management-operations"></a>관리 작업에 대 한 사용자 지정 역할

또한 Azure는 관리 리소스에 액세스 하기 위한 사용자 지정 RBAC 역할을 정의할 수 있도록 지원 합니다. 사용자 지정 역할에 대 한 자세한 내용은 [Azure 리소스에 대 한 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 참조 하세요.

## <a name="code-samples"></a>코드 샘플

Azure Storage 관리 라이브러리에서 관리 작업에 권한을 부여 하 고이를 호출 하는 방법을 보여 주는 코드 예제는 다음 샘플을 참조 하세요.

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.JS](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>클래식 배포와 Azure Resource Manager 비교

Resource Manager와 클래식 배포 모델은 Azure 솔루션을 배포하고 관리하는 별개의 두 가지 방식을 나타냅니다. 새 저장소 계정을 만들 때 Azure Resource Manager 배포 모델을 사용 하는 것이 좋습니다. 가능 하면 Microsoft는 리소스 관리자 모델을 사용 하 여 기존 클래식 저장소 계정을 다시 만드는 것을 권장 합니다. 클래식 배포 모델을 사용 하 여 저장소 계정을 만들 수는 있지만 클래식 모델은 유연성이 낮지만 결국 사용 되지 않습니다.

Azure 배포 모델에 대 한 자세한 내용은 [리소스 관리자 및 클래식 배포](../../azure-resource-manager/management/deployment-models.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager 개요](/azure/azure-resource-manager/resource-group-overview)
- [Azure 리소스에 대한 RBAC(역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)
- [Azure Storage 리소스 공급자에 대 한 확장성 목표](scalability-targets-resource-provider.md)
