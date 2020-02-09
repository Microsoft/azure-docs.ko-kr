---
title: Azure Migrate 어플라이언스 배포 및 검색 문제 해결
description: Azure Migrate 어플라이언스를 배포 하 고 컴퓨터를 검색 하는 데 도움을 받으세요.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 37da62a4eb0f934133d6486872ba319138299614
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048698"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Azure Migrate 어플라이언스 및 검색 문제 해결

이 문서는 [Azure Migrate](migrate-services-overview.md) 어플라이언스를 배포할 때와 어플라이언스를 사용 하 여 온-프레미스 컴퓨터를 검색할 때 발생 하는 문제를 해결 하는 데 도움이 됩니다.


## <a name="whats-supported"></a>지원되는 내용은 무엇입니까?

어플라이언스 지원 요구 사항을 [검토](migrate-appliance.md) 합니다.


## <a name="invalid-ovf-manifest-entry"></a>"잘못 된이 매니페스트 항목"

"제공 된 매니페스트 파일이 잘못 되었습니다. 잘못 된 위치 매니페스트 항목입니다." 오류가 표시 되 면 다음을 수행 합니다.

1. 해당 해시 값을 확인 하 여 Azure Migrate 어플라이언스 OVA 파일이 올바르게 다운로드 되었는지 확인 합니다. [자세히 알아봅니다](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). 해시 값이 일치 하지 않으면 OVA 파일을 다시 다운로드 하 고 배포를 다시 시도 합니다.
2. 그래도 배포가 실패 하 고 VMware vSphere 클라이언트를 사용 하 여 파일을 배포 하는 경우 vSphere 웹 클라이언트를 통해 배포 해 보세요. 그래도 배포가 실패 하면 다른 웹 브라우저를 사용해 보세요.
3. VSphere 웹 클라이언트를 사용 하 고 vCenter Server 6.5 또는 6.7에 배포 하려는 경우 ESXi 호스트에 직접 OVA를 배포 해 보세요.
   - 웹 클라이언트 (https://<*호스트 IP 주소*>/uiu)와 함께 ESXi 호스트에 직접 연결 (vCenter Server 대신) 합니다.
   - **Home** > **Inventory**에서 **파일** > 배포 파일 **템플릿 배포**를 선택 합니다. OVA로 이동 하 여 배포를 완료 합니다.
4. 배포가 여전히 실패하는 경우 Azure Migrate 지원에 문의합니다.

## <a name="cant-connect-to-the-internet"></a>인터넷에 연결할 수 없음

이 문제는 어플라이언스 컴퓨터가 프록시 뒤에 있는 경우에 발생할 수 있습니다.

- 프록시에 필요한 경우 권한 부여 자격 증명을 제공 해야 합니다.
- URL 기반 방화벽 프록시를 사용 하 여 아웃 바운드 연결을 제어 하는 경우 [이러한 url](migrate-appliance.md#url-access) 을 허용 목록에 추가 합니다.
- 가로채기 프록시를 사용 하 여 인터넷에 연결 하는 경우 [다음 단계](https://docs.microsoft.com/azure/migrate/concepts-collector)를 사용 하 여 프록시 인증서를 어플라이언스 VM으로 가져옵니다.

##  <a name="datetime-synchronization-error"></a>날짜/시간 동기화 오류

날짜 및 시간 동기화 (802)에 대 한 오류는 서버 클록이 현재 시간과 5 분 넘게 동기화 되지 않았을 수 있음을 나타냅니다. 현재 시간과 일치 하도록 수집기 VM의 클록 시간을 변경 합니다.

1. VM에서 관리자 명령 프롬프트를 엽니다.
2. 표준 시간대를 확인 하려면 **w32tm/crers**를 실행 합니다.
3. 시간을 동기화 하려면 **w32tm/resync**를 실행 합니다.


## <a name="unabletoconnecttoserver"></a>UnableToConnectToServer

이 연결 오류가 발생 하는 경우 vCenter Server *Servername*: 9443에 연결 하지 못할 수 있습니다. 오류 세부 정보는 메시지를 수락할 수*있는 https://:* 9443/sdk에서 수신 대기 하는 엔드포인트이 없음을 표시 합니다.

- 최신 버전의 어플라이언스를 실행 하 고 있는지 확인 합니다. 그렇지 않은 경우 어플라이언스를 [최신 버전](https://docs.microsoft.com/azure/migrate/concepts-collector)으로 업그레이드 하세요.
- 최신 버전에서 문제가 계속 발생 하는 경우 기기가 지정 된 vCenter Server 이름을 확인할 수 없거나 지정 된 포트가 잘못 되었을 수 있습니다. 기본적으로 포트를 지정 하지 않으면 수집기가 포트 번호 443에 연결을 시도 합니다.

    1. 어플라이언스에서 *Servername*.com을 Ping 합니다.
    2. 1 단계가 실패 하면 IP 주소를 사용 하 여 vCenter server에 연결 해 봅니다.
    3. VCenter Server 연결할 올바른 포트 번호를 식별 합니다.
    4. VCenter Server 실행 중인지 확인 합니다.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>오류 60052/60039: 기기가 등록 되지 않았을 수 있습니다.

- 오류 60052, "어플라이언스를 Azure Migrate 프로젝트에 성공적으로 등록 하지 못할 수 있습니다."는 어플라이언스를 등록 하는 데 사용 된 Azure 계정에 권한이 부족 한 경우 발생 합니다.
    - 어플라이언스를 등록 하는 데 사용 되는 Azure 사용자 계정에 구독에 대 한 참가자 이상의 권한이 있는지 확인 합니다.
    - 필요한 Azure 역할 및 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) .
- 오류 60039, "어플라이언스를 등록 하는 데 사용 된 Azure Migrate 프로젝트를 찾을 수 없어 등록이 실패 하는 경우" 기기가 Azure Migrate 프로젝트에 성공적으로 등록 되지 않을 수 있습니다. "가 발생할 수 있습니다.
    - Azure Portal에서 프로젝트가 리소스 그룹에 있는지 여부를 확인 합니다.
    - 프로젝트가 존재 하지 않는 경우 리소스 그룹에 새 Azure Migrate 프로젝트를 만들고 어플라이언스를 다시 등록 합니다. 새 프로젝트를 만드는 [방법에 대해 알아봅니다](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) .

## <a name="error-6003060031-key-vault-management-operation-failed"></a>오류 60030/60031: Key Vault 관리 작업에 실패 했습니다.

60030 또는 60031 오류 "Azure Key Vault 관리 작업에 실패 했습니다."가 표시 되 면 다음을 수행 합니다.
- 어플라이언스를 등록 하는 데 사용 되는 Azure 사용자 계정에 구독에 대 한 참가자 이상의 권한이 있는지 확인 합니다.
- 계정에 오류 메시지에 지정 된 키 자격 증명 모음에 대 한 액세스 권한이 있는지 확인 한 후 작업을 다시 시도 하십시오.
- 이 문제가 계속되면 Microsoft 지원에 문의하세요.
- 필요한 Azure 역할 및 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) .

## <a name="error-60028-discovery-couldnt-be-initiated"></a>오류 60028: 검색을 시작할 수 없습니다.

오류 60028: "오류로 인해 검색을 시작할 수 없습니다. 지정 된 호스트 또는 클러스터 목록에 대 한 작업이 실패 했습니다. "는 VM 정보에 액세스 하거나 검색 하는 데 문제가 있어 오류에 나열 된 호스트에서 검색을 시작할 수 없음을 나타냅니다. 나머지 호스트를 추가 했습니다.

- **호스트 추가** 옵션을 사용 하 여 오류에 나열 된 호스트를 다시 추가 합니다.
- 유효성 검사 오류가 있는 경우 수정 지침을 검토 하 여 오류를 수정 하 고 **검색 저장 및 시작** 옵션을 다시 시도 합니다.

## <a name="error-60025-azure-ad-operation-failed"></a>오류 60025: Azure AD 작업이 실패 했습니다. 
오류 60025: "Azure AD 작업이 실패 했습니다. Azure AD 응용 프로그램을 만들거나 업데이트 하는 동안 발생 한 오류는 검색을 시작 하는 데 사용 되는 Azure 사용자 계정이 어플라이언스를 등록 하는 데 사용 된 계정과 다를 때 발생 합니다. 다음 작업 중 하나를 수행합니다.

- 검색을 시작 하는 사용자 계정이 어플라이언스를 등록 하는 데 사용한 것과 동일한 지 확인 합니다.
- 검색 작업이 실패 하는 사용자 계정에 Azure Active Directory 응용 프로그램 액세스 권한을 제공 합니다.
- Azure Migrate 프로젝트에 대해 이전에 만든 리소스 그룹을 삭제 합니다. 다시 시작할 다른 리소스 그룹을 만듭니다.
- Azure Active Directory 응용 프로그램 사용 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) .


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>오류 50004: 호스트 또는 클러스터에 연결할 수 없습니다.

오류 50004: "서버 이름을 확인할 수 없기 때문에 호스트 또는 클러스터에 연결할 수 없습니다. WinRM 오류 코드: 0x803381B9 "는 어플라이언스에 대 한 Azure DNS 서비스에서 사용자가 제공한 클러스터 또는 호스트 이름을 확인할 수 없을 때 발생할 수 있습니다.

- 클러스터에이 오류가 표시 되 면 클러스터 FQDN을 설정 합니다.
- 클러스터의 호스트에 대해이 오류가 나타날 수도 있습니다. 이는 어플라이언스를 클러스터에 연결할 수 있지만 클러스터에서 Fqdn이 아닌 호스트 이름을 반환 한다는 것을 나타냅니다. 이 오류를 해결 하려면 IP 주소와 호스트 이름의 매핑을 추가 하 여 어플라이언스의 hosts 파일을 업데이트 합니다.
    1. 관리자 권한으로 메모장을 엽니다.
    2. C:\Windows\System32\Drivers\etc\hosts 파일을 엽니다.
    3. 행에 IP 주소 및 호스트 이름을 추가 합니다. 이 오류가 표시 되는 각 호스트 또는 클러스터에 대해 반복 합니다.
    4. Hosts 파일을 저장 한 후 닫습니다.
    5. 어플라이언스 관리 앱을 사용 하 여 어플라이언스를 호스트에 연결할 수 있는지 여부를 확인 합니다. 30 분 후 Azure Portal에 이러한 호스트에 대 한 최신 정보가 표시 됩니다.

## <a name="discovered-vms-not-in-portal"></a>검색 된 Vm이 포털에 없음

검색 상태가 "검색 중입니다." 이지만 포털에서 Vm이 아직 표시 되지 않는 경우 몇 분 정도 기다립니다.
- VMware VM의 경우 약 15 분이 걸립니다.
- Hyper-v VM 검색에 추가 된 각 호스트에 대해 약 2 분이 소요 됩니다.

대기 하 고 상태가 변경 되지 않으면 **서버** 탭에서 **새로 고침** 을 선택 합니다. 서버 평가 및 Azure Migrate: 서버 마이그레이션 Azure Migrate에서 검색 된 서버의 수가 표시 됩니다.

작동 하지 않고 VMware 서버를 검색 하는 경우:

- 지정한 vCenter 계정에 하나 이상의 VM에 대 한 액세스 권한이 있는 권한이 올바르게 설정 되어 있는지 확인 합니다.
- Vcenter 계정이 vCenter VM 폴더 수준에서 액세스 권한을 부여 하는 경우 VMware Vm을 검색할 수 Azure Migrate. 범위 검색에 대해 [자세히 알아보세요](tutorial-assess-vmware.md#set-the-scope-of-discovery) .

## <a name="vm-data-not-in-portal"></a>포털에 없는 VM 데이터

검색 된 Vm이 포털에 표시 되지 않거나 VM 데이터가 오래 된 경우 몇 분 정도 기다립니다. 검색 된 VM 구성 데이터의 변경 내용이 포털에 표시 되는 데 최대 30 분이 걸립니다. 응용 프로그램 데이터의 변경 내용이 나타날 때까지 몇 시간이 걸릴 수 있습니다. 이 시간 이후 데이터가 없으면 다음과 같이 새로 고쳐 보세요.

1. 서버 ** > ** **Azure Migrate 서버 평가**에서 **개요**를 선택 합니다.
2. **관리**에서 **에이전트 상태**를 선택 합니다.
3. **에이전트 새로 고침**을 선택 합니다.
4. 새로 고침 작업이 완료 될 때까지 기다립니다. 이제 최신 정보가 표시 됩니다.

## <a name="deleted-vms-appear-in-portal"></a>삭제 된 Vm이 포털에 표시 됨

Vm을 삭제 하 고 포털에 계속 표시 되는 경우 30 분 동안 기다립니다. 그래도 표시 되 면 위에서 설명한 대로 새로 고칩니다.

## <a name="common-app-discovery-errors"></a>일반적인 앱 검색 오류

Azure Migrate은 Azure Migrate: 서버 평가를 사용 하 여 응용 프로그램, 역할 및 기능의 검색을 지원 합니다. 앱 검색은 현재 VMware에만 지원 됩니다. 앱 검색을 설정 하는 데 필요한 요구 사항 및 단계에 대해 [자세히 알아보세요](how-to-discover-applications.md) .

일반적인 앱 검색 오류는 표에 요약 되어 있습니다. 

**오류** | **원인** | **동작**
--- | --- | --- | ---
1만: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | 이 문제는 컴퓨터 운영 체제가 Windows 또는 Linux가 아닌 경우에 발생할 수 있습니다. | Windows/Linux 용 앱 검색만 사용 합니다.
10001: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | 내부 오류-어플라이언스에 일부 누락 파일이 있습니다. | Microsoft 지원에 문의하세요.
10002: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | 기기의 검색 에이전트가 제대로 작동 하지 않을 수 있습니다. | 24 시간 내에 문제가 해결 되지 않으면 고객 지원 담당자에 게 문의 하세요.
10003 "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | 기기의 검색 에이전트가 제대로 작동 하지 않을 수 있습니다. | 24 시간 내에 문제가 해결 되지 않으면 고객 지원 담당자에 게 문의 하세요.
10004: "< Windows/Linux > 컴퓨터에 대해 설치 된 응용 프로그램을 검색할 수 없습니다." |  < Windows/Linux > 컴퓨터에 액세스 하기 위한 자격 증명을 어플라이언스에서 제공 하지 않았습니다.| < Windows/Linux > 컴퓨터에 대 한 액세스 권한이 있는 어플라이언스에 자격 증명을 추가 합니다.
10005: "온-프레미스 서버에 액세스할 수 없습니다." | 액세스 자격 증명이 잘못 되었을 수 있습니다. | 어플라이언스 자격 증명을 업데이트 하 여 관련 컴퓨터에 액세스할 수 있는지 확인 합니다. 
10006: "온-프레미스 서버에 액세스할 수 없습니다." | 이 문제는 컴퓨터 운영 체제가 Windows 또는 Linux가 아닌 경우에 발생할 수 있습니다.|  Windows/Linux 용 앱 검색만 사용 합니다.
9000/9001/9002: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | VMware 도구가 설치 되어 있지 않거나 손상 되었을 수 있습니다. | 관련 컴퓨터에 VMware tools를 설치/다시 설치 하 고 실행 중인지 확인 합니다.
9003: 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. " | 이 문제는 컴퓨터 운영 체제가 Windows 또는 Linux가 아닌 경우에 발생할 수 있습니다. | Windows/Linux 용 앱 검색만 사용 합니다.
9004: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | VM이 꺼진 경우에 발생할 수 있습니다. | 검색의 경우 VM이 켜져 있는지 확인 합니다.
9005: "VM에 설치 된 응용 프로그램을 검색할 수 없습니다. | 이 문제는 컴퓨터 운영 체제가 Windows 또는 Linux가 아닌 경우에 발생할 수 있습니다. | Windows/Linux 용 앱 검색만 사용 합니다.
9006/9007: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | 기기의 검색 에이전트가 제대로 작동 하지 않을 수 있습니다. | 24 시간 내에 문제가 해결 되지 않으면 고객 지원 담당자에 게 문의 하세요.
9008: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | 내부 오류가 있을 수 있습니다.  | Tf 문제는 24 시간 내에 해결 되지 않으므로 지원 담당자에 게 문의 하세요.
9009: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | 서버의 Windows UAC (사용자 계정 컨트롤) 설정이 제한적이 고 설치 된 응용 프로그램의 검색을 방지 하는 경우 발생할 수 있습니다. | 서버에서 ' 사용자 계정 컨트롤 ' 설정을 검색 하 고 서버에서 하위 두 수준 중 하나로 UAC 설정을 구성 합니다.
9010: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | 내부 오류가 있을 수 있습니다.  | Tf 문제는 24 시간 내에 해결 되지 않으므로 지원 담당자에 게 문의 하세요.
8084: "VMware 오류로 인해 응용 프로그램을 검색할 수 없습니다. <Exception from VMware>" | Azure Migrate 어플라이언스는 VMware Api를 사용 하 여 응용 프로그램을 검색 합니다. 응용 프로그램을 검색 하는 동안 vCenter Server에서 예외가 throw 되는 경우이 문제가 발생할 수 있습니다. VMware의 오류 메시지는 포털에 표시 된 오류 메시지에 표시 됩니다. | [VMware 설명서](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)에서 메시지를 검색 하 고 문제를 해결 하는 단계를 수행 합니다. 해결할 수 없는 경우 Microsoft 지원에 문의 하세요.
9012: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | 내부 오류로 인해 문제가 발생할 수 있습니다.  | 24 시간 내에 문제가 해결 되지 않으면 고객 지원 담당자에 게 문의 하세요.
9013: "서버에 설치 된 응용 프로그램을 검색할 수 없습니다." | VM에 로그인 할 때마다 새 임시 프로필이 생성 됩니다.  | 제공 된 게스트 사용자에 대해 임시 프로필을 만들지 않았는지 확인 합니다.



## <a name="next-steps"></a>다음 단계
[VMware](how-to-set-up-appliance-vmware.md), [hyper-v](how-to-set-up-appliance-hyper-v.md)또는 [물리적 서버용](how-to-set-up-appliance-physical.md)어플라이언스를 설정 합니다.
