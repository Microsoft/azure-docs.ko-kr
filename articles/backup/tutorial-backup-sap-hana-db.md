---
title: 자습서 - Azure VM에서 SAP HANA 데이터베이스 백업
description: 이 자습서에서는 Azure VM에서 실행되는 SAP HANA 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 50c71d58a2409d0062c414b4328eaf8a919e338b
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757492"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>자습서: Azure VM에서 SAP HANA 데이터베이스 백업

이 자습서에서는 Azure VM에서 실행되는 SAP HANA 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업하는 방법을 보여 줍니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 자격 증명 모음 만들기 및 구성
> * 데이터베이스 검색
> * 백업 구성

현재 지원되는 모든 시나리오는 [여기](sap-hana-backup-support-matrix.md#scenario-support)에 나와 있습니다.

>[!NOTE]
>2020년 8월 1일부터 RHEL(7.4, 7.6, 7.7 및 8.1)에 대한 SAP HANA 백업이 일반 공급됩니다.

## <a name="prerequisites"></a>필수 구성 요소

백업을 구성하기 전에 다음을 수행해야 합니다.

* SAP HANA를 실행하는 VM과 동일한 지역 및 구독에서 [Recovery Services 자격 증명 모음](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)을 식별하거나 만듭니다.
* 아래 [네트워크 연결 설정](#set-up-network-connectivity) 절차에 설명된 대로 VM에서 인터넷에 연결하도록 허용합니다. 그래야 Azure에 연결할 수 있습니다.
* SAP HANA Server VM 이름 및 리소스 그룹 이름의 결합된 길이가 Azure Resource Manager(ARM_ VM(및 클래식 VM의 경우 77자))의 경우 84자를 초과하지 않는지 확인합니다. 이 제한은 서비스에서 일부 문자를 예약했기 때문에 발생합니다.
* **hdbuserstore**에 다음 조건을 충족하는 키가 있어야 합니다.
  * 기본 **hdbuserstore**에 있어야 합니다. 기본값은 SAP HANA가 설치된 `<sid>adm` 계정입니다.
  * MDC의 경우 키가 **NAMESERVER**의 SQL 포트를 가리켜야 합니다. SDC의 경우 **INDEXSERVER**의 SQL 포트를 가리켜야 합니다.
  * 사용자를 추가하고 삭제하려면 자격 증명이 있어야 합니다
* HANA가 설치된 가상 머신에서 루트 사용자로 SAP HANA 백업 구성 스크립트(사전 등록 스크립트)를 실행합니다. [이 스크립트](https://aka.ms/scriptforpermsonhana)를 실행하면 HANA 시스템이 백업 준비가 됩니다. [사전 등록 스크립트의 기능](#what-the-pre-registration-script-does) 섹션을 참조하면 사전 등록 스크립트에 대해 자세히 이해할 수 있습니다.

>[!NOTE]
>사전 등록 스크립트는 RHEL(7.4, 7.6 및 7.7)에서 실행되는 SAP HANA 워크로드용 **compat-unixODBC234** 및 RHEL 8.1용 **unixODBC**를 설치합니다. [이 패키지는 RHEL for SAP HANA(RHEL 7 Server의 경우), RPM(SAP 솔루션용 업데이트 서비스) 리포지토리에 있습니다](https://access.redhat.com/solutions/5094721).  Azure Marketplace RHEL 이미지의 경우 리포지토리는 **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**입니다.

## <a name="set-up-network-connectivity"></a>네트워크 연결 설정

모든 작업을 실행하려면 Azure VM에서 실행되는 SAP HANA 데이터베이스를 Azure Backup 서비스, Azure Storage 및 Azure Active Directory에 연결해야 합니다. 프라이빗 엔드포인트를 사용하거나 필요한 공용 IP 주소 또는 FQDN에 대한 액세스를 허용하면 됩니다. 필요한 Azure 서비스에 대한 적절한 연결을 허용하지 않으면 데이터베이스 검색, 백업 구성, 백업 수행, 데이터 복원과 같은 작업 시 오류가 발생할 수 있습니다.

다음 표에는 연결을 설정하는 데 사용할 수 있는 여러 가지 방법이 나와 있습니다.

| **옵션**                        | **장점**                                               | **단점**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 프라이빗 엔드포인트                 | 가상 네트워크 내 개인 IP를 통한 백업 허용  <br><br>   네트워크 및 자격 증명 모음에 대한 세부적인 제어 지원 | 표준 프라이빗 엔드포인트 [비용](https://azure.microsoft.com/pricing/details/private-link/) 발생 |
| NSG 서비스 태그                  | 범위 변경이 자동으로 병합되어 관리가 더 쉬움   <br><br>   추가 비용 없음 | NSG에만 사용할 수 있음  <br><br>    전체 서비스에 대한 액세스 제공 |
| Azure Firewall FQDN 태그          | 필요한 FQDN이 자동으로 관리되어 관리가 더 쉬움 | Azure Firewall하고만 함께 사용할 수 있음                         |
| 서비스 FQDN/IP에 대한 액세스 허용 | 추가 비용 없음   <br><br>  모든 네트워크 보안 어플라이언스 및 방화벽과 함께 작동 | 광범위한 IP 또는 FQDN 세트에 액세스해야 할 수 있음   |
| HTTP 프록시 사용                 | VM에 대한 인터넷 액세스의 단일 지점                       | 프록시 소프트웨어로 VM을 실행하기 위해 추가 비용이 있음         |

이러한 옵션을 사용하는 방법에 대한 자세한 내용은 아래에 나와 있습니다.

### <a name="private-endpoints"></a>프라이빗 엔드포인트

프라이빗 엔드포인트를 사용하면 가상 네트워크 내의 서버에서 안전하게 Recovery Services 자격 증명 모음에 연결할 수 있습니다. 프라이빗 엔드포인트는 VNET 주소 공간의 IP를 자격 증명 모음에 사용합니다. 가상 네트워크 내의 리소스와 자격 증명 모음 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크를 통해 이동합니다. 이렇게 하면 퍼블릭 인터넷에서 공개되지 않습니다. Azure Backup의 프라이빗 엔드포인트에 대한 자세한 내용은 [여기](./private-endpoints.md)를 참조하세요.

### <a name="nsg-tags"></a>NSG 태그

NSG(네트워크 보안 그룹)를 사용하는 경우 *AzureBackup* 서비스 태그를 사용하여 Azure Backup에 대한 아웃바운드 액세스를 허용하세요. Azure Backup 태그 외에 *Azure AD* 및 *Azure Storage*에 대한 유사한 [NSG 규칙](../virtual-network/security-overview.md#service-tags)을 만들어 인증 및 데이터 전송에 대한 연결도 허용해야 합니다.  다음 단계에서는 Azure Backup 태그에 대한 규칙을 만드는 프로세스에 대해 설명합니다.

1. **모든 서비스**에서 **네트워크 보안 그룹**으로 이동하여 네트워크 보안 그룹을 선택합니다.

1. **설정** 아래에서 **아웃바운드 보안 규칙**을 선택합니다.

1. **추가**를 선택합니다. [보안 규칙 설정](../virtual-network/manage-network-security-group.md#security-rule-settings)에 설명된 대로 새 규칙을 만드는 데 필요한 세부 정보를 모두 입력합니다. **대상** 옵션이 *서비스 태그*로 설정되고 **대상 서비스 태그**가 *AzureBackup*으로 설정되어 있는지 확인합니다.

1. **추가**를 클릭하여 새로 만든 아웃바운드 보안 규칙을 저장합니다.

마찬가지로, Azure Storage 및 Azure AD에 대한 NSG 아웃바운드 보안 규칙을 만들 수 있습니다. 서비스 태그에 대한 자세한 내용은 이 [문서](../virtual-network/service-tags-overview.md)를 참조하세요.

### <a name="azure-firewall-tags"></a>Azure Firewall 태그

Azure Firewall을 사용하는 경우 *AzureBackup* [Azure Firewall FQDN 태그](../firewall/fqdn-tags.md)를 사용하여 애플리케이션 규칙을 만듭니다. 이는 Azure Backup에 대한 모든 아웃바운드 액세스를 허용합니다.

### <a name="allow-access-to-service-ip-ranges"></a>서비스 IP 범위에 대한 액세스 허용

서비스 IP에 대한 액세스를 허용하도록 선택할 경우 사용할 수 있는 JSON 파일의 IP 범위를 [여기](https://www.microsoft.com/download/confirmation.aspx?id=56519)에서 참조하세요. Azure Backup, Azure Storage 및 Azure Active Directory에 해당하는 IP에 대한 액세스를 허용해야 합니다.

### <a name="allow-access-to-service-fqdns"></a>서비스 FQDN에 대한 액세스 허용

다음 FQDN을 사용하여 서버에서 필요한 서비스에 액세스하도록 허용할 수도 있습니다.

| 서비스    | 액세스할 도메인 이름                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | [이 문서](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)에 따라 섹션 56 및 59에서 FQDN에 대한 액세스 허용 |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>HTTP 프록시 서버를 사용하여 트래픽 라우팅

Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업하는 경우 VM의 백업 확장에서 HTTPS API를 사용하여 관리 명령을 Azure Backup에 보내고 데이터를 Azure Storage에 보냅니다. 백업 확장도 인증에 Azure AD를 사용합니다. HTTP 프록시를 통해 이 세 가지 서비스에 대한 백업 확장 트래픽을 라우팅합니다. 필요한 서비스에 대한 액세스를 허용하려면 위에서 언급한 IP 및 FQDN 목록을 사용하세요. 인증된 프록시 서버는 지원되지 않습니다.

## <a name="what-the-pre-registration-script-does"></a>사전 등록 스크립트의 기능

사전 등록 스크립트를 실행하면 다음 기능이 수행됩니다.

* Linux 배포에 따라 스크립트는 Azure Backup 에이전트에 필요한 모든 패키지를 설치하거나 업데이트합니다.
* Azure Active Directory 및 Azure Storage와 같은 종속 서비스 및 Azure Backup 서버와의 아웃바운드 네트워크 연결 검사를 수행합니다.
* [사전 요구 사항](#prerequisites)의 일부로 나열된 사용자 키를 사용하여 HANA 시스템에 로그인합니다. 사용자 키는 HANA 시스템에서 백업 사용자(AZUREWLBACKUPHANAUSER)를 만드는 데 사용되며 사전 등록 스크립트가 성공적으로 실행된 후 사용자 키를 삭제할 수 있습니다.
* AZUREWLBACKUPHANAUSER에는 다음과 같은 필수 역할 및 권한이 할당됩니다.
  * DATABASE ADMIN(MDC의 경우) 및 BACKUP ADMIN(SDC의 경우): 복원 중에 새 데이터베이스를 만듭니다.
  * CATALOG READ: 백업 카탈로그를 읽습니다.
  * SAP_INTERNAL_HANA_SUPPORT: 몇 개의 프라이빗 테이블에 액세스합니다.
* 이 스크립트는 모든 작업(데이터베이스 쿼리, 복원 작업, 백업 구성 및 실행)을 처리하는 HANA 백업 플러그 인용 AZUREWLBACKUPHANAUSER에 대한 **hdbuserstore**에 키를 추가합니다.

>[!NOTE]
> [필수 구성 요소](#prerequisites)의 일부로 나열된 사용자 키를 등록 전 스크립트(`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME`)에 매개 변수로 명시적으로 전달할 수 있습니다. <br><br>
>스크립트가 허용하는 다른 매개 변수를 알아보려면 `bash msawb-plugin-config-com-sap-hana.sh --help` 명령을 사용합니다.

키 생성을 확인하려면 HANA 머신에서 SIDADM 자격 증명을 사용하여 HDBSQL 명령을 실행합니다.

```hdbsql
hdbuserstore list
```

명령 출력에서 사용자가 AZUREWLBACKUPHANAUSER로 표시된 {SID}{DBNAME} 키가 표시됩니다.

>[!NOTE]
> `/usr/sap/{SID}/home/.hdb/` 아래에 고유한 SSFS 파일 세트가 있는지 확인하세요. 이 경로에는 하나의 폴더만 있어야 합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. Recovery Services 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

Recovery Services 자격 증명 모음을 만들려면:

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

   ![모든 서비스 선택](./media/tutorial-backup-sap-hana-db/all-services.png)

3. **모든 서비스** 대화 상자에서 **Recovery Services**를 입력합니다. 입력 내용에 따라 리소스 목록이 필터링됩니다. 리소스 목록에서 **Recovery Services 자격 증명 모음**을 선택합니다.

   ![Recovery Services 자격 증명 모음 선택](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. **Recovery Services** 자격 증명 모음 대시보드에서 **추가**를 선택합니다.

   ![Recovery Services 자격 증명 모음 추가](./media/tutorial-backup-sap-hana-db/add-vault.png)

   **Recovery Services 자격 증명 모음** 대화 상자가 열립니다. **이름, 구독, 리소스 그룹** 및 **위치**에 대한 값을 제공합니다.

   ![Recovery Services 자격 증명 모음 만들기](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Name**: Recovery Services 자격 증명 모음을 식별하는 데 사용되며, Azure 구독에서 고유해야 합니다. 2자 이상 50자 이하의 이름을 지정합니다. 이름은 문자로 시작해야 하며, 문자, 숫자, 하이픈만 포함할 수 있습니다. 이 자습서에서는 **SAPHanaVault**라는 이름을 사용했습니다.
   * **구독**: 사용할 구독을 선택합니다. 단일 구독의 멤버인 경우 해당 이름이 표시됩니다. 사용할 구독을 잘 모르는 경우 기본(제안된) 구독을 사용합니다. 회사 또는 학교 계정이 둘 이상의 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다. 여기서는 **SAP HANA 솔루션 랩 구독** 구독을 사용했습니다.
   * **리소스 그룹**: 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 여기서는 **SAPHANADemo**를 사용했습니다.<br>
   구독에서 사용 가능한 리소스 그룹 목록을 보려면 **기존 그룹 사용**을 선택한 다음, 드롭다운 목록 상자에서 리소스를 선택합니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 선택하고 이름을 입력합니다. 리소스 그룹에 대한 전체 내용은 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)를 참조하세요.
   * **위치**: 자격 증명 모음에 대한 지리적 지역을 선택합니다. 자격 증명 모음은 SAP HANA를 실행하는 Virtual Machine과 동일한 지역에 있어야 합니다. **미국 동부 2**를 사용했습니다.

5. **검토 + 만들기**를 선택합니다.

   ![검토 + 만들기 선택](./media/tutorial-backup-sap-hana-db/review-create.png)

이제 Recovery Services 자격 증명 모음이 만들어집니다.

## <a name="discover-the-databases"></a>데이터베이스 검색

1. 자격 증명 모음의 **시작**에서 **백업**을 클릭합니다. **작업이 실행되는 위치**에서 **Azure VM의 SAP HANA**를 선택합니다.
2. **검색 시작**을 클릭합니다. 그러면 자격 증명 모음 지역에서 보호되지 않는 Linux VM의 검색이 시작됩니다. 보호하려는 Azure VM이 표시됩니다.
3. **Virtual Machines 선택**에서 링크를 클릭하여 데이터베이스 검색을 위해 SAP HANA VM에 액세스할 수 있는 권한을 Azure Backup 서비스에 제공하는 스크립트를 다운로드합니다.
4. 백업하려는 SAP HANA 데이터베이스를 호스팅하는 VM에서 스크립트를 실행합니다.
5. VM에서 스크립트가 실행되면 **Virtual Machines 선택**에서 해당 VM을 선택합니다. 그런 다음, **DB 검색**을 클릭합니다.
6. Azure Backup에서 VM의 모든 SAP HANA 데이터베이스를 검색합니다. 검색하는 동안 Azure Backup은 VM을 자격 증명 모음에 등록하고 확장을 해당 VM에 설치합니다. 에이전트는 데이터베이스에 설치되지 않습니다.

   ![데이터베이스 검색](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>백업 구성

이제 백업하려는 데이터베이스가 검색되었으므로 백업을 사용하도록 설정해 보겠습니다.

1. **백업 구성**을 클릭합니다.

   ![백업 구성](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. **백업할 항목 선택**에서 보호하려는 데이터베이스를 하나 이상 선택한 다음, **확인**을 클릭합니다.

   ![백업할 항목 선택](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. **백업 정책 > 백업 정책 선택**에서 다음 섹션의 지침에 따라 데이터베이스에 대한 새 백업 정책을 만듭니다.

   ![백업 정책 선택](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. 정책이 만들어지면 **백업 메뉴**에서 **백업 사용**을 클릭합니다.

   ![백업 사용 클릭](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. 포털의 **알림** 영역에서 백업 구성 진행률을 추적합니다.

## <a name="creating-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행되는 시기와 보존 기간을 정의합니다.

* 정책은 자격 증명 모음 수준에서 만들어집니다.
* 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.

정책 설정을 다음과 같이 지정합니다.

1. **정책 이름**에 새 정책의 이름을 입력합니다. 여기서는 **SAPHANA**를 입력 합니다.

   ![새 정책 이름 입력](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. **전체 백업 정책**에서 **백업 빈도**를 선택합니다. **매일** 또는 **매주**를 선택할 수 있습니다. 이 자습서에서는 **매일** 백업을 선택했습니다.

   ![백업 빈도 선택](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. **보존 범위**에서 전체 백업에 대한 보존 설정을 구성합니다.
   * 기본적으로 모든 옵션이 선택되어 있습니다. 사용하지 않으려는 보존 범위 제한을 지우고, 사용할 제한을 설정합니다.
   * 모든 백업 유형(전체/차등/로그)의 최소 보존 기간은 7일입니다.
   * 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
   * 특정 일의 백업은 매주 보존 범위 및 설정에 기반한 태그로 지정되어 보존됩니다.
   * 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.
4. **전체 백업 정책** 메뉴에서 **확인**을 클릭하여 설정을 적용합니다.
5. 그런 다음, **차등 백업**을 선택하여 차등 정책을 추가합니다.
6. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다. **30일** 동안 보존되는 차등 백업을 매주 **일요일** **오전 2시**에 수행하도록 설정했습니다.

   ![차등 백업 정책](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >증분 백업은 현재 지원되지 않습니다.
   >

7. **확인**을 클릭하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.
8. **로그 백업**을 선택하여 트랜잭션 로그 백업 정책을 추가합니다.
   * **로그 백업**은 기본적으로 **사용**으로 설정됩니다. SAP HANA에서 모든 로그 백업을 관리하므로 이 기능을 사용하지 않도록 설정할 수 없습니다.
   * 백업 일정은 **2시간**으로 설정하고, 보존 기간은 **15일**로 설정했습니다.

    ![로그 백업 정책](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > 하나의 전체 백업이 성공적으로 완료된 후에만 로그 백업을 수행합니다.
   >

9. **확인**을 클릭하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.
10. 백업 정책 정의가 완료되면 **확인**을 클릭합니다.

이제 SAP HANA 데이터베이스에 대한 백업이 성공적으로 구성되었습니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM에서 실행되는 SAP HANA 데이터베이스에서 요청 시 백업을 실행하는 방법](backup-azure-sap-hana-database.md#run-an-on-demand-backup)을 알아봅니다.
* [Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원하는 방법](sap-hana-db-restore.md)을 알아봅니다.
* [Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법](sap-hana-db-manage.md)을 알아봅니다.
* [SAP HANA 데이터베이스를 백업할 때 발생하는 일반적인 문제를 해결하는 방법](backup-azure-sap-hana-database-troubleshoot.md)을 알아봅니다.
