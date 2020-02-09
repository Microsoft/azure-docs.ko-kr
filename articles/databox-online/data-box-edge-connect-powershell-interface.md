---
title: Windows PowerShell 인터페이스를 통해 Microsoft Azure Data Box Edge 장치에 연결 및 관리 | Microsoft Docs
description: Windows PowerShell 인터페이스를 통해 Data Box Edge에 연결 하 고 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613859"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure Data Box Edge 장치 관리

Azure Data Box Edge 솔루션을 사용 하면 데이터를 처리 하 고 네트워크를 통해 Azure에 보낼 수 있습니다. 이 문서에서는 Data Box Edge 장치에 대한 구성 및 관리 작업을 설명 합니다. Azure Portal, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용 하 여 장치를 관리할 수 있습니다.

이 문서에서는 PowerShell 인터페이스를 사용 하 여 수행 하는 작업을 중점적으로 설명 합니다.

이 문서에는 다음과 같은 절차가 포함 되어 있습니다.

- PowerShell 인터페이스에 연결
- 지원 패키지 만들기
- 인증서 업로드
- 장치 다시 설정
- 장치 정보 보기
- 계산 로그 가져오기
- 계산 모듈 모니터링 및 문제 해결

## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>인증서 업로드

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

IoT Edge 인증서를 업로드 하 여 IoT Edge 장치와이 장치에 연결할 수 있는 다운스트림 장치 간에 보안 연결을 설정할 수도 있습니다. 설치 해야 하는 다음과 같은 세 가지 IoT Edge 인증서 (*pem* 형식)가 있습니다.

- 루트 CA 인증서 또는 소유자 CA
- 디바이스 CA 인증서
- 장치 키 인증서

다음 예에서는이 cmdlet을 사용 하 여 IoT Edge 인증서를 설치 하는 방법을 보여 줍니다.

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
이 cmdlet을 실행 하면 네트워크 공유에 대한 암호를 제공 하 라는 메시지가 표시 됩니다.

인증서에 대한 자세한 내용은 인증서 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) 또는 [게이트웨이에서 인증서 설치](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)를 참조 하세요.

## <a name="view-device-information"></a>장치 정보 보기
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>디바이스 재설정

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>계산 로그 가져오기

장치에서 계산 역할이 구성 된 경우 PowerShell 인터페이스를 통해 계산 로그를 가져올 수도 있습니다.

1. [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. `Get-AzureDataBoxEdgeComputeRoleLogs`를 사용 하 여 장치에 대한 계산 로그를 가져옵니다.

    다음 예에서는이 cmdlet을 사용 하는 방법을 보여 줍니다.

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    다음은 cmdlet에 사용 되는 매개 변수에 대한 설명입니다.
    - `Path`: 계산 로그 패키지를 만들려는 공유의 네트워크 경로를 제공 합니다.
    - `Credential`: 네트워크 공유에 대한 사용자 이름을 제공 합니다. 이 cmdlet을 실행 하는 경우 공유 암호를 제공 해야 합니다.
    - `FullLogCollection`:이 매개 변수를 사용 하면 로그 패키지에 모든 계산 로그가 포함 됩니다. 기본적으로 로그 패키지에는 로그 하위 집합만 포함 되어 있습니다.

## <a name="monitor-and-troubleshoot-compute-modules"></a>계산 모듈 모니터링 및 문제 해결

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>원격 세션 종료

원격 PowerShell 세션을 종료 하려면 PowerShell 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Data Box Edge](data-box-edge-deploy-prep.md)를 배포합니다.
