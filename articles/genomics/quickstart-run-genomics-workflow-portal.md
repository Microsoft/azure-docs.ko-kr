---
title: '빠른 시작: 워크플로 실행 - Microsoft Genomics'
description: 빠른 시작은 Azure Blob Storage에 입력 데이터를 로드하고 Microsoft Genomics 서비스를 통해 워크플로를 실행하는 방법을 보여줍니다.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: 7720238bb7e2ff133935b9af545628f744d828d1
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642319"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>빠른 시작: Microsoft Genomics 서비스를 통해 워크플로 실행

이 빠른 시작에서는 Azure Blob Storage 계정에 입력 데이터를 업로드하고 Python Genomics 클라이언트를 사용하여 Microsoft Genomics 서비스를 통해 워크플로를 실행합니다. Microsoft Genomics는 원시 읽기에서 시작하여 정렬된 읽기 및 변형 호출을 생성하여 신속하게 게놈을 처리할 수 있는 2차 분석을 위한 확장형 보안 서비스입니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- `pip`가 설치된 [Python 2.7.12 이상](https://www.python.org/downloads/release/python-2714/) 및 시스템 경로에 추가된 `python`. Microsoft Genomics 클라이언트는 Python 3과 호환되지 않습니다. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>설정: Azure Portal에서 Microsoft Genomics 계정 만들기

Microsoft Genomics 계정을 만들려면 Azure Portal에서 [Genomics 계정 만들기](https://portal.azure.com/#create/Microsoft.Genomics)로 이동합니다. Azure 구독이 아직 없으면 Microsoft Genomics 계정을 만들기 전에 하나 만드세요. 

![Azure Portal의 Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Azure Portal의 Microsoft Genomics")

앞의 이미지와 같이 다음 정보로 Genomics 계정을 구성합니다. 

 |**설정**          |  **제안 값**  | **필드 설명** |
 |:-------------       |:-------------         |:----------            |
 |Subscription         | 구독 이름|귀하의 Azure 서비스에 대한 청구 단위입니다. 구독에 대한 자세한 내용은 [구독](https://account.azure.com/Subscriptions)을 참조하세요. |      
 |Resource group       | MyResourceGroup       |  리소스 그룹을 사용하여 여러 Azure 리소스(스토리지 계정, 게놈 계정 등)를 단일 그룹으로 그룹화하여 간단히 관리할 수 있습니다. 자세한 내용은 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)을 참조하세요. 유효한 리소스 그룹 이름은 [이름 지정 규칙](/azure/architecture/best-practices/resource-naming)을 참조하세요. |
 |계정 이름         | MyGenomicsAccount     |고유한 계정 식별자를 선택합니다. 유효한 이름은 [이름 지정 규칙](/azure/architecture/best-practices/resource-naming)을 참조하세요. |
 |위치                   | 미국 서부 2                    |    미국 서부 2, 서유럽 및 동남 아시아에서 서비스를 사용할 수 있습니다. |

상단 메뉴 모음에서 **알림**을 선택하여 배포 프로세스를 모니터링할 수 있습니다.

![알림](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "공지")

Microsoft Genomics에 대한 자세한 내용은 [Microsoft Genomics란?](overview-what-is-genomics.md)을 참조하세요.

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>설정: Microsoft Genomics Python 클라이언트 설치

로컬 환경에 Python과 Microsoft Genomics Python 클라이언트 `msgen`을 모두 설치해야 합니다. 

### <a name="install-python"></a>Python 설치

Microsoft Genomics Python 클라이언트는 Python 2.7.12 이상 2.7.xx 버전과 호환됩니다. 제안되는 버전은 2.7.14입니다. [여기](https://www.python.org/downloads/release/python-2714/)에서 다운로드를 찾을 수 있습니다. 

> [!IMPORTANT]
> Python 3.x는 Python 2.7.xx와 호환되지 않습니다.  `msgen`은 Python 2.7 애플리케이션입니다. `msgen`를 실행하는 경우 활성 Python 환경에서 Python 2.7.xx 버전을 사용해야 합니다. Python 3.x 버전에서 `msgen`을 사용하려고 하면 오류가 발생할 수 있습니다.

### <a name="install-the-microsoft-genomics-python-client-msgen"></a>Microsoft Genomics Python 클라이언트 `msgen` 설치

Python `pip`를 사용하여 Microsoft Genomics 클라이언트 `msgen`을 설치합니다. 다음 지침은 이미 시스템 경로에 Python2.x가 있다고 가정합니다. 설치된 `pip`가 인식되지 않는 문제가 발생하면 Python 및 스크립트 하위 폴더를 시스템 경로에 추가해야 합니다.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

`msgen`을 시스템 수준 바이너리로서 설치하지 않고 시스템 수준 Python 패키지를 수정하지 않으려면 `pip`에 `–-user` 플래그를 사용합니다.
패키지 기반 설치 또는 setup.py를 사용하면 필수 패키지가 모두 설치됩니다.

### <a name="test-msgen-python-client"></a>`msgen` Python 클라이언트 테스트
Microsoft Genomics 클라이언트를 테스트하려면 Genomics 계정에서 구성 파일을 다운로드합니다. Azure Portal의 왼쪽 상단에서 **모든 서비스**를 선택하여, Genomics 계정으로 이동한 다음, Genomics 계정을 검색하여 선택합니다.

![Azure Portal에서 Microsoft Genomics 찾기](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Azure Portal에서 Microsoft Genomics 찾기")

방금 만든 Genomics 계정을 선택하고 **액세스 키**로 이동하여 구성 파일을 다운로드합니다.

![Microsoft Genomics에서 구성 파일 다운로드](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Microsoft Genomics에서 구성 파일 다운로드")

Microsoft Genomics Python 클라이언트가 다음 명령으로 작동하는지 테스트합니다.

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Microsoft Azure Storage 계정 만들기 
Microsoft Genomics 서비스는 입력 파일이 Azure Storage 계정에 블록 Blob으로 저장될 것으로 예상합니다. 또한 출력 파일을 Azure Storage 계정의 사용자 지정 컨테이너에 블록 Blob으로 기록합니다. 입력 및 출력 파일은 서로 다른 Storage 계정에 있을 수 있습니다.
Azure Storage 계정에 이미 데이터가 있다면 Genomics 계정과 동일한 위치에 있는지 확인하면 됩니다. 그렇지 않으면 Microsoft Genomics 서비스를 실행할 때 송신 비용이 발생합니다. Azure 스토리지 계정이 아직 없는 경우 계정을 만들어서 데이터를 업로드해야 합니다. 스토리지 계정은 무엇이며 어떤 서비스를 제공하는지 등 Azure 스토리지 계정에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)에서 확인할 수 있습니다. Azure 스토리지 계정을 만들려면 Azure Portal에서 [스토리지 계정 만들기](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM)로 이동합니다.  

![스토리지 계정 만들기 페이지](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "스토리지 계정 만들기 페이지")

앞의 이미지에 보이는 다음과 같은 정보를 사용하여 스토리지 계정을 구성합니다. 스토리지 계정에 대해 대부분의 표준 옵션을 사용하되, 계정은 일반적인 용도가 아닌 Blob Storage 전용으로 지정합니다. Blob Storage 용량은 다운로드 및 업로드 시 2-5배 빨라질 수 있습니다.  기본 배포 모델인 Azure Resource Manager를 권장합니다.  

 |**설정**          |  **제안 값**  | **필드 설명** |
 |:-------------------------       |:-------------         |:----------            |
 |Subscription         | Azure 구독 |구독에 대한 자세한 내용은 [구독](https://account.azure.com/Subscriptions)을 참조하세요. |      
 |Resource group       | MyResourceGroup       |  Genomics 계정과 동일한 리소스 그룹을 선택할 수 있습니다. 유효한 리소스 그룹 이름은 [명명 규칙](/azure/architecture/best-practices/resource-naming)을 참조하세요. |
 |스토리지 계정 이름         | MyStorageAccount     |고유한 계정 식별자를 선택합니다. 유효한 이름은 [명명 규칙](/azure/architecture/best-practices/resource-naming)을 참조하세요. |
 |위치                  | 미국 서부 2                  | Genomics 계정과 동일한 위치를 사용하여 송신 비용을 낮추고 대기 시간을 줄입니다.  | 
 |성능                  | 표준                   | 기본값은 표준입니다. 표준 및 프리미엄 스토리지 계정에 대한 자세한 내용은 [Microsoft Azure Storage 소개](https://docs.microsoft.com/azure/storage/common/storage-introduction)를 참조하세요.    |
 |계정 종류       | BlobStorage       |  Blob Storage는 다운로드 및 업로드 시 범용보다 2-5배 빨라질 수 있습니다. |
 |복제                  | 로컬 중복 스토리지                  | 로컬 중복 스토리지는 스토리지 계정을 만든 지역의 데이터 센터 내에서 데이터를 복제합니다. 자세한 내용은 [ Azure Storage 복제 ](https://docs.microsoft.com/azure/storage/common/storage-redundancy)를 참조하세요.    |
 |액세스 계층                  | 핫                   | 핫 액세스는 스토리지 계정의 개체가 더 자주 액세스됨을 나타냅니다.    |

그런 다음, **검토 + 만들기**를 선택하여 스토리지 계정을 만듭니다. Genomics 계정을 만들 때처럼 상단 메뉴 모음에서 **알림**을 선택하여 배포 프로세스를 모니터링할 수 있습니다. 

## <a name="upload-input-data-to-your-storage-account"></a>스토리지 계정에 입력 데이터 업로드

Microsoft Genomics 서비스는 페어드 엔드 리드(fastq 또는 bam 파일)가 입력 파일로 필요합니다. 자신의 데이터를 업로드하거나 공개적으로 사용 가능한 샘플 데이터를 사용하여 탐색할 수 있습니다. 공개적으로 사용 가능한 샘플 데이터를 사용하려면 여기에서 호스팅합니다.

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

스토리지 계정 내에서 입력 데이터 용으로 하나의 Blob 컨테이너를 만들고 출력 데이터용으로 두 번째 Blob 컨테이너를 만들어야 합니다.  입력 데이터를 입력 Blob 컨테이너에 업로드합니다. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), [BlobPorter](https://github.com/Azure/blobporter) 또는 [ AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 비롯한 다양한 도구를 사용하여 이 작업을 수행할 수 있습니다. 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-msgen-python-client"></a>`msgen` Python 클라이언트를 사용하여 Microsoft Genomics 서비스를 통해 워크플로 실행

Microsoft Genomics 서비스를 통해 워크플로를 실행하려면 *config.txt* 파일을 편집하여 데이터에 대한 입력 및 출력 스토리지 컨테이너를 지정합니다.
Genomics 계정에서 다운로드한 *config.txt* 파일을 엽니다. 지정해야 하는 섹션은 구독 키와 아래에 있는 여섯 개의 항목, 스토리지 계정 이름, 입력 및 출력에 대한 키 및 컨테이너 이름입니다. Azure Portal에서 스토리지 계정의 **액세스 키**로 이동하거나 Azure Storage Explorer에서 직접 이 정보를 찾을 수 있습니다.  

![Genomics 구성](./media/quickstart-run-genomics-workflow-portal/genomics-config.PNG "Genomics 구성")

GATK4를 실행하려는 경우 `process_name` 매개 변수를 `gatk4`로 설정합니다.

기본적으로 Genomics 서비스는 VCF 파일을 출력합니다. VCF 출력 대신 gVCF 출력을 원하는 경우(GATK 3.x에서 `-emitRefConfidence` 및 GATK 4.x에서 `emit-ref-confidence`와 같음) 위의 그림과 같이 `emit_ref_confidence` 매개 변수를 *config.txt*에 추가하고 `gvcf`로 설정합니다.  다시 VCF 출력으로 변경하려면 *config.txt* 파일에서 제거하거나 `emit_ref_confidence` 매개 변수를 `none`으로 설정합니다. 

`bgzip`은 vcf 또는 gvcf 파일을 압축하는 도구이고, `tabix`는 압축된 파일의 인덱스를 만듭니다. 기본적으로 Genomics 서비스는 ".g.vcf" 출력에 대해 `bgzip`을 실행한 후 `tabix`를 실행하지만, ".vcf" 출력에 대해서는 두 도구를 기본적으로 실행하지 않습니다. 서비스를 실행하면 ".gz"(bgzip 출력) 및 ".tbi"(tabix 출력) 파일이 생성됩니다. 인수는 부울이며, ".vcf" 출력에 대해서는 기본적으로 false로 설정되고 ".g.vcf" 출력에 대해서는 기본적으로 true로 설정됩니다. 명령줄에서 사용하려면 `-bz` 또는 `--bgzip-output`을 `true`(bgzip 및 tabix 실행) 또는 `false`로 지정합니다. 이 인수를 *config.txt* 파일에 사용하려면 `bgzip_output: true` 또는 `bgzip_output: false`을 파일에 추가합니다.

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-using-the-msgen-python-client"></a>`msgen` Python 클라이언트를 사용하여 Microsoft Genomics 서비스에 워크플로 제출

Microsoft Genomics Python 클라이언트를 사용하여 다음 명령으로 워크플로를 제출합니다.

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

다음 명령을 사용하여 워크플로의 상태를 볼 수 있습니다. 
```python
msgen list -f c:\temp\config.txt 
```

워크플로가 완료되면 직접 구성한 출력 컨테이너에서 Azure 스토리지 계정의 출력 파일을 볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 샘플 입력 데이터를 Azure 스토리지에 업로드했고 `msgen` Python 클라이언트를 통해 워크플로를 Microsoft Genomics 서비스에 제출했습니다. Microsoft Genomics 서비스에서 사용할 수 있는 기타 입력 파일 형식에 대한 자세한 내용은 다음 페이지를 참조하세요. [쌍으로 연결된 FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [여러 개의 FASTQ 또는 BAM](quickstart-input-multiple.md) "Genomics Tutorial.ipynb" 파일을 다운로드하고 [Jupyter](https://docs.microsoft.com/azure/notebooks/tutorial-create-run-jupyter-notebook)와 같은 Notebook 리더에서 이 파일을 열고 실행하면 [Azure Notebooks 예제](https://aka.ms/genomicsnotebook)를 사용하여 이 자습서를 살펴볼 수 있습니다.
