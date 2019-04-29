---
title: Azure Blob, Azure 파일 또는 Azure 디스크를 사용할 시기 결정
description: 사용할 기술을 결정할 수 있도록 Azure에서 데이터를 저장 및 액세스하는 여러 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 869d2105ccf635a46a21e9b7f382ddbef713d68b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483420"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Azure Blob, Azure 파일 또는 Azure 디스크를 사용할 시기 결정
Microsoft Azure에서는 Azure Storage에서 클라우드에 데이터를 저장 및 액세스하기 위한 여러 가지 기능을 제공합니다. 이 문서에서는 Azure 파일, Blob 및 디스크에 대해 설명하고 이러한 기능 중에서 선택하는 데 도움이 되도록 작성되었습니다.

## <a name="scenarios"></a>시나리오
다음 표에서 파일, Blob 및 디스크를 비교하고 각각에 대해 적절한 예제 시나리오를 보여 줍니다.

| 기능 | 설명 | 사용하는 경우 |
|--------------|-------------|-------------|
| **Azure 파일** | 어디에서나 저장된 파일에 액세스할 수 있는 SMB 인터페이스, 클라이언트 라이브러리 및 [REST 인터페이스](/rest/api/storageservices/file-service-rest-api)를 제공합니다. | 이미 기본 파일 시스템 API를 사용하는 클라우드로 애플리케이션을 “전환”하여 Azure에서 실행 중인 다른 애플리케이션과 데이터를 공유하려고 합니다.<br/><br/>여러 가상 머신에서 액세스해야 하는 개발 및 디버깅 도구를 저장하려고 합니다. |
| **Azure Blob** | 구조화되지 않은 데이터를 블록 Blob에서 대규모로 저장 및 액세스할 수 있도록 클라이언트 라이브러리 및 [REST 인터페이스](/rest/api/storageservices/blob-service-rest-api)를 제공합니다.<br/><br/>엔터프라이즈 빅 데이터 분석 솔루션을 위한 [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)도 지원합니다. | 애플리케이션에서 스트리밍 및 임의 액세스 시나리오를 지원하도록 하려고 합니다.<br/><br/>어디에서든 애플리케이션 데이터에 액세스할 수 있게 되기를 원합니다.<br/><br/>Azure에서 엔터프라이즈 Data Lake를 빌드하고 빅 데이터 분석을 수행하려고 합니다. |
| **Azure 디스크** | 데이터를 연결된 가상 하드 디스크에서 영구적으로 저장 및 액세스할 수 있도록 클라이언트 라이브러리 및 [REST 인터페이스](/rest/api/compute/manageddisks/disks/disks-rest-api)를 제공합니다. | 기본 파일 시스템 API를 사용하는 애플리케이션을 데이터를 영구 디스크에 읽고 쓰도록 전환하려고 합니다.<br/><br/>가상 머신 외부에서 액세스할 필요가 없는 데이터를 디스크가 연결된 컴퓨터에 저장하려고 합니다. |

## <a name="comparison-files-and-blobs"></a>비교: 파일 및 Blob
다음 표는 Azure 파일과 Azure Blob을 비교하여 보여 줍니다.  
  
||||  
|-|-|-|  
|**특성**|**Azure Blob**|**Azure 파일**|  
|내구성 옵션|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|접근성|REST API|REST API<br /><br /> SMB 2.1 및 SMB 3.0(표준 파일 시스템 API)|  
|연결|REST APIs -- 전 세계|REST APIs - 전 세계<br /><br /> SMB 2.1 -- 지역 내<br /><br /> SMB 3.0 -- 전 세계|  
|엔드포인트|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|디렉터리|단일 구조 네임스페이스|실제 디렉터리 개체|  
|이름 대/소문자 구분|대/소문자 구분|대/소문자 구분 안 함, 대/소문자 유지|  
|용량|PiB 계정 제한 최대 2개 |5TiB 파일 공유|  
|처리량|블록 Blob당 최대 60MiB/s|공유당 최대 60MiB/s|  
|개체 크기|블록 Blob당 최대 약 4.75TiB|파일당 최대 1TiB|  
|요금 청구 용량|기록된 바이트 기준|파일 크기 기준|  
|클라이언트 라이브러리|여러 언어|여러 언어|  
  
## <a name="comparison-files-and-disks"></a>비교: 파일 및 디스크
Azure 파일은 Azure 디스크를 보완합니다. 디스크는 한 번에 하나의 Azure Virtual Machine에만 연결할 수 있습니다. 디스크는 Azure Storage에 페이지 Blob으로 저장된 고정 형식의 VHD이며 가상 머신에서 지속형 데이터를 저장하는 데 사용됩니다. Azure 파일에서 파일 공유는 로컬 디스크가 액세스되는 것과 동일한 방식(기본 파일 시스템 API 사용)으로 액세스할 수 있으며 여러 가상 머신 간에 공유할 수 있습니다.  
 
다음 표는 Azure 파일과 Azure 디스크를 비교하여 보여 줍니다.  
 
||||  
|-|-|-|  
|**특성**|**Azure 디스크**|**Azure 파일**|  
|범위|단일 가상 머신에 배타적으로 적용|여러 가상 머신 간에 공유 액세스|  
|스냅숏 및 복사|예|예|  
|구성|가상 컴퓨터 시작 시 연결됨|가상 컴퓨터가 시작된 후 연결됨|  
|Authentication|기본 제공|net use로 설정|  
|REST를 사용하여 액세스|VHD 내의 파일에 액세스할 수 없음|공유에 저장된 파일에 액세스할 수 있음|  
|최대 크기|4TiB 디스크|공유 내 5TiB 파일 공유 및 1TiB 파일|  
|최대 IOps|500IOps|1000IOps|  
|처리량|디스크당 최대 60MiB/s|대상은 파일 공유당 60MiB/초임(IO가 더 크면 더 많이 가져올 수 있음)|  

## <a name="next-steps"></a>다음 단계
데이터가 저장 및 액세스되는 방식을 결정할 때 관련 비용도 고려해야 합니다. 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.
  
일부 SMB 기능은 클라우드에 적용되지 않습니다. 자세한 내용은 [Azure File 서비스에서 지원되지 않는 기능](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)을 참조하세요.
  
디스크에 대한 자세한 내용은 [관리 디스크 소개](../../virtual-machines/windows/managed-disks-overview.md) 및 [Windows Virtual Machine에 데이터 디스크를 연결하는 방법](../../virtual-machines/windows/attach-managed-disk-portal.md)을 참조하세요.
