---
title: Azure Cache for Redis에서 데이터 가져오기 및 내보내기 | Microsoft Docs
description: 프리미엄 Azure Cache for Redis 인스턴스를 사용하여 Blob 스토리지에서 데이터를 가져오고 내보내는 방법을 알아봅니다.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
origin.date: 07/31/2017
ms.date: 02/27/2019
ms.author: v-junlch
ms.openlocfilehash: dfa8b47ced70386efa1daa44af318f1da55f49e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542348"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Azure Cache for Redis에서 데이터 가져오기 및 내보내기
가져오기/내보내기는 Azure Cache for Redis 데이터 관리 작업입니다. 즉 프리미엄 캐시에서 Azure Storage 계정의 Blob으로 Azure Cache for Redis 데이터베이스(RDB) 스냅숏을 가져오고 내보내는 방식으로 Azure Cache for Redis에서 데이터를 가져오고 내보낼 수 있습니다. 

- **내보내기** - Azure Cache for Redis RDB 스냅숏을 페이지 Blob으로 내보낼 수 있습니다.
- **가져오기** - Azure Cache for Redis RDB 스냅숏을 페이지 Blob 또는 블록 Blob에서 가져올 수 있습니다.

가져오기/내보내기를 사용하면 다양한 Azure Cache for Redis 인스턴스 간에 마이그레이션하거나 데이터를 사용하기 전에 캐시에 채울 수 있습니다.

이 문서에서는 Azure Cache for Redis를 사용하여 데이터를 가져오고 내보내는 방법에 대한 지침과 자주 묻는 질문에 대한 답변을 제공합니다.

> [!IMPORTANT]
> Import/Export는 미리 보기 상태이며, [프리미엄 계층](cache-premium-tier-intro.md) 캐시에만 제공됩니다.
>
>

## <a name="import"></a>가져오기
가져오기는 Linux, Windows 또는 Amazon Web Services 및 기타 클라우드 공급자에서 실행되는 Redis를 비롯한 환경이나 클라우드에서 실행되는 Redis 서버로부터 Redis 호환 RDB 파일을 가져오는 데 사용됩니다. 데이터 가져오기는 미리 채워진 데이터로 캐시를 만드는 손쉬운 방법입니다. 가져오기 프로세스 중에는 Azure Cache for Redis에서 RDB 파일을 Azure Storage에서 메모리로 로드한 다음, 키를 캐시에 삽입합니다.

> [!NOTE]
> 가져오기 작업을 시작하기 전에 RDB(Redis 데이터베이스) 파일이 Azure Cache for Redis 인스턴스와 동일한 지역 및 구독에 있는 Azure 스토리지의 페이지 Blob 또는 블록 Blob에 업로드되었는지 확인합니다. 자세한 내용은 [Azure Blob Storage 시작](../storage/blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요. [Azure Cache for Redis 내보내기](#export) 기능을 사용하여 RDB 파일을 내보낸 경우 RDB 파일이 이미 페이지 Blob에 저장되어 있고 이를 가져올 준비가 됩니다.
>
>

1. 내보낸 캐시 Blob을 하나 이상 가져오려면 Azure Portal에서 [캐시로 이동](cache-configure.md#configure-azure-cache-for-redis-settings)하여 **리소스 메뉴**에서 **데이터 가져오기**를 클릭합니다.

    ![데이터 가져오기][cache-import-data]
2. **Blob 선택** 을 클릭하고 가져올 데이터를 포함하는 저장소 계정을 선택합니다.

    ![저장소 계정 선택][cache-import-choose-storage-account]
3. 가져올 데이터를 포함하는 컨테이너를 클릭합니다.

    ![컨테이너 선택][cache-import-choose-container]
4. Blob 이름의 왼쪽 영역을 클릭하여 가져올 Blob을 하나 이상 선택한 다음 **선택**을 클릭합니다.

    ![Blob 선택][cache-import-choose-blobs]
5. **가져오기** 를 클릭하여 가져오기 프로세스를 시작합니다.

   > [!IMPORTANT]
   > 가져오기 프로세스가 진행되는 동안 캐시 클라이언트는 캐시에 액세스할 수 없으며, 캐시에 있는 기존 데이터는 삭제됩니다.
   >
   >

    ![가져오기][cache-import-blobs]

    Azure Portal의 알림에 따르거나 [감사 로그](../azure-resource-manager/resource-group-audit.md)의 이벤트를 확인하여 가져오기 작업의 진행 상황을 모니터링할 수 있습니다.

    ![가져오기 진행][cache-import-data-import-complete]

## <a name="export"></a>내보내기
내보내기를 사용하면 Azure Cache for Redis에 저장된 데이터를 Redis 호환 RDB 파일로 내보낼 수 있습니다. 이 기능을 사용하여 데이터를 Azure Cache for Redis 인스턴스에서 다른 인스턴스 또는 다른 Redis 서버로 이동할 수 있습니다. 내보내기 프로세스 중에는 임시 파일이 Azure Cache for Redis 서버 인스턴스를 호스팅하는 VM에 만들어지고, 지정된 스토리지 계정에 업로드됩니다. 성공 또는 실패 상태로 내보내기 작업이 완료되면, 임시 파일은 삭제됩니다.

1. 캐시의 현재 콘텐츠를 저장소로 내보내려면 Azure Portal에서 [캐시로 이동](cache-configure.md#configure-azure-cache-for-redis-settings)하여 **리소스 메뉴**에서 **데이터 내보내기**를 클릭합니다.

    ![저장소 컨테이너 선택][cache-export-data-choose-storage-container]
2. **스토리지 컨테이너 선택**을 클릭하고 원하는 스토리지 계정을 선택합니다. 저장소 계정은 캐시와 동일한 구독 및 지역 내에 있어야 합니다.

   > [!IMPORTANT]
   > 내보내기는 페이지 Blob을 사용하고, 클래식 및 Resource Manager 스토리지 계정 양쪽 모두에서 지원되지만, Blob Storage 계정에서는 현재 지원되지 않습니다. 자세한 내용은 [Azure Storage 계정 개요](../storage/common/storage-account-overview.md)를 참조하세요.
   >
   >

    ![Storage 계정][cache-export-data-choose-account]
3. 원하는 Blob 컨테이너를 선택하고 **선택**을 클릭합니다. 새 컨테이너를 사용하려면 **컨테이너 추가** 를 클릭하여 컨테이너를 추가한 다음 목록에서 선택합니다.

    ![저장소 컨테이너 선택][cache-export-data-container]
4. **Blob 이름 접두사**를 입력하고 **내보내기**를 클릭하여 내보내기 프로세스를 시작합니다. Blob 이름 접두사는 내보내기 작업에 의해 생성되는 파일의 이름에 접두사를 붙이는 데 사용됩니다.

    ![내보내기][cache-export-data]

    Azure Portal의 알림에 따르거나 [감사 로그](../azure-resource-manager/resource-group-audit.md)에서 이벤트를 확인하여 내보내기 작업의 진행 상황을 모니터링할 수 있습니다.

    ![데이터 내보내기 완료][cache-export-data-export-complete]

    내보내기 프로세스를 진행하는 동안 캐시는 사용할 수 있는 상태로 유지됩니다.

## <a name="importexport-faq"></a>Import/Export FAQ
이 섹션은 Import/Export 기능에 대한 질문과 대답을 포함합니다.

- [어떤 가격 책정 계층에서 Import/Export를 사용할 수 있나요?](#what-pricing-tiers-can-use-importexport)
- [Redis 서버에서 데이터를 가져올 수 있나요?](#can-i-import-data-from-any-redis-server)
- [가져올 수 있는 RDB 버전은 무엇인가요?](#what-rdb-versions-can-i-import)
- [Import/Export 작업을 진행하는 동안 내 캐시를 사용할 수 있나요?](#is-my-cache-available-during-an-importexport-operation)
- [Redis 클러스터에 Import/Export를 사용할 수 있나요?](#can-i-use-importexport-with-redis-cluster)
- [Import/Export는 사용자 지정 데이터베이스 설정에서 어떻게 작동합니까?](#how-does-importexport-work-with-a-custom-databases-setting)
- [Import/Export가 Redis 지속성과 어떻게 다른가요?](#how-is-importexport-different-from-redis-persistence)
- [PowerShell, CLI, 또는 다른 관리 클라이언트를 사용하여 Import/Export를 자동화할 수 있나요?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
- [Import/Export 작업을 진행하는 동안 시간 초과 오류가 발생했습니다. 무엇을 의미하나요?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
- [Azure Blob Storage로 데이터를 내보내다가 오류가 발생했습니다. 어떻게 된 건가요?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>어떤 가격 책정 계층에서 Import/Export를 사용할 수 있나요?
Import/Export는 프리미엄 가격 책정 계층에서만 사용할 수 있습니다.

### <a name="can-i-import-data-from-any-redis-server"></a>Redis 서버에서 데이터를 가져올 수 있나요?
예, Azure Cache for Redis 인스턴스에서 내보낸 데이터를 가져오는 것 외에도, Linux, Windows, 또는 클라우드 공급자(예: Amazon Web Services)와 같은 모든 클라우드 또는 환경에서 실행되는 Redis 서버에서 RDB 파일을 가져올 수 있습니다. 이렇게 하려면 원하는 Redis 서버의 RDB 파일을 Azure Storage 계정의 페이지 Blob 또는 블록 Blob으로 업로드한 다음, 프리미엄 Azure Cache for Redis 인스턴스로 가져옵니다. 예를 들어, 프로덕션 캐시의 데이터를 내보내서 마이그레이션 또는 테스트를 위한 스테이징 환경의 일부로 사용되는 캐시로 가져올 수 있습니다.

> [!IMPORTANT]
> 페이지 Blob을 사용할 때 Azure Cache for Redis 이외의 Redis 서버에서 내보낸 데이터를 성공적으로 가져오려면 페이지 Blob 크기가 512바이트 한도에 맞추어져야 합니다. 필요한 바이트 패딩을 수행하는 샘플 코드는 [샘플 페이지 Blob 업로드](https://github.com/JimRoberts-MS/SamplePageBlobUpload)를 참조하세요.
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>가져올 수 있는 RDB 버전은 무엇인가요?

Azure Cache for Redis는 RDB 버전 7을 통해 RDB 가져오기를 지원합니다.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Import/Export 작업을 진행하는 동안 내 캐시를 사용할 수 있나요?
- **내보내기** - 캐시는 사용 가능한 상태로 유지되며, 내보내기 작업을 진행하는 동안 캐시를 계속 사용할 수 있습니다.
- **가져오기** - 캐시는 가져오기 작업이 시작되면 사용할 수 없게 되고, 가져오기 작업이 완료되면 사용할 수 있게 됩니다.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Redis 클러스터에 Import/Export를 사용할 수 있나요?
예, 클러스터형 캐시와 비클러스터형 캐시 사이에서 가져오기/내보내기를 수행할 수 있습니다. Redis 클러스터는 [0 데이터베이스만 지원](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)하기 때문에, 0 이외의 데이터베이스에 저장된 데이터를 가져올 수 없습니다. 클러스터형 캐시 데이터를 가져올 때, 키가 클러스터의 분할에 재배포됩니다.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Import/Export는 사용자 지정 데이터베이스 설정에서 어떻게 작동합니까?
일부 가격 책정 계층에는 다른 [데이터베이스 제한](cache-configure.md#databases)이 있으므로 캐시 생성 중에 `databases` 설정에 대한 사용자 지정 값을 구성했다면 가져오기 할 때 고려 사항이 있습니다.

- 내보내기 한 계층보다 낮은 `databases` 제한을 가진 가격 책정 계층으로 가져오기할 때:
  - 모든 가격 책정 계층에 대해 기본값이 16개인 `databases`을 사용하는 경우 데이터 손실은 전혀 없습니다.
  - 들여오기하는 계층의 대한 제한내에 포함되는 `databases`의 사용자 지정 수를 사용하는 경우, 데이터 손실은 전혀 없습니다.
  - 내보낸 데이타가 새 계층의 제한을 초과하는 데이타베이스의 데이타를 포함한 경우, 더 높은 데이타베이스의 데이타는 들여오기되지 않습니다.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Import/Export가 Redis 지속성과 어떻게 다른가요?
Azure Cache for Redis 지속성을 사용하면 Redis에 저장된 데이터를 Azure Storage에 유지할 수 있습니다. 지속성이 구성되면 Azure Cache for Redis에서 구성 가능한 백업 빈도에 따라 Azure Cache for Redis 스냅숏을 Redis 이진 형식으로 디스크에 유지합니다. 주 캐시 및 복제본 캐시의 기능을 무력화하는 재해가 발생하면, 캐시 데이터는 최신 스냅숏을 사용하여 자동으로 복원됩니다. 자세한 내용은 [프리미엄 Azure Cache for Redis에 대한 데이터 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)을 참조하세요.

가져오기/내보내기를 사용하면 Azure Cache for Redis에서 데이터를 가져오거나 내보낼 수 있습니다. Redis 지속성을 사용하여 백업 및 복원을 구성하지 않습니다.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>PowerShell, CLI, 또는 다른 관리 클라이언트를 사용하여 Import/Export를 자동화할 수 있나요?
예, PowerShell 명령은 [Azure Cache for Redis 가져오기](cache-howto-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) 및 [Azure Cache for Redis 내보내기](cache-howto-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis)를 참조하세요.

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Import/Export 작업을 진행하는 동안 시간 초과 오류가 발생했습니다. 무엇을 의미하나요?
작업을 시작하기 전에 **데이터 가져오기**나 **데이터 내보내기** 블레이드에 15분 넘게 머무르는 경우 다음 예제와 유사한 오류 메시지를 받게 됩니다.

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

이를 해결하려면, 15분이 지나기 전에 가져오기 또는 내보내기 작업을 시작하세요.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Azure Blob Storage로 데이터를 내보내다가 오류가 발생했습니다. 어떻게 된 건가요?
내보내기는 페이지 Blob으로 저장된 RDB 파일에 대해서만 작동합니다. 현재 핫 및 쿨 계층의 Blob Storage 계정을 비롯한 다른 Blob 형식이 지원되지 않습니다. 자세한 내용은 [Azure Storage 계정 개요](../storage/common/storage-account-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
더 많은 프리미엄 캐시 기능을 사용하는 방법에 대해 알아봅니다.

- [Azure Cache for Redis 프리미엄 계층 소개](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png


<!-- Update_Description: update metedata properties -->