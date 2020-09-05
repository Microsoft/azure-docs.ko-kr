---
title: Azure API에서 FHIR에 대 한 내보내기 설정 구성
description: 이 문서에서는 Azure API for FHIR에서 내보내기 설정을 구성 하는 방법을 설명 합니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 7ed4e471764fbf8ecc9b82a7d614ae52b97360f1
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267261"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>내보내기 설정 구성 및 저장소 계정 설정

FHIR 용 azure API는 Azure API 용 Azure API에서 저장소 계정으로 데이터를 내보낼 수 있는 $export 명령을 지원 합니다.

FHIR 용 Azure API에서 내보내기를 구성 하는 데는 세 가지 단계가 있습니다.

1. Azure API에서 관리 되는 Id를 사용 하도록 설정 합니다.
2. Azure storage 계정 만들기 (이전에 수행 되지 않은 경우) 및 저장소 계정에 대 한 Azure API에 대 한 사용 권한 할당
3. Azure API에서 저장소 계정 내보내기로 FHIR의 저장소 계정 선택

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Azure API에서 관리 되는 Id를 사용 하도록 설정

내보내기에 대 한 Azure API를 구성 하는 첫 번째 단계는 서비스에서 시스템 차원의 관리 id를 사용 하도록 설정 하는 것입니다. Azure에서 관리 되는 Id에 대 한 모든 정보는 [여기](../active-directory/managed-identities-azure-resources/overview.md)에서 읽을 수 있습니다.

이렇게 하려면 FHIR 서비스에 대 한 Azure API로 이동 하 고 Id 블레이드를 선택 합니다. 상태를 켜기로 변경 하면 Azure API for FHIR 서비스에서 관리 id를 사용할 수 있습니다.

![관리 Id 사용](media/export-data/fhir-mi-enabled.png)

이제 다음 단계로 이동 하 여 저장소 계정을 만들고 서비스에 사용 권한을 할당할 수 있습니다.

## <a name="adding-permission-to-storage-account"></a>저장소 계정에 사용 권한 추가

내보내기의 다음 단계는 저장소 계정에 쓰도록 Azure API 용 Azure API에 대 한 사용 권한을 할당 하는 것입니다.

저장소 계정을 만든 후 저장소 계정에서 Access Control (IAM) 블레이드로 이동 하 여 역할 할당 추가를 선택 합니다.

![관리 Id 사용](media/export-data/fhir-export-role-assignment.png)

그런 다음 서비스 이름에 역할 저장소 Blob 데이터 참가자를 추가 합니다.

![관리 Id 사용](media/export-data/fhir-export-role-add.png)

이제 다음 단계를 수행할 준비가 되었습니다. 여기서 FHIR 용 Azure API의 저장소 계정을 $export에 대 한 기본 저장소 계정으로 선택할 수 있습니다.

## <a name="selecting-the-storage-account-for-export"></a>$export에 대 한 저장소 계정 선택

마지막 단계는 Azure API 용 Azure API가 데이터를로 내보내는 데 사용할 Azure storage 계정을 할당 하는 것입니다. 이렇게 하려면 Azure Portal의 FHIR 서비스에 대 한 Azure API의 통합 블레이드로 이동 하 고 저장소 계정을 선택 합니다.

![관리 Id 사용](media/export-data/fhir-export-storage.png)

그런 다음 $export 명령을 사용 하 여 데이터를 내보낼 준비가 된 것입니다.

>[!div class="nextstepaction"]
>[추가 설정](azure-api-for-fhir-additional-settings.md)
