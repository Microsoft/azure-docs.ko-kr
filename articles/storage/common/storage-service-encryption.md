---
title: 미사용 데이터에 대한 Azure Storage 암호화
description: Azure Storage은 데이터를 클라우드로 유지 하기 전에 자동으로 암호화 하 여 데이터를 보호 합니다. Microsoft에서 관리 하는 키를 사용 하 여 저장소 계정에서 데이터를 암호화 하거나 고유한 키를 사용 하 여 암호화를 관리할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 08/24/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e949c3db6d8c0cafab8556dbfde367e6e49273e9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078200"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>미사용 데이터에 대한 Azure Storage 암호화

Azure Storage는 데이터가 클라우드에 유지 될 때 자동으로 암호화 합니다. Azure Storage 암호화를 통해 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족할 수 있습니다.

## <a name="about-azure-storage-encryption"></a>Azure Storage 암호화 정보

Azure Storage의 데이터는 256 비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용 하 여 투명 하 게 암호화 되 고 해독 되며, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며 FIPS 140-2 규격입니다. Azure Storage 암호화는 Windows의 BitLocker 암호화와 비슷합니다.

Azure Storage 암호화는 리소스 관리자 및 클래식 저장소 계정을 포함 하 여 모든 저장소 계정에 대해 사용 하도록 설정 됩니다. Azure Storage 암호화를 사용 하지 않도록 설정할 수 없습니다. 기본적으로 데이터 보안을 유지 하기 때문에 Azure Storage 암호화를 활용 하기 위해 코드 또는 응용 프로그램을 수정할 필요가 없습니다.

저장소 계정의 데이터는 성능 계층 (표준 또는 프리미엄), 액세스 계층 (핫 또는 쿨) 또는 배포 모델 (Azure Resource Manager 또는 클래식)에 관계 없이 암호화 됩니다. 보관 계층의 모든 blob도 암호화 됩니다. 모든 Azure Storage 중복성 옵션은 암호화를 지원 하 고 지역에서 복제를 사용 하는 경우 기본 및 보조 지역의 모든 데이터를 암호화 합니다. Blob, 디스크, 파일, 큐 및 테이블을 포함 하 여 모든 Azure Storage 리소스가 암호화 됩니다. 모든 개체 메타 데이터도 암호화 됩니다. Azure Storage 암호화에 대 한 추가 비용은 없습니다.

2017 년 10 월 20 일 이후에 Azure Storage으로 작성 된 모든 블록 blob, 추가 blob 또는 페이지 blob은 암호화 됩니다. 이 날짜 이전에 만들어진 blob은 백그라운드 프로세스에 의해 계속 암호화 됩니다. 2017 년 10 월 20 일 전에 만들어진 blob의 암호화를 강제 적용 하려면 blob을 다시 작성 하면 됩니다. Blob의 암호화 상태를 확인 하는 방법을 알아보려면 [blob의 암호화 상태 확인](../blobs/storage-blob-encryption-status.md)을 참조 하세요.

암호화 Azure Storage 기본 암호화 모듈에 대 한 자세한 내용은 암호화 [API: 차세대](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)을 참조 하세요.

Azure managed disks의 암호화 및 키 관리에 대 한 자세한 내용은 Windows Vm에 대 한 [azure managed disks의 서버 쪽 암호화](../../virtual-machines/windows/disk-encryption.md) 또는 Linux vm에 대 한 [azure Managed disks의 서버 쪽 암호화](../../virtual-machines/linux/disk-encryption.md) 를 참조 하세요.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

새 저장소 계정의 데이터는 기본적으로 Microsoft 관리 키를 사용 하 여 암호화 됩니다. Microsoft에서 관리 하는 키를 사용 하 여 데이터를 암호화 하거나 사용자 고유의 키를 사용 하 여 암호화를 관리할 수 있습니다. 사용자 고유의 키로 암호화를 관리 하도록 선택 하는 경우 두 가지 옵션이 있습니다. 두 가지 유형의 키 관리 또는 두 가지 모두를 사용할 수 있습니다.

- Blob storage 및 Azure Files에서 데이터를 암호화 하 고 암호 해독 하는 데 사용할 Azure Key Vault를 사용 하 여 *고객 관리 키* 를 지정할 수 있습니다. <sup>1, 2</sup> 고객 관리 키에 대 한 자세한 내용은 Azure Key Vault에서 [고객이 관리 하는 키를 사용 하 여 Azure Storage 암호화 관리를](encryption-customer-managed-keys.md)참조 하세요.
- Blob storage 작업에서 *고객이 제공한 키* 를 지정할 수 있습니다. Blob 저장소에 대 한 읽기 또는 쓰기 요청을 수행 하는 클라이언트에는 blob 데이터의 암호화 및 암호 해독 방법에 대 한 세부적인 제어를 요청 하는 암호화 키가 포함 될 수 있습니다. 고객 제공 키에 대 한 자세한 내용은 [Blob 저장소에 대 한 요청에 암호화 키 제공](encryption-customer-provided-keys.md)을 참조 하세요.

다음 표에서는 Azure Storage 암호화에 대 한 키 관리 옵션을 비교 합니다.

| 키 관리 매개 변수 | Microsoft 관리형 키 | 고객 관리형 키 | 고객이 제공한 키 |
|--|--|--|--|
| 암호화/암호 해독 작업 | Azure | Azure | Azure |
| 지원 되는 Azure Storage 서비스 | 모두 | Blob storage, Azure Files<sup>1, 2</sup> | Blob Storage |
| 키 스토리지 | Microsoft 키 저장소 | Azure Key Vault | 고객의 고유 키 저장소 |
| 키 회전 책임 | Microsoft | 고객 | 고객 |
| 키 컨트롤 | Microsoft | 고객 | 고객 |

<sup>1</sup> 큐 저장소에서 고객 관리 키를 사용 하도록 지 원하는 계정을 만드는 방법에 대 한 자세한 내용은 [큐에 대 한 고객 관리 키를 지 원하는 계정 만들기](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 참조 하세요.<br />
<sup>2</sup> 테이블 저장소에서 고객 관리 키를 사용 하도록 지 원하는 계정을 만드는 방법에 대 한 자세한 내용은 [테이블에 대 한 고객 관리 키를 지 원하는 계정 만들기](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)를 참조 하세요.

> [!NOTE]
> Microsoft 관리 키는 준수 요구 사항에 따라 적절 하 게 회전 됩니다. 특정 키 회전 요구 사항이 있는 경우 사용자가 직접 순환을 관리 하 고 감사할 수 있도록 고객이 관리 하는 키로 이동 하는 것이 좋습니다.

## <a name="encryption-scopes-for-blob-storage-preview"></a>Blob 저장소의 암호화 범위 (미리 보기)

기본적으로 저장소 계정은 저장소 계정으로 범위가 지정 된 키를 사용 하 여 암호화 됩니다. Microsoft 관리 키 또는 Azure Key Vault에 저장 된 고객 관리 키를 사용 하 여 데이터를 암호화 하는 키에 대 한 액세스를 보호 하 고 제어 하도록 선택할 수 있습니다.

암호화 범위를 사용 하면 필요에 따라 컨테이너 또는 개별 blob의 수준에서 암호화를 관리할 수 있습니다. 암호화 범위를 사용 하 여 동일한 저장소 계정에 있지만 다른 고객에 게 속한 데이터 간에 보안 경계를 만들 수 있습니다.

Azure Storage 리소스 공급자를 사용 하 여 저장소 계정에 대 한 하나 이상의 암호화 범위를 만들 수 있습니다. 암호화 범위를 만들 때 범위가 Microsoft 관리 키로 보호 되는지 또는 Azure Key Vault에 저장 된 고객 관리 키를 사용 하 여 보호 되는지 지정 합니다. 동일한 저장소 계정에 있는 다양 한 암호화 범위는 Microsoft에서 관리 하거나 고객이 관리 하는 키 중 하나를 사용할 수 있습니다.

암호화 범위를 만든 후에는 컨테이너 또는 blob를 만들도록 요청에 대 한 암호화 범위를 지정할 수 있습니다. 암호화 범위를 만드는 방법에 대 한 자세한 내용은 [암호화 범위 만들기 및 관리 (미리 보기)](../blobs/encryption-scope-manage.md)를 참조 하세요.

> [!NOTE]
> 미리 보기 중에는 읽기 액세스 지역 중복 저장소 (RA-GRS) 및 읽기 액세스 지역 중복 저장소 (RA-GZRS) 계정에 암호화 범위가 지원 되지 않습니다.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> 암호화 범위 미리 보기는 비프로덕션 용도로만 사용 됩니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.
>
> 예기치 않은 비용을 방지 하려면 현재 필요 하지 않은 모든 암호화 범위를 사용 하지 않도록 설정 해야 합니다.

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>암호화 범위를 사용 하 여 컨테이너 또는 blob 만들기

암호화 범위 아래에 생성 된 blob는 해당 범위에 대해 지정 된 키로 암호화 됩니다. Blob을 만들 때 개별 blob에 대 한 암호화 범위를 지정 하거나, 컨테이너를 만들 때 기본 암호화 범위를 지정할 수 있습니다. 컨테이너 수준에서 기본 암호화 범위를 지정 하는 경우 해당 컨테이너의 모든 blob은 기본 범위와 연결 된 키로 암호화 됩니다.

기본 암호화 범위가 있는 컨테이너에서 blob을 만드는 경우 기본 암호화 범위를 재정의할 수 있도록 컨테이너가 구성 된 경우 기본 암호화 범위를 재정의 하는 암호화 범위를 지정할 수 있습니다. 기본 암호화 범위를 재정의 하지 않도록 하려면 컨테이너를 구성 하 여 개별 blob에 대 한 재정의를 거부 합니다.

암호화 범위에 속하는 blob에 대 한 읽기 작업은 암호화 범위를 사용 하지 않는 한 투명 하 게 수행 됩니다.

### <a name="disable-an-encryption-scope"></a>암호화 범위를 사용 하지 않도록 설정

암호화 범위를 사용 하지 않도록 설정 하면 HTTP 오류 코드 403 (사용할 수 없음)이 발생 하 고 암호화 범위를 사용 하 여 수행 된 이후의 읽기 또는 쓰기 작업이 실패 합니다. 암호화 범위를 다시 사용 하도록 설정 하면 읽기 및 쓰기 작업이 다시 정상적으로 진행 됩니다.

암호화 범위를 사용 하지 않도록 설정 하면 더 이상 요금이 청구 되지 않습니다. 불필요 한 요금을 방지 하는 데 필요 하지 않은 모든 암호화 범위를 사용 하지 않도록 설정 합니다.

암호화 범위가 Azure Key Vault의 고객 관리 키로 보호 되는 경우 암호화 범위를 사용 하지 않도록 설정 하기 위해 키 자격 증명 모음에서 연결 된 키를 삭제할 수도 있습니다. Azure Key Vault의 고객 관리 키는 일시 삭제 및 제거 보호로 보호 되 고 삭제 된 키는 해당 속성에 대해 정의 된 동작의 영향을 받습니다. 자세한 내용은 Azure Key Vault 설명서에서 다음 항목 중 하나를 참조 하세요.

- [PowerShell에서 일시 삭제를 사용하는 방법](../../key-vault/general/soft-delete-powershell.md)
- [CLI에서 일시 삭제를 사용하는 방법](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> 암호화 범위를 삭제할 수는 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../../key-vault/general/overview.md)
- [Azure Portal에서 Azure Storage 암호화를 위해 고객이 관리하는 키 구성](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure Storage 암호화용 고객 관리형 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure Storage 암호화용 고객 관리형 키 구성](storage-encryption-keys-cli.md)
