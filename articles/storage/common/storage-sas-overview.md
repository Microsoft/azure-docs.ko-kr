---
title: SAS (공유 액세스 서명)를 사용 하 여 데이터에 대 한 제한 된 액세스 권한 부여
titleSuffix: Azure Storage
description: SAS (공유 액세스 서명)를 사용 하 여 blob, 큐, 테이블 및 파일을 비롯 한 Azure Storage 리소스에 대 한 액세스 권한을 위임 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7a5967f52a187fe289c6fb1ca72af2d5fd17f010
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121931"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여

SAS (공유 액세스 서명)는 데이터의 보안을 손상 시 키 지 않고 저장소 계정의 리소스에 대 한 안전한 위임 된 액세스를 제공 합니다. SAS를 사용 하면 클라이언트가 데이터에 액세스 하는 방법을 세부적으로 제어할 수 있습니다. 클라이언트에서 액세스할 수 있는 리소스, 해당 리소스에 대 한 사용 권한, SAS가 유효한 기간 (기타 매개 변수)을 제어할 수 있습니다.

## <a name="types-of-shared-access-signatures"></a>공유 액세스 서명의 유형

Azure Storage는 다음과 같은 세 가지 유형의 공유 액세스 서명을 지원 합니다.

- **사용자 위임 SAS.** 사용자 위임 SAS는 Azure Active Directory (Azure AD) 자격 증명과 SAS에 대해 지정 된 사용 권한으로 보호 됩니다. 사용자 위임 SAS는 Blob 저장소에만 적용 됩니다.

    사용자 위임 SAS에 대 한 자세한 내용은 [사용자 위임 Sas 만들기 (REST API)](/rest/api/storageservices/create-user-delegation-sas)를 참조 하세요.

- **서비스 SAS** 서비스 SAS는 저장소 계정 키를 사용 하 여 보호 됩니다. Service SAS는 Azure Storage 서비스 중 하나 (Blob 저장소, 큐 저장소, 테이블 저장소 또는 Azure Files)에서 리소스에 대 한 액세스를 위임 합니다. 

    서비스 SAS에 대 한 자세한 내용은 [서비스 Sas 만들기 (REST API)](/rest/api/storageservices/create-service-sas)를 참조 하세요.

- **계정 SAS** 계정 SAS는 저장소 계정 키를 사용 하 여 보호 됩니다. 계정 SAS는 하나 이상의 스토리지 서비스에서 리소스에 대한 액세스 권한을 위임합니다. 서비스 또는 사용자 위임 SAS를 통해 사용할 수 있는 모든 작업은 계정 SAS를 통해서도 사용할 수 있습니다. 또한 계정 SAS를 사용 하면 서비스 **속성 가져오기/설정** , **서비스 통계 가져오기** 작업과 같이 서비스 수준에서 적용 되는 작업에 대 한 액세스를 위임할 수 있습니다. 또한 서비스 SAS로 허용되지 않는 Blob 컨테이너, 테이블, 큐, 파일 공유에서 읽기, 쓰기, 삭제 작업에 대한 액세스 권한을 위임할 수 있습니다. 

    계정 SAS에 대 한 자세한 내용을 보려면 [계정 sas (REST API)를 만드세요](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> 가능 하면 계정 키를 사용 하는 대신 보안 모범 사례로 Azure AD 자격 증명을 사용 하는 것이 좋습니다 .이는 더 쉽게 손상 될 수 있습니다. 응용 프로그램 디자인에 Blob storage에 액세스 하기 위해 공유 액세스 서명이 필요한 경우 Azure AD 자격 증명을 사용 하 여 뛰어난 보안을 위해 가능한 한 사용자 위임 SAS를 만듭니다.

공유 액세스 서명은 다음 두 가지 형식 중 하나를 사용할 수 있습니다.

- 임시 **SAS:** 임시 SAS를 만들 때 SAS의 시작 시간, 만료 시간 및 사용 권한이 SAS URI에 모두 지정 되거나 시작 시간이 생략 되는 경우 묵시적으로 지정 됩니다. 모든 유형의 SAS는 임시 SAS 일 수 있습니다.
- **저장 된 액세스 정책을 사용 하는 서비스 SAS:** 저장 된 액세스 정책은 blob 컨테이너, 테이블, 큐 또는 파일 공유 일 수 있는 리소스 컨테이너에 정의 됩니다. 저장 된 액세스 정책은 하나 이상의 서비스 공유 액세스 서명에 대 한 제약 조건을 관리 하는 데 사용할 수 있습니다. 서비스 SAS를 저장 된 액세스 정책과 연결 하는 경우 SAS는 저장 된 액세스 정책에 대해 정의&mdash;시작 시간, 만료 시간 및 사용 권한&mdash;제약 조건을 상속 합니다.

> [!NOTE]
> 사용자 위임 SAS 또는 계정 SAS는 임시 SAS 여야 합니다. 사용자 위임 SAS 또는 계정 SAS에 대해 저장 된 액세스 정책이 지원 되지 않습니다.

## <a name="how-a-shared-access-signature-works"></a>공유 액세스 서명 사용 방법

공유 액세스 서명은 하나 이상의 스토리지 리소스를 가리키는 서명된 URI이며 쿼리 매개 변수의 특별 집합이 포함된 토큰이 들어 있습니다. 토큰은 클라이언트가 리소스를 액세스하는 방식을 나타냅니다. 쿼리 매개 변수 중 하나인 서명은 SAS 매개 변수에서 생성 되 고 SAS를 만드는 데 사용 된 키로 서명 됩니다. 이 서명은 Azure Storage에서 스토리지 리소스에 대한 액세스 권한을 부여하는 데 사용됩니다.

### <a name="sas-signature"></a>SAS 서명

다음 두 가지 방법 중 하나로 SAS에 서명할 수 있습니다.

- Azure Active Directory (Azure AD) 자격 증명을 사용 하 여 만든 *사용자 위임 키* . 사용자 위임 SAS는 사용자 위임 키로 서명 됩니다.

    사용자 위임 키를 가져오고 SAS를 만들려면 Azure AD 보안 주체에 **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** 작업을 포함 하는 RBAC (역할 기반 액세스 제어) 역할을 할당 해야 합니다. 사용자 위임 키를 가져올 권한이 있는 RBAC 역할에 대 한 자세한 내용은 [사용자 위임 SAS 만들기 (REST API)](/rest/api/storageservices/create-user-delegation-sas)를 참조 하세요.

- 저장소 계정 키를 사용 합니다. 서비스 SAS와 계정 SAS는 모두 저장소 계정 키로 서명 됩니다. 계정 키로 서명 된 SAS를 만들려면 응용 프로그램에 계정 키에 대 한 액세스 권한이 있어야 합니다.

### <a name="sas-token"></a>SAS 토큰

SAS 토큰은 클라이언트 쪽에서 생성 하는 문자열입니다. 예를 들어 Azure Storage 클라이언트 라이브러리 중 하나를 사용 합니다. SAS 토큰은 어떤 방식으로든 Azure Storage에서 추적 되지 않습니다. 클라이언트 쪽에서 SAS 토큰 개수를 제한 없이 만들 수 있습니다. SAS를 만든 후에는 저장소 계정의 리소스에 액세스 해야 하는 클라이언트 응용 프로그램에이를 배포할 수 있습니다.

클라이언트 응용 프로그램이 요청의 일부로 Azure Storage SAS URI를 제공 하는 경우 서비스는 SAS 매개 변수 및 서명을 검사 하 여 요청에 대 한 권한을 부여 하는 데 유효한 지 확인 합니다. 서비스에서 서명이 유효하다고 확인한 경우 요청이 승인됩니다. 그렇지 않은 경우 오류 코드 403(금지됨) 요청이 거부됩니다.

다음은 리소스 URI 및 SAS 토큰을 표시 하는 서비스 SAS URI의 예입니다.

![서비스 SAS URI의 구성 요소](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>공유 액세스 서명을 사용 하는 경우

저장소 계정의 리소스에 대 한 보안 액세스를 제공 하려는 경우에는 해당 리소스에 대 한 사용 권한이 없는 클라이언트에 대 한 보안 액세스를 제공 하려는 경우 SAS를 사용 합니다.

SAS가 유용한 일반적인 시나리오로는 다른 사용자가 스토리지 계정에서 데이터를 읽고 쓰는 서비스가 있습니다. 스토리지 계정에 사용자 데이터를 저장하는 시나리오에는 다음과 같은 두 가지 일반적인 디자인 패턴이 있습니다.

1. 클라이언트는 인증을 수행하는 프런트 엔드 프록시 서비스를 통해 데이터를 업로드하고 다운로드합니다. 프런트 엔드 프록시 서비스는 비즈니스 규칙의 유효성을 검사할 수 있다는 장점이 있지만, 데이터의 양이 많거나 트랜잭션 볼륨이 높은 경우 수요에 맞게 확장 가능한 서비스를 구축하려면 많은 비용이 필요하거나 어려움이 따를 수 있습니다.

   ![시나리오 다이어그램: 프런트 엔드 프록시 서비스](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. 간단한 서비스에서 필요에 따라 클라이언트를 인증한 다음 SAS를 생성합니다. 클라이언트 응용 프로그램이 SAS를 수신 하면 sas에서 정의한 사용 권한 및 SAS에서 허용 하는 간격으로 저장소 계정 리소스에 직접 액세스할 수 있습니다. SAS를 사용하면 프런트 엔드 프록시 서비스를 통해 모든 데이터를 라우팅할 필요성이 감소됩니다.

   ![시나리오 다이어그램: SAS 공급자 서비스](./media/storage-sas-overview/sas-storage-provider-service.png)

대부분의 실제 서비스에서는 이러한 두 가지 방법을 혼합하여 사용할 수 있습니다. 예를 들어 일부 데이터는 프런트 엔드 프록시를 통해 처리 및 확인하고 일부 데이터는 SAS를 사용하여 직접 저장하거나 읽습니다.

또한 특정 시나리오에서 복사 작업의 원본 개체에 대 한 액세스 권한을 부여 하려면 SAS가 필요 합니다.

- 다른 스토리지 계정에 있는 다른 Blob에 Blob을 복사하는 경우 SAS를 사용하여 원본 Blob에 대한 액세스 권한을 부여해야 합니다. 필요에 따라 SAS를 사용하여 대상 Blob에 대한 액세스 권한을 부여할 수도 있습니다.
- 다른 스토리지 계정에 있는 다른 파일에 파일을 복사하는 경우 SAS를 사용하여 원본 파일에 대한 액세스 권한을 부여해야 합니다. 필요에 따라 SAS를 사용하여 대상 파일에 대한 액세스 권한을 부여할 수도 있습니다.
- Blob을 파일에 복사하거나 파일을 Blob에 복사하는 경우 원본 및 대상 개체가 동일한 스토리지 계정 내에 있더라도 SAS를 사용하여 원본 개체에 대한 액세스 권한을 부여해야 합니다.

## <a name="best-practices-when-using-sas"></a>SAS를 사용하는 경우 모범 사례

애플리케이션에서 공유 액세스 서명을 사용할 경우 다음과 같은 두 가지 잠재적 위험에 대해 잘 알고 있어야 합니다.

- SAS가 누설될 경우 SAS를 획득한 모든 사용자가 SAS를 사용하여 스토리지 계정을 손상시킬 수 있습니다.
- 클라이언트 애플리케이션에 제공된 SAS가 만료되어 애플리케이션이 서비스에서 새 SAS를 검색할 수 없는 경우 애플리케이션의 기능이 저하될 수 있습니다.

다음은 공유 액세스 서명을 사용하여 이러한 위험을 완화하는 데 도움이 될 수 있는 권장 사항입니다.

- **항상 HTTPS를 사용하여** SAS를 만들거나 분산합니다. HTTP를 통해 SAS를 전달할 경우 SAS가 노출되면 메시지 가로채기(man-in-the-middle) 공격을 수행하는 공격자가 SAS를 읽은 다음 의도된 사용자처럼 SAS를 사용할 수 있으므로, 악의적인 사용자가 중요한 데이터를 손상시킬 수 있습니다.
- **가능 하면 사용자 위임 SAS를 사용 합니다.** 사용자 위임 SAS는 서비스 SAS 또는 계정 SAS에 뛰어난 보안을 제공 합니다. 사용자 위임 SAS는 Azure AD 자격 증명으로 보호 되므로 사용자의 코드를 사용 하 여 계정 키를 저장할 필요가 없습니다.
- **SAS에 대 한 해지 계획을 준비 해야 합니다.** SAS가 손상 되 면 응답할 준비가 되어 있는지 확인 합니다.
- **서비스 SAS에 대 한 저장 된 액세스 정책을 정의 합니다.** 저장 된 액세스 정책은 저장소 계정 키를 다시 생성 하지 않고도 서비스 SAS에 대 한 사용 권한을 철회 하는 옵션을 제공 합니다. 만료 시간을 매우 길게(또는 무제한) 설정하고 정기적으로 업데이트하여 만료 시간을 미래의 시간으로 이동해야 합니다.
- **Ad hoc SAS 서비스 SAS 또는 계정 SAS에서 단기 만료 시간을 사용 합니다.** 그러면 SAS가 손상되더라도 단기적으로만 유효합니다. 이 방법은 저장된 액세스 정책을 참조할 수 없는 경우에 특히 중요합니다. 또한 짧은 만료 시간은 업로드할 수 있는 시간을 제한하여 Blob에 쓸 수 있는 데이터의 양을 제한할 수 있습니다.
- **필요한 경우 클라이언트가 SAS를 자동으로 갱신하도록 허용합니다.** 클라이언트는 만료일 이전에 SAS를 갱신하여 SAS를 제공하는 서비스를 사용할 수 없을 때 다시 시도할 수 있는 시간적 여유를 확보해야 합니다. 만료 기간 내에 완료할 수 있는 즉각적인 소규모 단기 작업에 SAS를 사용할 경우에는 SAS를 갱신할 필요가 없습니다. 하지만 클라이언트가 SAS를 통해 일상 요청을 수행하는 경우에는 중간에 만료될 수 있습니다. 따라서 이전에 언급한 SAS 단기 실행 요구 사항과 클라이언트가 조기에 갱신을 요청하여 갱신 이전에 SAS가 만료되어 가동 중단이 발생하는 것을 방지해야 하는 요구 사항을 적절하게 조율하는 것이 중요합니다.
- **SAS 시작 시간에 유의하세요.** SAS 시작 시간을 **지금**으로 설정한 경우 클럭 오차(컴퓨터의 차이에 따른 현재 시간의 차이)로 인해 처음 몇 분 동안 간헐적으로 오류가 발생할 수 있습니다. 일반적으로 시작 시간을 최소 15분 이전으로 설정하거나 설정하지 않습니다. 그러면 시작 시간이 즉시 유효해집니다. 이는 만료 시간에도 일반적으로 적용됩니다. 요청 방향에서 최대 15분의 클럭 오차가 발생할 수 있습니다. 2012-02-12 이전 REST 버전을 사용하는 클라이언트의 경우 저장된 액세스 정책을 참조하지 않는 SAS의 최대 기간은 1시간이고, 이 시간보다 더 긴 기간을 지정하는 정책은 실패합니다.
- **SAS 날짜/시간 형식에 주의 하세요.** SAS의 시작 시간 및/또는 만료를 설정 하는 경우 일부 유틸리티 (예: 명령줄 유틸리티 AzCopy)의 경우 datetime 형식이 SAS 토큰을 사용 하 여 작동 하는 데 필요한 시간 (초)을 ' +% Y-% m-% dT% H:%M:% SZ '로 설정 해야 합니다.  
- **액세스할 리소스를 구체적으로 지정하세요.** 보안을 위한 최적의 방법은 사용자에게 필요한 최소 권한을 제공하는 것입니다. 사용자가 단일 엔터티에 대한 읽기 권한만 필요한 경우 해당 엔터티에 대한 읽기 권한만 부여하고 모든 엔터티에 대한 읽기/쓰기/삭제 권한은 부여하지 않습니다. 또한 SAS가 공격자의 수중에 넘어갈 가능성이 낮아지므로 SAS가 손상될 경우 손해가 줄어듭니다.
- **사용자의 계정에 SAS를 비롯 한 모든 사용량에 대 한 요금이 청구 됨을 이해 합니다.** Blob에 대 한 쓰기 권한을 제공 하는 경우 사용자는 200 GB blob을 업로드 하도록 선택할 수 있습니다. 사용자에게 읽기 권한도 제공한 경우 사용자가 이 Blob을 10번 다운로드하도록 선택하여 2TB의 발신 비용이 청구될 수 있습니다. 또한 제한된 권한을 제공하여 악의적인 사용자의 작업 가능성을 낮추세요. 단기 실행 SAS를 사용하여 이 위협을 줄이세요. 이때 종료 시간의 클럭 오차에 유의하세요.
- **SAS를 사용 하 여 작성 된 데이터의 유효성을 검사 합니다.** 클라이언트 애플리케이션이 스토리지 계정에 데이터를 쓸 경우 해당 데이터에 문제가 있을 수 있습니다. 데이터를 사용할 준비가 되기 이전에 애플리케이션에서 데이터의 유효성을 검사하거나 권한을 부여해야 하는 경우 데이터를 작성한 이후 애플리케이션에서 데이터를 사용하기 이전에 유효성 검사를 수행해야 합니다. 그러면 SAS를 적절한 방법으로 습득한 사용자나 누설된 SAS를 악용하는 사용자로 인해 계정이 손상되거나 데이터에 악의적인 데이터가 기록되는 것을 방지할 수 있습니다.
- **SAS를 사용 하지 않을 시기를 확인 합니다.** 경우에 따라 저장소 계정에 대 한 특정 작업과 관련 된 위험이 SAS 사용의 이점 보다 더 큽니다. 그런 작업에 대해서는 비즈니스 규칙 유효성 검사, 인증 및 감사를 수행한 이후에 스토리지 계정에 쓰는 중간 계층 서비스를 만듭니다. 또한 다른 방법으로 액세스를 관리하는 것이 더 간단한 경우도 있습니다. 예를 들어 컨테이너의 모든 Blob을 공개적으로 읽기 가능하도록 설정하려면 모든 클라이언트가 액세스하도록 SAS를 제공하는 대신에 컨테이너를 공용으로 설정할 수 있습니다.
- **Azure Monitor 및 Azure Storage 로그를 사용 하 여 응용 프로그램을 모니터링 합니다.** Azure Monitor 및 저장소 분석 로깅을 사용 하 여 SAS 공급자 서비스의 가동 중단 또는 저장 된 액세스 정책의 실수로 제거로 인 한 권한 부여 오류의 급증을 관찰할 수 있습니다. 자세한 내용은 Azure Monitor 및 [Azure 스토리지 분석 로깅](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) [의 Azure Storage 메트릭](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 을 참조 하세요.

## <a name="get-started-with-sas"></a>SAS 시작

공유 액세스 서명을 시작 하려면 각 SAS 유형에 대 한 다음 문서를 참조 하세요.

### <a name="user-delegation-sas"></a>사용자 위임 SAS

- [PowerShell을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Azure CLI를 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [.NET을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>서비스 SAS

- [.NET을 사용 하 여 컨테이너 또는 blob에 대 한 서비스 SAS 만들기](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>계정 SAS

- [.NET을 사용 하 여 계정 SAS 만들기](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>다음 단계

- [공유 액세스 서명 (REST API)을 사용 하 여 액세스 위임](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [사용자 위임 SAS (REST API) 만들기](/rest/api/storageservices/create-user-delegation-sas)
- [Service SAS (REST API) 만들기](/rest/api/storageservices/create-service-sas)
- [계정 SAS (REST API) 만들기](/rest/api/storageservices/create-account-sas)
