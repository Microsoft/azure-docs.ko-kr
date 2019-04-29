---
title: 특정 제안 API 검색 | Microsoft Docs
description: API는 게시자 네임스페이스 내에서 지정된 제안을 검색합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9484cf0f549db94be8f1ac2363addca952a3cff3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61096066"
---
<a name="retrieve-a-specific-offer"></a>특정 제안 검색
=========================

게시자 네임스페이스 내에서 지정된 제안을 검색합니다.  

제안의 특정 버전을 검색하거나 초안, 보기 또는 프로덕션 슬롯에서 제안을 검색할 수도 있습니다. 슬롯을 지정하지 않으면 기본값은 `draft`입니다. 미리 보기 또는 게시하지 않은 제안을 검색하려고 하면 `404 Not Found` 오류가 발생합니다.

> [!WARNING]
> 비밀 형식 필드의 비밀 값은 이 API에서 검색되지 않습니다.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI 매개 변수
--------------


| **Name**    | **설명**                                                                          | **데이터 형식** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId입니다. 예: Contoso                                                        | String        |
| offerId     | 제안을 고유하게 식별하는 GUID입니다.                                                 | String        |
| 버전     | 검색 중인 제안의 버전입니다. 기본적으로 최신 제안 버전이 검색됩니다. | 정수        |
| slotId      | 제안이 검색될 슬롯으로, 다음 중 하나일 수 있습니다.      <br/>  - `Draft`(기본값)는 현재 초안에 있는 제안 버전을 검색합니다.  <br/>  -  `Preview`는 현재 미리 보기에 있는 제안 버전을 검색합니다.     <br/>  -  `Production`은 현재 프로덕션 환경에 있는 제안 버전을 검색합니다.          |      enum |
| api-version | 최신 버전 API                                                                    | Date          |
|  |  |  |


<a name="header"></a>헤더
------

|  **Name**          |   **값**            |
|  ---------------   |  --------------        |
|  콘텐츠 형식      | `application/json`     |
|  권한 부여     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>본문 예제
------------

### <a name="response"></a>response

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
            "microsoft-azure-marketplace.categories": [
                "devService",
                "networking",
                "database",
                "cache",
                "security"
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```


### <a name="response-body-properties"></a>응답 본문 속성

|  **Name**       |   **설명**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | 제안의 형식을 식별합니다.                                                                                                    |
|  publisherId    | 게시자의 고유 식별자입니다.                                                                                              |
|  status         | 제안의 상태입니다. 가능한 값 목록을 보려면 아래 [제안 상태](#offer-status)를 참조하세요.                                  |
|  Id             | 제안을 고유하게 식별하는 GUID입니다.                                                                                         |
|  버전        | 제안의 현재 버전입니다. 버전 속성은 클라이언트가 수정할 수 없습니다. 각 게시 후에 증분됩니다.    |
|  정의     | 워크로드의 실제 정의입니다.                                                                                               |
|  changedTime    | 제안을 마지막으로 수정한 UTC 날짜/시간입니다.                                                                                   |
|  |  |


### <a name="response-status-codes"></a>응답 상태 코드

| **코드**  | **설명**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - 요청이 성공적으로 처리되었으며 해당 게시자의 모든 제안이 클라이언트에 반환되었습니다.               |
|  400      | `Bad/Malformed request` - 오류 응답 본문에 자세한 정보가 들어 있을 수 있습니다.                                                 |
|  403      | `Forbidden` - 클라이언트는 지정된 네임스페이스에 액세스할 수 없습니다.                                                        |
|  404      | `Not found` - 지정된 엔터티가 존재하지 않습니다. 클라이언트는 publisherId, offerId 및 version(지정된 경우)을 확인해야 합니다.      |
|  |  |


### <a name="offer-status"></a>제안 상태

|  **Name**                   |   **설명**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | 제안이 게시된 적이 없습니다.               |
|  NotStarted                 | 제안이 새 제안이지만 시작되지 않았습니다.              |
|  WaitingForPublisherReview  | 제안이 게시자 승인을 기다리고 있습니다.      |
|  실행 중                    | 제안 제출을 처리 중입니다.          |
|  Succeeded                  | 제안 제출 처리를 완료했습니다.    |
|  Canceled                   | 제안 제출이 취소되었습니다.                |
|  실패                     | 제안 제출에 실패했습니다.                      |
|  |  |
