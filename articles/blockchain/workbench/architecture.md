---
title: Azure Blockchain Workbench 아키텍처
description: Azure Blockchain Workbench 아키텍처 및 해당 구성 요소의 개요를 설명합니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 83c5e1405c402a1c6c98f9dbcaaf74891eb75e6d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437774"
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain Workbench 아키텍처

Azure Blockchain Workbench는 여러 Azure 구성 요소를 사용하는 솔루션을 제공함으로써 블록체인 애플리케이션 개발을 간소화합니다. Azure Marketplace에서 솔루션 템플릿을 사용하여 Blockchain Workbench를 배포할 수 있습니다. 템플릿을 사용하면 블록체인 스택, 클라이언트 응용 프로그램 종류, IoT 통합을 포함하여 배포할 구성 요소 및 모듈을 선택할 수 있습니다. Blockchain Workbench가 배포되면 웹앱, iOS 앱 및 Android 앱에 액세스할 수 있습니다.

![Blockchain Workbench 아키텍처](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>ID 및 인증

Blockchain Workbench를 사용하면 컨소시엄이 Azure AD(Azure Active Directory)를 사용하여 해당 엔터프라이즈 ID를 페더레이션할 수 있습니다. Workbench는 Azure AD에 저장된 엔터프라이즈 ID를 사용하여 온체인 ID에 대한 새 사용자 계정을 생성합니다. ID 매핑은 클라이언트 API 및 애플리케이션에 인증된 로그인을 용이하게 하며 조직의 인증 정책을 사용합니다. Workbench는 지정된 스마트 계약 내에서 특정 역할에 엔터프라이즈 ID를 연관시키는 기능도 제공합니다. 또한 Workbench는 해당 역할이 수행할 수 있는 작업과 시간을 식별할 수 있는 메커니즘을 제공합니다.

Blockchain Workbench를 배포한 후 사용자는 클라이언트 애플리케이션, REST 기반 클라이언트 API 또는 메시징 API를 통해 Blockchain Workbench와 상호 작용합니다. 모든 경우에 있어 상호 작용은 Azure AD(Azure Active Directory) 또는 디바이스별 자격 증명을 통해 인증되어야 합니다.

사용자는 참석자의 메일 주소로 메일 초대장을 보냄으로써 컨소시엄 Azure AD로 해당 ID를 페더레이션합니다. 이들 사용자는 로그인 시 이름, 암호 및 정책을 통해 인증됩니다. 해당 조직의 2단계 인증이 그 예입니다.

Azure AD는 Blockchain Workbench에 액세스할 수 있는 모든 사용자를 관리하는 데 사용됩니다. 스마트 계약에 연결된 각 디바이스는 Azure AD에도 연결되어 있습니다.

사용자를 특수 관리자 그룹에 할당할 때도 Azure AD가 사용됩니다. 관리자 그룹과 연결된 사용자는 계약의 배포 및 계약에 액세스하기 위해 사용자에게 권한을 부여하는 등의 권한 및 작업에 대한 액세스를 Blockchain Workbench 내에서 부여받습니다. 이 그룹에 속하지 않은 사용자는 관리자 작업에 액세스할 수 없습니다.

## <a name="client-applications"></a>클라이언트 애플리케이션

Workbench는 자동으로 생성된 웹 및 모바일(iOS, Android)용 클라이언트 애플리케이션을 제공하며 이를 사용하여 블록체인 애플리케이션의 유효성을 검사, 테스트 및 확인할 수 있습니다. 애플리케이션 인터페이스는 스마트 계약 메타데이터를 기반으로 동적으로 생성되며 모든 사용 사례를 수용할 수 있습니다. 클라이언트 애플리케이션은 사용자 관련 프런트 엔드를 Blockchain Workbench에서 생성한 전체 블록체인 애플리케이션에 제공합니다. 클라이언트 애플리케이션은 Azure AD(Azure Active Directory)를 통해 사용자를 인증한 다음, 스마트 계약의 비즈니스 컨텍스트에 맞는 사용자 환경을 제공합니다. 사용자 환경에서는 권한 있는 개인이 새 스마트 계약 인스턴스를 생성할 수 있으며 스마트 계약이 나타내는 비즈니스 프로세스의 적절한 시점에서 특정 유형의 트랜잭션을 실행할 수 있는 기능을 제공합니다.

웹 애플리케이션에서 권한 있는 사용자는 관리자 콘솔에 액세스할 수 있습니다. 이 콘솔은 Azure AD의 관리자 그룹에 있는 사용자가 사용할 수 있으며 다음 기능에 대한 액세스 권한을 제공합니다.

* 인기 시나리오의 경우 Microsoft에서 제공하는 스마트 계약을 배포합니다. 예를 들면 자산 전송 시나리오가 있습니다.
* 자체 스마트 계약을 업로드하고 배포합니다.
* 특정 역할의 컨텍스트에서 스마트 계약에 대한 사용자 액세스 권한을 할당합니다.

배포에 대한 자세한 내용은 [GitHub의 Azure Blockchain Workbench 샘플 클라이언트 애플리케이션](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-development-kit/connect/mobile/blockchain-workbench/workbench-client)을 참조하세요.

## <a name="gateway-service-api"></a>게이트웨이 서비스 API

Blockchain Workbench에는 REST 기반 게이트웨이 서비스 API가 포함되어 있습니다. 블록체인에 쓸 때 API는 메시지를 생성하여 이벤트 브로커에게 전달합니다. API에서 데이터를 요청하면 쿼리가 오프 체인 SQL 데이터베이스로 전송됩니다. SQL 데이터베이스에는 지원되는 스마트 계약에 대한 컨텍스트 및 구성 정보를 제공하는 온 체인 데이터 및 메타데이터의 복제본이 포함되어 있습니다. 쿼리는 계약의 메타데이터에서 알리는 형식으로 오프 체인 복제본에서 필요한 데이터를 반환합니다.

개발자는 Blockchain Workbench 클라이언트 앱에 의존하지 않고도 블록체인 솔루션을 빌드 또는 통합하기 위해 게이트웨이 서비스 API에 액세스할 수 있습니다.

> [!NOTE]
> API에 대한 인증된 액세스를 사용하도록 설정하기 위해 두 개의 클라이언트 애플리케이션이 Azure Active Directory에 등록됩니다. Azure Active Directory에는 각 애플리케이션 유형(기본 및 웹)마다 고유한 애플리케이션 등록이 필요합니다. 

## <a name="message-broker-for-incoming-messages"></a>수신 메시지를 위한 메시지 브로커

Blockchain Workbench로 직접 메시지를 보내려는 개발자는 직접 메시지를 Service Bus에 보낼 수 있습니다. 예를 들어 메시지 API를 시스템 간 통합 또는 IoT 디바이스에 사용할 수 있습니다.

## <a name="message-broker-for-downstream-consumers"></a>다운스트림 소비자를 위한 메시지 브로커

애플리케이션의 수명 주기 동안 이벤트가 발생합니다. 게이트웨이 API 또는 원장에서 이벤트를 트리거할 수 있습니다. 이벤트 알림은 이벤트를 기반으로 다운스트림 코드를 시작할 수 있습니다.

Blockchain Workbench는 두 가지 유형의 이벤트 소비자를 자동으로 배포합니다. 한 소비자는 블록체인 이벤트에 의해 트리거되어 오프 체인 SQL 저장소를 채웁니다. 다른 소비자는 문서의 업로드 및 저장과 관련하여 API에서 생성된 이벤트에 대한 메타데이터를 캡처하는 것입니다.

## <a name="message-consumers"></a>메시지 소비자

 메시지 소비자는 Service Bus에서 메시지를 가져옵니다. 메시지 소비자를 위한 기본 이벤트 모델은 추가 서비스 및 시스템의 확장을 허용합니다. 예를 들어 CosmosDB를 채우기 위한 지원을 추가하거나 Azure Streaming Analytics를 사용하여 메시지를 평가할 수 있습니다. 다음 섹션에서는 Blockchain Workbench에 포함된 메시지 소비자에 대해 설명합니다.

### <a name="distributed-ledger-consumer"></a>분산 원장 소비자

DLT(분산 원장 기술) 메시지에는 블록체인에 쓰여지는 트랜잭션의 메타데이터가 포함됩니다. 소비자는 메시지를 검색하고 데이터를 트랜잭션 작성기, 서명자 및 라우터로 푸시합니다.

### <a name="database-consumer"></a>데이터베이스 소비자

데이터베이스 소비자는 Service Bus에서 메시지를 가져와 데이터를 연결된 데이터베이스(예: SQL 데이터베이스)로 푸시합니다.

### <a name="storage-consumer"></a>저장소 소비자

저장소 소비자는 Service Bus에서 메시지를 가져와 데이터를 연결된 저장소로 푸시합니다. 예를 들어 해시된 문서를 Azure Storage에 저장합니다.

## <a name="transaction-builder-and-signer"></a>트랜잭션 작성기 및 서명자

인바운드 메시지 브로커의 메시지를 블록체인에 기록해야 하는 경우 DLT 소비자가 처리합니다. DLT 소비자는 실행하려고 하는 트랜잭션에 대한 메타데이터가 포함된 메시지를 검색한 다음, 해당 정보를 *트랜잭션 작성기 및 서명자*에게 전송하는 서비스입니다. *트랜잭션 작성기 및 서명자*는 데이터 및 원하는 블록체인 대상을 기반으로 블록체인 트랜잭션을 어셈블합니다. 어셈블되면 트랜잭션이 서명됩니다. 개인 키는 Azure Key Vault에 저장됩니다.

 Blockchain Workbench는 Key Vault에서 적절한 개인 키를 검색하고 Key Vault 외부에서 트랜잭션에 서명합니다. 서명되면 트랜잭션 라우터와 원장으로 트랜잭션이 전송됩니다.

## <a name="transaction-routers-and-ledgers"></a>트랜잭션 라우터 및 원장

트랜잭션 라우터와 원장은 서명된 트랜잭션을 가져와 적절한 블록체인에 라우팅합니다. 현재 Blockchain Workbench는 Ethereum을 대상 블록체인으로 지원합니다.

## <a name="dlt-watcher"></a>DLT 감시자

DLT(분산 원장 기술) 감시자는 Blockchain Workbench에 연결된 블록체인에서 발생하는 이벤트를 모니터링합니다.
이벤트는 개인 및 시스템과 관련된 정보를 반영합니다. 예를 들어 새 계약 인스턴스 작성, 트랜잭션 실행 및 상태 변경 등이 해당됩니다. 이벤트는 캡처되어 아웃바운드 메시지 브로커로 전송되므로 다운스트림 소비자가 해당 이벤트를 사용할 수 있습니다.

예를 들어 SQL 소비자는 이벤트를 모니터링하고 이벤트를 사용하며 포함된 값으로 SQL 데이터베이스를 채웁니다. 복사본을 사용하면 오프 체인 저장소에서 온 체인 데이터의 복제본을 다시 만들 수 있습니다.

## <a name="azure-sql-database"></a>Azure SQL 데이터베이스

Blockchain Workbench에 연결된 Azure SQL 데이터베이스는 계약 정의, 구성 메타데이터 및 블록체인에 저장된 SQL 액세스 가능 데이터 복제본을 저장합니다. 데이터베이스에 직접 액세스하면 이 데이터를 쉽게 쿼리, 시각화 또는 분석할 수 있습니다. 개발자 및 다른 사용자는 보고, 분석 또는 기타 데이터 중심 통합을 위해 데이터베이스를 사용할 수 있습니다. 예를 들어 사용자는 Power BI를 사용하여 트랜잭션 데이터를 시각화할 수 있습니다.

이 오프 체인 저장소는 엔터프라이즈 조직이 블록체인 원장이 아닌 SQL로 데이터를 쿼리할 수 있는 기능을 제공합니다. 또한 오프 체인 저장소는 블록체인 기술 스택에 대해 독립적인 표준 스키마를 표준화함으로써 프로젝트, 시나리오 및 조직 전체에서 보고서 및 기타 아티팩트를 재사용할 수 있게 해줍니다.

## <a name="azure-storage"></a>Azure Storage

Azure Storage는 계약 및 계약과 관련된 메타데이터를 저장하는 데 사용됩니다.

구매 주문서 및 선하 증권에서부터 뉴스 및 의료 영상에 사용된 이미지, 경찰 카메라 및 영화 등 연속 촬영 장비에서 찍은 비디오에 이르기까지 많은 블록체인 중심 시나리오에서 문서가 중요한 역할을 합니다. 문서는 블록체인에 직접 배치하기에는 적합하지 않습니다.

Blockchain Workbench는 블록체인 비즈니스 논리를 사용하여 문서 또는 기타 미디어 콘텐츠를 추가하는 기능을 지원합니다. 문서 또는 미디어 콘텐츠의 해시가 블록체인에 저장되고 실제 문서 또는 미디어 콘텐츠는 Azure Storage에 저장됩니다. 관련 트랜잭션 정보는 인바운드 메시지 브로커에 전달되어 패키지화되고, 서명되며 블록체인으로 라우팅됩니다. 이 프로세스는 아웃바운드 메시지 브로커를 통해 공유되는 이벤트를 트리거합니다. SQL DB는 이 정보를 사용하여 나중에 쿼리할 수 ​​있도록 DB로 보냅니다. 다운스트림 시스템 역시 적절한 작동을 위해 이러한 이벤트를 사용할 수 있습니다.

## <a name="monitoring"></a>모니터링

Workbench는 Application Insights 및 Azure Monitor를 사용하여 애플리케이션 로깅을 제공합니다. Application Insights는 Blockchain Workbench의 기록된 모든 정보를 저장하는 데 사용되고 오류, 경고 및 성공한 작업을 포함합니다. Application Insights는 개발자가 Blockchain Workbench를 사용하여 문제를 디버깅하는 데 사용할 수 있습니다. 

Azure Monitor는 블록체인 네트워크의 상태에 대한 정보를 제공합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 배포](../../blockchain-workbench/blockchain-workbench-deploy.md)