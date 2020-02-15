---
title: '자습서: Google Maps에서 Azure Maps로 마이그레이션 | Microsoft Azure Maps'
description: Google Maps에서 Microsoft Azure Maps로 마이그레이션하는 방법에 대한 자습서입니다. Azure Maps API 및 SDK로 전환하는 방법을 안내하는 지침입니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 1f6f282406c6813b2b126c300f21bda21e8f9464
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988977"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Google Maps에서 Azure Maps로 마이그레이션

이 자습서에서는 Google Maps의 웹, 모바일 및 서버 기반 애플리케이션을 Microsoft Azure Maps 플랫폼으로 마이그레이션하는 방법에 대한 인사이트를 제공합니다. 이 자습서에는 Azure Maps로 마이그레이션하기 위한 비교 코드 샘플, 마이그레이션 제안 사항 및 모범 사례가 포함되어 있습니다.

## <a name="azure-maps-platform-overview"></a>Azure Maps 플랫폼 개요

Azure Maps는 모든 산업의 개발자에게 지리적 컨텍스트를 웹 및 모바일 애플리케이션에 제공하도록 정기적으로 업데이트되는 최신 지도 데이터가 포함된 강력한 지리 공간적 기능을 제공합니다. Azure Maps에는 REST API의 Azure One API 호환 세트가 있습니다. 이러한 REST API는 맵, 검색, 라우팅, 트래픽, 표준 시간대, 지리적 위치, 지오펜싱, 지도 데이터, 날씨, 이동성 및 공간 작업을 제공합니다. 웹 및 Android SDK 모두가 함께 제공되는 작업은 여러 플랫폼에서 개발이 쉽고 유연하며 이식이 가능합니다.

## <a name="high-level-platform-comparison"></a>상위 수준 플랫폼 비교

다음 표에서는 Google Maps 기능에 해당하는 Azure Maps 기능의 상위 수준 목록을 제공합니다. 이 목록에는 Azure Maps 기능이 모두 표시되어 있지는 않습니다. 추가 Azure Maps에는 내게 필요한 옵션, 지오펜싱 API, 등시성, 공간 작업, 직접 맵 타일 액세스, 일괄 처리 서비스 및 데이터 검사 비교(예: 이미지 적용 범위)가 포함되어 있습니다.

| Google Maps 기능         | Azure Maps 지원                     |
|-----------------------------|:--------------------------------------:|
| 웹 SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | 계획                                |
| REST 서비스 API           | ✓                                      |
| 방향(라우팅)        | ✓                                      |
| 거리 행렬             | ✓                                      |
| 상승                   | 계획                                |
| 지오코딩(정방향/역방향) | ✓                                      |
| 지리적 위치                 | 해당 없음                                    |
| 장소 검색               | ✓                                      |
| 장소 세부 정보              | 해당 없음 – 사용 가능한 웹 사이트 및 전화 번호 |
| 장소 사진               | 해당 없음                                    |
| 장소 자동 완성          | ✓                                      |
| 정적 맵                 | ✓                                      |
| 정적 주소 보기          | 해당 없음                                    |
| 표준 시간대                   | ✓                                      |
| Maps Embedded API           | 해당 없음                                    |
| 맵 URL                    | 해당 없음                                    |

Google Maps는 기본 키 기반 인증을 제공합니다. Azure Maps는 기본 키 기반 인증과 매우 안전한 Azure Active Directory 인증을 모두 제공합니다.

## <a name="licensing-considerations"></a>라이선스 고려 사항

Google Maps에서 Azure Maps로 마이그레이션할 때 라이선스와 관련하여 다음 사항을 고려해야 합니다.

- Azure Maps는 로드된 맵 타일의 수에 따라 대화형 맵의 사용량에 대한 요금이 청구되는 반면, Google Maps는 맵 컨트롤의 로드에 대한 요금이 부과됩니다. 대화형 Azure Maps SDK에서 맵 타일은 개발자의 비용 부담을 줄이기 위해 자동으로 캐시됩니다. 로드되는 모든 15개의 맵 타일에 대해 하나의 Azure Maps 트랜잭션이 생성됩니다. 대화형 Azure Maps SDK는 512픽셀 타일을 사용하고 평균적으로는 페이지 보기당 하나 이상의 트랜잭션을 생성합니다.
- Google Maps 웹 서비스에서 Azure Maps 웹 SDK로 정적 맵 이미지를 대체하는 것이 훨씬 더 비용 효율적입니다. Azure Maps 웹 SDK는 맵 타일을 사용하고 사용자가 맵을 이동 및 확대/축소하는 경우가 아니면 맵 부하당 트랜잭션의 일부만 생성합니다. Azure Maps 웹 SDK에는 확대/축소를 사용하지 않도록 설정하는 옵션이 있습니다. 또한 Azure Maps 웹 SDK는 정적 맵 웹 서비스보다 훨씬 더 많은 시각화 옵션을 제공합니다.
- Azure Maps를 사용하면 해당 플랫폼의 데이터를 Azure에 저장할 수 있습니다. [사용 약관](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)에 따라 최대 6개월 동안 다른 곳에서 캐시될 수도 있습니다.

다음은 Azure Maps에 대한 몇 가지 관련 리소스입니다.

- [Azure Maps 가격 책정](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps 사용 약관](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)(Microsoft Online Services 사용 약관에 포함됨)
- [Azure Maps에서 올바른 가격 책정 계층 선택](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>권장 마이그레이션 계획

다음은 상위 수준 마이그레이션 계획입니다.

1. 애플리케이션에서 사용 중인 Google Maps SDK 및 서비스에 대한 목록을 작성하고, Azure Maps에서 마이그레이션할 다른 SDK 및 서비스를 제공하는지 확인합니다.
2. [https://azure.com](https://azure.com)에서 Azure 구독을 만듭니다(아직 Azure 구독이 없는 경우).
3. Azure Maps 계정([설명서](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys))과 인증 키 또는 Azure Active Directory([설명서](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication))를 만듭니다.
4. 애플리케이션 코드를 마이그레이션합니다.
5. 마이그레이션된 애플리케이션을 테스트합니다.
6. 마이그레이션된 애플리케이션을 프로덕션 환경에 배포합니다.

## <a name="azure-maps-technical-resources"></a>Azure Maps 기술 리소스

다음은 Azure Maps에 대한 유용한 기술 리소스 목록입니다.

- 개요: [https://azure.com/maps](https://azure.com/maps)
- 설명서: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- 웹 SDK 코드 샘플: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- 개발자 포럼: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- 동영상: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- 블로그: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps 피드백(UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>마이그레이션 지원

개발자는 [포럼](https://aka.ms/AzureMapsForums) 또는 여러 Azure 지원 옵션([https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)) 중 하나를 통해 마이그레이션 지원을 검색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 Google Maps 애플리케이션을 마이그레이션하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [웹앱 마이그레이션](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Android 앱 마이그레이션](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [웹 서비스 마이그레이션](migrate-from-google-maps-web-services.md)
