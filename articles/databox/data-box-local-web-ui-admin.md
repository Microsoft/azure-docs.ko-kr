---
title: 로컬 웹 UI를 사용 하 여 Azure Data Box/Azure Data Box Heavy 관리
description: 로컬 웹 UI를 사용 하 여 Data Box 및 Data Box Heavy 장치를 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/10/2020
ms.author: alkohli
ms.openlocfilehash: 7cac14708adecbdf3c809e3a9656d25c727d80e3
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206144"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>로컬 웹 UI를 사용 하 여 Data Box를 관리 하 고 Data Box Heavy

이 문서에서는 Data Box 및 Data Box Heavy 장치에서 수행할 수 있는 몇 가지 구성 및 관리 작업에 대해 설명 합니다. Azure Portal UI와 장치의 로컬 웹 UI를 통해 Data Box 및 Data Box Heavy 장치를 관리할 수 있습니다. 이 문서에서는 로컬 웹 UI를 사용하여 수행할 수 있는 작업을 중점적으로 설명합니다.

Data Box 및 Data Box Heavy에 대 한 로컬 웹 UI는 장치의 초기 구성에 사용 됩니다. 로컬 웹 UI를 사용 하 여 장치를 종료 하거나 다시 시작 하 고, 진단 테스트를 실행 하 고, 소프트웨어를 업데이트 하 고, 복사 로그를 보고, Microsoft 지원에 대 한 로그 패키지를 생성할 수도 있습니다. 독립적인 노드가 두 개인 Data Box Heavy 장치에서는 장치의 각 노드에 해당 하는 두 개의 개별 로컬 웹 Ui에 액세스할 수 있습니다.

이 문서에는 다음 자습서가 포함되어 있습니다.

- 지원 패키지 생성
- 디바이스 종료 또는 다시 시작
- BOM (자재 자료) 또는 매니페스트 파일 다운로드
- 디바이스의 사용 가능한 용량 확인
- 체크섬 유효성 검사 건너뛰기

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="generate-support-package"></a>지원 패키지 생성

디바이스 문제가 발생하는 경우 시스템 로그에서 지원 패키지를 만들 수 있습니다. Microsoft 지원에서는 이 패키지를 사용하여 문제를 해결합니다. 지원 패키지를 생성 하려면 다음 단계를 수행 합니다.

1. 로컬 웹 UI에서 **지원 담당자** 로 이동 하 여 지원 **패키지 만들기**를 선택 합니다.

    ![지원 패키지 만들기 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. 지원 패키지가 수집됩니다. 이 작업은 몇 분 정도 걸립니다.

    ![지원 패키지 만들기 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. 지원 패키지 만들기가 완료 되 면 **지원 패키지 다운로드**를 선택 합니다.

    ![지원 패키지 만들기 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. 다운로드 위치를 찾아서 선택합니다. 폴더를 열어 내용을 확인합니다.

    ![지원 패키지 만들기 5](media/data-box-local-web-ui-admin/create-support-package-5.png)

## <a name="shut-down-or-restart-your-device"></a>디바이스 종료 또는 다시 시작

로컬 웹 UI를 사용 하 여 장치를 종료 하거나 다시 시작할 수 있습니다. 다시 시작하기 전에 호스트에서 공유를 오프라인으로 전환한 후 디바이스를 다시 시작하는 것이 좋습니다. 이렇게 하면 데이터 손상 가능성이 최소화 됩니다. 디바이스를 종료할 때 데이터 복사가 진행 중이지 않은지 확인합니다.

장치를 종료 하려면 다음 단계를 수행 합니다.

1. 로컬 웹 UI에서 **종료 또는 다시 시작**으로 이동합니다.
2. **종료**를 선택합니다.

    ![Data Box 종료 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. 확인 메시지가 표시 되 면 **확인** 을 선택 하 여 계속 합니다.

    ![Data Box 종료 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

디바이스가 종료되면 전면 패널의 전원 단추를 사용하여 디바이스를 켭니다.

Data Box를 다시 시작하려면 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **종료 또는 다시 시작**으로 이동합니다.
2. **다시 시작**을 선택합니다.

    ![Data Box 다시 시작 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. 확인 메시지가 표시 되 면 **확인** 을 선택 하 여 계속 합니다.

   디바이스가 종료되었다가 다시 시작됩니다.

## <a name="download-bom-or-manifest-files"></a>BOM 또는 매니페스트 파일 다운로드

BOM 또는 매니페스트 파일은 Data Box 또는 Data Box Heavy에 복사 된 파일의 목록을 포함 합니다. 이러한 파일은 장치를 배송할 준비를 할 때 가져오기 순서에 대해 생성 됩니다.

시작 하기 전에 다음 단계를 수행 하 여 가져오기 순서로 BOM 또는 매니페스트 파일을 다운로드 합니다.

1. 장치의 로컬 웹 UI로 이동 합니다. 장치가 **배송 준비** 단계를 완료 했는지 확인 합니다. 디바이스 준비가 완료되면 디바이스 상태가 **배송 준비 완료**로 표시됩니다.

    ![디바이스 배송 준비 완료](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. **파일 목록 다운로드** 를 선택 하 여 Data Box에 복사 된 파일 목록을 다운로드 합니다.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. 파일 탐색기에서, 디바이스에 연결하는 데 사용된 프로토콜 및 사용된 Azure Storage 유형에 따라 별도의 파일 목록이 생성되는 것을 볼 수 있습니다.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![스토리지 유형 및 연결 프로토콜에 대한 파일](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   다음 표는 파일 이름을 Azure Storage 유형 및 사용된 연결 프로토콜로 매핑합니다.

    |파일 이름  |Azure Storage 유형  |사용된 연결 프로토콜 |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |블록 Blob         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |페이지 Blob         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Azure 파일         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |페이지 Blob         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |블록 Blob         |REST         |

이 목록을 사용하여 Data Box가 Azure 데이터 센터로 반환된 후 Azure Storage 계정에 업로드된 파일을 확인합니다. 샘플 매니페스트 파일은 아래에 표시되어 있습니다.

> [!NOTE]
> Data Box Heavy에는 장치의 두 노드에 해당 하는 두 개의 파일 목록 (BOM 파일)이 표시 됩니다.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

이 파일은 Data Box 또는 Data Box Heavy에 복사 된 모든 파일의 목록을 포함 합니다. 이 파일의 *crc64* 값은 해당 파일에 대해 생성된 체크섬과 관련이 있습니다.

## <a name="view-available-capacity-of-the-device"></a>디바이스의 사용 가능한 용량 확인

디바이스 대시보드를 통해 디바이스에서 사용 가능한 용량과 사용한 용량을 확인할 수 있습니다.

1. 로컬 웹 UI에서 **대시보드 보기**로 이동합니다.
2. **연결 및 복사** 아래에 디바이스의 사용 가능한 공간과 사용한 공간이 표시됩니다.

    ![사용 가능한 용량 확인](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>체크섬 유효성 검사 건너뛰기

기본적으로는 배송을 준비할 때 데이터의 체크섬이 생성됩니다. 드물지만 파일 형식(작은 파일 크기)에 따라 성능이 저하될 수 있습니다. 이러한 경우에는 체크섬을 건너뛸 수 있습니다.

발송 준비 중에 체크섬 계산은 가져오기 주문에 대해서만 수행 되며 내보내기 주문에 대해서는 수행 되지 않습니다. 

성능이 매우 낮은 경우가 아니면 체크섬은 사용하는 것이 좋습니다.

1. 장치의 로컬 웹 UI의 오른쪽 위 모서리에서 **설정**으로 이동 합니다.

    ![체크섬 사용 안 함](media/data-box-local-web-ui-admin/disable-checksum.png)

2. 체크섬 유효성 검사를 **사용 안 함**으로 설정합니다.
3. **적용**을 선택합니다.

> [!NOTE]
> 체크섬 계산 건너뛰기 옵션은 Azure Data Box 잠금 해제 된 경우에만 사용할 수 있습니다. 장치가 잠겨 있으면이 옵션이 표시 되지 않습니다.

## <a name="enable-smb-signing"></a>SMB 서명 사용

SMB (서버 메시지 블록) 서명은 SMB를 사용 하는 통신을 패킷 수준에서 디지털 서명할 수 있는 기능입니다. 이 서명은 전송 중인 SMB 패킷을 수정 하는 공격을 방지 합니다.

SMB 서명과 관련 된 자세한 내용은 [서버 메시지 블록 서명 개요](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing)를 참조 하세요.

Azure 장치에서 SMB 서명을 사용 하도록 설정 하려면 다음을 수행 합니다.

1. 장치의 로컬 웹 UI의 오른쪽 위 모서리에서 **설정**을 선택 합니다.

    ![설정 열기](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **사용** SMB 서명.

    ![SMB 서명 사용](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. **적용**을 선택합니다.
4. 로컬 웹 UI에서 **종료 또는 다시 시작**으로 이동합니다.
5. **다시 시작**을 선택합니다.

## <a name="enable-tls-11"></a>TLS 1.1 사용

기본적으로 Azure Data Box는 TSL 1.1 보다 더 안전 하므로 암호화에 TLS (Transport Layer Security) 1.2를 사용 합니다. 그러나 사용자 또는 클라이언트가 브라우저를 사용 하 여 TLS 1.2을 지원 하지 않는 데이터에 액세스 하는 경우 TLS 1.1을 사용 하도록 설정할 수 있습니다.

TLS와 관련 된 자세한 내용은 [Azure Data Box Gateway 보안](../databox-online/data-box-gateway-security.md)을 참조 하세요.

Azure 장치에서 TLS 1.1을 사용 하도록 설정 하려면 다음을 수행 합니다.

1. 장치의 로컬 웹 UI의 오른쪽 위 모서리에서 **설정**을 선택 합니다.

    ![설정 열기](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **사용** TLS 1.1.

    ![TLS 1.1 사용](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. **적용**을 선택합니다.
4. 로컬 웹 UI에서 **종료 또는 다시 시작**으로 이동합니다.
5. **다시 시작**을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal를 통해 Data Box 및 Data Box Heavy를 관리](data-box-portal-admin.md)하는 방법을 알아봅니다.
