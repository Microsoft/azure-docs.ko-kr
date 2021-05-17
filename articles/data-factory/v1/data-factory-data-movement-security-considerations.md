---
title: Azure Data Factory에서 데이터 이동을 위한 보안 고려 사항
description: Azure Data Factory에서 데이터 이동 보안에 대해 알아봅니다.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 33b1ad381b3f7865768f9e39295a2985f8aa5234
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375105"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - 데이터 이동을 위한 보안 고려 사항

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory에 대한 데이터 이동 보안 고려 사항](../data-movement-security-considerations.md)을 참조하세요.

## <a name="introduction"></a>소개
이 문서에서는 Azure Data Factory의 데이터 이동 서비스가 데이터를 보호하는 데 사용하는 기본 보안 인프라에 대해 설명합니다. Azure Data Factory 관리 리소스는 Azure 보안 인프라를 기반으로 하며 Azure가 제공하는 모든 가능한 보안 수단을 사용합니다.

Data Factory 솔루션에서 하나 이상의 데이터 [파이프라인](data-factory-create-pipelines.md)를 만듭니다. 파이프라인은 함께 작업을 수행하는 활동의 논리적 그룹화입니다. 이 파이프라인은 데이터 팩터리가 작성된 지역에 상주합니다. 

Data Factory는 **미국 서부**, **미국 동부** 및 **북유럽** 지역에서만 사용할 수 있지만, 데이터 이동 서비스는 [여러 지역에서 전역적으로](data-factory-data-movement-activities.md#global) 사용할 수 있습니다. Data Factory 서비스는 데이터 이동 서비스가 아직 해당 지역에 배포되지 않은 경우 서비스가 대체 지역을 사용하도록 명시적으로 지시하지 않는 한 데이터가 지리적 영역/지역을 벗어나지 않도록 합니다. 

Azure Data Factory 자체는 인증서를 사용하여 암호화된 클라우드 데이터 저장소에 대한 링크된 서비스 자격 증명을 제외한 모든 데이터를 저장하지 않습니다. 데이터 기반 워크플로를 만들어서 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 간의 데이터 이동을 조정하고 다른 지역 또는 온-프레미스 환경에서 [컴퓨팅 서비스](data-factory-compute-linked-services.md)를 사용하여 데이터의 처리를 조정할 수 있습니다. 또한 프로그래밍 방식 및 UI 메커니즘을 모두 사용하여 [워크플로를 모니터링하고 관리](data-factory-monitor-manage-pipelines.md) 할 수 있습니다.

Azure Data Factory를 사용한 데이터 이동은 다음에 대해 **인증을 받았습니다**.
-    [HIPAA/HITECH](/compliance/regulatory/offering-hipaa-hitech)  
-    [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-    [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-    [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Azure 규정 준수 및 Azure의 자체 인프라 보안 방법에 관심이 있을 경우 [Microsoft 보안 센터](https://microsoft.com/en-us/trustcenter/default.aspx)를 방문하세요. 

이 문서에서는 다음 두 가지 데이터 이동 시나리오에서 보안 고려 사항을 검토합니다. 

- **클라우드 시나리오** - 이 시나리오에서는 출처와 목적지 모두 인터넷을 통해 공개적으로 액세스할 수 있습니다. 여기에는 Azure Storage, Azure Synapse Analytics, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, Salesforce와 같은 SaaS 서비스, FTP 및 OData와 같은 웹 프로토콜과 같은 관리 되는 클라우드 저장소 서비스가 포함 됩니다. 지원되는 데이터 원본 목록은 [여기](data-factory-data-movement-activities.md#supported-data-stores-and-formats)에 있습니다.
- **하이브리드 시나리오** - 이 시나리오에서는 원본 또는 대상이 방화벽 뒤에 있거나 회사 내 회사 네트워크 내에 있거나 데이터 저장소가 프라이빗 네트워크/가상 네트워크(주로 원본)에 있으며 공개적으로 액세스할 수 없습니다. 가상 머신에서 호스팅되는 데이터베이스 서버도 이 시나리오에 해당합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>클라우드 시나리오
### <a name="securing-data-store-credentials"></a>데이터 저장소 자격 증명 보안
Azure Data Factory는 **Microsoft에서 관리하는 인증서** 를 사용하여 **암호화** 하여 데이터 저장소 자격 증명을 보호합니다. 이 인증서는 **2년마다** 갱신됩니다(인증서 갱신 및 자격 증명 마이그레이션 포함). 이러한 암호화된 자격 증명은 **Azure Data Factory 관리 서비스에서 관리하는 Azure Storage** 에 안전하게 저장됩니다. Azure Storage 보안에 대한 자세한 내용은 [Azure Storage 보안 개요](../../storage/blobs/security-recommendations.md)를 참조하세요.

### <a name="data-encryption-in-transit"></a>전송 중 암호화
클라우드 데이터 저장소가 HTTPS 또는 TLS를 지원하는 경우 Data Factory의 데이터 이동 서비스와 클라우드 데이터 저장소 간의 모든 데이터 전송은 보안 채널 HTTPS 또는 TLS를 통해 이루어집니다.

> [!NOTE]
> **Azure SQL Database** 및 **Azure Synapse Analytics** 에 대 한 모든 연결은 데이터를 데이터베이스에 전송 하는 동안 항상 암호화 (SSL/TLS)가 필요 합니다. JSON 편집기를 사용하여 파이프라인을 작성하는 동안 **encryption** 속성을 추가하고 **연결 문자열** 에서 **true** 로 설정합니다. [복사 마법사](data-factory-azure-copy-wizard.md)를 사용하면 마법사가 기본적으로 이 속성을 설정합니다. **Azure Storage** 의 경우 연결 문자열에 **HTTPS** 를 사용할 수 있습니다.

### <a name="data-encryption-at-rest"></a>휴지 상태의 암호화
일부 데이터 저장소가 저장소 데이터 암호화를 지원합니다. 이러한 데이터 저장소에 데이터 암호화 메커니즘을 사용하는 것이 좋습니다. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
Azure Synapse Analytics의 TDE (투명한 데이터 암호화)는 미사용 데이터에 대 한 실시간 암호화 및 암호 해독을 수행 하 여 악의적인 활동의 위협 으로부터 보호 하는 데 도움이 됩니다. 이 동작은 클라이언트에 대해 투명합니다. 자세한 내용은 [Azure Synapse Analytics에서 데이터베이스 보안](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)을 참조 하세요.

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database는 애플리케이션을 변경할 필요 없이 실시간으로 데이터 암호화 및 암호 해독을 수행하여 악의적인 활동의 위협으로부터 보호하는 TDE(투명한 데이터 암호화)도 지원합니다. 이 동작은 클라이언트에 대해 투명합니다. 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)를 참조하세요. 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
또한 Azure Data Lake Store는 계정에 저장된 데이터에 대한 암호화를 제공합니다. 사용할 경우 Data Lake Store는 자동으로 데이터를 영구 저장하기 전에 데이터를 암호화하고, 검색하기 전에 데이터를 해독하므로 데이터에 액세스하는 클라이언트는 투명합니다. 자세한 내용은 [Azure Data Lake Store의 데이터 보안](../../data-lake-store/data-lake-store-security-overview.md)을 참조하세요. 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage 및 Azure Table Storage
Azure Blob Storage 및 Azure Table Storage는 자동으로 스토리지에 영구히 저장하기 전에 데이터를 암호화하고 검색하기 전에 데이터를 해독하는 SSE(저장소 서비스 암호화)를 지원합니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](../../storage/common/storage-service-encryption.md)를 참조하세요.

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3는 미사용 데이터의 클라이언트 및 서버 암호화를 모두 지원합니다. 자세한 내용은 [암호화를 사용하여 데이터 보호](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)를 참조하세요. 현재, 데이터 팩터리는 VPC(가상 프라이빗 클라우드) 내에서 Amazon S3를 지원하지 않습니다.

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift는 미사용 데이터에 대한 클러스터 암호화를 지원합니다. 자세한 내용은 [Amazon Redshift 데이터베이스 암호화](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)를 참조하세요. 현재, 데이터 팩터리는 VPC 내에서 Amazon Redshift를 지원하지 않습니다. 

#### <a name="salesforce"></a>Salesforce
Salesforce는 모든 파일, 첨부 파일, 사용자 정의 필드의 암호화를 허용하는 Shield Platform Encryption을 지원합니다. 자세한 내용은 [웹 서버 OAuth 인증 흐름 이해](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)를 참조하세요.  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>하이브리드 시나리오(데이터 관리 게이트웨이 사용)
하이브리드 시나리오에서는 데이터 관리 게이트웨이를 온-프레미스 네트워크 또는 가상 네트워크(Azure) 또는 가상 프라이빗 클라우드(Amazon) 내부에 설치해야 합니다. 게이트웨이는 로컬 데이터 저장소에 액세스할 수 있어야 합니다. 게이트웨이에 대한 자세한 내용은 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 참조하세요. 

![데이터 관리 게이트웨이 채널](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**명령 채널** 은 Data Factory의 데이터 이동 서비스와 데이터 관리 게이트웨이 간의 통신을 허용합니다. 통신에는 활동과 관련된 정보가 들어 있습니다. 데이터 채널은 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터를 전송하는 데 사용됩니다.    

### <a name="on-premises-data-store-credentials"></a>온-프레미스 데이터 저장소 자격 증명
온-프레미스 데이터 저장소의 자격 증명은 로컬이 아닌 클라우드에 저장됩니다. 세 가지 방법으로 설정할 수 있습니다. 

- Azure Portal/복사 마법사에서 HTTPS를 통해 **일반 텍스트**(보안 수준 낮음)를 사용합니다. 자격 증명은 일반 텍스트로 온-프레미스 게이트웨이에 전달됩니다.
- **복사 마법사에서 JavaScript 암호화 라이브러리** 사용 중.
- **한 번 클릭 기반 자격 증명 관리자 앱** 사용. 1회성 애플리케이션은 게이트웨이에 액세스할 수 있는 온-프레미스 시스템에서 실행되며 데이터 저장소에 대한 인증 정보를 설정합니다. 이 옵션과 다음 옵션은 가장 안전한 옵션입니다. 자격 증명 관리자 앱은 기본적으로 보안 통신을 위해 게이트웨이가 있는 컴퓨터에서 포트 8050을 사용합니다.  
- [AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) PowerShell cmdlet을 사용 하 여 자격 증명을 암호화 합니다. Cmdlet은 해당 게이트웨이 구성하는 인증서를 사용하여 자격 증명을 암호화를 사용합니다. 이 cmdlet이 반환 하는 암호화 된 자격 증명을 사용 하 여 [AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) cmdlet에서 사용 하는 json 파일에서 **ConnectionString** 의 **encryptedcredential** 요소에 추가 하거나 포털의 Data Factory 편집기에서 json 코드 조각에 추가할 수 있습니다. 이 옵션과 클릭 1회 애플리케이션은 가장 안전한 옵션입니다. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript 암호화 라이브러리 기반 암호화
[복사 마법사](data-factory-copy-wizard.md)의 [JavaScript Cryptography 라이브러리](https://www.microsoft.com/download/details.aspx?id=52439)를 사용하여 데이터 저장소 자격 증명을 암호화할 수 있습니다. 이 옵션을 선택하면 복사 마법사가 게이트웨이의 공개 키를 검색하여 이를 사용하여 데이터 저장소 자격 증명을 암호화합니다. 자격 증명은 게이트웨이 컴퓨터에 의해 해독되고 Windows [DPAPI](/previous-versions/ms995355(v=msdn.10))에 의해 보호됩니다.

**지원되는 브라우저:** IE8, IE9, IE10, IE11, Microsoft Edge 및 최신 Firefox, Chrome, Opera, Safari 브라우저를 지원합니다. 

#### <a name="click-once-credentials-manager-app"></a>클릭 한 번 자격 증명 관리자 앱
파이프라인을 제작할 때 Azure Portal/복사 마법사에서 클릭 한번 기반의 자격 증명 관리자 앱을 시작할 수 있습니다. 이 애플리케이션은 자격 증명이 일반 텍스트로 전송되지 않도록 합니다. 기본적으로 보안 통신을 위해 게이트웨이가 있는 시스템에서 **8050** 포트를 사용합니다. 필요한 경우 이 포트를 변경할 수 있습니다.  
  
![게이트웨이용 HTTPS 포트](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

현재 Data Management Gateway는 단일 **인증서** 를 사용합니다. 이 인증서는 게이트웨이 설치 중 작성됩니다(2016년 11월 이후에 작성된 Data Management Gateway 및 2.4.xxxx.x 이상 버전에 적용됨). 이 인증서를 자신의 SSL/TLS 인증서로 바꿀 수 있습니다. 이 인증서는 일회용 신임 관리자 애플리케이션에서 데이터 저장소 자격 증명을 설정하기 위해 게이트웨이 시스템에 안전하게 연결하는 데 사용됩니다. 게이트웨이가 있는 컴퓨터에서 Windows [DPAPI](/previous-versions/ms995355(v=msdn.10))를 사용하여 데이터 저장소 자격 증명을 안전하게 온-프레미스에 저장합니다. 

> [!NOTE]
> 2016년 11월 또는 버전 2.3.xxxx.x 이전에 설치된 이전 게이트웨이는 암호화된 정보를 사용하여 클라우드에 계속 저장됩니다. 게이트웨이를 최신 버전으로 업그레이드하더라도 자격 증명이 온-프레미스 컴퓨터로 마이그레이션되지 않습니다.    
  
| 게이트웨이 버전(생성 중) | 저장된 자격 증명 | 자격 증명 암호화/보안 | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | 클라우드에서 | 인증서를 사용하여 암호화 됨(Credential Manager 앱에서 사용하는 것과 다른) | 
| > = 2.4.xxxx.x | 온-프레미스 | DPAPI를 통해 보안 | 
  

### <a name="encryption-in-transit"></a>전송 중 암호화
모든 데이터 전송은 보안 채널 **HTTPS** 및 **TLS over TCP** 를 통해 Azure 서비스와의 통신 중 man-in-the-middle 공격을 방지합니다.
 
또한 [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) 또는 [ExpressRoute](../../expressroute/expressroute-introduction.md)를 사용하여 온-프레미스 네트워크와 Azure 사이의 통신 채널을 더욱 안전하게 보호할 수 있습니다.

가상 네트워크(VNet)는 클라우드의 사용자 네트워크를 논리적으로 나타내는 표현입니다. IPSec VPN(사이트 간) 또는 Express Route(프라이빗 피어링)를 설정하여 온-프레미스 네트워크를 Azure 가상 네트워크(VNet)에 연결할 수 있습니다.        

다음 표는 하이브리드 데이터 이동을 위한 원본 및 대상 위치의 다양한 조합에 따라 네트워크 및 게이트웨이 구성 권장 사항을 요약한 것입니다.

| 원본 | 대상 | 네트워크 구성 | 게이트웨이 설치 |
| ------ | ----------- | --------------------- | ------------- | 
| 온-프레미스 | 가상 네트워크에 배포된 가상 머신 및 클라우드 서비스 | IPSec VPN(지점 및 사이트 간 또는 사이트 간) | 게이트웨이는 Vnet의 온-프레미스 또는 Azure 가상 머신(VM)에 설치할 수 있습니다. | 
| 온-프레미스 | 가상 네트워크에 배포된 가상 머신 및 클라우드 서비스 | ExpressRoute(프라이빗 피어링) | 게이트웨이는 VNet의 Azure VM 또는 온-프레미스 환경에 설치할 수 있습니다. | 
| 온-프레미스 | 공개 엔드포인트가 있는 Azure 기반 서비스 | ExpressRoute(공용 피어링) | 게이트웨이를 온-프레미스에 설치해야 합니다. | 

다음 이미지는 ExpressRoute 및 IPSec VPN(Virtual Network 사용)을 사용하여 온-프레미스 데이터베이스와 Azure 서비스간에 데이터를 이동시키기 위한 데이터 관리 게이트웨이의 사용법을 보여 줍니다.

**Express 경로:**
 
![게이트웨이와 함께 ExpressRoute 사용](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![게이트웨이가 있는 IPSec VPN](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-filtering-ip-address-of-gateway"></a>방화벽 구성 및 게이트웨이의 IP 주소 필터링

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>온-프레미스/개인 네트워크에 대한 방화벽 요구 사항    
기업에서는 **기업 방화벽** 이 중앙 라우터에서 실행됩니다. 그리고 **Windows 방화벽** 은 게이트웨이가 설치된 로컬 시스템에서 데몬으로 실행됩니다. 

다음 표는 **아웃바운드 포트** 및 **회사 방화벽** 에 대한 도메인 요구 사항을 제공합니다.

| 도메인 이름 | 아웃바운드 포트 | Description |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Data Factory에서 데이터 이동 서비스에 연결하기 위해 게이트웨이가 필요합니다. |
| `*.core.windows.net` | 443 | [복사 준비](data-factory-copy-activity-performance.md#staged-copy) 기능을 사용할 때 게이트웨이가 Azure Storage 계정에 연결하는 데 사용합니다. | 
| `*.frontend.clouddatahub.net` | 443 | Azure Data Factory 서비스에 연결하기 위해 게이트웨이가 필요합니다. | 
| `*.database.windows.net` | 1433    | (선택 사항) 대상이 Azure SQL Database/Azure Synapse Analytics 인 경우에 필요 합니다. 1433 포트를 열지 않고 준비 된 복사 기능을 사용 하 여 Azure SQL Database/Azure Synapse Analytics로 데이터를 복사 합니다. | 
| `*.azuredatalakestore.net` | 443 | (선택 사항) 목적지가 Azure Data Lake 매장인 경우 필요 | 

> [!NOTE] 
> 각 데이터 원본에서 요구 하는 대로 회사 방화벽 수준에서 포트/필터링 도메인을 관리 해야 할 수 있습니다. 이 표에서는 Azure SQL Database Azure Synapse Analytics Azure Data Lake Store 예제로만 사용 합니다.   

다음 표에서는 **Windows 방화벽** 에 대한 **인바운드 포트** 요구 사항을 제공합니다.

| 인바운드 포트 | 설명 | 
| ------------- | ----------- | 
| 8050(TCP) | 게이트웨이의 온-프레미스 데이터 저장소에 대한 신임을 안전하게 설정하기 위해 신임 관리자 애플리케이션에서 필요합니다. | 

![게이트웨이 포트 요구 사항](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurationsfiltering-in-data-store"></a>데이터 저장소의 IP 구성/필터링
클라우드의 일부 데이터 저장소에도 액세스 하는 컴퓨터의 IP 주소를 승인 해야 합니다. 게이트웨이 컴퓨터의 IP 주소가 방화벽에서 적절 하 게 승인/구성 되었는지 확인 합니다.

다음 클라우드 데이터 저장소는 게이트웨이 컴퓨터의 IP 주소를 승인 해야 합니다. 기본적으로 이러한 데이터 저장소 중 일부에는 IP 주소를 승인할 필요가 없습니다. 

- [Azure SQL Database](../../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
- [Azure Data Lake Storage](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/how-to-configure-firewall.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>질문과 대답

**질문:** 게이트웨이를 다른 데이터 팩터리에서 공유할 수 있습니까?
**대답:** 이 기능은 아직 지원하지 않습니다. 적극적으로 노력하고 있습니다.

**질문:** 게이트웨이가 작동하는 데 필요한 포트 요구 사항은 무엇입니까?
**대답:** 게이트웨이는 인터넷을 열 수 있는 HTTP 기반 연결을 만듭니다. 이 연결을 만들기 위해 게이트웨이에서 **443 및 80 아웃바운드 포트** 를 열어야 합니다. Credential Manager 애플리케이션의 경우(회사 방화벽 수준이 아닌) 시스템 수준에서만 **8050 인바운드 포트** 를 엽니다. Azure SQL Database 또는 Azure Synapse Analytics를 원본/대상으로 사용 하는 경우 **1433** 포트도 열어야 합니다. 자세한 내용은 [방화벽 구성 및 IP 주소 필터링](#firewall-configurations-and-filtering-ip-address-of gateway) 섹션을 참조 하세요. 

**질문:** 게이트웨이의 인증서 요구 사항은 무엇입니까?
**대답:** 현재 게이트웨이에는 데이터 저장소 자격 증명을 안전하게 설정하기 위해 자격 증명 관리자 애플리케이션에서 사용하는 인증서가 필요합니다. 이 인증서는 게이트웨이 설정에 의해 생성되고 구성된 자체 서명된 인증서입니다. 대신 자체 TLS/SSL 인증서를 사용할 수 있습니다. 자세한 정보는 [클릭 한번 자격 증명 관리자 애플리케이션](#click-once-credentials-manager-app) 섹션을 참조하세요. 

## <a name="next-steps"></a>다음 단계
복사 활동의 성능에 대한 자세한 내용은 [복사 활동 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.