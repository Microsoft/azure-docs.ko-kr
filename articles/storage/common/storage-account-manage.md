---
title: Azure Portal - Azure Storage에서 스토리지 계정 설정 관리 | Microsoft Docs
description: Azure Portal에서 액세스 제어 설정 구성, 계정 액세스 키 재생성, 액세스 계층 변경 또는 계정에서 사용하는 복제 유형 수정을 포함하여 저장소 계정 설정을 관리하는 방법에 대해 알아봅니다. 또한 포털에서 저장소 계정을 삭제하는 방법도 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/05/2019
ms.author: tamram
ms.openlocfilehash: fa574558afeec5a7706482a142c0187e6a34bdb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484277"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Azure Portal에서 저장소 계정 설정 관리

저장소 계정에 대한 다양한 설정은 [Azure Portal](https://portal.azure.com)에서 사용할 수 있습니다. 이 문서에서는 이러한 설정 및 해당 설정을 사용하는 방법에 대해 설명합니다.

## <a name="access-control"></a>액세스 제어

Azure Storage Blob storage 및 Queue storage를 통해 역할 기반 액세스 제어 (RBAC)에 대 한 Azure Active Directory를 사용 하 여 인증을 지원합니다. Azure AD로 인증 하는 방법에 대 한 자세한 내용은 참조 하세요. [Authenticate 권한을 Azure blob 및 Azure Active Directory를 사용 하 여 큐](storage-auth-aad.md)합니다.

Azure Portal의 **액세스 제어** 설정은 RBAC 역할을 사용자, 그룹, 서비스 주체 및 관리 ID에 할당하는 간단한 방법을 제공합니다. RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 참조 하세요. [RBAC 사용 하 여 blob 및 큐 데이터에 대 한 관리 액세스 권한을](storage-auth-aad-rbac.md)합니다.

> [!NOTE]
> Azure AD 자격 증명을 통한 사용자 또는 애플리케이션 권한 부여는 다른 인증 수단보다 보안 수준이 높고 사용이 간편합니다. 애플리케이션에서 공유 키 인증을 계속 사용할 수 있는 동안 Azure AD를 사용하면 코드에서 계정 액세스 키를 저장하지 않아도 됩니다. SAS(공유 액세스 서명)를 계속 사용하여 저장소 계정의 리소스에 세분화된 액세스 권한을 부여할 수도 있습니다. 하지만 Azure AD에서는 SAS 토큰을 관리하거나 손상된 SAS를 해지하는 방법을 걱정할 필요 없이 유사한 기능을 제공합니다. 

## <a name="tags"></a>태그들

Azure Storage는 사용자 지정된 분류를 사용하여 Azure 리소스 구성을 위한 Azure Resource Manager 태그를 지원합니다. 저장소 계정에 태그를 적용하여 논리적 방식으로 구독 내에서 그룹화할 수 있습니다. 

저장소 계정에서 태그 이름은 128자로 제한되며 태그 값은 256자로 제한됩니다.

자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../../azure-resource-manager/resource-group-using-tags.md)을 참조하세요.

## <a name="access-keys"></a>액세스 키

저장소 계정을 만들 때 Azure는 두 개의 512비트 저장소 계정 액세스 키를 생성합니다. 이러한 키를 사용하여 공유 키를 통해 저장소 계정에 대한 액세스 권한을 부여할 수 있습니다. 애플리케이션을 중단하지 않고 키를 회전 및 다시 생성할 수 있으며 정기적으로 그렇게 하는 것이 좋습니다.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="view-and-copy-access-keys"></a>액세스 키 보기 및 복사

저장소 계정 자격 증명을 보려면:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 저장소 계정을 찾습니다.
3. 저장소 계정 개요의 **설정** 섹션에서 **액세스 키**를 선택합니다. 계정 액세스 키는 물론 각 키의 전체 연결 문자열이 나타납니다.
4. **key1** 아래에서 **키** 값을 찾고, **복사** 단추를 클릭하여 계정 키를 복사합니다.
5. 또는 전체 연결 문자열을 복사할 수 있습니다. **key1** 아래에서 **연결 문자열** 값을 찾고, **복사** 단추를 클릭하여 연결 문자열을 복사합니다.

    ![Azure portal에서 액세스 키를 확인 하는 방법을 보여 주는 스크린샷](media/storage-manage-account/portal-connection-string.png)

### <a name="regenerate-access-keys"></a>액세스 키 다시 생성

저장소 계정을 안전하게 유지하려면 액세스 키를 정기적으로 다시 생성하는 것이 좋습니다. 키를 회전할 수 있도록 두 개의 액세스 키가 할당됩니다. 키를 회전할 경우, 애플리케이션이 프로세스 전체에 대해 Azure Storage에 대한 액세스를 유지하는지 확인해야 합니다. 

> [!WARNING]
> 액세스 키를 다시 생성하면 저장소 계정 키에 종속된 모든 애플리케이션과 Azure 서비스에 영향을 미칠 수 있습니다. 계정 키를 사용하여 스토리지 계정에 액세스하는 모든 클라이언트는 미디어 서비스, 클라우드, 데스크톱 및 모바일 응용 프로그램, Azure Storage용 그래픽 사용자 인터페이스 응용 프로그램(예: [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/))을 포함하여 새로운 키를 사용하도록 업데이트되어야 합니다. 

저장소 계정 키를 회전하려면 다음 프로세스를 따릅니다.

1. 보조 키를 사용 하도록 응용 프로그램 코드에서 연결 문자열을 업데이트합니다.
2. 저장소 계정의 기본 액세스 키를 다시 생성합니다. Azure Portal의 **액세스 키** 블레이드에서 **Key1 다시 생성**을 클릭하고 **예**를 클릭하여 새 키를 생성하려는 것을 확인합니다.
3. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.
4. 같은 방식으로 보조 액세스 키를 다시 생성합니다.

## <a name="account-configuration"></a>계정 구성

저장소 계정을 만든 후 해당 구성을 수정할 수 있습니다. 예를 들어, 데이터를 복제하는 방식을 변경하거나 계정의 액세스 계층을 핫(Hot)에서 쿨(Cool)로 변경할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 저장소 계정으로 이동하고 **설정**에서 **구성**을 클릭하여 계정 구성을 확인하거나 변경합니다.

저장소 계정 구성 변경으로 추가 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 페이지를 참조하세요.

## <a name="delete-a-storage-account"></a>저장소 계정 삭제
더 이상 사용하지 않는 저장소 계정을 제거하려면 [Azure 포털](https://portal.azure.com)의 저장소 계정으로 이동하여 **삭제**를 클릭합니다. 저장소 계정 삭제는 계정의 모든 데이터를 포함한 전체 계정을 삭제합니다.

> [!WARNING]
> 삭제된 저장소 계정을 복원할 수 없거나 삭제 전에 포함된 콘텐츠를 검색할 수 없습니다. 계정을 삭제하기 전에 저장할 내용을 백업했는지 확인합니다. 또한 해당 계정의 리소스에 대해 true를 유지합니다. Blob, 테이블, 큐 또는 파일을 삭제하면 영구적으로 삭제됩니다.
> 

Azure 가상 머신과 연결된 저장소 계정을 삭제하려고 하는 경우 아직 사용 중인 저장소 계정에 대한 오류가 발생할 수 있습니다. 이 오류의 문제를 해결하는 도움말은 [저장소 계정을 삭제할 때 오류 문제 해결](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정 개요](storage-account-overview.md)
- [저장소 계정을 만드는](storage-quickstart-create-account.md)
