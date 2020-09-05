---
title: Microsoft Azure의 데이터 암호화 모델
description: 이 문서에서는 Microsoft Azure의 데이터 암호화 모델에 대 한 개요를 제공 합니다.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 51d7cb32ef2d1d7750a0bb53cfa3ccc111bd1302
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89143736"
---
# <a name="data-encryption-models"></a>데이터 암호화 모델

Azure의 다양한 리소스 공급자가 미사용 데이터 암호화를 구현하는 방법을 이해하려면 먼저 다양한 암호화 모델 및 장단점을 이해해야 합니다. 이러한 정의는 Azure의 모든 리소스 공급자에서 공유되어 공용 언어 및 분류를 보장합니다.

서버 쪽 암호화에는 다음 세 가지 시나리오가 있습니다.

- 서비스 관리 키를 사용하여 서버 쪽 암호화
  - Azure 리소스 공급자에서 암호화 및 암호 해독 작업 수행
  - Microsoft에서 키 관리
  - 전체 클라우드 기능

- Azure Key Vault에서 고객 관리 키를 사용하여 서버 쪽 암호화
  - Azure 리소스 공급자에서 암호화 및 암호 해독 작업 수행
  - 고객이 Azure Key Vault를 통해 키 제어
  - 전체 클라우드 기능

- 고객 제어 하드웨어에서 고객 관리 키를 사용하여 서버 쪽 암호화
  - Azure 리소스 공급자에서 암호화 및 암호 해독 작업 수행
  - 고객이 고객 제어 하드웨어에서 키 제어
  - 전체 클라우드 기능

서버 쪽 암호화 모델은 Azure 서비스에서 수행되는 암호화를 참조합니다. 이 모델에서는 리소스 공급자에서 암호화 및 암호 해독 작업을 수행합니다. 예를 들어 Azure Storage는 일반 텍스트 작업으로 데이터를 받을 수 있으며, 암호화 및 암호 해독을 내부적으로 수행합니다. 리소스 공급자는 제공된 구성에 따라 Microsoft 또는 고객이 관리하는 암호화 키를 사용할 수도 있습니다.

![서버](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

서버 쪽 미사용 데이터 암호화 모델마다 키 관리의 고유한 특성을 나타냅니다. 여기에는 암호화 키를 만들고 저장하는 방법, 액세스 모델 및 키 회전 절차가 포함됩니다.  

클라이언트 쪽 암호화의 경우 다음을 고려합니다.

- Azure 서비스에서 암호 해독된 데이터를 볼 수 없음
- 고객이 온-프레미스(또는 다른 보안 저장소)에서 키 관리 및 저장 - 키를 Azure 서비스에서 사용할 수 없습니다.
- 제한된 클라우드 기능

Azure에서 지원 되는 암호화 모델은 앞에서 설명한 대로 "클라이언트 암호화" 및 "서버 쪽 암호화" 라는 두 개의 기본 그룹으로 분할 됩니다. 사용되는 저장 데이터 암호화 모델과는 관계없이 Azure 서비스에서는 항상 TLS 또는 HTTPS와 같은 보안 전송 프로토콜을 사용하는 것이 좋습니다. 따라서 암호화 전송은 전송 프로토콜을 통해 처리되어야 하며, 사용할 미사용 데이터 암호화 모델을 결정하는 주요 요소가 되어서는 안됩니다.

## <a name="client-encryption-model"></a>클라이언트 쪽 암호화 모델

클라이언트 쪽 암호화 모델은 서비스 또는 호출 애플리케이션이 리소스 공급자 또는 Azure 외부에서 수행되는 암호화를 나타냅니다. 이 암호화는 Azure의 서비스 애플리케이션이나 고객 데이터 센터에서 실행되는 애플리케이션에서 수행할 수 있습니다. 두 경우 모두 Azure 리소스 공급자에서 이 암호화 모델을 활용할 때 어떤 방식으로든 데이터의 암호를 해독할 수 없거나 암호화 키에 대한 액세스 권한이 없는 데이터의 암호화된 Blob을 받습니다. 이 모델에서는 키 관리가 호출 서비스/애플리케이션에서 수행되며 Azure 서비스에 대해 불투명합니다.

![클라이언트](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>서비스 관리 키를 사용하여 서버 쪽 암호화

많은 고객에 대한 필수적 요구 사항은 데이터가 사용되고 있지 않을 때마다 암호화되도록 하는 것입니다. 서비스 관리 키를 사용하는 서버 쪽 암호화는 고객이 암호화를 위한 특정 리소스(Storage 계정, SQL DB 등)를 표시하고, 모든 키 관리 측면(예: 키 발급, 교체 및 백업)을 Microsoft에 맡길 수 있게 함으로써 이 모델을 가능하게 합니다. 미사용 암호화를 지 원하는 대부분의 Azure 서비스는 일반적으로 암호화 키 관리를 Azure로 오프 로드 하는이 모델을 지원 합니다. Azure 리소스 공급자는 키를 만들고, 안전한 스토리지에 저장하고, 필요할 때 검색합니다. 이는 서비스에서 키에 대한 모든 권한을 가지며 자격 증명 수명 주기 관리를 완벽하게 제어할 수 있음을 의미합니다.

![관리](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

따라서 서비스 관리 키를 사용하는 서버 쪽 암호화는 고객에게 낮은 오버헤드로 저장 데이터 암호화를 유지해야 하는 요구 사항을 신속하게 해결합니다. 가능한 경우 고객은 일반적으로 대상 구독 및 리소스 공급자에 대한 Azure Portal을 열고 데이터 암호화를 원하는지를 나타내는 상자를 선택합니다. 일부 리소스 관리자에서는 기본적으로 서비스 관리 키를 사용하는 서버 쪽 암호화가 설정되어 있습니다.

Microsoft 관리 키를 사용하는 서버 쪽 암호화는 이 서비스에 키를 저장하고 관리하기 위한 모든 권한이 있음을 의미합니다. 일부 고객은 보안을 더 강화할 수 있다고 생각하기 때문에 키를 관리하려고 할 수 있지만, 이 모델을 평가할 때는 사용자 지정 키 스토리지 솔루션과 관련된 비용과 위험을 고려해야 합니다. 대부분의 경우 조직에서는 온-프레미스 솔루션의 리소스 제약 또는 위험이 저장 데이터 암호화 키의 클라우드 관리 위험보다 클 수 있다고 결정할 수 있습니다.  그러나이 모델은 암호화 키의 생성 또는 수명 주기를 제어 하는 요구 사항이 있는 조직이 나 서비스를 관리 하는 서비스 (서비스에 대 한 전체 관리 모델에서 키 관리 분리)와는 다른 직원이 서비스의 암호화 키를 관리 하는 데 충분 하지 않을 수 있습니다.

### <a name="key-access"></a>키 액세스

서비스 관리 키를 사용하는 서버 쪽 암호화를 사용하면 키 생성, 스토리지 및 서비스 액세스가 모두 서비스에서 관리됩니다. 일반적으로 기본 Azure 리소스 공급자는 키 암호화 키를 안전한 내부 저장소에 저장하는 한편, 데이터에 가까운 저장소에 데이터 암호화 키를 저장하여 빠르게 사용하고 액세스할 수 있습니다.

**장점**

- 간단한 설정
- Microsoft에서 키 회전, 백업 및 중복성을 관리합니다.
- 고객은 구현 또는 사용자 지정 키 관리 체계의 위험과 관련된 비용을 부담하지 않습니다.

**단점**

- 고객이 암호화 키(키 사양, 수명 주기, 해지 등)를 제어할 수 없습니다.
- 서비스에 대한 전체 관리 모델에서 키 관리를 분리할 수 없습니다.

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객 관리 키를 사용하여 서버 쪽 암호화

미사용 데이터를 암호화하고 암호화 키를 제어해야 하는 요구 사항이 있는 시나리오의 경우 고객은 Key Vault에서 고객 관리 키를 사용하여 서버 쪽 암호화를 사용할 수 있습니다. 일부 서비스는 Azure Key Vault에 루트 키 암호화 키만 저장하고, 데이터에 가까운 내부 위치에는 암호화된 데이터 암호화 키를 저장할 수 있습니다. 이 시나리오에서는 고객이 자신의 키를 Key Vault(BYOK(Bring Your Own Key))로 가져오거나 새 키를 생성하여 원하는 리소스를 암호화하는 데 사용할 수 있습니다. 리소스 공급자는 암호화 및 암호 해독 작업을 수행 하지만 구성 된 키 암호화 키를 모든 암호화 작업의 루트 키로 사용 합니다.

키 암호화 키가 손실 되 면 데이터가 손실 됩니다. 이러한 이유로 키를 삭제 해서는 안 됩니다. 키를 만들거나 회전할 때마다 백업 해야 합니다. 키 암호화 키를 저장 하는 자격 증명 모음에 대해 [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 를 사용 하도록 설정 해야 합니다. 키를 삭제 하는 대신 enabled를 false로 설정 하거나 만료 날짜를 설정 합니다.

### <a name="key-access"></a>키 액세스

Azure Key Vault에서 고객 관리 키를 사용하는 서버 쪽 암호화 모델에는 필요에 따라 키를 액세스하여 암호화하고 암호 해독하는 서비스가 포함되어 있습니다. 미사용 데이터 암호화는 액세스 제어 정책을 통해 해당 서비스에 제공됩니다. 이 정책은 키를 받을 수 있는 서비스 ID 액세스 권한을 부여합니다. 연결된 구독을 대신하여 실행되는 Azure 서비스는 해당 구독의 ID를 사용하여 구성할 수 있습니다. 이 서비스는 Azure Active Directory 인증을 수행하고, 구독을 대신하여 작동하는 해당 서비스로 식별할 수 있는 인증 토큰을 받습니다. 그러면 해당 토큰을 Key Vault에 제공하여 액세스 권한이 부여된 키를 얻을 수 있습니다.

암호화 키를 사용하는 작업의 경우 암호 해독, 암호화, unwrapKey, wrapKey, 확인, 서명, 가져오기(get), 목록, 업데이트, 만들기, 가져오기(import), 삭제, 백업 및 복원과 같은 작업에 대한 액세스 권한을 서비스 ID에 부여할 수 있습니다.

미사용 데이터를 암호화하거나 암호 해독하는 데 사용할 키를 얻으려면, 리소스 관리자 서비스 인스턴스가 실행될 서비스 ID에 UnwrapKey(암호 해독 키를 가져옴) 및 WrapKey(새 키를 만들 때 키 자격 증명 모음에 키를 삽입함)가 있어야 합니다.

>[!NOTE]
>Key Vault 권한 부여에 대한 자세한 내용은 [Azure Key Vault 설명서](../../key-vault/general/secure-your-key-vault.md)의 키 자격 증명 모음 보호 페이지를 참조하세요.

**장점**

- 사용 되는 키에 대 한 모든 권한-암호화 키는 고객의 제어에 따라 고객의 Key Vault에서 관리 됩니다.
- 하나의 마스터에 여러 서비스를 암호화하는 기능
- 서비스에 대한 전체 관리 모델에서 키 관리를 분리할 수 있습니다.
- 지역 간 서비스 및 키 위치를 정의할 수 있습니다.

**단점**

- 키 액세스 관리에 대한 책임이 전적으로 고객에게 있습니다.
- 키 수명 주기 관리에 대한 책임이 전적으로 고객에게 있습니다.
- 추가 설치 및 구성 오버헤드

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>고객 제어 하드웨어에서 고객 관리 키를 사용 하는 서버 쪽 암호화

일부 Azure 서비스는 HYOK(Host Your Own Key) 키 관리 모델을 사용할 수 있습니다. 이 관리 모드는 미사용 데이터를 암호화 하 고 Microsoft의 통제를 벗어난 소유 리포지토리의 키를 관리 해야 하는 경우에 유용 합니다. 이 모델에서 서비스는 외부 사이트에서 키를 검색해야 합니다. 성능 및 가용성 보장은 영향을 받으며, 구성은 더 복잡합니다. 또한 암호화 및 암호 해독 작업 중에 DEK에 대한 액세스 권한이 서비스에 있으므로 이 모델에서는 고객이 Azure Key Vault에서 키를 관리하는 경우와 비슷한 방식으로 전반적인 보안을 보장합니다.  따라서 특정 키 관리 요구 사항이 필요하지 않는 한 대부분의 조직에는 이 모델이 적합하지 않습니다. 이러한 제한 사항으로 인해 대부분의 Azure 서비스는 고객이 제어 하는 하드웨어에서 서버 관리 키를 사용 하는 서버 쪽 암호화를 지원 하지 않습니다.

### <a name="key-access"></a>키 액세스

고객 제어 하드웨어에서 서비스 관리 키를 사용 하는 서버 쪽 암호화가 사용 되는 경우 해당 키는 고객이 구성한 시스템에서 유지 관리 됩니다. 이 모델을 지원하는 Azure 서비스에서는 고객이 제공한 키 저장소에 보안 연결을 설정하는 수단을 제공합니다.

**장점**

- 루트 키에 대한 모든 권한 사용 - 암호화 키는 고객이 제공한 저장소에서 관리됩니다.
- 하나의 마스터에 여러 서비스를 암호화하는 기능
- 서비스에 대한 전체 관리 모델에서 키 관리를 분리할 수 있습니다.
- 지역 간 서비스 및 키 위치를 정의할 수 있습니다.

**단점**

- 키 스토리지, 보안, 성능 및 가용성에 대한 전적인 책임
- 키 액세스 관리에 대한 전적인 책임
- 키 수명 주기 관리에 대한 전적인 책임
- 중요한 설정, 구성 및 지속적인 유지 관리 비용
- 고객 데이터 센터와 Azure 데이터 센터 간의 네트워크 가용성에 대한 종속성 증가

## <a name="supporting-services"></a>지원 서비스
각 암호화 모델을 지 원하는 Azure 서비스는 다음과 같습니다.

| 제품, 기능 또는 서비스 | 서비스 관리 키를 사용하는 서버 쪽   | 고객이 관리 하는 키를 사용 하는 서버 쪽 | 클라이언트 쪽에서 클라이언트 관리 키를 사용 하는 경우  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **AI 및 기계 학습**      |                    |                    |                    |
| Azure Cognitive Search           | 예                | 예                | -                  |
| Azure Cognitive Services         | 예                | 예                | -                  |
| Azure Machine Learning           | 예                | 예                | -                  |
| Azure Machine Learning Studio    | 예                | 미리 보기, RSA 2048비트 | -               |
| Content Moderator                | 예                | 예                | -                  |
| Face                             | 예                | 예                | -                  |
| Language Understanding           | 예                | 예                | -                  |
| Personalizer                     | 예                | 예                | -                  |
| QnA Maker                        | 예                | 예                | -                  |
| Speech Services                  | 예                | 예                | -                  |
| Translator Text                  | 예                | 예                | -                  |
| Power BI                         | 예                | 미리 보기, RSA 2048비트 | -                  |
| **분석**                    |                    |                    |                    |
| Azure Stream Analytics           | 예                | 해당 없음\*              | -                  |
| Event Hubs                       | 예                | 예                | -                  |
| Functions                        | 예                | 예                | -                  |
| Azure Analysis Services          | 예                | -                  | -                  |
| Azure Data Catalog               | 예                | -                  | -                  |
| Azure HDInsight                  | 예                | 모두                | -                  |
| Azure Monitor Application Insights | 예                | 예                | -                  |
| Azure Monitor Log Analytics      | 예                | 예                | -                  |
| Azure Data Explorer              | 예                | 예                | -                  |
| Azure 데이터 팩터리               | 예                | 예                | -                  |
| Azure Data Lake Store            | 예                | 예, RSA 2048비트  | -                  |
| **컨테이너**                   |                    |                    |                    |
| Azure Kubernetes Service         | 예                | 예                | -                  |
| Container Instances              | 예                | 예                | -                  |
| Container Registry               | 예                | 예                | -                  |
| **Compute**                      |                    |                    |                    |
| Virtual Machines                 | 예                | 예                | -                  |
| 가상 머신 확장 집합        | 예                | 예                | -                  |
| SAP HANA                         | 예                | 예                | -                  |
| App Service                      | 예                | 예\*\*            | -                  |
| Automation                       | 예                | 예\*\*            | -                  |
| Azure 기능                  | 예                | 예\*\*            | -                  |
| Azure Portal                     | 예                | 예\*\*            | -                  |
| Logic Apps                       | 예                | 예                | -                  |
| Azure 관리 되는 응용 프로그램       | 예                | 예\*\*            | -                  |
| Service Bus                      | 예                | 예                | -                  |
| Site Recovery                    | 예                | 예                | -                  |
| **데이터베이스**                    |                    |                    |                    |
| Virtual Machines의 SQL Server   | 예                | 예                | 예                |
| Azure SQL Database               | 예                | 예, RSA 3072 비트  | 예                |
| Azure SQL Database for MariaDB   | 예                | -                  | -                  |
| MySQL에 대 한 Azure SQL Database     | 예                | 예                | -                  |
| PostgreSQL에 대 한 Azure SQL Database | 예               | 예                | -                  |
| Azure Synapse Analytics          | 예                | 예, RSA 3072 비트  | -                  |
| SQL Server Stretch Database      | 예                | 예, RSA 3072 비트  | 예                |
| Table Storage                    | 예                | 예                | 예                |
| Azure Cosmos DB                  | 예                | 예                | -                  |
| Azure Databricks                 | 예                | 예                | -                  |
| Azure Database Migration Service | 예                | 해당 없음\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps Services            | 예                | -                  | 예                |
| Azure Repos                      | 예                | -                  | 예                |
| **ID**                     |                    |                    |                    |
| Azure Active Directory           | 예                | -                  | -                  |
| Azure Active Directory Domain Services | 예          | 예                | -                  |
| **통합**                  |                    |                    |                    |
| Service Bus                      | 예                | 예                | 예                |
| Event Grid                       | 예                | -                  | -                  |
| API Management                   | 예                | -                  | -                  |
| **IoT 서비스**                 |                    |                    |                    |
| IoT Hub                          | 예                | 예                | 예                |
| IoT Hub Device Provisioning      | 예                | 예                | -                  |
| **관리 및 거버넌스**    |                    |                    |                    |
| Azure Site Recovery              | 예                | -                  | -                  |
| Azure Migrate                    | 예                | 예                | -                  |
| **미디어**                        |                    |                    |                    |
| Media Services                   | 예                | -                  | 예                |
| **보안**                     |                    |                    |                    |
| IoT용 Azure Security Center    | 예                | 예                | -                  |
| Azure Sentinel                   | 예                | 예                | -                  |
| **스토리지**                      |                    |                    |                    |
| Blob Storage                     | 예                | 예                | 예                |
| 프리미엄 Blob Storage             | 예                | 예                | 예                |
| Disk Storage                     | 예                | 예                | -                  |
| 울트라 디스크 저장소               | 예                | 예                | -                  |
| 관리 되는 디스크 저장소             | 예                | 예                | -                  |
| File Storage                     | 예                | 예                | -                  |
| 파일 Premium Storage             | 예                | 예                | -                  |
| 파일 동기화                        | 예                | 예                | -                  |
| Queue storage                    | 예                | 예                | 예                |
| Avere vFXT                       | 예                | -                  | -                  |
| Azure Cache for Redis            | 예                | 해당 없음\*              | -                  |
| Azure NetApp Files               | 예                | 예                | -                  |
| Archive Storage                  | 예                | 예                | -                  |
| StorSimple                       | 예                | 예                | 예                |
| Azure Backup                     | 예                | 예                | 예                |
| Data Box                         | 예                | -                  | 예                |
| Data Box Edge                    | 예                | 예                | -                  |

\* 이 서비스는 데이터를 유지 하지 않습니다. 임시 캐시 (있는 경우)는 Microsoft 키를 사용 하 여 암호화 됩니다.

\*\* 이 서비스는 사용자 고유의 Key Vault, 저장소 계정 또는 고객이 관리 하는 키를 사용 하 여 서버 쪽 암호화를 이미 지 원하는 다른 데이터 유지 서비스에 데이터를 저장 하도록 지원 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure에서 암호화를 사용](encryption-overview.md)하는 방법을 알아봅니다.
- Azure에서 [이중 암호화](double-encryption.md) 를 사용 하 여 데이터 암호화와 함께 제공 되는 위협을 완화 하는 방법에 대해 알아봅니다.
