---
title: FHIR 용 Azure API에서 $export 명령을 호출 하 여 내보내기 실행
description: 이 문서에서는 식별 되지 않은 내보내기를 설정 하 고 사용 하는 방법을 설명 합니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 10d901f73006051e8b1ddd02aeb36b229c6a7761
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270287"
---
# <a name="how-to-export-fhir-data"></a>FHIR 데이터를 내보내는 방법

내보내기 설정 구성 및 Azure storage 계정 만들기는 [여기](configure-export-data.md)를 참조 하세요.

## <a name="exporting-fhir-resources-using-export-command"></a>$export 명령을 사용 하 여 FHIR 리소스 내보내기

내보내기에 대 한 Azure API를 구성 하 고 나면 이제 $export 명령을 사용 하 여 내보내기를 구성 하는 동안 지정한 저장소 계정으로 서비스에서 데이터를 내보낼 수 있습니다. FHIR 서버에서 $export 명령을 호출 하는 방법에 대 한 자세한 내용은의 $export 사양 설명서를 참조 하세요 [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

FHIR 용 Azure API의 $export 명령은 데이터를 내보낼 구성 된 저장소 계정 내에서 컨테이너를 지정 하는 데 사용할 수 있는 선택적 _ \_ conatiner_ 매개 변수를 사용 합니다.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> 현재 FHIR 용 Azure API는의 $export 사양에 정의 된 대로 시스템 수준 내보내기를 지원 [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) 합니다. 또한 쿼리 매개 변수가 현재 지원 _ \_ 되기 때문_ 에만 지원 됩니다.

## <a name="exporting-de-identified-data-preview"></a>식별 되지 않은 데이터 내보내기 (미리 보기)

$Export 명령을 사용 하 여 FHIR 서버에서 식별 되지 않은 데이터를 내보낼 수도 있습니다. [익명화에 대 한 Fhir 도구의](https://github.com/microsoft/FHIR-Tools-for-Anonymization)익명화 엔진을 사용 하 고 쿼리 매개 변수에서 익명화 구성 세부 정보를 가져옵니다. 사용자 고유의 익명화 config 파일을 만들거나 HIPAA Safe Harbor 메서드에 대 한 [샘플 구성 파일](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) 을 시작 점으로 사용할 수 있습니다. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|쿼리 매개 변수            | 예 |옵션| 설명|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.js|식별 되지 않은 내보내기에 필요 |구성 파일의 이름입니다. [여기](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)에서 구성 파일 형식을 참조 하세요. 이 파일은 내보내기 위치로 구성 된 동일한 Azure storage 계정 내의 **익명화** 이라는 컨테이너 내에 보관 되어야 합니다. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|식별 되지 않은 내보내기의 경우 선택 사항|이는 구성 파일의 Etag입니다. Blob 속성에서 Azure storage 탐색기를 사용 하 여 Etag를 가져올 수 있습니다.|

> [!IMPORTANT]
> 원시 내보내기와 식별 되지 않은 내보내기는 모두 내보내기 구성의 일부로 지정 된 동일한 Azure storage 계정에 기록 합니다. 다른 식별 되지 않은 구성에 해당 하는 다른 컨테이너를 사용 하 고 컨테이너 수준에서 사용자 액세스를 관리 하는 것이 좋습니다.
