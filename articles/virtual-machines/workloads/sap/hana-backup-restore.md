---
title: Azure(큰 인스턴스)의 SAP HANA에서 HANA 백업 및 복원 | Microsoft Docs
description: Azure(큰 인스턴스)의 SAP HANA에서 HANA 백업 및 복원을 수행하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c03a7e5763f580bf1e17232a5850064710c8227
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098323"
---
# <a name="backup-and-restore"></a>Backup 및 복원

>[!IMPORTANT]
>이 문서는 SAP HANA 관리 설명서 또는 SAP 노트를 대체하지 않습니다. reader가 특히 백업, 복원, 고가용성 및 재해 복구의 항목에서 SAP HANA 관리 및 작업을 이해하고 전문 지식을 가졌다고 가정하겠습니다. 이 문서에서는 SAP HANA Studio의 스크린샷이 표시됩니다. SAP 관리 도구 스크린의 콘텐츠, 구조 및 특성과 도구 자체는 릴리스할 SAP HANA 릴리스마다 변경될 수 있습니다.

사용자 환경에서 수행되는 단계와 프로세스를 해당 HANA 버전 및 릴리스로 연습하는 것이 중요합니다. 이 문서에 설명된 일부 프로세스는 이해하기 쉽도록 간소화되었으며 최종 운영 핸드북의 자세한 단계로 사용하면 안 됩니다. 구성에 대한 작업 핸드북을 만들려는 경우 프로세스를 테스트 및 실행하고 특정 구성과 관련된 프로세스를 문서화해야 합니다. 

운영 데이터베이스에서 가장 중요한 측면 중 하나는 치명적인 이벤트로부터 해당 데이터베이스를 보호하는 것입니다. 이러한 이벤트의 원인은 자연 재해에서 단순한 사용자 오류에 이르기까지 다양합니다.

특정 시점(예: 누군가가 중요한 데이터를 삭제하기 전에)으로 복원하는 기능을 사용하여 데이터베이스를 백업하면 중단이 발생하기 전의 상태에 가능한 한 가깝게 복원할 수 있습니다.

두 가지 백업 복원 하는 이러한 기능을 달성 하기 위해 수행 해야 합니다.

- 데이터베이스 백업: 전체, 증분 또는 차등 백업
- 트랜잭션 로그 백업

애플리케이션 수준에서 수행되는 전체 데이터베이스 백업 외에도 스토리지 스냅숏으로 백업을 수행할 수 있습니다. 저장소 스냅숏은 트랜잭션 로그 백업을 대체하지 않습니다. 트랜잭션 로그 백업은 데이터베이스를 특정 시점으로 복원하거나 이미 커밋된 트랜잭션에서 로그를 비우는 데 중요합니다. 하지만 저장소 스냅숏은 데이터베이스의 롤포워드 이미지를 신속하게 제공하여 복구를 가속화할 수 있습니다. 

Azure(큰 인스턴스)의 SAP HANA는 두 가지 백업 및 복원 옵션을 제공합니다.

- DIY(Do It Yourself) 충분 한 디스크 공간이 있는지를 확인 한 후 다음 디스크 백업 메서드 중 하나를 사용 하 여 전체 데이터베이스 및 로그 백업을 수행 합니다. HANA 대규모 인스턴스 단위에 연결된 볼륨이나 Azure VM(가상 머신)에 설정된 NFS(네트워크 파일 공유)에 직접 백업할 수 있습니다. 후자의 경우 고객은 Azure에서 Linux VM을 설정하고, VM에 Azure Storage를 연결하고, 해당 VM에 구성된 NFS 서버를 통해 스토리지를 공유합니다. HANA 큰 인스턴스 단위에 직접 연결된 볼륨에 대해 백업을 수행하는 경우, (Azure Storage를 기반으로 하는 NFS 공유를 내보내는 Azure VM을 설정한 후에) 백업을 Azure 스토리지 계정에 복사해야 합니다. Azure 백업 자격 증명 모음 또는 Azure 콜드 저장소를 사용할 수도 있습니다. 

   또 다른 옵션은 백업을 Azure Storage 계정에 복사한 후에 저장하기 위해 타사 데이터 보호 도구를 사용하는 것입니다. DIY 백업 옵션은 준수 및 감사 목적으로 인해 장기간 저장되어야 하는 데이터에 필요할 수 있습니다. 모든 경우에 백업은 VM 및 Azure Storage를 통해 표시되는 NFS 공유로 복사됩니다.

- 인프라 백업 및 복원 기능 Azure(대규모 인스턴스)의 SAP HANA 기본 인프라가 제공하는 백업 및 복원 기능을 사용할 수도 있습니다. 이 옵션은 백업 및 빠른 복원에 대한 필요를 충족합니다. 이 섹션의 나머지 부분에서는 HANA 큰 인스턴스와 함께 제공되는 백업 및 복원 기능을 설명합니다. 이 섹션에서는 HANA 대규모 인스턴스가 제공하는 재해 복구 기능에 대한 백업 및 복원의 관계에 대해서도 설명합니다.

> [!NOTE]
>   HANA 큰 인스턴스의 기본 인프라에서 사용하는 스냅숏 기술은 SAP HANA 스냅숏에 대한 종속성을 갖습니다. 현재, SAP HANA 스냅숏은 SAP HANA 다중 테넌트 데이터베이스 컨테이너의 다중 테넌트와 함께 작동하지 않습니다. 테넌트가 하나만 배포되면 SAP HANA 스냅숏이 작동하여 이 메서드를 사용할 수 있습니다.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Azure(큰 인스턴스)의 SAP HANA 저장소 스냅숏 사용

Azure(큰 인스턴스)의 SAP HANA에 기반한 저장소 인프라는 볼륨의 저장소 스냅숏을 지원합니다. 볼륨의 백업 및 복원은 다음 사항을 고려하여 지원됩니다.

- 전체 데이터베이스 백업 대신 저장소 볼륨 스냅숏을 자주 사용합니다.
- /hana/data 및 /hana/shared(/usr/sap 포함) 볼륨을 통해 스냅숏을 트리거하는 경우 SAP HANA 스냅숏이 저장소 스냅숏을 실행하기 전에 스냅숏 기술이 SAP HANA 스냅숏을 시작합니다. 이 SAP HANA 스냅숏은 저장소 스냅숏을 복구한 후에 최종 로그 복원에 대한 설치 지점입니다. 찾기가 성공 하려면 HANA 스냅숏이 활성 HANA 인스턴스가 필요 합니다.  HSR 시나리오에서 저장소 스냅숏은 HANA 스냅숏을 수행할 수 없는 현재의 보조 노드에서 지원되지 않습니다.
- 저장소 스냅숏이 성공적으로 실행된 후에 SAP HANA 스냅숏이 삭제됩니다.
- 트랜잭션 로그 백업은 자주 수행되며 /hana/logbackups 볼륨 또는 Azure에 저장됩니다. 트랜잭션 로그 백업을 포함하는 /hana/logbackups 볼륨을 트리거하여 별도의 스냅숏을 생성할 수 있습니다. 이 경우에 HANA 스냅숏을 실행할 필요가 없습니다.
- 시간에 특정 시점으로 데이터베이스를 복원 해야, 요청 Azure 복원에는 Microsoft Azure 지원 (프로덕션 작동 중단) 또는 SAP HANA를 특정 저장소 스냅숏으로 합니다. 예를 들어 샌드박스 시스템을 원래 상태로 복원하는 계획이 있습니다.
- 저장소 스냅숏에 포함된 SAP HANA 스냅숏은 저장소 스냅숏을 만든 후에 실행되고 저장된 트랜잭션 로그 백업에 적용하는 오프셋 지점입니다.
- 이러한 트랜잭션 로그 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원합니다.

다음과 같은 세 가지 볼륨 클래스를 대상으로 지정하여 저장소 스냅숏을 수행할 수 있습니다.

- /hana/data 및 /hana/shared(/usr/sap 포함)에 대해 결합된 스냅숏. 이 스냅숏을 만들려면 저장소 스냅숏에 대한 준비로 SAP HANA 스냅숏을 만들어야 합니다. SAP HANA 스냅숏은 데이터베이스가 저장소 관점에서 일관된 상태에 있도록 합니다. 설치 지점인 복원 프로세스를 위한 것입니다.
- /hana/logbackups에 대한 별도 스냅숏.
- 운영 체제 파티션

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)에서 최신 스냅숏 스크립트와 설명서를 다운로드하세요. 스냅숏 스크립트 패키지를 다운로드 하는 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), 하나는 제공 하는 기능에 대 한 PDF 설명서는 3 개의 파일을 가져옵니다. 진행 하는 장의 지침에 따라 '스냅숏 도구 가져오기' 경우 해야 도구 집합을 다운로드 합니다.

## <a name="storage-snapshot-considerations"></a>저장소 스냅숏 고려 사항

>[!NOTE]
>저장소 스냅숏은 HANA 큰 인스턴스 단위에 할당된 저장소 공간을 사용합니다. 저장소 스냅숏을 예약하는 경우 다음과 같은 측면과 유지할 저장소 스냅숏의 수를 고려해야 합니다. 

Azure(큰 인스턴스)의 SAP HANA에 대한 저장소 스냅숏의 구체적인 역학은 다음과 같습니다.

- 만들어진 시점에서 특정 저장소 스냅숏은 작은 저장소를 사용합니다.
- 데이터 콘텐츠가 변경되고 저장소 볼륨에서 SAP HANA 데이터 파일의 콘텐츠가 변경되면 스냅숏은 데이터 변경 사항은 물론 원래 블록 콘텐츠도 저장해야 합니다.
- 결과적으로 저장소 스냅숏의 크기가 증가합니다. 스냅숏이 오래 존재할수록 저장소 스냅숏은 커집니다.
- 저장소 스냅샷의 수명 동안 SAP HANA 데이터베이스 볼륨에 변경 내용이 많을수록 저장소 스냅샷이 소진하는 공간이 커집니다.

Azure(큰 인스턴스)의 SAP HANA는 SAP HANA 데이터 및 로그 볼륨에 대해 고정된 볼륨 크기로 제공됩니다. 이러한 볼륨의 스냅숏을 수행하면 볼륨 공간이 줄어듭니다. 저장소 스냅숏을 예약할 시기를 결정 해야 합니다. 또한 저장소 볼륨의 공간 소비를 모니터링하고 저장하는 스냅숏의 수를 관리해야 합니다. 대량의 데이터를 가져오거나 HANA 데이터베이스에 대해 다른 중요한 변경을 수행할 때 저장소 스냅숏을 사용하지 않도록 설정할 수 있습니다. 


다음 섹션에서는 일반적인 권장 사항을 비롯한 이러한 스냅숏을 수행하는 방법에 대한 정보를 제공합니다.

- 하드웨어가 볼륨당 255개의 스냅숏을 유지할 수 있지만 이 숫자보다 훨씬 낮게 유지하는 것이 좋습니다. 권장되는 구성은 250개 이하입니다.
- 스냅숏 저장소를 수행하기 전에 여유 공간을 모니터링하고 추적합니다.
- 사용 가능한 공간에 따라 저장소 스냅숏의 수를 줄입니다. 보관할 스냅숏의 수를 줄이거나 볼륨을 확장할 수 있습니다. 추가 저장소는 1테라바이트 단위로 주문할 수 있습니다.
- SAP 플랫폼 마이그레이션 도구(R3load)를 사용하여 데이터를 SAP HANA로 이동하거나 백업에서 SAP HANA에 데이터를 복원하는 것과 같은 작업 중에는 /hana/data 볼륨에 대해 저장소 스냅숏을 사용하지 않도록 설정합니다. 
- SAP HANA 테이블을 크게 재구성하는 동안 저장소 스냅숏을 가능하면 사용하지 않아야 합니다.
- 저장소 스냅숏은 Azure(대규모 인스턴스)에서 SAP HANA의 재해 복구 기능을 활용하기 위한 필수 구성 요소입니다.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>셀프 서비스 저장소 스냅숏을 사용하기 위한 필수 구성 요소

스냅숏 스크립트가 성공적으로 실행되도록 하려면 Perl이 HANA 대규모 인스턴스 서버의 Linux 운영 체제에 설치되어 있는지 확인합니다. Perl은 HANA 큰 인스턴스 단위에 미리 설치되어 제공됩니다. Perl 버전을 확인하려면 다음 명령을 사용합니다.

`perl -v`

![이 명령을 실행하면 공개 키가 복사됩니다.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>저장소 스냅숏 설정

HANA 대규모 인스턴스를 사용하여 저장소 스냅숏을 설정하려면 다음과 같은 단계를 수행합니다.
1. Perl이 HANA 큰 인스턴스 서버의 Linux 운영 체제에 설치되어 있는지 확인합니다.
1. /etc/ssh/ssh\_config를 수정하여 _MACs hmac-sha1_ 줄을 추가합니다.
1. 적용할 수 있는 경우 실행하는 SAP HANA 인스턴스마다 마스터 노드에서 SAP HANA 백업 사용자 계정을 만듭니다.
1. 모든 SAP HANA 큰 인스턴스 서버에 SAP HANA HDB 클라이언트를 설치합니다.
1. 각 지역의 첫 번째 SAP HANA 큰 인스턴스 서버에서 스냅숏 생성을 제어하는 기본 저장소 인프라에 액세스하기 위한 공개 키를 만듭니다.
1. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)의 스크립트 및 구성 파일을 SAP HANA 설치의 **hdbsql** 위치에 복사합니다.
1. 적절한 고객 사양에 필요한 대로 *HANABackupDetails.txt* 파일을 수정합니다.

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)에서 최신 스냅숏 스크립트와 설명서를 다운로드하세요. 위에 나와 있는 자세한 단계는, 참조 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

### <a name="consideration-for-mcod-scenarios"></a>MCOD 시나리오에 대한 고려 사항
하나의 HANA 대규모 인스턴스 단위에 여러 SAP HANA 인스턴스가 있는 [MCOD 시나리오](https://launchpad.support.sap.com/#/notes/1681092)를 실행하는 경우, 각 SAP HANA 인스턴스에 별도의 저장소 볼륨을 프로비전했습니다. MDC 및 기타 고려 사항에 대 한 내용은 확인 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) 장에 **'기억해 야 할 중요 한 내용이'** 합니다.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1단계: SAP HANA HDB 클라이언트 설치

Azure 대규모 인스턴스의 SAP HANA에 설치된 Linux 운영 체제는 백업 및 재해 복구를 위해 SAP HANA 저장소 스냅숏을 실행하는 데 필요한 폴더와 스크립트를 포함합니다. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)에서 최신 릴리스를 확인하세요. 스크립트의 최신 릴리스 버전 4.0입니다. 각 스크립트마다 동일한 주 릴리스 내의 다른 부 릴리스를 가질 수 있습니다.

SAP HANA를 설치하는 동안 HANA 대규모 인스턴스 단위에 SAP HANA HDB 클라이언트를 설치하는 것은 사용자의 책임입니다.

### <a name="step-2-change-the-etcsshsshconfig"></a>2단계: /etc/ssh/ssh\_config 변경

이 단계에서 최신 버전에 대 한 확인에서 자세히 설명 되어 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) 챕터에 **'저장소와의 통신 사용**


### <a name="step-3-create-a-public-key"></a>3단계: 공개 키 만들기

HANA 대규모 인스턴스 테넌트의 저장소 스냅숏 인터페이스에 대한 액세스할 수 있도록 설정하려면 공개 키를 통한 로그인 프로시저를 설정해야 합니다. 테넌트에 있는 첫 번째 Azure의 SAP HANA(대규모 인스턴스) 서버에서 저장소 인프라에 액세스하는 데 사용할 공개 키를 만듭니다. 공개 키를 사용하면 저장소 스냅숏 인터페이스에 로그인하는 데 암호가 필요하지 않습니다. 공개 키를 만들면 암호 자격 증명을 유지할 필요 없습니다. 정확한 단계는 공용을 생성 하는 방법에 설명 된 키 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) 챕터에 **'저장소와의 통신 사용**


### <a name="step-4-create-an-sap-hana-user-account"></a>4단계: SAP HANA 사용자 계정 만들기

SAP HANA 스냅숏 만들기를 시작하려면 저장소 스냅숏 스크립트에서 사용할 수 있는 SAP HANA에 사용자 계정을 만들어야 합니다. 이를 위해 SAP HANA Studio 내에 SAP HANA 사용자 계정을 만듭니다. 사용자는 MDC에 대한 SID 데이터베이스가 아니라 SYSTEMDB 아래에 만들어야 합니다. 단일 컨테이너 환경에서 사용자는 테 넌 트 데이터베이스에 생성 됩니다. 이 계정에는 다음 권한이 있어야 합니다. **Backup 관리** 및 **카탈로그 읽기**. 사용자 설정 및 사용자를 사용 하는 정확한 단계를 읽어보세요 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 챕터에 **'SAP HANA와의 통신 사용**


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5단계: SAP HANA 사용자 계정 권한 부여

이 단계에서는 스크립트가 런타임 시 암호를 제출하지 않아도 되도록 사용자가 만든 SAP HANA 사용자 계정에 권한을 부여합니다. SAP HANA 명령 `hdbuserstore`을 사용하면 하나 이상의 SAP HANA 노드에 저장되는 SAP HANA 사용자 키를 만들 수 있습니다. 사용자 키를 사용하면 스크립팅 프로세스 내에서 암호를 관리하지 않고도 SAP HANA에 액세스할 수 있습니다. 스크립팅 프로세스는 이 아티클의 뒤에서 설명하겠습니다.

>[!IMPORTANT]
>스냅숏 명령 실행은 동일한 사용자 컨텍스트를 사용 하 여 이러한 구성 명령을 실행 합니다. 그렇지 않은 경우 스냅숏 명령을 제대로 작동 수 없습니다.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6단계: 스냅숏 스크립트 가져오기, 스냅숏 구성, 구성 및 연결 테스트

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)에서 최신 버전의 스크립트를 다운로드하십시오. 스크립트 설치 하고자 하는 방식으로 스크립트의 릴리스 4.0 majorly 변경 되었습니다. 정확한 세부 정보를 참조 하세요 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) 챕터에 **'SAP HANA와의 통신 사용**

명령의 정확한 순서에 대 한 장에서 **'스냅숏 도구 (기본값)를 쉽게 설치'** 문서의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 기본 설치의 사용량을 사용 하는 것이 좋습니다. 버전에서 업그레이드 하려는 경우 섹션을 확인 하는 3.x에서 4.0 **'기존 설치를 업그레이드 합니다.'** 의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 4.0 도구 집합을 제거 하는 것에 대 한의 지침을 따릅니다 **'의 스냅숏 도구 제거'** 에 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

에 설명 된 단계를 실행할 것을 잊지 마세요 **'의 스냅숏 도구 설치를 완료'** 의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

다른 스크립트 및 파일의 목적은 설치 될 때와 나열 되 고에 자세히 설명 **'도구란 이러한 스냅숏?'** 문서의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

스냅숏 도구를 구성 하기 전에 올바르게 구성 했는지 HANA 백업 위치 및 설정에 설명 된 대로 있는지 확인 **' SAP HANA 구성 '** 문서의 [Microsoft 도구를 SAP HANA에 대 한 스냅숏 Azure에서](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

스냅숏 도구 집합의 구성에서 자세히 설명 되어 **'구성 파일 HANABackupCustomerDetails.txt'** 문서의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

#### <a name="testing-connectivity-with-sap-hana"></a>SAP HANA를 사용 하 여 연결 테스트

모든 구성 데이터를 *HANABackupCustomerDetails.txt* 파일에 추가한 후에 HANA 인스턴스 데이터에 대한 구성이 올바른지 확인합니다. `testHANAConnection` 스크립트를 사용합니다. 이것은 SAP HANA 강화 또는 스케일 아웃 구성과 별개입니다.

자세한 내용은 참조 하십시오 **testHANAConnection-SAP HANA와의 연결 확인'** 문서의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

#### <a name="testing-storage-connectivity"></a>저장소 연결 테스트

다음 테스트 단계에서는 *HANABackupCustomerDetails.txt* 구성 파일에 추가한 데이터를 기준으로 저장소에 대한 연결을 확인한 다음, 테스트 스냅숏으로 실행합니다. 실행 하기 전에 `azure_hana_backup` 명령,이 테스트를 실행 해야 합니다. 이 테스트에 대 한 명령의 시퀀스에 포함 된 **'저장소-testStorageSnapshotConnection 사용 하 여 연결을 확인 합니다.'** 문서의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

저장소 가상 머신 인터페이스에 로그인이 성공하면 스크립트는 2단계로 진행되고 테스트 스냅숏을 만듭니다. SAP HANA의 3노드 스케일 아웃 구성에 대한 출력은 다음과 같습니다.

스크립트를 사용 하 여 테스트 스냅숏을 성공적으로 실행 된, 실제 저장소 스냅숏을 예약으로 진행할 수 있습니다. 성공하지 못했다면 계속 진행하기 전에 문제를 조사합니다. 테스트 스냅숏은 첫 번째 실제 스냅숏이 완료될 때까지 유지되어야 합니다.


### <a name="step-7-perform-snapshots"></a>7단계: 스냅숏 수행

준비 단계가 완료 되 면 구성 하 고 실제 저장소 스냅숏을 예약할 시작할 수 있습니다. 예약할 스크립트는 SAP HANA 강화 및 규모 확장 구성에 작동합니다. 백업 스크립트의 주기적인 일반 실행의 경우 cron 유틸리티를 사용하여 스크립트를 예약합니다. 

정확한 명령 구문 및 기능에 대 한 **'수행 스냅숏 백업-azure_hana_backup'** 문서의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.  

`azure_hana_backup` 스크립트를 실행하면 다음 세 가지 단계에서 저장소 스냅숏이 만들어집니다.

1. SAP HANA 스냅숏을 실행
1. 저장소 스냅숏을 실행
1. 저장소 스냅숏을 실행하기 전에 만든 SAP HANA 스냅숏을 제거

스크립트를 실행하려면 복사된 HDB 실행 파일 폴더에서 호출합니다. 

보존 기간은 스크립트를 실행할 경우 매개 변수로 제출되는 스냅숏 개수와 함께 관리됩니다. 저장소 스냅숏이 적용되는 기간은 실행 기간 및 스크립트 실행 시 매개 변수로 제출된 스냅숏 개수입니다. 유지되는 스냅숏 개수가 스냅숏 스크립트의 호출에서 매개 변수로 이름이 지정된 수를 초과하면 새 스냅숏을 실행하기 전에 동일한 레이블의 가장 오래된 저장소가 삭제됩니다. 호출의 마지막 매개 변수로 지정한 숫자는 유지되는 스냅숏 개수를 제어하는 데 사용할 수 있는 숫자입니다. 이 숫자를 사용하여 스냅숏에 사용되는 디스크 공간을 간접적으로 제어할 수도 있습니다. 


## <a name="snapshot-strategies"></a>스냅숏 전략
다른 유형에 대한 스냅숏 빈도는 HANA 대규모 인스턴스 재해 복구 기능을 사용하는지 여부에 따라 달라집니다. 이 기능은 저장소 스냅숏을 사용합니다. 그러려면 저장소 스냅숏의 빈도 및 실행 기간에 대한 특별한 권장 사항이 필요할 수 있습니다. 

다음 고려 사항 및 권장 사항에서는 HANA 대규모 인스턴스가 제공하는 재해 복구 기능을 사용하지 *않는다*고 가정합니다. 대신, 저장소 스냅숏을 사용하여 백업을 보유하고 지난 30일 동안의 지정 시간 복구를 제공할 수 있습니다. 스냅숏 수와 공간이 제한된다고 가정할 경우 고객은 다음과 같은 요구 사항을 고려했습니다.

- 지정 시간 복구에 대한 복구 시간.
- 사용된 공간.
- 재해로부터 잠재적 복구를 위한 복구 지점 및 복구 시간 목표.
- 디스크에 대한 HANA 전체 데이터베이스 백업의 최종 실행. 디스크 또는 **backint** 인터페이스에 대한 전체 데이터베이스 백업이 수행될 때마다 저장소 스냅숏의 실행에 실패합니다. 저장소 스냅숏을 기반으로 전체 데이터베이스 백업을 실행하려는 경우 이 시간 동안 저장소 스냅숏의 실행이 비활성화되어 있는지 확인하십시오.
- 볼륨당 스냅숏 수(250개로 제한)

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

HANA 큰 인스턴스의 재해 복구 기능을 사용하지 않는 고객의 경우 스냅숏 기간이 덜 빈번합니다. 이러한 경우에 고객은 12시간 또는 24시간 기간으로 /hana/data, /hana/shared(/usr/sap 포함)의 결합된 스냅숏을 수행하고 1달 동안 해당 스냅숏을 유지합니다. 로그 백업 볼륨의 스냅숏도 마찬가지입니다. 그러나 SAP HANA 트랜잭션 로그 백업은 로그 백업 볼륨에 대해 실행 5 ~ 15 분 기간에 발생합니다.

예약된 저장소 스냅숏은 cron를 사용하여 수행하는 것이 가장 좋습니다. 모든 백업 및 재해 복구 요구 사항에 대해 동일한 스크립트를 사용하고, 다양하게 요청된 백업 시간을 일치시키도록 스크립트 입력을 수정합니다. 이러한 스냅숏은 해당 실행 시간(매시간, 12시간, 매일 또는 매주)에 따라 cron에서 모두 다르게 예약됩니다. 

/etc/crontab의 cron 일정 예제는 다음과 같습니다.
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
이전 예에서 시간별로 결합된 스냅숏이 hana/data를 포함 하는 볼륨 및 /hana/shared/SID (/ usr/sap 포함)를 포함 하는 위치입니다. 지난 2일 내에 더 빠른 지정 시간 복구에 이 형식의 스냅숏을 사용합니다. 또한 해당 볼륨에 대한 일별 스냅숏이 있습니다. 따라서 2일 간의 시간별 스냅숏과 4주 간의 일별 스냅숏을 갖습니다. 또한 트랜잭션 로그 백업 볼륨은 매일 백업됩니다. 또한 이러한 백업은 4주 동안 유지됩니다. crontab의 세 번째 줄에 표시된 대로 HANA 트랜잭션 로그의 백업은 5분마다 실행되도록 예약됩니다. 저장소 스냅숏을 실행하는 여러 cron 작업의 시작 시간이 차이를 두기 때문에 해당 스냅숏이 특정 시점에 한꺼번에 실행되지 않습니다. 

Hana/data 및 /hana/shared/SID/usr/sap 등을 포함 하는 볼륨을 포괄 하는 결합된 된 스냅숏을 수행 하는 다음 예제에서는 시간 단위로 위치입니다. 이 스냅숏은 이틀 동안 보관합니다. 트랜잭션 로그 백업 볼륨의 스냅숏은 5분 단위로 실행되고 4시간 동안 유지됩니다. 이전과 마찬가지로, HANA 트랜잭션 로그 파일의 백업은 5분 간격으로 실행되도록 예약됩니다. 트랜잭션 로그 백업 볼륨의 스냅숏은 트랜잭션 로그 백업이 시작된 후 2분 지연되어 수행됩니다. 해당하는 2분 이내에 정상적인 상황에서 SAP HANA 트랜잭션 로그 백업이 완료되어야 합니다. 이전과 마찬가지로, 부팅 LUN이 포함된 볼륨은 저장소 스냅숏에 의해 하루 1번 백업되고 4주 동안 보관됩니다.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

다음 그래픽은 부팅 LUN을 제외하고 이전 예제의 순서를 보여줍니다.

![백업과 스냅숏 간의 관계](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA는 /hana/log 볼륨에 대해 정기적인 쓰기를 수행하여 데이터베이스에 대한 커밋된 변경 내용을 문서화합니다. 정기적으로 SAP HANA는 /hana/data 볼륨에 저장점을 씁니다. crontab에 지정된 대로 SAP HANA 트랜잭션 로그 백업은 5분마다 실행됩니다. 또한 SAP HANA 스냅숏을 /hana/data 및 /hana/shared/SID 볼륨 결합된 된 저장소 스냅숏을 트리거한 결과로 매시간 실행 되도록 표시 됩니다. HANA 스냅숏이 성공한 후 결합된 저장소 스냅숏이 실행됩니다. crontab에 설명된 대로 /hana/logbackup 볼륨의 저장소 스냅숏은 HANA 트랜잭션 로그 백업이 있고 약 2분 후에 5분마다 실행됩니다.

> 

>[!IMPORTANT]
> SAP HANA 트랜잭션 백업에 대한 저장소 스냅숏 사용은 스냅숏이 SAP HANA 로그 백업과 함께 수행된 경우에만 유용합니다. 이러한 트랜잭션 로그 백업은 저장소 스냅숏 사이의 기간에 적용되어야 합니다. 

30일이라는 지정 시간 복구의 사용자에 대한 커밋을 설정한 경우 다음을 수행해야 합니다.

- 극단적인 경우 지난 /hana/data 및 30 일 이전에 /hana/shared/SID 스냅숏 조합된 된 저장소에 액세스 합니다.
- 결합된 저장소 스냅숏 사이의 시간에 해당하는 인접한 트랜잭션 로그 백업이 있어야 합니다. 따라서 트랜잭션 로그 백업 볼륨의 가장 오래된 스냅숏은 30일이 되어야 합니다. 트랜잭션 로그 백업을 Azure Storage에 있는 다른 NFS 공유에 복사하는 경우에는 그렇지 않습니다. 이러한 경우에 해당 NFS 공유에서 오래된 트랜잭션 로그 백업을 끌어올 수 있습니다.

저장소 스냅숏 및 트랜잭션 로그 백업의 최종 저장소 복제를 활용하려면 SAP HANA가 트랜잭션 로그 백업을 쓰는 위치를 변경해야 합니다. 이것은 HANA Studio에서 변경할 수 있습니다. SAP HANA가 전체 로그 세그먼트를 자동으로 백업하지만 확정된 로그 백업 간격을 지정해야 합니다. 재해 복구 옵션을 사용하는 경우 일반적으로 확정된 기간으로 로그 백업을 실행하기 때문에 특히 그렇습니다. 다음과 같은 경우 로그 백업 간격으로 15분이 설정되었습니다.

![SAP HANA Studio에서 SAP HANA 백업 로그 예약](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15분보다 더 자주 수행되는 백업을 선택할 수도 있습니다. 더 잦은 설정은 HANA 대규모 인스턴스의 재해 복구 기능과 결합하여 자주 사용됩니다. 일부 고객은 5분마다 트랜잭션 로그 백업을 수행합니다.  

이전에 데이터베이스를 백업한 적이 없는 경우 최종 단계는 파일 기반 데이터베이스 백업을 수행하여 백업 카탈로그 내에 있어야 하는 단일 백업 항목을 만드는 것입니다. 그렇지 않으면 SAP HANA가 지정된 로그 백업을 시작할 수 없습니다.

![단일 백업 항목을 만드는 파일 기반 백업 수행](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


첫 번째로 성공한 저장소 스냅숏을 실행 한 후 6 단계에서 실행 된 테스트 스냅숏을 삭제 해야 합니다. 읽기 **'제거 테스트 스냅숏을-removeTestStorageSnapshot'** 문서의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) 세부 정보에 대 한 합니다. 


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>디스크 볼륨에서 스냅숏의 개수 및 크기 모니터링

특정 저장소 볼륨에서 스냅숏의 개수 및 스냅숏의 저장소 사용을 모니터링할 수 있습니다. `ls` 명령은 스냅숏 디렉터리 또는 파일을 표시하지 않습니다. 그러나 Linux OS 명령 `du`는 저장소 스냅숏이 동일한 볼륨에 저장되므로 이러한 저장소 스냅숏에 대한 세부 정보를 보여 줍니다. 이 명령은 다음 옵션과 함께 사용할 수 있습니다.

- `du –sh .snapshot`: 이 옵션은 스냅숏 디렉터리 내에서 모든 스냅숏을 제공합니다.
- `du –sh --max-depth=1`: 이 옵션은 **.snapshot** 폴더에 저장된 모든 스냅숏 및 각 스냅숏의 크기를 나열합니다.
- `du –hc`: 이 옵션은 모든 스냅숏에 사용되는 전체 크기를 제공합니다.

이러한 명령을 사용하여 만들고 저장한 스냅숏이 볼륨에서 모든 저장소를 사용하지 않도록 합니다.

>[!NOTE]
>부팅 LUN의 스냅숏은 이전 명령으로 표시되지 않습니다.

### <a name="getting-details-of-snapshots"></a>스냅숏 세부 정보 가져오기
스냅숏에 대한 자세한 내용을 보려면 `azure_hana_snapshot_details` 스크립트를 사용할 수도 있습니다. 이 스크립트는 재해 복구 위치에 활성 서버가 있을 때 어떤 위치에서도 실행될 수 있습니다. 이 스크립트는 스냅숏이 포함된 각 볼륨별로 다음과 같이 세분화된 출력을 제공합니다. 
   * 볼륨의 총 스냅숏 크기
   * 해당 볼륨의 각 스냅숏에 다음 세부 정보가 포함됩니다. 
      - 스냅숏 이름 
      - 만든 시간 
      - 스냅숏 크기
      - 스냅숏 빈도
      - 스냅숏과 연결된 HANA 백업 ID(해당되는 경우)

명령 및 출력 검사는 구문에 대 한 **'스냅숏을-azure_hana_snapshot_details 나열'** 문서의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다. 



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>서버에서 스냅숏 개수 감소

앞에서 설명한 대로 사용자가 저장한 스냅숏의 특정 레이블 수를 줄일 수 있습니다. 스냅숏을 시작하는 명령의 마지막 두 매개 변수는 보존하려는 스냅숏의 수와 레이블입니다.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

앞의 예제에서 스냅숏 레이블은 **dailyhana**이고, 보존할 이 레이블의 스냅숏 개수는 **28**입니다. 디스크 공간 사용량에 응답하는 대로 저장된 스냅숏 수를 줄이려고 할 수 있습니다. 스냅숏 수를 (예를 들어 15개로) 줄이는 가장 좋은 방법은 마지막 매개 변수를 **15**로 설정하여 스크립트를 실행하는 것입니다.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

이 설정으로 스크립트를 실행하면 새 저장소 스냅숏을 포함하여 스냅숏 개수가 15가 됩니다. 15개의 오래된 스냅숏은 삭제되고 15개의 가장 최근 스냅숏은 유지됩니다.

 >[!NOTE]
 > 이 스크립트는 1시간을 초과하는 스냅숏이 있는 경우에만 스냅숏 수를 줄입니다. 스크립트는 1시간 미만인 스냅숏을 삭제하지 않습니다. 이러한 제한 사항은 제공되는 선택적 재해 복구 기능과 관련되어 있습니다.

더 이상 백업 접두사를 사용 하 여 스냅숏 집합을 유지 하려는 경우 **dailyhana** 구문 예제에서 사용 하 여 스크립트를 실행할 수 없습니다 **0** 을 보존 숫자로 합니다. 그러면 해당 레이블과 일치하는 모든 스냅숏이 제거됩니다. 그러나 모든 스냅숏을 제거하면 HANA 대규모 인스턴스 재해 복구 기능에 영향을 미칠 수 있습니다.

특정 스냅숏을 삭제하는 또 다른 옵션은 `azure_hana_snapshot_delete` 스크립트를 사용하는 것입니다. 이 스크립트는 HANA Studio에서 확인된 HANA 백업 ID를 사용하거나 스냅숏 이름 자체를 사용하여 스냅숏 또는 스냅숏 집합을 삭제하도록 디자인되었습니다. 현재, 백업 ID는 **hana** 스냅숏 유형에 대해 생성된 스냅숏에만 연결됩니다. **로그** 및 **부팅** 형식의 스냅숏 백업은 SAP HANA 스냅숏을 수행하지 않습니다. 따라서 해당 스냅숏에는 백업 ID가 없습니다. 스냅숏 이름을 입력하는 경우 입력한 스냅숏 이름과 일치하는 다른 볼륨의 모든 스냅숏을 찾습니다. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

스크립트 세부 정보를 참조 하세요 **스냅숏으로-azure_hana_snapshot_delete 삭제'** 문서의 [Microsoft Azure의 SAP HANA에 대 한 도구를 스냅숏](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)합니다.

**root** 사용자로 스크립트를 실행합니다.

>[!IMPORTANT]
>데이터가 삭제하려는 스냅숏에서만 존재하는 경우 스냅숏을 삭제하면 해당 데이터가 영구적으로 손실됩니다.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>저장소 스냅숏에서 파일 수준 복원

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
스냅숏 형식 **hana** 및 **logs**의 경우 **.snapshot** 디렉터리의 볼륨에서 직접 스냅숏에 액세스할 수 있습니다. 각 스냅숏에 대해 하위 디렉터리가 있습니다. 해당 하위 디렉터리의 스냅숏 시점에 있는 상태인 각 파일을 실제 디렉터리 구조로 복사할 수 있습니다. 스크립트의 현재 버전에서는 한지 **아니요** (장애 조치 중 DR 사이트에서 셀프 서비스 DR의 일부 스크립트 스냅숏 복원을 수행할 수 있습니다) 경우에 셀프 서비스 등록으로 스냅숏 복원에 대 한 제공 된 스크립트를 복원 합니다. 기존의 사용 가능한 스냅숏에서 원하는 스냅숏을 복원하려면 서비스 요청을 열어 Microsoft 운영 팀에 문의해야 합니다.

>[!NOTE]
>단일 파일 복원은 HANA 큰 인스턴스 단위의 유형에 독립적인 부팅 LUN의 스냅숏에 대해 작동하지 않습니다. **.snapshot** 디렉터리는 부팅 LUN에서 노출되지 않습니다. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>가장 최근 HANA 스냅숏으로 복구

프로덕션 중단 시나리오에서 저장소 스냅숏에서 복구하는 프로세스는 Microsoft Azure 지원에서 고객 인시던트로 시작될 수 있습니다. 프로덕션 시스템에서 데이터가 삭제되고 데이터를 검색하는 유일한 방법이 프로덕션 데이터베이스를 복원하는 것인 경우 긴급한 문제입니다.

다른 경우, 지정 시간 복구는 긴급성이 낮고 사전에 계획될 수 있습니다. 우선 순위 플래그를 발생 시키는 대신 Azure에서 SAP HANA를 사용 하 여이 복구를 계획할 수 있습니다. 예를 들어 새로운 개선 패키지를 적용하여 SAP 소프트웨어를 업그레이드하도록 계획하는 경우가 있을 수 있습니다. 이런 경우 개선 패키지를 업그레이드하기 전의 상태를 나타내는 스냅숏으로 되돌려야 합니다.

요청을 보내기 전에 준비해야 합니다. 그런 다음 Azure 팀의 SAP HANA 요청을 처리 하 고 복원된 된 볼륨을 제공할 수 있습니다. 나중에 사용자는 스냅숏을 기반으로 HANA 데이터베이스를 복원할 수 있습니다.

섹션에 설명 된 새 도구 집합을 사용 하 여 복원 된 스냅숏을 가져오도록 가능성 **'스냅숏으로 복원 하는 방법'** 문서의 [수동 복구 가이드에 대 한 Azure의 SAP HANA 저장소 스냅숏에서](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)합니다.

요청에 대해 준비하는 방법을 아래에 나와 있습니다.

1. 복원할 스냅숏을 결정합니다. 달리 지시가 없는 경우 hana/data 볼륨만 복원됩니다. 

1. HANA 인스턴스를 종료합니다.

   ![HANA 인스턴스를 종료합니다.](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 각 HANA 데이터베이스 노드에서 데이터 볼륨을 분리합니다. 데이터 볼륨이 운영 체제에 계속 탑재되는 경우 스냅숏 복원이 실패합니다.
   ![각 HANA 데이터베이스 노드에서 데이터 볼륨 분리](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Azure 지원 요청을 열어서 특정 스냅숏의 복원에 대한 지침을 포함합니다.

   - 복원 중: Azure의 SAP HANA 조정, 검증 및 올바른 저장소 스냅숏이 복원 된 확인 전화 회의 참석 하도록 요청할 수도 있습니다. 

   - 복원한 후에: Azure 서비스에서 SAP HANA 저장소 스냅숏을 복원 되었을 때이 알려 줍니다.

1. 복원 프로세스가 완료되면 모든 데이터 볼륨을 다시 탑재합니다.

   ![모든 데이터 볼륨 다시 탑재](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



파일을 가져오고, 예를 들어 SAP HANA 데이터 저장소 스냅숏에서 복구 하는 또 다른 방법은 문서의 7 단계에 설명 되어 있습니다 [수동 복구 가이드에 대 한 Azure의 SAP HANA 저장소 스냅숏에서](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)합니다.

문서 [수동 복구 가이드에 대 한 Azure의 SAP HANA 저장소 스냅숏에서](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) 스냅숏 백업에서 복원 시퀀스를 보여 줍니다. 복원의 실행에 대 한 해당 문서를 사용 합니다. 

>[!Note]
>7 단계 Microsoft operations에서 복원할 스냅숏을 가져온 경우에 실행할 필요는 없습니다.


### <a name="recover-to-another-point-in-time"></a>다른 지정 시점으로 복구
문서 [수동 복구 가이드에 대 한 Azure의 SAP HANA 저장소 스냅숏에서](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) 섹션에서 시간에 특정 시점으로 복원 시퀀스를 보여 줍니다 **'시간에 데이터베이스를 다음 지정 시점 복구'**. 에 특정 시점으로 복원 실행에 대 한 해당 문서를 사용 합니다. 


## <a name="next-steps"></a>다음 단계
- 참조 [재해 복구 원칙 및 준비](hana-concept-preparation.md)합니다.
