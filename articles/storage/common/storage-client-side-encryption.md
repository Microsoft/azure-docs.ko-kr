---
title: Microsoft Azure Storage용 .NET을 사용하는 클라이언트 쪽 암호화 | Microsoft Docs
description: .NET용 Azure Storage 클라이언트 라이브러리는 Azure Storage 애플리케이션의 보안을 최대화하기 위해 클라이언트 쪽 암호화 및 Azure Key Vault와의 통합을 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f54b91ab1ea4521c17a3b40c88214f5637ab47a3
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021615"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Microsoft Azure Storage용 클라이언트 쪽 암호화 및 Azure Key Vault
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>개요
[.Net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet) 는 Azure Storage에 업로드 하기 전에 클라이언트 응용 프로그램 내에서 데이터를 암호화 하 고 클라이언트에 다운로드 하는 동안 데이터 암호를 해독 합니다. 라이브러리 또한 스토리지 계정 키 관리를 위해 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)와의 통합을 지원합니다.

클라이언트 쪽 암호화와 Azure Key Vault를 사용하여 Blob을 암호화하는 프로세스를 안내하는 단계별 자습서는 [Azure Key Vault를 사용하여 Microsoft Azure Storage에서 Blob 암호화 및 해독](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)을 참조하세요.

Java를 사용하는 클라이언트 쪽 암호화는 [Microsoft Azure Storage용 Java를 사용하는 클라이언트 쪽 암호화](storage-client-side-encryption-java.md)를 참조하세요.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>봉투(Envelope) 기술을 통해 암호화 및 암호 해독
암호화 및 암호 해독 프로세스는봉투(Envelope) 기법을 따릅니다.

### <a name="encryption-via-the-envelope-technique"></a>봉투(Envelope) 기술을 통해 암호화
암호화는 봉투(Envelope) 기술을 통해 다음과 같은 방식으로 작동합니다.

1. Azure Storage 클라이언트 라이브러리는 1회용 대칭 키인 콘텐츠 암호화 키(CEK)를 생성합니다.
2. 사용자 데이터는 이 CEK를 사용하여 암호화됩니다.
3. 그런 다음 키 암호화 KEK를 사용하여 CEK를 래핑(암호화)합니다. KEK는 키 식별자로 식별되고 비대칭 키 쌍 또는 대칭 키일 수 있으며 로컬로 관리되거나 Azure Key Vault에 저장됩니다.
   
    스토리지 클라이언트 라이브러리 자체는 KEK에 액세스할 수 없습니다. 라이브러리는 자격 증명 모음에서 제공되는 키 래핑 알고리즘을 호출합니다. 사용자는 원하는 경우 키 래핑/래핑 해제를 위해 사용자 지정 공급자를 사용하도록 선택할 수 있습니다.

4. 그런 다음 암호화된 데이터를 Azure Storage 서비스에 업로드합니다. 일부 추가 암호화 메타데이터와 함께 래핑된 키에 메타 데이터로(Blob) 저장 되거나 암호화 된 데이터 (메시지 큐 및 테이블 엔터티)와 보관 합니다.

### <a name="decryption-via-the-envelope-technique"></a>봉투(Envelope) 기술을 통해 암호해독
암호해독은 봉투(Envelope) 기술을 통해 다음과 같은 방식으로 작동합니다.

1. 클라이언트 라이브러리는 사용자가 키 암호화 키를 로컬로 또는 Azure 키 자격증명모음으로 관리한다고 가정합니다. 사용자는 암호화에 사용된 특정 키를 알 필요가 없습니다. 대신 키를 서로 다른 키 식별자를 확인 하는 키 확인자 수를 설정하고 사용 합니다.
2. 클라이언트 라이브러리는 서비스에 저장된 모든 암호화 자료와 함께 암호화된 데이터를 다운로드 합니다.
3. 래핑된 콘텐츠 암호화 키 (CEK)는 키 암호화 키를(KEK) 사용하여 래핑해제(암호 해독)합니다. 여기서 다시, 클라이언트 라이브러리는 KEK에 대한 액세스권한이 없습니다. 단순히 사용자 지정 또는 래핑 해제 알고리즘 키 자격 증명 모음 공급자를 호출합니다.
4. 그리고 콘텐츠 암호화 키 (CEK)는  암호화 된 사용자 데이터의 암호를 해독 하는데 사용 됩니다.

## <a name="encryption-mechanism"></a>암호화 메커니즘
스토리지 클라이언트 라이브러리는 사용자 데이터를 암호화하기 위해 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 를 사용합니다. 특히, AES를 이용한 [CBC(암호화 블록 체인)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) 모드입니다. 각 서비스는 하는 일이 각각 다르므로 여기서 이것들을 살펴볼 것입니다.

### <a name="blobs"></a>Blob
클라이언트 라이브러리는 현재 전체 blob 암호화만 지원합니다. 특히 사용자가 **가 uploadfrom** 메서드나 **OpenWrite** 메서드를 사용할 때 암호화가 지원 됩니다. 다운로드는 전체 및 범위 다운로드가 모두 지원됩니다.

암호화 하는 동안 클라이언트 라이브러리는 임의 IV (Initialization Vector) 32 바이트의 임의의 콘텐츠 암호화 키 (CEK)와 함께 16 바이트를 생성 하고 이 정보를 사용 여 blob 데이터의 봉투 (envelope) 암호화를 수행 합니다. 래핑된 CEK 및 일부 추가 암호화 메타 데이터 서비스에서 암호화 된 blob과 함께 메타 데이터를 blob으로 저장합니다.

> [!WARNING]
> blob에 대해 고유 메타데이터를 편집하거나 업로드 할 경우, 메타데이타가 유지되는지 확인하세요. 이 메타데이터 없이 새 메타데이터를 업로드하는 경우 래핑된 CEK, IV 및 기타 메타데이터가 손실되고 Blob 콘텐츠를 절대로 다시 검색할 수 없습니다.
> 
> 

암호화 된 blob 다운로드 **에는 downloadto** / **BlobReadStream** 편의 메서드를 사용 하 여 전체 blob의 콘텐츠를 검색 하는 작업이 포함 됩니다. 래핑된 CEK는 IV (blob 메타 데이터로 저장된 경우)와 함께 암호해독되고 사용되어 지며 해독된 데이터가 사용자에게 돌아갑니다.

암호화 된 blob에서 임의의 범위 (**downloadrange** 메서드)를 다운로드 하려면 요청 된 범위를 성공적으로 암호를 해독 하는 데 사용할 수 있는 소량의 추가 데이터를 얻기 위해 사용자가 제공 하는 범위를 조정 해야 합니다.

이 스키마를 사용하여 모든 blob 유형(블록 blob, 페이지 blob 및 추가 blob)을 암호화/암호 해독할 수 있습니다.

### <a name="queues"></a>큐
큐 메시지의 모든 형식이 될 수, 있으므로 클라이언트 라이브러리는 IV (Initialization Vector) 및 암호화 된 콘텐츠 암호화 키 (CEK) 메시지 텍스트에 포함 된 사용자 지정 형식을 정의 합니다.

암호화 하는 동안 클라이언트 라이브러리는 32 바이트의 임의 CEK 함께 16 바이트의 임의 IV를 생성하고 이 정보를 사용하여 큐 메시지 텍스트의 봉투 (envelope) 암호화를 수행 합니다. 래핑된 CEK 및 일부 추가 암호화 메타 데이터를 암호화 된 큐 메시지에 추가합니다. (아래 참조)이 수정 된 메시지는 서비스에 저장 됩니다.

```xml
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

암호를 해독 하는 동안, 래핑된 키는 큐 메시지에서 추출되고 래핑이 해제됩니다. IV 또한 큐메시지에서 추출되고 큐 메시지 데이터를 암호해독하기 위해 래핑해제된 키와 함께 사용 됩니다. 참고로 암호화 메타데이터는 작아야하므로(500바이트 이하),큐 메시지는 64KB의 제한이 있어야만 영향을 관리 할 수 있습니다.

### <a name="tables"></a>테이블
클라이언트 라이브러리는 작업 삽입 및 삭제의 엔터티 속성 암호화를 지원합니다.

> [!NOTE]
> 병합은 현재 지원 되지 않습니다. 속성의 하위 집합은 이전에 다른 키를 사용하여 암호화됐을 가능성이 있기 때문에 단순히 새로운 속성을 병합하는 것과 메타데이터를 업데이트 하는 것은 데이터 손실을 불러 올 수 있습니다. 서비스에서 기존 엔터티를 읽을 수 있는 추가 서비스 호출을 수행 하거나 속성 당 새 키를 사용하는 것 모두에 성능상의 이유로 적합하지 않습니다.
> 
> 

테이블 데이터 암호화는 다음과 같이 작동합니다.  

1. 사용자는 암호화할 속성을 지정합니다.
2. 클라이언트 라이브러리는 모든 엔터티에 대해 16바이트의 임의 IV(Initialization Vector)와 함께 32바이트의 임의 CEK(콘텐츠 암호화 키)를 생성하고 속성당 새 IV를 파생하여 암호화해야 하는 개별적인 속성에 대해 봉투(envelope) 암호화를 수행합니다. 암호화된 속성은 이진 데이터로 저장됩니다.
3. 래핑된 CEK 및 일부 추가 암호화 메타 데이터는 다음  추가 예약 된 두 가지 속성으로 저장 됩니다. 첫 번째 예약 된 속성 (_ClientEncryptionMetadata1)은 IV, 버전, 래핑된 키의 정보를 담고 있는 문자열 속성입니다. 또 다른 예약된 속성(_ClientEncryptionMetadata2)은 암호화된 속성에 대한 정보를 담고 있는 이진 속성입니다. 이 두 번째 속성(_ClientEncryptionMetadata2)의 정보는 자체적으로 암호화됩니다.
4. 이 추가적인 예약 속성이 암호화에 필요하기 때문에 사용자들은 252가지 사용자 지정 속성 대신 250가지를 갖게 됩니다. 엔터티의 총 크기는 1MB 미만이어야 합니다.

문자열 속성만 암호화 할 수 있다는 것을 참고하세요. 다른 유형의 속성이 암호화 된 경우, 문자열로 변환합니다. 암호화된 문자열은 서비스에 이진 속성으로 저장되고 암호 해독 후에는 다시 문자열로 변환됩니다.

테이블의 경우, 암호화 정책 외에도 사용자가 암호화할 속성을 지정해야 합니다. 이것은 특성(TableEntity에서 파생 되는 POCO 엔터티)을 지정[EncryptProperty]하거나 암호화 해결 프로그램 요청 옵션에서 수행할 수 있습니다. 암호화 해결 프로그램은 파티션 키, 행 키, 그리고 속성 이름 및 암호화 여부 속성을 나타내는  Bool방식을 반환하는 대표자입니다. 암호화 하는 동안 클라이언트 라이브러리는 네트워크에 쓰는 동안 속성을 암호화 해야 하는지 여부를 결정하는데 이 정보를 사용합니다. 대리자 속성은 암호화 하는 방법 논리의 가능성도 제공 합니다. 예를 들어 X 인 경우에는 속성 A를 암호화 하 고, 그렇지 않으면 A와 B 속성을 암호화 합니다. 엔터티를 읽거나 쿼리 하는 동안에는이 정보를 제공 하지 않아도 됩니다.

### <a name="batch-operations"></a>Batch 작업
일괄 처리 작업에서, 같은 kek가 배치 작업 안의 모든 행간에 사용되는데, 클라이언트 라이브러리는 배치 작업당 오직 하나의 옵션개체(하나의 정책/kek 때문에 )만 허용하기 때문입니다. 그러나 클라이언트 라이브러리는 배치 안에 새로운 임의 IV와 행 당 임의 CEK를 내부적으로 만듭니다. 사용자가 암호화 해결 프로그램에 이동작을 정의하여 배치의 모든 작업에 대해 암호화 할 다른 속성들을 선택할 수 있습니다.

### <a name="queries"></a>쿼리
> [!NOTE]
> 엔터티는 암호화되므로 암호화된 속성을 필터링하는 쿼리를 실행할 수 없습니다.  시도하면 암호화되지 않은 데이터와 암호화된 데이터를 비교하려고 하기 때문에 결과가 잘못됩니다.
> 
> 
> 쿼리 작업을 수행 하려면 결과 집합에 있는 모든 키를 확인할 수 있는 키 확인자를 지정 해야 합니다. 공급자에는 쿼리 결과에 포함 된 엔터티를 확인할 수 없으면, 클라이언트 라이브러리는 오류를 throw 합니다. 서버 쪽 프로젝션을 수행하는 모든 쿼리에 대해 클라이언트 라이브러리는 선택한 열에 기본적으로 특별한 암호 메타데이터 속성(_ClientEncryptionMetadata1 및 _ClientEncryptionMetadata2)을 추가합니다.

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Azure Key Vault를 사용하여, 사용자는 키와 비밀(예: 인증 키, 스토리지 계정 키, 데이터 암호화 키, PFX 파일 및 암호)을 암호화하여 하드웨어 보안 모듈(HSM)로 보호된 키를 사용합니다. 자세한 내용은 [Azure Key Vault란?](../../key-vault/general/overview.md)을 참조하세요.

스토리지 클라이언트 라이브러리는 Azure 내에서 키를 관리 하기 위한 공통 프레임 워크를 제공 하기 위해 키 자격 증명 모음 핵심 라이브러리를 사용 합니다. 사용자는 또한 키 자격 증명 모음 확장 라이브러리를 사용하여 추가적인 이점을 제공을 받습니다. 이 확장 라이브러리는 간단하고 원활한 대칭/RSA 로컬 및 집계와 캐싱같은 클라우드 키 공급자 관련 유용한 기능을 제공합니다. .

### <a name="interface-and-dependencies"></a>인터페이스 및 종속성
세 가지 키 자격증명 모음 패키지가 있습니다.

* Microsoft.Azure.KeyVault.Core는 IKey 및 IKeyResolver 포함합니다. 어떤 부속품도 없는 작은 패키지입니다. .NET용 스토리지 클라이언트 라이브러리는 이를 종속성으로 정의합니다.
* Microsoft.Azure.키 자격증명 모음은 키 자격 증명 모음 REST 클라이언트를 포함합니다.
* Microsoft.Azure.KeyVault.Extensions 은 암호화 알고리즘 및 RSAKey와  SymmetricKey의 구현이 포함 된 확장 프로그램 코드를 포함합니다. 코어 및 KeyVault 네임 스페이스에 의존하고 (여러 키 공급자를 사용하여 사용자가 원하는) 경우 집계 해결 프로그램 및 캐싱 키 해결 프로그램을 정의 하는 기능을 제공 합니다. 비록 스토리지 클라이언트 라이브러리가 이 패키지에 직접적으로 의존하지 않지만, 사용자가 그들의 키를 저장하거나 로컬과 클라우드 암호화 공급자를 소비하는 키 자격증명 모음 확장을 사용에 Azure Key Vault를 사용하고 싶을 때는 이 패키지가 필요합니다.

키 자격증명모음은 고급 가치 마스터키로 고안되었으며 키 자격증명 모음당 스로틀 한계는 이것을 염두에 두고 만들어졌습니다. 키 자격 증명 모음을 사용하여 클라이언트측 암호화를 수행할 때 모델을 선호 로컬로 대칭 마스터 키 암호 키 자격 증명 모음에로 저장  하 고 캐시를 사용 하는 것입니다. 다음 작업을 수행합니다.

1. 암호를 오프라인으로 만들고 키 자격 증명 모음에 업로드 합니다.
2. 비밀의 기본 식별자를 현재 버전의 암호화에 대한 암호를 풀기 위해 매개변수로 사용하고 이 정보를 로컬로 캐시합니다. CachingKeyResolver를 사용합니다. 사용자는 자체 캐싱 논리가 구현되지 않는 것을 예상합니다.
3. 암호화 정책을 생성하는 동안 캐싱 확인자를 입력으로 사용합니다.

키 자격 증명 모음 사용법에 대한 자세한 내용은 [암호화 코드 샘플](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)에서 찾을 있습니다.

## <a name="best-practices"></a>모범 사례
암호화 지원은 .NET용 스토리지 클라이언트 라이브러리에만 사용할 수 있습니다. Windows Phone 및 Windows 런타임은 현재 암호화를 지원하지 않습니다.

> [!IMPORTANT]
> 클라이언트 쪽 암호화를 사용할 때는 이러한 중요점을 유의하세요.
> 
> * 암호화된 blob에서 읽거나 여기에 쓸 때는 전체 blob 업로드 명령 및 범위/전체 blob 다운로드 명령을 사용하세요. 블록 배치, 블록 목록 배치, 페이지 쓰기, 페이지 지우기 또는 블록 추가와 같은 프로토콜 작업을 사용하여 암호화된 blob에 쓰지 않도록 합니다. 그렇지 않으면 암호화된 blob이 손상되어 읽지 못하게 될 수 있습니다.
> * 테이블의 경우에는 유사한 제약 조건이 있습니다. 암호화 메타데이터를 업데이트하지 않고 암호화된 속성을 업데이트하지 않도록 주의해야 합니다.
> * 암호화된 blob에서 메타데이터를 설정하는 경우 메타데이터의 설정은 가산적이 아니므로 암호 해독에 필요한 암호화 관련 메타데이터를 덮어쓸 수도 있습니다. 이것은 스냅샷에 대해서 마찬가지입니다. 암호화된 blob의 스냅샷을 생성하는 동안 메타데이터를 지정하지 않도록 하세요. 메타데이터가 설정되어야 하는 경우 먼저 **FetchAttributes** 메서드를 호출하여 현재 암호화 메타데이터를 가져오고, 메타데이터가 설정되는 동안에는 동시 쓰기를 피합니다.
> * 암호화된 데이터에만 작동해야 하는 사용자의 기본 요청 옵션에는 **RequireEncryption** 속성을 사용하도록 설정합니다. 자세한 내용은 다음을 참조하세요.
> 
> 

## <a name="client-api--interface"></a>클라이언트 API / 인터페이스
EncryptionPolicy 개체를 만드는 동안 사용자만 키를 공급 (IKey 구현), 확인자만 키를 공급 (IKeyResolver 구현) 또는 둘 모두 키를 공급. IKey 래핑/래핑 해제에 대한 논리를 제공하고 키 식별자를 사용하여 식별 되는 기본 키 유형입니다. IKeyResolver 키는 암호 해독 프로세스에서 키를 해독하기 위해 사용됩니다. 키 식별자가 제공하는 IKey를 반환하는 ResolveKey 메서드를 정의 합니다. 이것은 사용자에게 여러 위치에서 관리되는 여러 키 중 하나를 선택할 수 있게 합니다.

* 암호화는 키가 항상 사용되고, 키가 없으면 오류가 발생합니다.
* 암호를 해독하려면
  * 키 확인자는 키를 가져오기 위해 지정된 경우 호출됩니다. 확인자를 지정 하 고 키 식별자에 대한 매핑이 없는 경우, 오류가 전달됩니다.
  * 확인자는 지정하지 않고 키는 지정한 경우 해당 식별자가 필요한 키 식별자와 일치하는 경우 키가 사용됩니다. 식별자가 일치하지 않으면 오류가 throw됩니다.

이 문서의 코드 예제는 암호화 정책을 설정하고 암호화된 데이터를 사용하는 방법을 보여줍니다. 하지만 Azure Key Vault를 사용하는 방법은 보여주지 않습니다. GitHub에 대한 [암호화 샘플](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)은 Key Vault 통합과 함께 Blob, 큐 및 테이블에 대한 보다 자세한 엔드투엔드 간 시나리오를 보여줍니다.

### <a name="requireencryption-mode"></a>RequireEncryption 모드
사용자는 모든 업로드 및 다운로드를 암호화해야 할 경우 작업 모드를 선택적으로 사용하도록 설정할 수 있습니다. 이 모드에서는 클라이언트에서 암호화 정책 없이 데이터를 업로드하거나 서비스에서 암호화되지 않은 데이터를 다운로드하려고 하면 실패합니다. 요청 옵션 개체의 **RequireEncryption** 속성이 이 동작을 제어합니다. 애플리케이션이 Azure Storage에 저장된 모든 개체를 암호화하는 경우 서비스 클라이언트 개체에 대한 기본 요청 옵션에서 **RequireEncryption** 속성을 설정할 수 있습니다. 예를 들어 모든 BLOB 작업에 대한 암호화가 해당 클라이언트 개체를 통해 수행되도록 하려면 **CloudBlobClient.DefaultRequestOptions.RequireEncryption**을 **true**로 설정합니다.


### <a name="blob-service-encryption"></a>Blob service 암호화
**BlobEncryptionPolicy** 개체를 만들고 요청 옵션에서 설정합니다(**DefaultRequestOptions**를 사용하여 API 기준으로 또는 클라이언트 수준에서). 다른 모든 요소에서 처리 되는 클라이언트 라이브러리는 내부적으로 처리됩니다.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

 // Set the encryption policy on the request options.
 BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

 // Upload the encrypted contents to the blob.
 blob.UploadFromStream(stream, size, null, options, null);

 // Download and decrypt the encrypted contents from the blob.
 MemoryStream outputStream = new MemoryStream();
 blob.DownloadToStream(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>큐 서비스 암호화
**QueueEncryptionPolicy** 개체를 만들고 요청 옵션에서 설정합니다(**DefaultRequestOptions**를 사용하여 API 기준으로 또는 클라이언트 수준에서). 다른 모든 요소에서 처리 되는 클라이언트 라이브러리는 내부적으로 처리됩니다.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

### <a name="table-service-encryption"></a>Table service 암호화
암호화 정책을 생성하고 요청 옵션에 설정하는 것 외에도 사용자는 **TableRequestOptions**에서 **EncryptionResolver**를 지정하거나 엔터티에 대해 [EncryptProperty] 특성을 설정해야 합니다.

#### <a name="using-the-resolver"></a>확인자를 사용하여

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>특성 사용
앞서 설명한 것처럼 엔터티가 TableEntity를 구현하는 경우 **EncryptionResolver**를 지정하는 대신 [EncryptProperty] 특성으로 속성을 데코레이트할 수 있습니다.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>암호화 및 성능
스토리지 데이터를 암호화하면 추가 성능 오버헤드가 발생합니다. 콘텐츠 키 및 IV를 생성해야 하고, 콘텐츠 자체를 암호화해야 하고, 추가 메타데이터의 형식을 지정한 후 업로드해야 합니다. 이 오버헤드는 암호화되는 데이터의 양에 따라 달라집니다. 고객은 항상 개발 중에 애플리케이션 성능을 테스트하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [자습서: Microsoft Azure Storage에서 Azure Key Vault를 사용하여 Blob 암호화 및 해독](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* [Azure Storage Client Library for .NET NuGet package](https://www.nuget.org/packages/WindowsAzure.Storage)
* Azure Key Vault NuGet [코어](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [클라이언트](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/), [확장](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) 패키지 다운로드  
* [Azure Key Vault 설명서](../../key-vault/general/overview.md)
