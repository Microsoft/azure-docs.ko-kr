---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ea8ed75bf91850abb95ebe983923989375c0fcf5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76759854"
---
Azure Portal에서 범용 v2 스토리지 계정을 만들려면 다음 단계를 수행합니다.

1. Azure Portal 메뉴에서 **모든 서비스**를 선택합니다. 리소스 목록에 **스토리지 계정**을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Storage 계정**을 선택합니다.
2. 나타나는 **Storage 계정** 창에서 **추가**를 선택합니다.
3. 스토리지 계정을 만들 구독을 선택합니다.
4. **리소스 그룹** 필드 아래에서 **새로 만들기**를 선택합니다. 다음 이미지에 표시된 대로 새 리소스 그룹의 이름을 입력합니다.

    ![포털에서 리소스 그룹을 만드는 방법을 보여주는 스크린샷](./media/storage-create-account-portal-include/create-resource-group-for-storage.png)

5. 그런 다음, 스토리지 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
6. 스토리지 계정의 위치를 선택하거나 기본 위치를 사용합니다.
7. 다음 필드는 기본값으로 유지합니다.

   |필드  |값  |
   |---------|---------|
   |배포 모델     |리소스 관리자         |
   |성능     |Standard         |
   |계정 종류     |StorageV2(범용 v2)         |
   |복제     |RA-GRS(읽기 액세스 지역 중복 스토리지)         |
   |액세스 계층     |핫         |

8. [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)를 사용하려는 경우 **고급** 탭을 선택한 다음, **계층 구조 네임스페이스**를 **사용**으로 설정합니다.
9. **검토 + 만들기**를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.
10. **만들기**를 선택합니다.

스토리지 계정 유형 및 기타 스토리지 계정 설정에 대한 자세한 내용은 [Azure Storage 계정 개요](https://docs.microsoft.com/azure/storage/common/storage-account-overview)를 참조하세요. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요. 
