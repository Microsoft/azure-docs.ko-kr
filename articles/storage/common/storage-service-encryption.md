---
title: 휴지 상태의 데이터에 대 한 암호화 Azure Storage
description: Azure Storage은 데이터를 클라우드로 유지 하기 전에 자동으로 암호화 하 여 데이터를 보호 합니다. Microsoft에서 관리 하는 키를 사용 하 여 저장소 계정의 암호화를 사용 하거나 사용자 고유의 키로 암호화를 관리할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 02/05/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 86d6a63601036abdde4ee7ae73114566d749feca
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082823"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>휴지 상태의 데이터에 대 한 암호화 Azure Storage

Azure Storage은 데이터를 클라우드에 보관할 때 자동으로 암호화 합니다. Azure Storage 암호화를 통해 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족할 수 있습니다.

## <a name="about-azure-storage-encryption"></a>Azure Storage 암호화 정보

Azure Storage의 데이터는 256 비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용 하 여 투명 하 게 암호화 되 고 해독 되며, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며 FIPS 140-2 규격입니다. Azure Storage 암호화는 Windows의 BitLocker 암호화와 비슷합니다.

Azure Storage 암호화는 리소스 관리자 및 클래식 저장소 계정을 포함 하 여 모든 새 저장소 계정에 대해 사용 하도록 설정 됩니다. Azure Storage 암호화를 사용 하지 않도록 설정할 수 없습니다. 기본적으로 데이터 보안을 유지 하기 때문에 Azure Storage 암호화를 활용 하기 위해 코드 또는 응용 프로그램을 수정할 필요가 없습니다.

저장소 계정은 성능 계층 (표준 또는 프리미엄) 또는 배포 모델 (Azure Resource Manager 또는 클래식)에 관계 없이 암호화 됩니다. 모든 Azure Storage 중복성 옵션은 암호화를 지원 하 고 저장소 계정의 모든 복사본은 암호화 됩니다. Blob, 디스크, 파일, 큐 및 테이블을 포함 하 여 모든 Azure Storage 리소스가 암호화 됩니다. 모든 개체 메타 데이터도 암호화 됩니다.

암호화는 Azure Storage 성능에 영향을 주지 않습니다. Azure Storage 암호화에 대 한 추가 비용은 없습니다.

2017 년 10 월 20 일 이후에 Azure Storage으로 작성 된 모든 블록 blob, 추가 blob 또는 페이지 blob은 암호화 됩니다. 이 날짜 이전에 만들어진 blob은 백그라운드 프로세스에 의해 계속 암호화 됩니다. 2017 년 10 월 20 일 전에 만들어진 blob의 암호화를 강제 적용 하려면 blob을 다시 작성 하면 됩니다. Blob의 암호화 상태를 확인 하는 방법을 알아보려면 [blob의 암호화 상태 확인](../blobs/storage-blob-encryption-status.md)을 참조 하세요.

암호화 Azure Storage 기본 암호화 모듈에 대 한 자세한 내용은 암호화 [API: 차세대](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)을 참조 하세요.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

Microsoft에서 관리 하는 키를 사용 하 여 저장소 계정의 암호화를 사용 하거나 사용자 고유의 키로 암호화를 관리할 수 있습니다. 사용자 고유의 키를 사용 하 여 암호화를 관리 하도록 선택 하는 경우 다음 두 가지 옵션을 사용할 수 있습니다.

- Blob storage 및 Azure Files에서 데이터를 암호화 하 고 암호 해독 하는 데 사용할 Azure Key Vault를 사용 하 여 *고객 관리 키* 를 지정할 수 있습니다. <sup>1, 2</sup>
- Blob storage 작업에서 *고객이 제공한 키* 를 지정할 수 있습니다. Blob 저장소에 대 한 읽기 또는 쓰기 요청을 수행 하는 클라이언트에는 blob 데이터의 암호화 및 암호 해독 방법에 대 한 세부적인 제어를 요청 하는 암호화 키가 포함 될 수 있습니다.

다음 표에서는 Azure Storage 암호화에 대 한 키 관리 옵션을 비교 합니다.

|                                        |    Microsoft에서 관리 하는 키                             |    고객 관리형 키                                                                                                                        |    고객이 제공한 키                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    암호화/암호 해독 작업    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    지원 되는 Azure Storage 서비스    |    모두                                                |    Blob storage, Azure Files<sup>1, 2</sup>                                                                                                               |    Blob Storage                                                                  |
|    키 저장소                         |    Microsoft 키 저장소    |    Azure Key Vault                                                                                                                              |    Azure Key Vault 또는 다른 키 저장소                                                                 |
|    키 회전 책임         |    Microsoft                                          |    Customer                                                                                                                                     |    Customer                                                                      |
|    키 사용                           |    Microsoft                                          |    Azure Portal, 저장소 리소스 공급자 REST API, Azure Storage 관리 라이브러리, PowerShell, CLI        |    Azure Storage REST API (Blob Storage), Azure Storage 클라이언트 라이브러리    |
|    키 액세스                          |    Microsoft만                                     |    Microsoft, 고객                                                                                                                    |    고객 전용                                                                 |

<sup>1</sup> 큐 저장소에서 고객 관리 키를 사용 하도록 지 원하는 계정을 만드는 방법에 대 한 자세한 내용은 [큐에 대 한 고객 관리 키를 지 원하는 계정 만들기](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 참조 하세요.<br />
<sup>2</sup> 테이블 저장소에서 고객 관리 키를 사용 하도록 지 원하는 계정을 만드는 방법에 대 한 자세한 내용은 [테이블에 대 한 고객 관리 키를 지 원하는 계정 만들기](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)를 참조 하세요.

다음 섹션에서는 키 관리에 대 한 각 옵션을 더 자세히 설명 합니다.

## <a name="microsoft-managed-keys"></a>Microsoft에서 관리 하는 키

기본적으로 저장소 계정은 Microsoft에서 관리 하는 암호화 키를 사용 합니다. 다음 그림에 표시 된 것 처럼 [Azure Portal](https://portal.azure.com)의 **암호화** 섹션에서 저장소 계정에 대 한 암호화 설정을 확인할 수 있습니다.

![Microsoft에서 관리 하는 키로 암호화 된 계정 보기](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용 하는 고객 관리 키

사용자 고유의 키를 사용 하 여 저장소 계정 수준에서 Azure Storage 암호화를 관리할 수 있습니다. 저장소 계정 수준에서 고객 관리 키를 지정 하는 경우 해당 키는 저장소 계정의 루트 암호화 키에 대 한 액세스를 보호 하 고 제어 하는 데 사용 되며, 모든 blob 및 파일 데이터를 암호화 하 고 암호 해독 하는 데 사용 됩니다. 고객 관리 키를 통해 액세스 제어를 보다 유연 하 게 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있습니다. 데이터를 보호 하는 데 사용 되는 암호화 키를 감사할 수도 있습니다.

Azure Key Vault를 사용 하 여 고객 관리 키를 저장 해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault Api를 사용 하 여 키를 생성할 수 있습니다. 저장소 계정 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테넌트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 무엇입니까?](../../key-vault/key-vault-overview.md)를 참조 하세요.

이 다이어그램에서는 Azure Storage Azure Active Directory 및 Azure Key Vault를 사용 하 여 고객이 관리 하는 키를 사용 하 여 요청 하는 방법을 보여 줍니다.

![에서 고객이 관리 하는 키가 작동 하는 방법을 보여 주는 다이어그램 Azure Storage](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

다음 목록에서는 다이어그램의 번호가 매겨진 단계에 대해 설명 합니다.

1. Azure Key Vault 관리자는 저장소 계정과 연결 된 관리 id에 암호화 키에 대 한 사용 권한을 부여 합니다.
2. Azure Storage 관리자는 저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 구성 합니다.
3. Azure Storage는 저장소 계정과 연결 된 관리 되는 id를 사용 하 여 Azure Active Directory를 통해 Azure Key Vault에 대 한 액세스를 인증 합니다.
4. Azure Storage은 Azure Key Vault의 고객 키를 사용 하 여 계정 암호화 키를 래핑합니다.
5. 읽기/쓰기 작업의 경우, Azure Storage는 암호화 및 암호 해독 작업을 수행 하기 위해 계정 암호화 키를 래핑하고 래핑 해제 하는 요청을 Azure Key Vault 보냅니다.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>저장소 계정에 대해 고객이 관리 하는 키 사용

저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 사용 하도록 설정 하면 Azure Storage는 연결 된 key vault에서 고객 관리 키를 사용 하 여 계정 암호화 키를 래핑합니다. 고객 관리 키를 사용 하도록 설정 해도 성능에는 영향을 주지 않으며, 계정은 시간 지연 없이 새 키로 즉시 암호화 됩니다.

Microsoft에서 관리 하는 키를 사용 하 여 새 저장소 계정이 항상 암호화 됩니다. 계정을 만들 때 고객 관리 키를 사용 하도록 설정할 수 없습니다. 고객 관리 키는 Azure Key Vault에 저장 되며, 저장소 계정과 연결 된 관리 되는 id에 대 한 키 권한을 부여 하는 액세스 정책을 사용 하 여 키 자격 증명 모음을 프로 비전 해야 합니다. 관리 id는 저장소 계정이 만들어진 후에만 사용할 수 있습니다.

고객 관리 키를 설정 또는 해제 하거나, 키 버전을 업데이트 하거나, 다른 키를 지정 하 여 Azure Storage 암호화에 사용 되는 키를 수정 하면 루트 키의 암호화가 변경 되지만 Azure Storage 계정의 데이터는 변경 되지 않습니다. 다시 암호화 해야 합니다.

Azure Storage 암호화를 위해 Azure Key Vault에서 고객이 관리 하는 키를 사용 하는 방법을 알아보려면 다음 문서 중 하나를 참조 하세요.

- [Azure Portal에서 Azure Storage 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure Storage 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure Storage 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 고객 관리 키는 Azure AD의 기능인 Azure 리소스에 대 한 관리 되는 id를 사용 합니다. 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에서 고객 관리 키를 구성 하는 경우 관리 id는 저장소 계정에 자동으로 할당 됩니다. 이후에 구독, 리소스 그룹 또는 저장소 계정을 Azure AD 디렉터리 간에 이동 하는 경우 저장소 계정과 연결 된 관리 id는 새 테넌트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 Faq에서 **AZURE AD 디렉터리 간 구독 전송** [및 azure 리소스에 대 한 관리 id의 알려진 문제](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)를 참조 하세요.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객이 관리 하는 키 저장

저장소 계정에서 고객이 관리 하는 키를 사용 하도록 설정 하려면 Azure Key Vault를 사용 하 여 키를 저장 해야 합니다. 키 자격 증명 모음에서 **일시 삭제** 및 **제거 안 함** 속성을 모두 사용 하도록 설정 해야 합니다.

2048 크기의 RSA 키만 Azure Storage 암호화에서 지원 됩니다. 키에 대 한 자세한 내용은 **Key Vault 키** [Azure Key Vault 키, 암호 및 인증서](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)정보를 참조 하세요.

### <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객이 관리 하는 키를 회전할 수 있습니다. 키를 회전 하는 경우 새 키 URI를 사용 하도록 저장소 계정을 업데이트 해야 합니다. Azure Portal에서 새 버전의 키를 사용 하도록 저장소 계정을 업데이트 하는 방법을 알아보려면 [Azure Portal를 사용 하 여 Azure Storage에 대 한 고객 관리 키 구성](storage-encryption-keys-portal.md)에서 **키 버전 업데이트** 섹션을 참조 하세요.

키를 회전 해도 저장소 계정의 데이터에 대 한 다시 암호화는 트리거되지 않습니다. 사용자에 게 필요한 추가 작업은 없습니다.

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대 한 액세스 취소

고객 관리 키에 대 한 액세스를 취소 하려면 PowerShell 또는 Azure CLI를 사용 합니다. 자세한 내용은 [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) 또는 [Azure Key Vault CLI](/cli/azure/keyvault)를 참조 하세요. 액세스를 취소 하면 Azure Storage 의해 암호화 키에 액세스할 수 없으므로 저장소 계정의 모든 데이터에 대 한 액세스가 효과적으로 차단 됩니다.

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Azure managed disks에 대 한 고객 관리 키 (미리 보기)

고객 관리 키를 사용 하 여 Azure managed disks (미리 보기)의 암호화를 관리할 수도 있습니다. 고객 관리 키는 Azure Storage 리소스 보다는 관리 디스크에서 다르게 작동 합니다. 자세한 내용은 Windows 용 [azure managed disks의 서버 쪽 암호화](../../virtual-machines/windows/disk-encryption.md) 또는 Linux 용 [azure Managed disks의 서버 쪽 암호화](../../virtual-machines/linux/disk-encryption.md) 를 참조 하세요.

## <a name="customer-provided-keys-preview"></a>고객이 제공한 키 (미리 보기)

Azure Blob 저장소에 대 한 요청을 수행 하는 클라이언트에는 개별 요청에 대 한 암호화 키를 제공 하는 옵션이 있습니다. 요청에 암호화 키를 포함 하면 Blob 저장소 작업의 암호화 설정에 대 한 세부적인 제어 기능을 제공 합니다. 고객이 제공한 키 (미리 보기)는 Azure Key Vault 또는 다른 키 저장소에 저장할 수 있습니다.

Blob 저장소에 대 한 요청에 고객이 제공한 키를 지정 하는 방법을 보여 주는 예제는 .NET을 [사용 하 여 blob 저장소에 대 한 요청에 고객이 제공한 키 지정](../blobs/storage-blob-customer-provided-key.md)을 참조 하세요. 

### <a name="encrypting-read-and-write-operations"></a>읽기 및 쓰기 작업 암호화

클라이언트 응용 프로그램에서 요청에 대 한 암호화 키를 제공 하는 경우 Azure Storage는 blob 데이터를 읽고 쓰는 동안 암호화 및 암호 해독을 투명 하 게 수행 합니다. Azure Storage는 blob 내용과 함께 암호화 키의 SHA-256 해시를 작성 합니다. 해시는 blob에 대 한 모든 후속 작업이 동일한 암호화 키를 사용 하는지 확인 하는 데 사용 됩니다. 

Azure Storage는 클라이언트가 요청과 함께 보내는 암호화 키를 저장 하거나 관리 하지 않습니다. 암호화 또는 암호 해독 프로세스가 완료 되는 즉시 키가 안전 하 게 삭제 됩니다.

클라이언트가 고객이 제공한 키를 사용 하 여 blob을 만들거나 업데이트 하는 경우 해당 blob에 대 한 후속 읽기 및 쓰기 요청 에서도 키를 제공 해야 합니다. 이미 고객이 제공한 키로 암호화 된 blob에 대 한 요청에서 키를 제공 하지 않으면 요청이 실패 하 고 오류 코드 409 (충돌)이 발생 합니다.

클라이언트 응용 프로그램에서 요청에 대 한 암호화 키를 보내고 저장소 계정도 Microsoft 관리 키 또는 고객이 관리 하는 키를 사용 하 여 암호화 되는 경우 Azure Storage는 암호화 및 암호 해독을 위해 요청에 제공 된 키를 사용 합니다.

요청의 일부로 암호화 키를 보내려면 클라이언트는 HTTPS를 사용 하 여 Azure Storage에 대 한 보안 연결을 설정 해야 합니다.

각 blob 스냅숏에는 자체 암호화 키가 있을 수 있습니다.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>고객이 제공한 키를 지정 하기 위한 요청 헤더

REST 호출의 경우 클라이언트는 다음 헤더를 사용 하 여 요청에 대 한 암호화 키 정보를 Blob 저장소에 안전 하 게 전달할 수 있습니다.

|요청 헤더 | Description |
|---------------|-------------|
|`x-ms-encryption-key` |쓰기 및 읽기 요청 모두에 필요 합니다. B A s e 64로 인코딩된 AES-256 암호화 키 값입니다. |
|`x-ms-encryption-key-sha256`| 쓰기 및 읽기 요청 모두에 필요 합니다. 암호화 키의 b a s e 64로 인코딩된 SHA256입니다. |
|`x-ms-encryption-algorithm` | 쓰기 요청에 필요 하며 읽기 요청의 경우 선택 사항입니다. 지정 된 키를 사용 하 여 데이터를 암호화할 때 사용할 알고리즘을 지정 합니다. AES256 이어야 합니다. |

요청에 암호화 키를 지정 하는 것은 선택 사항입니다. 그러나 쓰기 작업을 위해 위에 나열 된 헤더 중 하나를 지정 하는 경우 모든 헤더를 지정 해야 합니다.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>고객이 제공한 키를 지 원하는 Blob storage 작업

다음 Blob 저장소 작업은 요청에 고객이 제공한 암호화 키를 보내는 작업을 지원 합니다.

- [Blob 배치](/rest/api/storageservices/put-blob)
- [블록 목록 배치](/rest/api/storageservices/put-block-list)
- [블록 배치](/rest/api/storageservices/put-block)
- [URL에서 블록 배치](/rest/api/storageservices/put-block-from-url)
- [페이지 배치](/rest/api/storageservices/put-page)
- [URL에서 페이지 배치](/rest/api/storageservices/put-page-from-url)
- [추가 블록](/rest/api/storageservices/append-block)
- [Blob 속성 설정](/rest/api/storageservices/set-blob-properties)
- [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata)
- [Blob 가져오기](/rest/api/storageservices/get-blob)
- [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)
- [Blob 메타 데이터 가져오기](/rest/api/storageservices/get-blob-metadata)
- [스냅숏 Blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>고객이 제공한 키 회전

요청에 전달 된 암호화 키를 회전 하려면 blob을 다운로드 하 고 새 암호화 키를 사용 하 여 다시 업로드 합니다.

> [!IMPORTANT]
> 요청에 제공 된 키로 암호화 된 컨테이너 또는 blob에 대 한 읽기 또는 쓰기에 Azure Portal를 사용할 수 없습니다.
>
> Azure Key Vault와 같은 보안 키 저장소의 Blob 저장소에 대 한 요청에 제공 하는 암호화 키를 보호 해야 합니다. 암호화 키 없이 컨테이너 또는 blob에 대해 쓰기 작업을 시도 하면 작업이 실패 하 고 개체에 대 한 액세스 권한이 손실 됩니다.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage 암호화 및 디스크 암호화

Azure Storage 암호화는 Azure 가상 머신 디스크를 반환 하는 페이지 blob을 암호화 합니다. 또한 로컬 임시 디스크를 포함 하 여 모든 Azure virtual machine 디스크를 선택적으로 [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md)으로 암호화할 수 있습니다. Azure Disk Encryption는 Windows에서 업계 표준 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 를 사용 하 고 Linux에서 [DM-](https://en.wikipedia.org/wiki/Dm-crypt) 컴퓨터를 사용 하 여 Azure Key Vault와 통합 된 운영 체제 기반 암호화 솔루션을 제공 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../../key-vault/key-vault-overview.md)
- [Azure Portal에서 Azure Storage 암호화를 위해 고객이 관리하는 키 구성](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure Storage 암호화용 고객 관리형 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure Storage 암호화용 고객 관리형 키 구성](storage-encryption-keys-cli.md)
