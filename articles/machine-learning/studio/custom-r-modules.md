---
title: 사용자 지정 R 모듈 정의
titleSuffix: Azure Machine Learning Studio
description: 이 토픽에서는 Azure Machine Learning Studio에서 사용자 지정 R 모듈을 작성하여 배포하는 방법을 설명합니다. 사용자 지정 R 모듈의 정의와 이를 정의하는 데 사용되는 파일을 설명합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 0dec86eff9b9df70514be6f32f3aad60bfb311ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751220"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio"></a>Azure Machine Learning Studio에 사용할 사용자 지정 R 모듈 정의

이 토픽에서는 Azure Machine Learning Studio에서 사용자 지정 R 모듈을 작성하여 배포하는 방법을 설명합니다. 사용자 지정 R 모듈의 정의와 이를 정의하는 데 사용되는 파일을 설명합니다. 또한 이러한 파일을 생성하여 Machine Learning 작업 영역에서 모듈을 정의하는 파일을 구조화하고 배포용 모듈을 등록하는 방법을 보여 줍니다. 그런 다음 사용자 지정 모듈의 정의에 사용되는 요소 및 특성에 대해 자세히 설명합니다. 보조 기능과 파일 및 여러 출력을 사용하는 방법도 소개합니다. 



## <a name="what-is-a-custom-r-module"></a>사용자 지정 R 모듈이란?
**사용자 지정 모듈**은 작업 영역에 업로드하고 Azure Machine Learning Studio 실험의 일부로 실행할 수 있는 사용자 정의 모듈입니다. 사용자 지정 R 모듈은 사용자 정의 R 함수를 실행하는 **사용자 지정 모듈** 입니다. **R** 은 통계학자 및 데이터 과학자가 알고리즘을 구현하는 데 널리 사용되는 통계 컴퓨팅 및 그래픽용 프로그래밍 언어입니다. 현재, R은 사용자 지정 모듈에서 지원되는 유일한 언어이지만 향후 릴리스에서는 추가 언어에 대한 지원이 예정되어 있습니다.

사용자 지정 모듈은 다른 모든 모듈처럼 사용할 수 있다는 점에서 Azure Machine Learning Studio에서 **첫 번째 클래스 상태**로 유지됩니다. 다른 모듈과 함께 실행하거나, 게시된 실험이나 시각화에 포함할 수 있습니다. 사용자는 모듈에 의해 구현되는 알고리즘, 사용할 입력 및 출력 포트, 모델링 매개 변수 및 기타 여러 런타임 동작을 제어할 수 있습니다. 사용자 지정 모듈을 포함한 실험을 공유하기 쉽도록 Azure AI 갤러리에 게시할 수도 있습니다.

## <a name="files-in-a-custom-r-module"></a>사용자 지정 R 모듈의 파일
사용자 지정 R 모듈은 최소한 다음 두 개의 파일을 포함하는 .zip 파일로 정의됩니다.

* 모듈에 의해 노출되는 R 함수를 구현하는 **소스 파일**
* 사용자 지정 모듈 인터페이스를 설명하는 **XML 정의 파일**

사용자 지정 모듈에서 액세스할 수 있는 기능을 제공하는 추가 보조 파일을 .zip 파일에 포함할 수도 있습니다. 이 옵션은 빠른 시작 예제에 이어지는 참조 섹션 **XML 정의 파일의 요소**의 **인수** 부분에서 설명됩니다.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>빠른 시작 예: 사용자 지정 R 모듈 정의, 패키지 및 등록
이 예제에서는 사용자 지정 R 모듈에 필요한 파일을 생성하고 이를 zip 파일에 패키지한 다음 Machine Learning 작업 영역에 모듈을 등록하는 방법을 보여 줍니다. 예제 zip 패키지 및 샘플 파일은 [CustomAddRows.zip 파일 다운로드](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409)에서 다운로드할 수 있습니다.

## <a name="the-source-file"></a>원본 파일
두 데이터 세트(데이터 프레임)의 행(관찰)을 연결하는 데 사용되는 **행 추가** 모듈의 표준 구현을 수정하는 **사용자 지정 행 추가** 모듈의 예제를 고려해 보겠습니다. 표준 **행 추가** 모듈은 `rbind` 알고리즘을 사용하여 두 번째 입력 데이터 세트의 행을 첫 번째 입력 데이터 세트의 끝에 추가합니다. 사용자 지정 `CustomAddRows` 함수는 두 데이터 세트를 허용할 뿐만 아니라 부울 스왑 매개 변수도 추가 입력으로 허용합니다. 스왑 매개 변수가 **FALSE**로 설정된 경우 표준 구현과 동일한 데이터 집합을 반환합니다. 그러나 스왑 매개 변수가 **TRUE**인 경우에는 함수는 대신 첫 번째 입력 데이터 세트의 행을 두 번째 데이터 세트의 끝에 추가합니다. **사용자 지정 행 추가** 모듈에 의해 노출되는 R `CustomAddRows` 함수의 구현을 포함하는 CustomAddRows.R 파일은 다음 R 코드를 포함합니다.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>XML 정의 파일
이 `CustomAddRows` 함수를 Azure Machine Learning Studio 모듈로 노출하려면 XML 정의 파일을 만들어 **사용자 지정 행 추가** 모듈의 표시 및 동작 방식을 지정해야 합니다. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


XML 파일의 **Input** 및 **Arg** 요소에 대한 **id** 특성 값은 CustomAddRows.R 파일에 있는 R 코드의 함수 매개 변수 이름(이 예제의 경우 *dataset1*, *dataset2* 및 *swap*)과 정확히 일치해야 합니다. 마찬가지로, **Language** 요소의 **entryPoint** 특성 값은 R 스크립트의 함수 이름(이 예제의 경우 *CustomAddRows*)과 정확히 일치해야 합니다. 

반면, **Output** 요소의 **id** 특성은 R 스크립트의 변수에 해당하지 않습니다. 둘 이상의 출력이 필요한 경우 XML 파일에 선언된 *Output* 요소와 **동일한 순서로** 결과가 배치된 목록이 R 함수에서 반환됩니다.

### <a name="package-and-register-the-module"></a>모듈 패키지 및 등록
이 두 파일을 *CustomAddRows.R*과 *CustomAddRows.xml*로 저장한 다음 두 파일을 *CustomAddRows.zip* 파일로 함께 압축합니다.

이 파일을 Machine Learning 작업 영역에 등록하려면 Machine Learning 스튜디오의 작업 영역으로 이동한 후 아래쪽에서 **+새로 만들기** 단추를 클릭하고 **모듈 -> Zip 패키지**를 선택하여 새 **사용자 지정 행 추가** 모듈을 업로드합니다.

![Zip 업로드](./media/custom-r-modules/upload-from-zip-package.png)

이제 Machine Learning 실험에서 **사용자 지정 행 추가** 모듈에 액세스할 준비가 완료되었습니다.

## <a name="elements-in-the-xml-definition-file"></a>인수
### <a name="module-elements"></a>Module 요소
**Module** 요소는 XML 파일에서 사용자 지정 모듈을 정의하는 데 사용됩니다. 여러 **module** 요소를 사용하여 여러 모듈을 하나의 XML 파일에 정의할 수 있습니다. 작업 영역의 각 모듈은 이름이 고유해야 합니다. 기존 사용자 지정 모듈과 동일한 이름으로 사용자 지정 모듈을 등록하면 기존 모듈이 새 모듈로 바뀝니다. 그러나 사용자 지정 모듈을 기존 Azure Machine Learning Studio 모듈과 동일한 이름으로 등록할 수 있습니다. 이 경우 모듈 팔레트의 **사용자 지정** 범주에 표시됩니다.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


**Module** 요소 내에서 두 개의 추가 선택적 요소를 지정할 수 있습니다.

* 모듈에 포함된 **Owner** 요소  
* 모듈에 대한 빠른 도움말 및 Machine Learning UI에서 모듈 위에 마우스 커서를 놓으면 표시되는 텍스트를 포함하는 **Description** 요소.

Module 요소의 문자 제한에 대한 규칙:

* **Module** 요소의 **name** 특성 값은 64자를 초과할 수 없습니다. 
* **Description** 요소의 내용은 128자를 초과할 수 없습니다.
* **Owner** 요소의 내용은 32자를 초과할 수 없습니다.

모듈의 결과는 결정적이거나 비결정적일 수 있습니다.** 기본적으로 모든 모듈은 결정적인 것으로 간주됩니다. 즉, 입력된 매개 변수 및 데이터 집합을 변경 되지 않는 들어 모듈이 반환 되어야 동일한 결과 주어진 경우는 함수에 실행 될 때. 이 동작에 따라 Azure Machine Learning Studio는 매개 변수 또는 입력 데이터가 변경된 경우에만 결정적으로 표시된 모듈을 다시 실행합니다. 캐시된 결과 반환은 훨씬 더 빠른 실험의 실행도 제공합니다.

현재 날짜 또는 시간을 반환하는 RAND 또는 함수와 같은 비결정적인 함수도 있습니다. 모듈이 비결정적 함수를 사용하는 경우 옵션 **isDeterministic** 특성을 **FALSE**로 설정하여 모듈을 비결정적으로 지정할 수 있습니다. 이는 모듈이 모듈 입력 및 매개 변수가 변경되지 않은 경우에도 실험이 실행될 때마다 다시 실행되는 것을 보장합니다. 

### <a name="language-definition"></a>언어 정의
XML 정의 파일의 **Language** 요소는 사용자 지정 모듈 언어를 지정하는 데 사용됩니다. 현재 지원되는 언어는 R뿐입니다. **sourceFile** 특성 값은 모듈을 실행할 때 호출할 함수가 포함된 R 파일의 이름이어야 합니다. 이 파일은 zip 패키지의 일부여야 합니다. **entryPoint** 특성 값은 호출되는 함수의 이름이며, 소스 파일에 정의된 유효한 함수와 일치해야 합니다.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>포트
사용자 지정 모듈의 입력 및 출력 포트는 XML 정의 파일의 **Ports** 섹션의 자식 요소에 지정됩니다. 이러한 요소의 순서에 따라 사용자의 레이아웃(UX)이 결정됩니다. XML 파일의 **Ports** 요소에 나열된 첫 번째 자식 **input** 또는 **output**은 Machine Learning UX의 맨 왼쪽 입력 포트가 됩니다.
각 입력 및 출력 포트에는 사용자가 Machine Learning UI에서 포트 위에 마우스 커서를 놓으면 표시되는 텍스트를 지정하는 선택적 **Description** 자식 요소가 있을 수 있습니다.

**포트 규칙**:

* **입력 및 출력 포트** 의 최대 개수는 각각 8개입니다.

### <a name="input-elements"></a>Input 요소
입력 포트를 통해 사용자는 R 함수 및 작업 영역으로 데이터를 전달할 수 있습니다. 입력 포트에 지원되는 **데이터 형식** 은 다음과 같습니다. 

**DataTable:** 이 형식은 data.frame으로 R 함수에 전달됩니다. 실제로 Machine Learning에서 지원되고 **DataTable** 과 호환되는 모든 형식(예: CSV 파일 또는 ARFF 파일)은 자동으로 data.frame으로 변환됩니다. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

각 **DataTable** 입력 포트와 연결된 **id** 특성에는 고유 값이 있어야 하며, 이러한 값은 R 함수의 해당 명명된 매개 변수와 일치해야 합니다.
실험에서 입력으로 전달되지 않는 선택적 **DataTable** 포트에는 R 함수로 전달되는 **NULL** 값이 있으며, 선택적 zip 포트는 입력이 연결되지 않은 경우 무시됩니다. **isOptional** 특성은 **DataTable** 및 **Zip** 형식에 둘 다에 대해 선택 사항이며, 기본적으로 *false*입니다.

**Zip:** 사용자 지정 모듈에서는 zip 파일을 입력으로 사용할 수 있습니다. 이 입력은 함수의 R 작업 디렉터리에 압축이 해제됩니다.

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

사용자 지정 R 모듈의 경우 Zip 포트에 대한 ID는 R 함수의 매개 변수와 일치할 필요가 없습니다. zip 파일은 R 작업 디렉터리에 자동으로 추출되기 때문입니다.

**입력 규칙:**

* **Input** 요소의 **id** 특성 값은 유효한 R 변수 이름이어야 합니다.
* **Input** 요소의 **id** 특성 값은 64자 이내여야 합니다.
* **Input** 요소의 **name** 특성 값은 64자 이내여야 합니다.
* **Description** 요소의 내용은 128자 이내여야 합니다.
* **Input** 요소의 **type** 특성 값은 *Zip* 또는 *DataTable*이어야 합니다.
* **Input** 요소의 **isOptional** 특성 값은 필수가 아니지만(지정하지 않을 경우 기본적으로 *false*) 지정한 경우 *true* 또는 *false*여야 합니다.

### <a name="output-elements"></a>Output 요소
**표준 출력 포트:** 출력 포트는 R 함수의 반환 값에 매핑되어 후속 모듈에 사용됩니다. *DataTable* 은 현재 지원되는 유일한 표준 출력 포트 형식입니다. 향후 *Learners* 및 *Transforms*이 지원될 예정입니다. *DataTable* 출력은 다음과 같이 정의됩니다.

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

사용자 지정 R 모듈의 출력은 **id** 특성 값이 R 스크립트의 항목에 해당할 필요는 없지만 고유해야 합니다. 단일 모듈 출력의 경우 R 함수의 반환 값은 *data.frame*이어야 합니다. 지원되는 데이터 형식의 개체를 두 개 이상 출력하려면 XML 정의 파일에 해당 출력 포트를 지정하고 개체를 목록으로 반환해야 합니다. 출력 개체는 반환되는 목록에서 개체가 배치된 순서를 나타내도록 왼쪽으로 오른쪽으로 출력 포트에 할당됩니다.

예를 들어 **사용자 지정 행 추가** 모듈을 새로 조인된 *dataset*과 함께 원래 두 데이터 세트 *dataset1* 및 *dataset2*를 출력(왼쪽부터 순서대로 *dataset*, *dataset1*, *dataset2*)하도록 수정하려면 CustomAddRows.xml 파일에서 다음과 같이 출력 포트를 정의합니다.

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


그런 다음 ‘CustomAddRows.R’에서 올바른 순서로 목록에 있는 개체 목록을 반환합니다.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**시각화 출력:** R 그래픽 디바이스의 출력 및 콘솔 출력을 표시하는 *Visualization* 형식의 출력 포트를 지정할 수도 있습니다. 이 포트는 R 함수 출력의 일부가 아니며 다른 출력 포트 형식의 순서와 간섭되지 않습니다. 사용자 지정 모듈에 시각화 포트를 추가하려면 해당 **type** 특성 값이 *Visualization*인 **Output** 요소를 추가합니다.

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**출력 규칙:**

* **Output** 요소의 **id** 특성 값은 유효한 R 변수 이름이어야 합니다.
* **Output** 요소의 **id** 특성 값은 32자 이내여야 합니다.
* **Output** 요소의 **name** 특성 값은 64자 이내여야 합니다.
* **Output** 요소의 **type** 특성 값은 *Visualization*이어야 합니다.

### <a name="arguments"></a>인수
**Arguments** 요소에 정의된 모듈 매개 변수를 통해 R 함수에 추가 데이터를 전달할 수 있습니다. 이러한 매개 변수는 모듈을 선택한 경우 Machine Learning UI의 맨 오른쪽 속성 창에 표시됩니다. 인수는 지원되는 형식 중 하나이거나, 필요한 경우 사용자 지정 열거형을 만들 수 있습니다. **Ports** 요소와 마찬가지로, **Arguments** 요소에는 매개 변수 이름 위에 마우스를 놓으면 표시되는 텍스트를 지정하는 선택적 **Description** 요소가 있을 수 있습니다.
defaultValue, minValue 및 maxValue와 같은 모듈의 선택적 속성을 **Properties** 요소의 특성으로 인수에 추가할 수 있습니다. **Properties** 요소의 유효한 속성은 인수 형식에 따라 다르며, 아래 섹션의 지원되는 인수 형식에 설명되어 있습니다. **"true"** 로 설정된 **isOptional** 속성을 가진 인수는 사용자가 값을 입력하지 않아도 됩니다. 인수에 값을 입력하지 않으면 진입점 함수에 전달되지 않습니다. 예: 선택적인 진입점 함수의 인수는 함수에 의해 명시적으로 처리되어야 합니다(예: 진입점 함수 정에서 할당된 NULL의 기본값). 선택적 인수는 사용자가 값을 제공하는 경우 다른 인수 제약 조건(즉, min 또는 max)을 적용합니다.
입력 및 출력과 마찬가지로 각 매개 변수에는 고유한 ID 값이 연결되어 있어야 합니다. 이 빠른 시작 예제에서 연결된 ID/매개 변수는 *swap*입니다.

### <a name="arg-element"></a>Arg 요소
모듈 매개 변수는 XML 정의 파일에서 **Arguments** 섹션의 **Arg** 자식 요소를 사용하여 정의됩니다. **Ports** 섹션의 하위 요소와 마찬가지로 **Arguments** 섹션의 매개 변수 순서는 UX 레이아웃을 정의합니다. 매개 변수는 XML 파일에 정의된 순서대로 위에서 아래로 UI에 표시됩니다. Machine Learning에서 지원하는 매개 변수 형식은 다음과 같습니다. 

**int** – 정수(32비트) 형식 매개 변수입니다.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *선택적 속성*: **min**, **max**, **default** 및 **isOptional**

**double** – 실수(Double) 형식 매개 변수입니다.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *선택적 속성*: **min**, **max**, **default** 및 **isOptional**

**bool** – UX에서 확인란으로 표시되는 부울 매개 변수입니다.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *선택적 속성*: **default** - 설정하지 않은 경우 false

**string**: 표준 문자열입니다.

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *선택적 속성*: **default** 및 **isOptional**

**ColumnPicker**: 열 선택 매개 변수입니다. 이 형식은 UX에서 열 선택기로 렌더링됩니다. 여기에서 **Property** 요소는 열을 선택할 포트의 ID를 지정하는 데 사용되며, 대상 포트 형식은 *DataTable*이어야 합니다. 열 선택의 결과는 선택한 열 이름이 포함된 문자열 목록으로 R 함수에 전달됩니다. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *필수 속성*: **portId** - 형식이 *DataTable*인 Input 요소의 ID와 일치합니다.
* *선택적 속성*:
  
  * **allowedTypes** - 선택할 수 있는 열 형식을 필터링합니다. 유효한 값은 다음과 같습니다. 
    
    * Numeric
    * Boolean
    * 범주
    * String
    * 레이블
    * 기능
    * Score
    * 모두
  * **default** -열 선택의 유효한 기본 선택 항목은 다음과 같습니다. 
    
    * 없음
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * 모두

**DropDown**: 사용자가 지정한 열거형(드롭다운) 목록입니다. 드롭다운 항목은 **Item** 요소를 사용하여 **Properties** 요소 내에 지정됩니다. 각 **Item**에 대한 **id**는 고유하고 유효한 R 변수여야 합니다. **Item**의 **name** 값은 표시되는 텍스트와 R 함수에 전달되는 값으로 사용됩니다.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *선택적 속성*:
  * **default** - 기본 속성 값은 **Item** 요소 중 하나의 id 값에 해당해야 합니다.

### <a name="auxiliary-files"></a>보조 파일
사용자 지정 모듈 ZIP 파일에 있는 모든 파일은 실행 시간 동안 사용할 수 있습니다. 모든 디렉터리 구조는 있는 그대로 유지됩니다. 따라서 파일 소싱이 로컬과 Azure Machine Learning Studio 실행에서 동일하게 작동합니다. 

> [!NOTE]
> 모든 파일은 'src' 디렉터리로 추출되므로 모든 경로에 'src/' 접두사가 있어야 합니다.
> 
> 

예를 들어 데이터 세트를 CustomAddRows에 출력하기 전에 NA가 있는 모든 행과 모든 중복 행을 데이터 세트에서 제거하려는 경우 RemoveDupNARows.R 파일에서 이 작업을 수행하는 R 함수를 이미 작성했다고 가정해 보겠습니다.

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
CustomAddRows 함수에서 보조 파일 RemoveDupNARows.R을 소싱할 수 있습니다.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

그런 다음 ‘CustomAddRows.R’, ‘CustomAddRows.xml’ 및 ‘RemoveDupNARows.R’이 포함된 zip 파일을 사용자 지정 R 모듈로 업로드합니다.

## <a name="execution-environment"></a>실행 환경
R 스크립트의 실행 환경에서는 **R 스크립트 실행** 모듈과 동일한 버전의 R을 사용하며, 동일한 기본 패키지를 사용할 수 있습니다. 사용자 지정 모듈 zip 패키지에 포함하여 사용자 지정 모듈에 추가 R 패키지를 추가할 수 있습니다. 사용자 고유의 R 환경과 마찬가지로 R 스크립트에서 로드합니다. 

**실행 환경에 대한 제한 사항** 은 다음과 같습니다.

* 비영구적 파일 시스템: 사용자 지정 모듈을 실행할 때 작성한 파일은 동일한 모듈의 여러 실행에서 유지되지 않습니다.
* 네트워크 액세스 불가

