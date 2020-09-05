---
title: Azure Backup을 사용하여 Azure에 SAP HANA 데이터베이스 백업
description: 이 문서에서는 Azure Backup 서비스를 사용하여 Azure 가상 머신에 SAP HANA 데이터베이스를 백업하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 07b82e166b0ec6f0d3a29de50584158b67750e8e
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146558"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Azure VM에서 SAP HANA 데이터베이스 백업

SAP HANA 데이터베이스는 낮은 RPO(복구 지점 목표)와 장기 보존이 필요한 중요한 워크로드입니다. [Azure Backup](backup-overview.md)을 사용하여 Azure VM(가상 머신)에서 실행되는 SAP HANA 데이터베이스를 백업할 수 있습니다.

이 문서에서는 Azure VM에서 실행되는 SAP HANA 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업하는 방법을 보여 줍니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
>
> * 자격 증명 모음 만들기 및 구성
> * 데이터베이스 검색
> * 백업 구성
> * 주문형 백업 작업 실행

>[!NOTE]
>2020년 8월 1일부터 RHEL(7.4, 7.6, 7.7 및 8.1)에 대한 SAP HANA 백업이 일반 공급됩니다.

>[!NOTE]
>**Azure VM의 SQL Server 일시 삭제 및 Azure VM 워크로드의 SAP HANA 일시 삭제**는 이제 미리 보기로 제공됩니다.<br>
>미리 보기에 등록 하려면에 의견을 보내 주세요 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>사전 요구 사항

백업용 데이터베이스를 설정하려면 [필수 조건](tutorial-backup-sap-hana-db.md#prerequisites) 및 [사전 등록 스크립트의 기능](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 섹션을 참조하세요.

### <a name="establish-network-connectivity"></a>네트워크 연결 설정

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

#### <a name="private-endpoints"></a>프라이빗 엔드포인트

프라이빗 엔드포인트를 사용하면 가상 네트워크 내의 서버에서 안전하게 Recovery Services 자격 증명 모음에 연결할 수 있습니다. 프라이빗 엔드포인트는 VNET 주소 공간의 IP를 자격 증명 모음에 사용합니다. 가상 네트워크 내의 리소스와 자격 증명 모음 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크를 통해 이동합니다. 이렇게 하면 퍼블릭 인터넷에서 공개되지 않습니다. Azure Backup의 프라이빗 엔드포인트에 대한 자세한 내용은 [여기](./private-endpoints.md)를 참조하세요.

#### <a name="nsg-tags"></a>NSG 태그

NSG(네트워크 보안 그룹)를 사용하는 경우 *AzureBackup* 서비스 태그를 사용하여 Azure Backup에 대한 아웃바운드 액세스를 허용하세요. Azure Backup 태그 외에 *Azure AD* 및 *Azure Storage*에 대한 유사한 [NSG 규칙](../virtual-network/security-overview.md#service-tags)을 만들어 인증 및 데이터 전송에 대한 연결도 허용해야 합니다.  다음 단계에서는 Azure Backup 태그에 대한 규칙을 만드는 프로세스에 대해 설명합니다.

1. **모든 서비스**에서 **네트워크 보안 그룹**으로 이동하여 네트워크 보안 그룹을 선택합니다.

1. **설정** 아래에서 **아웃바운드 보안 규칙**을 선택합니다.

1. **추가**를 선택합니다. [보안 규칙 설정](../virtual-network/manage-network-security-group.md#security-rule-settings)에 설명된 대로 새 규칙을 만드는 데 필요한 세부 정보를 모두 입력합니다. **대상** 옵션이 *서비스 태그*로 설정되고 **대상 서비스 태그**가 *AzureBackup*으로 설정되어 있는지 확인합니다.

1. **추가**를 클릭하여 새로 만든 아웃바운드 보안 규칙을 저장합니다.

마찬가지로, Azure Storage 및 Azure AD에 대한 NSG 아웃바운드 보안 규칙을 만들 수 있습니다. 서비스 태그에 대한 자세한 내용은 이 [문서](../virtual-network/service-tags-overview.md)를 참조하세요.

#### <a name="azure-firewall-tags"></a>Azure Firewall 태그

Azure Firewall을 사용하는 경우 *AzureBackup* [Azure Firewall FQDN 태그](../firewall/fqdn-tags.md)를 사용하여 애플리케이션 규칙을 만듭니다. 이는 Azure Backup에 대한 모든 아웃바운드 액세스를 허용합니다.

#### <a name="allow-access-to-service-ip-ranges"></a>서비스 IP 범위에 대한 액세스 허용

서비스 IP에 대한 액세스를 허용하도록 선택할 경우 사용할 수 있는 JSON 파일의 IP 범위를 [여기](https://www.microsoft.com/download/confirmation.aspx?id=56519)에서 참조하세요. Azure Backup, Azure Storage 및 Azure Active Directory에 해당하는 IP에 대한 액세스를 허용해야 합니다.

#### <a name="allow-access-to-service-fqdns"></a>서비스 FQDN에 대한 액세스 허용

다음 FQDN을 사용하여 서버에서 필요한 서비스에 액세스하도록 허용할 수도 있습니다.

| 서비스    | 액세스할 도메인 이름                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | [이 문서](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)에 따라 섹션 56 및 59에서 FQDN에 대한 액세스 허용 |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>HTTP 프록시 서버를 사용하여 트래픽 라우팅

Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업하는 경우 VM의 백업 확장에서 HTTPS API를 사용하여 관리 명령을 Azure Backup에 보내고 데이터를 Azure Storage에 보냅니다. 백업 확장도 인증에 Azure AD를 사용합니다. HTTP 프록시를 통해 이 세 가지 서비스에 대한 백업 확장 트래픽을 라우팅합니다. 필요한 서비스에 대한 액세스를 허용하려면 위에서 언급한 IP 및 FQDN 목록을 사용하세요. 인증된 프록시 서버는 지원되지 않습니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>데이터베이스 검색

1. 자격 증명 모음의 **시작**에서 **백업**을 클릭합니다. **작업이 실행되는 위치**에서 **Azure VM의 SAP HANA**를 선택합니다.
2. **검색 시작**을 클릭합니다. 그러면 자격 증명 모음 지역에서 보호되지 않는 Linux VM의 검색이 시작됩니다.

   * 검색 후에는 보호되지 않은 VM이 포털에 표시되며 이름 및 리소스 그룹별로 나열됩니다.
   * VM이 예상대로 나열되지 않으면 자격 증명 모음에 이미 백업되어 있는지 확인합니다.
   * 여러 VM은 동일한 이름을 사용할 수 있지만 서로 다른 리소스 그룹에 속합니다.

3. **Virtual Machines 선택**에서 링크를 클릭하여 데이터베이스 검색을 위해 SAP HANA VM에 액세스할 수 있는 권한을 Azure Backup 서비스에 제공하는 스크립트를 다운로드합니다.
4. 백업하려는 SAP HANA 데이터베이스를 호스팅하는 각 VM에서 스크립트를 실행합니다.
5. VM에서 스크립트가 실행되면 **Virtual Machines 선택**에서 해당 VM을 선택합니다. 그런 다음, **DB 검색**을 클릭합니다.
6. Azure Backup에서 VM의 모든 SAP HANA 데이터베이스를 검색합니다. 검색하는 동안 Azure Backup은 VM을 자격 증명 모음에 등록하고 확장을 해당 VM에 설치합니다. 에이전트는 데이터베이스에 설치되지 않습니다.

    ![SAP HANA 데이터베이스 검색](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>백업 구성  

이제 백업을 사용하도록 설정합니다.

1. 2단계에서 **백업 구성**을 클릭합니다.

    ![백업 구성](./media/backup-azure-sap-hana-database/configure-backup.png)
2. **백업할 항목 선택**에서 보호하려는 모든 데이터베이스 > **확인**을 차례로 선택합니다.

    ![백업할 항목 선택](./media/backup-azure-sap-hana-database/select-items.png)
3. **백업 정책** > **백업 정책 선택**에서 아래의 지침에 따라 데이터베이스에 대한 새 백업 정책을 만듭니다.

    ![백업 정책 선택](./media/backup-azure-sap-hana-database/backup-policy.png)
4. 정책이 만들어지면 **백업** 메뉴에서 **백업 사용**을 클릭합니다.

    ![백업 사용](./media/backup-azure-sap-hana-database/enable-backup.png)
5. 포털의 **알림** 영역에서 백업 구성 진행률을 추적합니다.

### <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 백업이 수행되는 시기와 보존 기간을 정의합니다.

* 정책은 자격 증명 모음 수준에서 만들어집니다.
* 다수의 자격 증명 모음은 자격 증명 모음은 동일한 백업 정책을 사용할 수 있지만 자격 증명 모음마다 백업 정책을 적용해야 합니다.

>[!NOTE]
>Azure Backup은 Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업할 때 일광 절약 시간제 변경을 자동으로 조정하지 않습니다.
>
>필요에 따라 정책을 수동으로 수정합니다.

정책 설정을 다음과 같이 지정합니다.

1. **정책 이름**에 새 정책의 이름을 입력합니다.

   ![정책 이름 입력](./media/backup-azure-sap-hana-database/policy-name.png)
2. **전체 백업 정책**에서 **백업 빈도**를 선택하고, **매일** 또는 **매주**를 선택합니다.
   * **매일**: 백업 작업이 시작되는 시간과 표준 시간대를 선택합니다.
       * 전체 백업을 실행해야 합니다. 이 옵션은 해제할 수 없습니다.
       * **전체 백업**을 클릭하여 정책을 확인합니다.
       * 매일 전체 백업에 대해서는 차등 백업을 만들 수 없습니다.
   * **매주**: 백업 작업이 실행되는 요일, 시간 및 표준 시간대를 선택합니다.

   ![백업 빈도 선택](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. **보존 범위**에서 전체 백업에 대한 보존 설정을 구성합니다.
    * 기본적으로 모든 옵션이 선택되어 있습니다. 사용하지 않으려는 보존 범위 제한을 지우고, 사용할 제한을 설정합니다.
    * 모든 백업 유형(전체/차등/로그)의 최소 보존 기간은 7일입니다.
    * 복구 지점은 보존 범위를 기반으로 보존 태그가 지정됩니다. 예를 들어, 매일, 전체 백업을 선택하면 매일 하나의 전체 백업만 트리거됩니다.
    * 특정 일의 백업은 매주 보존 범위 및 설정에 기반한 태그로 지정되어 보존됩니다.
    * 매월 및 매년 보존 범위도 유사한 방식으로 작동합니다.

4. **전체 백업 정책** 메뉴에서 **확인**을 클릭하여 설정을 적용합니다.
5. **차등 백업**을 선택하여 차등 정책을 추가합니다.
6. **차등 백업 정책**에서 **사용**을 선택하여 빈도 및 보존 컨트롤을 엽니다.
    * 많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.
    * 차등 백업은 최대 180일 동안 보존될 수 있습니다. 더 오래 보존해야 하는 경우에는 전체 백업을 사용해야 합니다.

    ![차등 백업 정책](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 증분 백업은 현재 지원되지 않습니다.

7. **확인**을 클릭하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.
8. **로그 백업**을 선택하여 트랜잭션 로그 백업 정책을 추가합니다.
    * **로그 백업**에서 **사용**을 선택합니다.  SAP HANA는 모든 로그 백업을 관리 하므로이를 비활성화할 수 없습니다.
    * 빈도 및 보존 컨트롤을 설정합니다.

    > [!NOTE]
    > 전체 백업이 성공적으로 완료된 후에만 로그 백업을 수행합니다.

9. **확인**을 클릭하여 정책을 저장하고 주 **백업 정책** 메뉴로 돌아갑니다.
10. 백업 정책 정의가 완료되면 **확인**을 클릭합니다.

> [!NOTE]
> 각 로그 백업은 이전의 전체 백업에 연결되어 복구 체인을 형성합니다. 이 전체 백업은 마지막 로그 백업의 보존 기간이 만료될 때까지 유지됩니다. 따라서 모든 로그가 복구될 수 있도록 전체 백업이 추가 기간 동안 보존될 수 있습니다. 사용자에 게 주간 전체 백업, 일별 차등 및 2 시간 로그가 있다고 가정해 보겠습니다. 모든 항목은 30일 동안 유지됩니다. 그러나 매주 전체 백업을 사용할 수 있는 경우, 즉 30 ~ 7 일 후에만 매주 전체를 정리/삭제할 수 있습니다. 예를 들어 매주 전체 백업은 11 월 16 일에 발생 합니다. 보존 정책에 따라이는 Dec 년 12 월까지 유지 되어야 합니다. 이 전체 백업에 대한 마지막 로그 백업은 예약된 다음 전체 백업일(11월 22일) 이전에 실행됩니다. 이 로그는 12월 22일까지 사용할 수 있으며, 11월 16일 전체 백업본은 그때까지 삭제할 수 없습니다. 따라서 11월 16일 전체 백업본은 12월 22일까지 유지됩니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

백업은 정책 일정에 따라 실행됩니다. 다음과 같이 주문형 백업을 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목**에서 SAP HANA 데이터베이스를 실행하는 VM을 선택하고 **지금 백업**을 클릭합니다.
3. **지금 백업**에서 수행할 백업 유형을 선택 합니다. 그런 다음 **확인**을 클릭합니다. 이 백업은이 백업 항목과 연결 된 정책에 따라 보존 됩니다.
4. 포털 알림을 모니터링합니다. 자격 증명 모음 대시보드 > **백업 작업** > **진행 중**에서 작업 진행률을 모니터링할 수 있습니다. 데이터베이스의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

기본적으로 요청 시 백업의 보존은 45 일입니다.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Azure Backup이 설정된 데이터베이스에서 SAP HANA Studio 백업 실행

Microsoft Azure Backup으로 백업 중인 데이터베이스의 로컬 백업(HANA Studio 사용)을 수행하려면 다음 단계를 따르세요.

1. 데이터베이스에 대한 전체 또는 로그 백업이 완료될 때까지 기다립니다. SAP HANA Studio/Cockpit에서 상태를 확인합니다.
1. 로그 백업을 사용하지 않도록 설정하고 관련 데이터베이스에 대한 백업 카탈로그를 파일 시스템에 설정합니다.
1. 이렇게 하려면 **systemdb** > **구성** > **데이터베이스 선택** > **필터(로그)** 를 두 번 클릭합니다.
1. **enable_auto_log_backup**을 **아니요**로 설정합니다.
1. **log_backup_using_backint**를 **False**로 설정합니다.
1. **Catalog_backup_using_backint** 을 **False**로 설정 합니다.
1. 데이터베이스의 주문형 전체 백업을 수행합니다.
1. 전체 백업 및 카탈로그 백업이 완료될 때까지 기다립니다.
1. 이전 설정을 Azure에 대한 설정으로 되돌립니다.
    * **enable_auto_log_backup**을 **예**로 설정합니다.
    * **log_backup_using_backint**를 **True**로 설정합니다.
    * **Catalog_backup_using_backint** 를 **True**로 설정 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원하는 방법](./sap-hana-db-restore.md)을 알아봅니다.
* [Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법](./sap-hana-db-manage.md)을 알아봅니다.
