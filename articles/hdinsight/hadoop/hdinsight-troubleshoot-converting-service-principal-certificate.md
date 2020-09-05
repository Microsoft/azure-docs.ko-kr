---
title: 인증서 콘텐츠를 base-64-Azure HDInsight로 변환
description: Azure HDInsight에서 서비스 주체 인증서 콘텐츠를 base-64로 인코딩된 문자열 형식으로 변환
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f4974d9e9a2ff3b63b36e45d406a016078bb290
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008168"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>HDInsight에서 서비스 사용자 인증서 콘텐츠를 base-64로 인코딩된 문자열 형식으로 변환

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

입력이 기본이 아닌 64 문자, 두 개 이상의 패딩 문자 또는 패딩 문자 사이에 공백이 아닌 문자를 포함 하 고 있기 때문에 올바른 Base-64 문자열이 아닙니다. 라는 오류 메시지가 표시 됩니다.

## <a name="cause"></a>원인

PowerShell 또는 Azure 템플릿 배포를 사용 하 여 Data Lake를 기본 또는 추가 저장소로 사용 하 여 클러스터를 만드는 경우 Data Lake 저장소 계정에 액세스 하기 위해 제공 된 서비스 주체 인증서 콘텐츠가 64 형식으로 제공 됩니다. Pfx 인증서 콘텐츠를 base-64로 인코딩된 문자열로 잘못 변환 하면이 오류가 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

Pfx 형식으로 서비스 사용자 인증서를 사용 하는 경우 (샘플 서비스 주체 만들기 단계는 [여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) 참조) 다음 PowerShell 명령 또는 c # 코드 조각을 사용 하 여 인증서 콘텐츠를 base-64 형식으로 변환 합니다.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
