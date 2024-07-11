---
description: 회사에 새로운 제품인 3D 데이터 센터 모니터링  솔루션을 개발해야만해요!
cover: >-
  https://images.unsplash.com/photo-1506399309177-3b43e99fead2?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw2fHxkYXRhY2VudGVyfGVufDB8fHx8MTcyMDU5MzI5MHww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 입사 후 첫 프로젝트, 이런게 실무인가?

### 3D 데이터 센터 모니터링 솔루션 개발 프로젝트 개요

데이터 센터의 복잡성과 규모가 증가함에 따라, \
이를 효과적으로 시각화하고 실시간으로 모니터링할 수 있는 솔루션에 대한 기업들의 수요가 급증하고 있었다.  \
이러한 요구에 부응하여, \
우리팀은 3D 시각화 기술을 활용하여 데이터 센터의 인프라를 직관적으로 관리할 수 있는 3D 모니터링 솔루션을 개발하고자 했다. 이번 글에서는 이러한 솔루션을 제품화하는 과정과 핵심 개념들을 상세히 다루고자 한다.\
설명의 편의성을 위해 새 프로젝트를 **DOMS** (Datacenter Operation Management Suite) 라고 부르겠다.&#x20;



### 과거 고객사들의 요구사항 분석

기존에 회사에서 Canvas 형태로 개발된 완전 3D 방식이 아닌, \
이미지를 활용한 3D 데이터 센터 모니터링 솔루션 사업을 진행했던 레퍼런스들을 살펴본 결과\
고객사들의 주요 요구사항은 아래와 같았다.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>과거 3D 데이터 모니터링 솔루션 사업 레퍼런스 중 하나</p></figcaption></figure>

1. 랙을 클릭하게 되면 해당 랙에 꽂혀있는 장비를 확인할 수 있어야 함
2. 랙의 색상을 통해 해당 랙에 꽂혀있는 장비들 중 장애가 발생한 곳을 확인할 수 있어야 함
3. 모니터링 하고 있는 데이터들에 대한 전체 통계를 확인할 수 있어야 함
4. 2D /  3D 뷰를 클릭하여 전환할 수 있어야함
5. 권한 별로 볼 수 있는 모니터링 대시보드가 나눠져야함
6. 3D 텍스트를 통해 각 랙이 어떤 위치에 배치되어있는 지 확인할 수 있어야함



요구사항을 우선 기술적으로 구현해내기 위해서는 각 요구사항들을 풀어낼 수 있는 라이브러리가 존재하는지 찾아야했다.\
그리고 솔루션이 개발된 이후에, 각 고객사마다 특수하게 요구할 수 도 있는 기술적인 요구사항들을 충족시키기 위한 확장성 있고, 수정이 용이한 구조로 짜는 게 핵심이였으며, 기존의 3D 데이터 센터 모니터링 솔루션의 문제점을 보완해야하는 것도 문제였다.



### 입사하자마자 3D 모델링을 핸들링?

나는 입사하자마자 DOMS 개발을 위해\
리액트 환경에서 한번도 구현해본적 없는 3D 모델링과 x,y 좌표계를 구현해야만 했다.\
우선 3D 환경을 쉽게 리액트에서 핸들링 할 수 있는 라이브러리를 찾았는데, \
3가지를 찾게 되었다.

1. [three.js](https://threejs.org)
2. [@react-three/fiber](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction)
3. [@react-three/drei](https://github.com/pmndrs/drei)

위 라이브러리를 사용하여, 실무에서 바로 개발을 해야 되는 상황이였기 때문에 \
빠르게 학습하기 위해 공식문서와 해당 내용을 다루고 있는 [Udemy 강의](https://www.udemy.com/course/threejs-using-react/?couponCode=ACCAGE0923)를 찾아 수강하였다.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption><p>강의가 길지 않았지만 기본적인 개념들을 배우기에는 충분했다.</p></figcaption></figure>

Three.js 는 3D 그래픽을 웹 브라우저에서 렌더링하기 위한,  JavaScript 라이브러리로, \
React Three Fiber 는 이를 React 의 컴포넌트 기반 접근 방식과 결합하여 \
더 쉽게 3D 장면을 구축하고 관리할 수 있도록 도와주는 라이브러리 라는 사실을 알게 되었다.\




### 3D 모델링과 좌표계 설계

react-three/fiber 의 Canvas 컴포넌트를 활용하여, \
Ground 와, x,y 좌표를 그릴 수 있는 방법을 찾기 시작하였다.\
어떻게 해야 Ground 와 x, y 좌표를 3D 환경에서 구현하고, 이것들을 활용하여, \
데이터 기반으로 각 랙들을 올바른 위치에 배치할 수 있을까 고민하기 시작했다.



3D 환경에서 x,y,z 좌표가 0,0,0 의 경우는 Canvas 영역의 정중앙이였고, \
Ground 너비 높이 값에 따라, 랙 배치가 달라져야만 했다. \
각 라인을 그리드 형태로 그리는 작업도 필요했기 때문에, \
전체적인  3D 좌표계를 만들기 위한 설계를 직접 그려나가면서 고민하기 시작했다.

<figure><img src="../.gitbook/assets/기술 블로그-3 (1).jpg" alt=""><figcaption><p>아이디어 스케치</p></figcaption></figure>





### 빌더의 필요성

DOMS 제작 초기에는 사업을 진행하기 위해 각 대시보드를 구성하는 메타 정보를 수정할 때, JS 파일을 직접 수정하고 빌드하는 방식으로 작업을 수행했다. 그러나 이 방식은 개발자만 할 수 있으며, 실제 구축 현장에서 엔지니어들이 핸들링하기 어렵다는 의견이 있었다. 이 문제를 해결하기 위해, 저는 DOMS에 편집 기능(이하 ‘빌더’)이 필요하다고 생각했다. 이에 따라, DOMS 제품 제작 당시 빌더 기능을 역으로 제안하였고, 우리 팀은 이를 추가하기로 결정했다.

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>엔지니어 혹은 개발자가 직접 수정해줘야했었던 JS 파일</p></figcaption></figure>



몽고디비와 Express를 약간 사용할 수 있었기 때문에 개발용 서버를 구축했다. 실제 팀의 백엔드 개발자들이 API를 만들기 전까지, 우리 UI 팀은 내가 만든 데이터 스키마와 API를 사용하여 빌더 기능을 개발하기 시작했다. 대시보드에 대한 CRUD 기능과 메타 정보를 저장하는 데이터 스키마를 만들어, 해당 구조를 백엔드 선임 개발자에게 전달하였다. 이후, 백엔드 개발자는 Spring으로 API를 동일한 구조로 교체했다.

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption><p>현재 서비스 되고 있는 3D 랙 편집 기능 '빌더'</p></figcaption></figure>



### Rack 모델 사고 싶어요

DOMS 초창기에는 3D 랙 모델이 존재하지 않았기 때문에, 정육면체 박스에 이미지를 입히는 형태로 사업을 나갔다.\
나는 회사에 새로운 랙 모델을 구매하자고 제안 하고 싶었지만 여러 절차가 있어서, 관련 절차가 복잡하다는 판단하에 상업적으로 구매가 가능한 랙 모델 라이센스를 나의 사비를 들여 구매하고 실제 DOMS 프로젝트에서 실험 해보고 싶었다.\
FBX 모델을 구매해서 현재 우리의 프로젝트에 직접 적용해 볼 수 있는지 궁금하였다. \
react-three/drei 의 [useFBX](https://github.com/pmndrs/drei?tab=readme-ov-file#fbx--usefbx) 라고 하는 훅을 사용하면 FBX 모델을 쉽게 Canvas 로 가져 올 수 있었다.

```javascript
import { useFBX } from "@react-three/drei";
```

실제 적용할 랙 모델을 구매전에 무료 FBX 모델을 사용하여 직접 import 해보고 랙 모델을 쇼핑하기 시작했다.

<figure><img src="../.gitbook/assets/스크린샷 2022-10-05 오전 11.14.51.png" alt="" width="375"><figcaption><p>3D 모델을 판매하는 TURBOSQUID</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (18).png" alt="" width="375"><figcaption><p>참지 못하고 구매하여 적용하였다</p></figcaption></figure>

<figure><img src="../.gitbook/assets/line 색 #888888.png" alt=""><figcaption><p>기존의 DOMS 랙 모델의 형태는 이런식으로 이미지를 매핑한 모델이었다.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption><p>적용된 모습</p></figcaption></figure>





### DOMS 의 완성

<figure><img src="../.gitbook/assets/스크린샷 2022-11-29 오후 4.58.53.png" alt=""><figcaption><p>초창기 DOMS 의 모습</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>새로운 랙 모델 추가와  전체적인 디자인이 개선된 DOMS</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>카메라 버튼으로 2D 뷰 전환</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>예약 모드 장비를 예약 할 수 있는 기능</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>자산 정보 관리 랙을 선택하면 꽂힌 장비 목록을 볼 수 있고, 전체적인 그룹별 랙과 장비를 모두 관리 할 수 있다.</p></figcaption></figure>





### 사업화 여정 그리고 제품화

DOMS 는 QA 팀을 거쳐 여러 테스트를 거친 이후에  제품으로서 판매할 준비가 되었고, 다양한 사업에 투입되었다.

<div align="left" data-full-width="false">

<figure><img src="../.gitbook/assets/IMG_9595 (2).PNG" alt="" width="563"><figcaption><p>DOMS 제품 패키지</p></figcaption></figure>

</div>



**1. 3D 데이터 센터 시각화 솔루션 개발 및 구축 (효성) \[22년 12월 21일]**

• 상황: 효성의 데이터 센터 운영 효율성을 높이기 위해 솔루션이 필요했다.\
• 행동: EMS팀과 협력하여 안정적으로 솔루션을 개발하고 구축했다.\
• 결과: 솔루션을 성공적으로 납품하며, 이를 기반으로 추가 사업 기회를 창출했다.



**2. 3D 데이터 센터 시각화 솔루션 개발 및 구축 (KLID) \[23년 2월 21일]**

• 상황: 효성 프로젝트의 성공 이후, 한국지역정보개발원이 솔루션을 필요로 했다.\
• 행동: 효성 프로젝트 경험을 바탕으로 맞춤형 기능을 추가하여 솔루션을 설계하고 개발했다.\
• 결과: 솔루션을 성공적으로 구축 및 납품하여, 관리 효율성을 크게 향상시켰다.



**3. 3D 데이터 센터 시각화 솔루션 납품 (LG-CNS) \[23년 2월 7일]**

• 상황: LG-CNS의 대규모 데이터 센터 운영 효율성을 높이기 위해 솔루션이 필요했다.\
• 행동: 맞춤형 솔루션을 설계하고, 철저한 테스트와 조정을 통해 안정성을 확보했다.\
• 결과: 최대 규모의 데이터 센터에 성공적으로 솔루션을 납품하며, 대규모 시장에서도 신뢰를 얻었다.



DOMS는 이러한 성과를 바탕으로 다른 사업들에도 꾸준히 판매되었으며, \
회사의 새로운 매출원으로 자리매김하게 되었다. \


\
