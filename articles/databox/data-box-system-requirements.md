---
title: Microsoft Azure Data Box 시스템 요구 사항 | Microsoft Docs
description: Azure Data Box의 소프트웨어 및 네트워킹 요구 사항에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 7d52af9e3948f40936795efab5b6671c3f71007a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746933"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box 시스템 요구 사항

이 문서에서는 Microsoft Azure Data Box 및 Data Box에 연결하는 클라이언트에 대한 중요한 시스템 요구 사항을 설명합니다. Data Box를 배포하기 전에 정보를 신중하게 검토하고 배포 및 후속 작업 중 필요에 따라 다시 검토하는 것이 좋습니다.

시스템 요구 사항에는 다음 내용이 포함됩니다.

* **호스트가 Data Box에 연결하기 위한 소프트웨어 요구 사항** - 지원되는 플랫폼, 로컬 웹 UI용 브라우저, SMB 클라이언트 및 Data Box에 연결할 수 있는 호스트에 대한 추가 요구 사항을 설명합니다.
* **Data Box의 네트워킹 요구 사항** - Data Box가 최적으로 작동하기 위한 네트워킹 요구 사항에 관한 정보를 제공합니다.


## <a name="software-requirements"></a>소프트웨어 요구 사항

소프트웨어 요구 사항에는 지원되는 운영 체제, 로컬 웹 UI에 지원되는 브라우저 및 SMB 클라이언트에 관한 정보가 포함됩니다.

### <a name="supported-operating-systems-for-clients"></a>클라이언트에 대해 지원되는 운영 체제

Data Box 디바이스에 연결된 클라이언트를 통해 데이터 복사 작업에 대해 지원되는 운영 체제의 목록은 다음과 같습니다.

| **운영 체제** | **버전** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Linux 클라이언트에 지원되는 파일 시스템

| **프로토콜** | **버전** | 
| --- | --- | 
| SMB |2.X 이상 |
| NFS | 4.1(포함)까지의 모든 버전|

### <a name="supported-storage-accounts"></a>지원되는 저장소 계정

아래에는 Data Box 디바이스용으로 지원되는 스토리지 계정 및 스토리지 유형의 목록이 나와 있습니다. 모든 유형의 스토리지 계정과 해당 계정의 모든 기능이 포함된 전체 목록은 [스토리지 계정의 유형](/azure/storage/common/storage-account-overview#types-of-storage-accounts)을 참조하세요.

| **스토리지 계정/지원되는 스토리지 유형** | **블록 Blob** |**페이지 Blob*** |**Azure 파일** |**참고 사항**|
| --- | --- | -- | -- | -- |
| Classic Standard | Y | Y | Y |
| 범용 v1 Standard  | Y | Y | Y | 핫 및 쿨이 모두 지원됩니다.|
| 범용 v1 Premium  |  | Y| | |
| 범용 v2 Standard  | Y | Y | Y | 핫 및 쿨이 모두 지원됩니다.|
| 범용 v2 Premium  |  |Y | | |
| Blob Storage Standard |Y | | |핫 및 쿨이 모두 지원됩니다. |

\*  *- 페이지 Blob에 업로드하는 데이터는 vhd와 같이 512바이트로 정렬된 데이터여야 합니다.*

>[!NOTE]
> Azure Data Lake Storage Gen 2 계정은 지원되지 않습니다.


### <a name="supported-storage-types"></a>지원되는 저장소 형식

Data Box 디바이스에 대해 지원되는 저장소 형식의 목록은 다음과 같습니다.

| **파일 형식** | **참고 사항** |
| --- | --- |
| Azure 블록 Blob | |
| Azure 페이지 Blob  | 데이터는 512바이트로 정렬되어야 합니다.|
| Azure 파일 | |


### <a name="supported-web-browsers"></a>지원되는 웹 브라우저

로컬 웹 UI에 대해 지원되는 웹 브라우저 목록입니다.

| **브라우저** | **버전** | **추가 요구 사항/메모** |
| --- | --- | --- |
| Google Chrome |최신 버전 |Chrome으로 테스트함|
| Microsoft Edge |최신 버전 | |
| FireFox | 최신 버전 | FireFox로 테스트됨|
| Internet Explorer |최신 버전 |로그인할 수 없는 경우 쿠키 및 Javascript가 사용 가능한지 확인합니다. UI 액세스를 사용하려면 디바이스에서 쿠키에 액세스할 수 있도록 디바이스 IP를 **개인 정보 작업**에 추가합니다. |


## <a name="networking-requirements"></a>네트워킹 요구 사항

데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용하여 데이터를 복사할 수 있지만 복사 속도는 느려집니다.

## <a name="next-step"></a>다음 단계

* [Azure Data Box 배포](data-box-deploy-ordered.md)

