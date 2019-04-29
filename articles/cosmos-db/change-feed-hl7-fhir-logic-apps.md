---
title: HL7 FHIR 리소스에 대한 변경 사항 피드 - Azure Cosmos DB
description: Azure Logic Apps, Azure Cosmos DB 및 Service Bus를 사용하여 HL7 FHIR 환자 의료 기록에 대한 변경 알림 설정 방법을 알아보세요.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: sngun
ms.openlocfilehash: 765596500e3ac294dc79f0785b12b03370fa652a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893912"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Logic Apps 및 Azure Cosmos DB를 사용하여 환자에게 HL7 FHIR 의료 기록 변경 통지

Azure MVP Howard Edidin은 최근에 환자 포털에 새 기능을 추가하길 원하는 한 의료 기관으로부터 문의를 받았습니다. 의료 기록이 업데이트되면 환자에게 알림을 보내고 환자가 이러한 업데이트를 구독할 수 있는 기능이 필요했습니다. 

이 문서는 Azure Cosmos DB, Logic Apps 및 Service Bus를 사용하여 이 의료 기관용으로 만든 변경 사항 피드 알림 솔루션을 안내합니다. 

## <a name="project-requirements"></a>프로젝트 요구 사항
- 공급자는 XML 형식으로 HL7 통합-임상 문서 아키텍처(C-CDA) 문서를 보냅니다. C-CDA 문서는 가족 이력, 예방접종 기록 등의 임상 문서는 물론 관리, 워크플로 및 재무 서류를 포함한 모든 유형의 임상 문서를 포괄하고 있습니다. 
- C-CDA 문서는 JSON 형식으로 [HL7 FHIR 리소스](https://hl7.org/fhir/2017Jan/resourcelist.html)로 변환됩니다.
- 수정된 FHIR 리소스 문서는 JSON 형식으로 전자 메일을 통해 전송됩니다.

## <a name="solution-workflow"></a>솔루션 워크플로 

높은 수준에서 프로젝트에 다음 워크플로 단계가 필요했습니다. 
1. C-CDA 문서를 FHIR 리소스로 변환합니다.
2. 수정된 FHIR 리소스에 대한 반복 트리거 폴링을 수행합니다. 
2. 사용자 지정 앱인 FhirNotificationApi를 호출하여 Azure Cosmos DB에 연결하고 새롭거나 수정된 문서를 쿼리합니다.
3. 해당 응답을 Service Bus 큐에 저장합니다.
4. Service Bus 큐에서 새 메시지를 폴링합니다.
5. 환자에게 전자 메일 알림을 보냅니다.

## <a name="solution-architecture"></a>솔루션 아키텍처
이 솔루션에는 위의 요구 사항을 충족하고 솔루션 워크플로를 완성하기 위해 세 개의 Logic Apps가 필요합니다. 세 가지 논리 앱은 다음과 같습니다.
1. **HL7-FHIR-매핑 앱**: HL7 C-CDA 문서를 수신하여 FHIR 리소스로 변환한 다음, Azure Cosmos DB에 저장합니다.
2. **EHR 앱**: Azure Cosmos DB FHIR 리포지토리를 쿼리하고 해당 응답을 Service Bus 큐에 저장합니다. 이 논리 앱에서는 [API 앱](#api-app)을 사용하여 새롭고 변경된 문서를 검색합니다.
3. **프로세스 알림 앱**: 본문에 FHIR 리소스 문서가 포함된 이메일 알림을 보냅니다.

![이 HL7 FHIR 의료 솔루션에 사용된 세 가지 Logic Apps](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>솔루션에 사용된 Azure 서비스

#### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
Azure Cosmos DB는 다음 그림과 같이 FHIR 리소스에 대한 리포지토리입니다.

![이 HL7 FHIR 의료 자습서에서 사용된 Azure Cosmos DB 계정](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Logic Apps는 워크플로 프로세스를 처리합니다. 다음 스크린샷은 이 솔루션용으로 만든 Logic Apps를 보여 줍니다. 


1. **HL7-FHIR-매핑 앱**: Logic Apps용 엔터프라이즈 통합 팩을 사용하여 HL7 C-CDA 문서를 수신하고 FHIR 리소스로 변환합니다. 엔터프라이즈 통합 팩은 C-CDA에서 FHIR 리소스로의 매핑을 처리합니다.

    ![HL7 FHIR 의료 레코드를 수신하는 데 사용한 논리 앱](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **EHR 앱**: Azure Cosmos DB FHIR 리포지토리를 쿼리하고 해당 응답을 Service Bus 큐에 저장합니다. GetNewOrModifiedFHIRDocuments 앱에 대한 코드는 아래와 같습니다.

    ![Azure Cosmos DB를 쿼리하는 데 사용한 논리 앱](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **프로세스 알림 앱**: 본문에 FHIR 리소스 문서가 포함된 이메일 알림을 보냅니다.

    ![본문에 HL7 FHIR 리소스가 포함된 환자 전자 메일을 보내는 논리 앱](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
다음 그림에서는 환자 큐를 보여 줍니다. 태그 속성 값은 전자 메일 제목에 사용됩니다.

![이 HL7 FHIR 자습서에서 사용한 Service Bus 큐](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>API 앱
API 앱은 Azure Cosmos DB에 연결해서 리소스 유형별로 새롭거나 수정된 FHIR 문서를 쿼리합니다. 이 앱에는 하나의 작업 **GetNewOrModifiedFhirDocuments**가 있는 하나의 컨트롤러 **FhirNotificationApi**가 있습니다. [API 앱의 소스](#api-app-source)를 참조하세요.

Azure Cosmos DB SQL .NET API에서 [`CreateDocumentChangeFeedQuery`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) 클래스를 사용하고 있습니다. 자세한 내용은 [변경 사항 피드 문서](change-feed.md)를 참조하세요. 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>GetNewOrModifiedFhirDocuments 작업

**입력**
- DatabaseId
- CollectionId
- HL7 FHIR 리소스 종류 이름
- 부울: 처음부터 시작
- Int: 반환된 문서 수

**Outputs**
- 성공: 상태 코드: 200, 응답: 문서 목록(JSON 배열)
- 실패: 상태 코드: 404, 응답: "'*resource name'* 리소스 유형에 대한 문서를 찾을 수 없음"

<a id="api-app-source"></a>

**API 앱에 대한 원본**

```csharp

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modified FHIR Documents By Resource Type " +
                "from Last Run Date or Beginning of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modified Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>FhirNotificationApi 테스트 

다음 이미지는 [FhirNotificationApi](#api-app-source)를 테스트하기 위해 Swagger가 사용되는 방식을 보여 줍니다.

![API 앱을 테스트하는 데 사용한 Swagger 파일](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Azure 포털 대시보드

다음 이미지는 Azure Portal에서 실행되는 이 솔루션에 대한 모든 Azure 서비스를 보여 줍니다.

![이 HL7 FHIR 자습서에서 사용한 모든 서비스를 보여 주는 Azure Portal](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>요약

- Azure Cosmos DB에서 새롭거나 수정된 문서에 대한 알림이 기본 지원되고 이 기능이 얼마나 사용하기 쉬운지 알게 되었습니다. 
- Logic Apps를 활용하여 코드를 쓰지 않아도 워크플로를 만들 수 있습니다.
- Azure Service Bus 큐를 사용하여 HL7 FHIR 문서 배포 처리

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 홈페이지](https://azure.microsoft.com/services/cosmos-db/)를 참조하세요. Logic Apps에 대한 자세한 내용은 [Logic Apps](https://azure.microsoft.com/services/logic-apps/)를 참조하세요.


