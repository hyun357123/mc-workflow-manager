# M-CMP mc-workflow-manager Wiki

This repository provides a Workflow Manager.

A sub-system of [M-CMP platform](https://github.com/m-cmp/docs/tree/main) to deploy and manage Multi-Cloud Infrastructures.

<hr />

## 1. Overview

M-CMP의 mc-workflow-manager 서브시스템이 제공하는 기능은 다음과 같다.
- ToolChain 관리
- Workflow Stage 관리
- Workflow 관리
- Software Catalog 관리

<hr />

## 2. Flow
### 2-1. Workflow Manager Flow
M-CMP의 mc-workflow-manager 서브시스템의 Flow는 다음과 같다.
1. ToolChain 등록 (연동될 OSS 정보들응 등록)
   - 연동되어 사용될 Jenkins, Gitlab, ArgoCD, Nexus, Tumblebug 등
   - Workflow 용 OSS인 Jenkins는 Workflow 관련 기능을 위해 필수로 등록한다.


2. Workflow Stage 등록 (Workflow를 구성할 Stage 등록)
   - 템플릿 개념의 Stage 등록 후 Workflow에서 사용 가능하다.
   - 필수로 등록 하지 않아도 된다.


3. Workflow 생성
    - 등록된 Stage등을 조합/직접 입력 하여 Workflow를 생성한다.


4. Workflow 실행
    - UI 또는 API 를 활용하여 Workflow를 실행한다.

### 2-2. Software Catalog Flow
1. Software Catalog 등록
<br /><br />

2. 연관 Workflow 및 관련 Catalog 등록
   - software catalog 연계 workflow 및 catalog 등록(optional)
     - 해당software 설치 workflow 실행, 재시작 등 연관 workflow 등록
     - 연관catalog 등록
   

3. Software Catalog 확인 및 타 Repository 검색
<br /><br />

4. 연관workflow 실행 등
   - 연관workflow 실행, 또는 연관 catalog 정보 확인 등
     - 해당software 설치 workflow 실행 등 등록된 연관 workflow 사용
     - 검색된외부sw를catalog에 등록 등

<hr />

## 3. 기능 API
### 3-1. ToolChain
<details open>
<summary>API</summary>

| Function                                              | API    | EndPoint                     | Parameter                                                                                                                                                                                                                                         | DESC                                                                                         |
|:------------------------------------------------------|:-------|:-----------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------|
| ToolChain List <br /> (툴체인 목록)                     | GET    | /config/oss/list             |                                                                                                                                                                                                                                                  | 툴체인 목록을 조회한다. <br />  연동사용OSS(nexus, jenkins 등)을 위한 정보를 입력 및 조회 <br /> db등의 정보이외에는직접등록후사용한다. |
| ToolChain Duplication Check <br /> (툴체인 중복 체크)    | GET    | /config/oss/duplicate        | (QueryParam) <br /> ossName=tumblebug& <br /> ossUrl=http:%2F%2Ftb.tb-cb.kro.kr:1323& <br /> ossUsername=default                                                                                                                                  | 등록될 툴체인 명 중복 검사를한다.                                                                          |
| ToolChain Connection Check <br /> (툴체인 커넥션 체크)   | POST   | /config/oss/connection/check | (Request Body) <br />{ <br />"ossCd":"TUMBLEBUG",  <br />"ossName":"tumblebug",  <br />"ossDesc":"",  <br />"ossUrl":"http://tb.tb-cb.kro.kr:1323",  <br />"ossUsername":"default",  <br />"ossPassword":"default",  <br />"ossToken":"“  <br />} | 등록될 툴체인의ID / PW로 연결확인을한다.                                                                    |
| ToolChain Regist <br /> (툴체인 등록)                   | POST   | /config/oss                  | (Request Body) <br /> { <br />"ossCd":"TUMBLEBUG", <br />"ossName":"tumblebug", <br />"ossDesc":"", <br />"ossUrl":"http://tb.tb-cb.kro.kr:1323", <br />"ossUsername":"default", <br />"ossPassword":"ZGVmYXVsdA==", <br />"ossToken":"" <br />}  | 툴체인을 등록한다.                                                                                   |
</details>

<hr />

### 3-2. Workflow Stage
<details open>
<summary>API</summary>

| Function                                                            | API   | EndPoint                       | Parameter                                                                                                                                                                                                                                                                                                  | DESC                                                                         |
|:--------------------------------------------------------------------|:------|:-------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------|
| Workflow Stage List <br /> (워크플로우 스테이지 목록)                   | GET   | /stage/pipeline/list           | -                                                                                                                                                                                                                                                                                                          | 젠킨스 스테이지 목록을 조회한다. <br />젠킨스 스테이지를 조합한 jenkins job (workflow) 구성을 위한 스테이지 목록 |
| Workflow StageCD Add <br /> (스테이지 구분 추가)                       | POST  | /common/group/Pipeline/code    | (Request Body)<br />{<br />"commonGroupCd":"Pipeline",<br />"commonCd":"TUMBLEBUG",<br />"codeName":"TUMBLEBUG",<br />"codeDesc":"Add StageCd",<br />"codeOrder":13<br />}                                                                                                                                 | 스테이지 구분을 추가한다. <br /> (여러 스테이지를 관리 할 상위 depth 개념)                            |
| Workflow Stage Name Duplicate <br /> (워크플로우 스테이지 이름 중복 체크) | GET   | /stage/pipeline/name/duplicate | (Query Param)<br />pipelineCd={스테이지구분}&<br />pipelineName={스테이지명}                                                                                                                                                                                                                                   | 스테이지 구분에 종속된 스테이지명 중복검사를 한다.                                                 |
| Workflow Stage Regist (워크플로우 스테이지 등록)                         | POST  | /stage/pipeline                | (Request Body)<br /> {<br /> "pipelineCd":"TUMBLEBUG",<br /> "pipelineName":"tumblebug-Test",<br /> "pipelineScript":"    <br />stage('tumblebug') {\n      TUMBLEBUG'\n  \n        steps {\n        echo '>>>>>STAGE: // 스크립트를작성해주세요.\n      }\n    }\n  \n",<br />"newStageCd":"tumblebug“<br />} | 스테이지를등록한다.                                                                   |
</details>
<hr />

### 3-3. Workflow
<details open>
<summary>API</summary>

| Function                                                 | API           | EndPoint                           | Parameter                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | DESC                                                |
|:---------------------------------------------------------|:--------------|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------|
| Workflow List <br /> (워크플로우 목록)                          | GET           | /workflow/list                     | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 워크플로우 목록을 조회한다.<br />스테이지를 조합하여 구성한 workflow 목록을 조회 |
| Workflow Name Duplicate <br /> (워크플로우 이름 중복 체크)          | GET           | /workflow/name/duplicate           | (Query Param)<br />workflowName=workflow-test                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 등록될 워크플로우 명을 중복검사 한다.                               |
| Workflow Default Script Create <br /> (워크플로우 기본 스크립트 생성) | POST          | /workflow/jenkins/pipeline/default | (Request Body)<br /> {<br /> "pipelineParam":[{"paramKey":"","paramValue":""}],<br /> "workflowName":"workflow-test",<br /> "workflowPurpose":"test",<br /> "jenkinsId":2<br /> }                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | 기본 스크립트를 생성한다.                                      |
| Workflow Regist <br /> (워크플로우 생성)                        | POST          | /workflow                          | (Request Body) ​<br />{​<br />"workflowName": "workflow-test",​<br />"workflowPurpose": "test",​<br />"jenkinsId": 2,​<br />"pipelineParam": [​<br />        {​<br />            "paramKey": "MCIS_NAME",​<br />            "paramValue": "vm_name"​<br />        }​<br />], <br />​"pipelineScript": "//It was created by the Devops portal.\npipeline {\n  agent any\n  \n  environment {\n    MC_SPIDER_REST_URI = http://tb.tb-cb.kro.kr:1323\n    CB_TUMBLEBUG_SWAGGER_URI = http://tb.tb-cb.kro.kr:1323/tumblebug/api/index.html\n  }\n  \n  stages {\n\n    stage('tumblebug') {\n      steps {\n        echo '>>>>>STAGE: TUMBLEBUG'\n  \n        // 스크립트를 작성해주세요.\n        echo 'MCIS NAME >> ' \"${MCIS_NAME}\"\n      }\n    }\n  \n\n  }\n}\n“",​<br />"pipelines": [​<br />        {​<br />            "pipelineScript": "//It was created by the Devops portal.\npipeline {\n  agent any\n  \n  environment {\n    MC_SPIDER_REST_URI = http://tb.tb-cb.kro.kr:1323\n    CB_TUMBLEBUG_SWAGGER_URI = http://tb.tb-cb.kro.kr:1323/tumblebug/api/index.html\n  }\n  \n  stages {\n",<br />​            "pipelineOrder": 0,​<br />            "isDefaultScript": true,​<br />            "defaultScriptTag": "DEFAULT_START"​<br />        },​<br />        {​<br />            "pipelineScript": "    stage('tumblebug') {\n      steps {\n        echo '>>>>>STAGE: TUMBLEBUG'\n  \n        // 스크립트를 작성해주세요.\n        echo 'MCIS NAME >> ' \"${MCIS_NAME}\"\n      }\n    }\n  \n",​<br />            "pipelineOrder": 1​<br />        },​<br />        {​<br />            "pipelineScript": "  }\n}\n",​<br />            "pipelineOrder": 2,​<br />            "isDefaultScript": true,​<br />            "defaultScriptTag": "DEFAULT_END"​<br />        }​<br />    ]​<br />}​ | 워크플로우 생성과 함께 Jenkins Job이 생성된다                                                                                                                                                    |
| Workflow Run <br /> (워크플로우 실행)                           | POST<br />GET |/workflow/run <br />/workflow/{workflowIdx}/run|(Request Body)​<br />{​<br />    "workflowId":47,    "​pipelineParam": [​<br />        {​<br />            "paramKey":"MCIS_NAME",​<br />            "paramValue":"test",​<br />        }<br />    ],​<br />}​| 등록된 워크플로우를 실행 한다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
</details>

<details open>
<summary><b>워크플로우 실행 추가 설명 *</b></summary>
등록된 workflow에 따라 실행 시 추가로 필요한 데이터가 존재할 수 있음(param)<br/>
GET방식 실행 시 설정된 value 기본값으로 실행<br/>
Ex) tomcat 9.0 설치 workflow<br/>
- MCIS, NAMESPACE 등 필요<br/>
- 개발서버기준 vm 삭제<br/>
API : (POST) /workflow/run<br/>
Param : (Request Body)<br/>

```JSON
{
   "workflowId":47,
   "pipelineParam": [
       {
         "paramKey":"MCIS", 
         "paramValue":"{mcisname}"
       },
       {
         "paramKey":"NAMESPACE", 
         "paramValue":"{namespace}"
       }
   ]
}
```

Ex2) 현재시간 반환 workflow, health check 등
- 단순실행(GET) 가능
</details>
<hr />

### 3-4. Software Catalog
<details open>
<summary>API</summary>

| Function                                            | API                | EndPoint                                                                                                                                                                                 | Parameter                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | DESC                                                          |
|:----------------------------------------------------|:-------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------|
| Software Catalog Regist <br />(Software Catalog 등록) | POST               | /catalog/software/                                                                                                                                                                      | (Request Body)(Request Body)<br />{<br />​  "scTitle": "test api call dummy",​<br />  "scVersion": "0.0.0",​<br />  "scIcon": "https://avatars.githubusercontent.com/u/133619646?s=48&v=4", <br />  "scSummary": "test for swCatalog insert \n aabbccdd",​<br />  "scUrl": ".",​<br />  "scCategory": "CORE",​<br />  "scReference": "https://github.com/cloud-barista/cb-tumblebug", <br />  "scDescription": "test dummy data insert"​<br />}​  | 새로 생성할 software정보를 입력(임시 api, 정책 비확정으로 현재 기본정보정도만 구현한 임시 api) |
| Software Catalog 연관 workflow/catalog 등록            | 1. GET<br />2. GET | 1. /catalog/software/relation/workflow/{생성된 catalogIdx}/{연결할 workflowIdx}​ <br /> 2. /catalog/software/relation/workflow/{입력 성공 시 반환된 data.scIdx)}/61(vm infra create)​  | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | 생성된 software catalog에 연동할 workflow를 입력​                       |
| Software Catalog List <br />(Software Catalog 목록)   | GET                | /catalog/software/{searchKeyword}                                                                                                                                                       | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | {searchKeyword}값이 없으면 전체조회​                                   |
| dockerHub image Detail <br />(dockerHub image 조회)   | GET                | /applications/search/dockerhub/catalog/{searchKeyword}                                                                                                                                  | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | dockerHub image 조회                                            |
| artifactHub helm Detail <br />(artifactHub helm 조회) | GET                | /applications/search/artifactHub/package/{searchKeyword}​                                                                                                                            | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | artifactHub helm 조회                                           |
| Workflow Run <br />(workflow 실행)                    | POST               | /workflow/run​                                                                                                                                                                       | {​ <br />    "workflowId": 61,​ <br />    "pipelineParam": [​ <br />      {"paramKey": "NAMESPACE", "paramValue": "ns01"},​ <br />      {"paramKey": "MCIS", "paramValue": "test-vm-0621-001"},​ <br />      {"paramKey": "SUBGROUP_SIZE", "paramValue": "1"},​ <br />      {"paramKey": "SPEC", "paramValue": "aws+ap-northeast-2+t2.small"}​ <br />    ]​ <br />}                                                                               | workflow 실행                                                   |
| Workflow Run <br />(생성된 vm infra 삭제 workflow)     | POST               | /workflow/run                                                                                                                                                                           | { <br />    "workflowId": 49,​ <br />    "pipelineParam": [​ <br />      {"paramKey": "NAMESPACE", "paramValue": "ns01"},​ <br />      {"paramKey": "MCIS", "paramValue": "test-vm-0621-001"}​ <br />    ]​ <br />}​                                                                                                                                                                                                                                    | 생성된 vm infra 삭제 workflow                                      |

</details>

<hr />