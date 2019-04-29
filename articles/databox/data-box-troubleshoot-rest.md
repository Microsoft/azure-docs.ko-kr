---
title: REST 인터페이스를 사용 하 여에 대 한 문제 해결 azure Data Box | Microsoft Docs
description: REST 인터페이스를 통해 데이터 복사 되 면 Azure Data Box에 표시 되는 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: ee2820d78e95924e09a0219753f87d6910c0e736
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782959"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Azure 데이터 상자 Blob 저장소와 관련 된 문제 해결

문제를 해결 하는 방법은이 문서에서는 세부 정보 데이터를 복사 하 여 Data Box에 REST 인터페이스를 통해 데이터 상자 Blob storage를 사용 하는 경우 표시 될 수 있습니다. 데이터 상자 Blob storage Python에 대 한 다른 응용 프로그램 또는 Azure Storage 탐색기, AzCopy 또는 Azure Storage 라이브러리 등 클라이언트 라이브러리를 사용 하 여 사용 하는 경우 이러한 문제 화면입니다.

## <a name="errors-seen-in-azure-storage-explorer"></a>Azure Storage 탐색기에 표시 되는 오류

이 섹션에서는 데이터 상자 Blob storage를 사용 하 여 Azure Storage 탐색기를 사용 하는 경우 직면 한 문제 중 일부를 자세히 설명 합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|자식 리소스를 검색할 수 없습니다. HTTP 헤더 중 하나에 대 한 값을 올바른 형식이 아닙니다.|**편집할** 메뉴에서 **대상 Azure Stack Api**합니다. <br>Azure Storage 탐색기를 다시 시작 합니다.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |확인 끝점 이름을 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 이 경로에서 호스트 파일에 추가 됩니다. <li>`C:\Windows\System32\drivers\etc\hosts` Windows, 또는 </li><li> `/etc/hosts` linux.</li>|
|자식 리소스를 검색할 수 없습니다. <br>자체 서명 된 인증서 세부 정보: |Azure Storage 탐색기에 장치에 대 한 SSL 인증서를 가져옵니다. <li>Azure portal에서 인증서를 다운로드 합니다. 자세한 내용은 [인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)합니다.</li><li>**편집** 메뉴에서 **SSL 인증서** 선택한 후 **인증서 가져오기**합니다.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>AzCopy에 대 한 Windows에서 표시 되는 오류

이 섹션에서는 데이터 상자 Blob storage를 사용 하 여 Windows에 AzCopy를 사용 하는 경우 직면 한 문제 중 일부를 자세히 설명 합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|이 오류를 표시 하기 전에 잠시 중지 AzCopy 명령 표시 됩니다. <br>디렉터리 https://를 열거 하지 못했습니다... 원격 이름이 확인할 수 없습니다. `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|확인 끝점 이름을 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 에서 호스트 파일에 추가 됩니다: `C:\Windows\System32\drivers\etc\hosts`합니다.|
|이 오류를 표시 하기 전에 잠시 중지 AzCopy 명령 표시 됩니다. <br>원본 위치를 구문 분석 오류입니다. 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대 한 트러스트 관계를 설정 하지 못했습니다.|시스템의 인증서 저장소에 장치에 대 한 SSL 인증서를 가져옵니다. 자세한 내용은 [인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)합니다.|


## <a name="errors-seen-in-azcopy-for-linux"></a>Linux 용 AzCopy에서 표시 되는 오류

이 섹션에서는 데이터 상자 Blob storage를 사용 하 여 Linux 용 AzCopy를 사용 하는 경우 직면 한 문제 중 일부를 자세히 설명 합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|20 분 동안이 오류를 표시 하기 전에 중지 AzCopy 명령 표시 됩니다. <br>원본 위치를 구문 분석 오류 `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`합니다. 이러한 장치 또는 주소|확인 끝점 이름을 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 에서 호스트 파일에 추가 됩니다: `/etc/hosts`합니다.|
|20 분 동안이 오류를 표시 하기 전에 중지 AzCopy 명령 표시 됩니다. <br>원본 위치를 구문 분석 하는 오류는 중... SSL 연결을 설정할 수 없습니다.|시스템의 인증서 저장소에 장치에 대 한 SSL 인증서를 가져옵니다. 자세한 내용은 [인증서 다운로드](data-box-deploy-copy-data-via-rest.md#download-certificate)합니다.|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Python 용 Azure Storage 라이브러리에 표시 되는 오류

이 섹션에서는 데이터 복사를 위해 Linux 클라이언트를 사용하는 경우 Data Box Disk를 배포하는 동안 발생하는 몇 가지 주요 문제를 자세히 설명합니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|HTTP 헤더 중 하나에 대 한 값을 올바른 형식이 아닙니다. |데이터 상자에 설치 된 Python 용 Microsoft Azure Storage 라이브러리 버전이 지원 되지 않습니다. 지원 되는 버전에 대 한 Azure 데이터 상자 Blob 저장소 요구 사항을 참조 하세요.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …|Python을 실행 하기 전에 REQUESTS_CA_BUNDLE 환경 변수 SSL Base64로 인코딩된 인증서 파일의 경로를 설정 합니다 (참조 하는 방법 [인증서 다운로드]()). <br>예를 들면 다음과 같습니다.<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>또는 시스템의 인증서 저장소에 인증서를 추가 하 고 해당 저장소의 경로를이 환경 변수를 설정 합니다. <br> 예를 들어, Ubuntu의 경우에: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>일반 오류

이러한 오류는 모든 응용 프로그램에 특정 되지 않습니다.

|오류 메시지  |권장 작업 |
|---------|---------|
|연결 시간이 초과 됩니다. |Data Box 장치에 로그인 하 고 잠겨 있지 않은지 확인 합니다. 언제 든 지 장치 다시 시작, 로그인 사용자가 될 때까지 잠금이 유지 합니다.|

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 합니다 [데이터 상자 Blob 저장소에 대 한 시스템 요구 사항](data-box-system-requirements-rest.md)합니다.
