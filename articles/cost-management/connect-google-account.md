---
title: Azure에서 Cloudyn에 Google Cloud Platform 계정 연결 | Microsoft Docs
description: Google Cloud Platform 계정을 연결하여 Cloudyn 보고서에서 비용 및 사용량 데이터를 볼 수 있습니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 7f63293900e116fd3175b0ea6d704993a2dcf591
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576292"
---
# <a name="connect-a-google-cloud-platform-account"></a>Google Cloud Platform 계정 연결

기존 Google Cloud Platform 계정을 Cloudyn에 연결할 수 있습니다. 사용자 계정을 Cloudyn에 연결하면 Cloudyn 보고서에서 비용 및 사용량 데이터를 볼 수 있습니다. 이 문서에서는 Cloudyn을 사용하여 Google 계정을 구성 및 연결하는 방법을 설명합니다.


## <a name="collect-project-information"></a>프로젝트 정보 수집

먼저 프로젝트에 대한 정보를 수집합니다.

1. [https://console.cloud.google.com](https://console.cloud.google.com)에서 Google Cloud Platform 콘솔에 로그인합니다.
2. Cloudyn에 등록할 프로젝트 정보를 검토하고 **프로젝트 이름**과 **프로젝트 ID**를 적어 둡니다. 이후 단계를 위해 이 정보를 보관합니다.  
    ![Google Cloud Platform 콘솔에 표시된 프로젝트 이름 및 프로젝트 ID](./media/connect-google-account/gcp-console01.png)
3. 청구가 사용하도록 설정 및 프로젝트에 연결되지 않은 경우 청구 계정을 만듭니다. 자세한 내용은 [새 청구 계정 만들기](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account)를 참조하세요.

## <a name="enable-storage-bucket-billing-export"></a>저장소 버킷 청구 내보내기 사용

Cloudyn은 저장소 버킷에서 Google 청구 데이터를 검색합니다. 나중에 Cloudyn 등록 중에 사용할 수 있도록 **버킷 이름** 및 **보고서 접두사** 정보를 보관합니다.

Google Cloud Storage를 사용하여 사용 보고서를 저장하는 경우 최소 요금이 발생합니다. 자세한 내용은 [클라우드 저장소 가격 책정](https://cloud.google.com/storage/pricing)을 참조하세요.

1. 파일로 청구 내보내기를 사용하도록 설정하지 않은 경우 [파일로 청구 내보내기를 사용하도록 설정하는 방법](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file)의 지침을 따르세요. JSON 또는 CSV 청구 내보내기 형식을 사용할 수 있습니다.
2. 사용하도록 설정한 경우에는 Google Cloud Platform 콘솔에서 **청구** > **청구 내보내기**로 이동합니다. 청구 **버킷 이름** 및 **보고서 접두사**를 적어둡니다.  
    ![청구 내보내기 페이지에 표시되는 청구 내보내기 정보](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Google Cloud Platform API 사용

사용량 및 자산 정보를 수집하려면 Cloudyn에서 다음과 같은 Google Cloud Platform API를 사용할 수 있어야 합니다.

- BigQuery API
- Google Cloud SQL
- Google Cloud Datastore API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine API

### <a name="enable-or-verify-apis"></a>API 사용 또는 확인

1. Google Cloud Platform 콘솔에서 Cloudyn에 등록할 프로젝트를 선택합니다.
2. **API 및 서비스** > **라이브러리**로 이동합니다.
3. 검색을 사용하여 이전에 나열된 각 API를 찾습니다.
4. 각 API에 대해 **API 사용**이 표시되는지 확인합니다. 표시되지 않는 경우 **사용**을 클릭합니다.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Cloudyn에 Google Cloud 계정 추가

1. Azure Portal에서 Cloudyn 포털을 열거나 [https://azure.cloudyn.com](https://azure.cloudyn.com/)으로 이동하여 로그인합니다.
2. **설정**(톱니바퀴 기호)을 클릭하고 **클라우드 계정**을 선택합니다.
3. **계정 관리**에서 **Google 계정** 탭을 선택하고 **새로 추가 +** 를 클릭합니다.
4. **Google 계정 이름**에 청구 계정의 메일 주소를 입력하고 **다음**을 클릭합니다.
5. Google 인증 대화 상자에서 Google 계정을 선택하거나 입력하고 cloudyn.com에 계정에 대한 액세스를 **허용**합니다.
6. 이전에 적어둔 요청 프로젝트 정보를 추가합니다. 여기에는 **프로젝트 ID**, **프로젝트** 이름, **청구** 버킷 이름 및 **청구 파일** 보고서 접두사가 포함됩니다. 그런 다음 **저장**을 클릭합니다.  
    ![Cloudyn 계정으로 Google 프로젝트 추가](./media/connect-google-account/add-project.png)

Google 계정이 계정 목록에 나타나며 **인증됨**으로 표시되어야 합니다. 그 아래에 Google 프로젝트 이름과 ID가 표시되고 녹색 확인 표시 기호가 있어야 합니다. 계정 상태는 **완료됨**이어야 합니다.

몇 시간 내에 Cloudyn 보고서에 Google 비용 및 사용량 정보가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- Cloudyn에 대해 자세히 알아보려면 Cloudyn용 [사용량 및 비용 검토](./tutorial-review-usage.md) 자습서를 계속 확인하세요.
