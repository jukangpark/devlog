---
description: 회사에 새로운 제품인 3D 데이터 센터 모니터링 솔루션을 개발해야 해요!
cover: >-
  https://images.unsplash.com/photo-1506399309177-3b43e99fead2?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw2fHxkYXRhY2VudGVyfGVufDB8fHx8MTcyMDU5MzI5MHww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 3D 데이터 센터 모니터링 솔루션 프로젝트 (작성중 - gif 추가해야함)

### DOMS 프로젝트 개요

데이터 센터를 3D 로 시각화하고 실시간으로 모니터링할 수 있는 솔루션에 대한 기업들의 수요가 급증하고 있었다. 그래서 우리팀은 데이터 센터의 인프라를 직관적으로 관리할 수 있는 3D 모니터링 솔루션을 개발하고자 했다. 이 글에서는 3D 모니터링 솔루션을 제품화하는 과정을 다루고자 한다. 설명의 편의성을 위해 새로운 솔루션을 **DOMS** (Datacenter Operation Management Suite) 라고 부르겠다.



### 과거 고객사들의 요구사항 분석

기존에 회사에서 3D 데이터 센터 모니터링 솔루션 사업을 진행했던 레퍼런스들을 살펴본 결과

<figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption><p>과거 3D 데이터 모니터링 솔루션 사업 레퍼런스 중 하나</p></figcaption></figure>

고객사들의 주요 요구사항은 아래와 같았다.

1. 랙을 클릭하게 되면 꽂혀있는 장비를 확인 가능
2. 장애가 생긴 장비 식별 가능
3. 관제하고 있는 데이터들의 전체 통계 확인 가능
4. 2D /  3D 뷰 전환 가능
5. 권한 별로 관제 가능한 대시보드 분리
6. 각 랙이 어떤 위치에 배치되어있는 지 확인 가능 (3D 라벨링 구현 필요)





### 입사하자마자 3D 모델링을 핸들링?

나는 입사하자마자 DOMS 개발을 위해 한번도 구현해본적 없는 3D 모델링과 x,y 좌표계를 구현해야만 했다. 우선 3D 환경을 쉽게 리액트에서 핸들링 할 수 있는 라이브러리들을 찾았다.

1. [three.js](https://threejs.org)
2. [@react-three/fiber](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction)
3. [@react-three/drei](https://github.com/pmndrs/drei)

위 라이브러리를 사용하여, 실무에서 바로 개발을 해야 되는 상황이였기 때문에 \
빠르게 학습하기 위해 공식문서와 해당 내용을 다루고 있는 [Udemy 강의](https://www.udemy.com/course/threejs-using-react/?couponCode=ACCAGE0923)를 찾아 수강하였다.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption><p>강의가 길지 않았지만 기본적인 개념들을 배우기에는 충분했다.</p></figcaption></figure>

3D 모델링과 관련된 기술적인 자세한 내용들은 [다음 글](https://jkng-96.gitbook.io/devlog/home/3d-1)에서 다루도록 하겠다.



### 3D 모델링과 좌표계 설계

react-three/fiber 의 Canvas 컴포넌트를 활용하여, Ground 와, x,y 좌표를 그릴 수 있는 방법을 찾기 시작하였다. 어떻게 해야 Ground 와 x, y 좌표를 3D 환경에서 구현하고, 이것들을 활용하여, 데이터 기반으로 각 랙들을 올바른 위치에 배치할 수 있을까 고민하기 시작했다.



3D 환경에서 x,y,z 좌표가 0,0,0 의 경우는 Canvas 영역의 정중앙이였고, Ground 너비 높이 값에 따라, 랙 배치가 달라져야만 했다. 각 라인을 그리드 형태로 그리는 작업도 필요했기 때문에, 전체적인  3D 좌표계를 만들기 위한 설계를 직접 그려나가면서 고민하기 시작했다.

<figure><img src="../.gitbook/assets/기술 블로그-3 (1).jpg" alt=""><figcaption><p>아이디어 스케치</p></figcaption></figure>

아이디어를 바탕으로 좌표계를 구현해냈고, 랙 컴포넌트에서 어떤 데이터 스키마로 좌표값을 받을지, 함수는 어떻게 구성할지 큰 틀을 정해놓고 코드를 작성하기 시작했다. 보다 기술적인 내용은 [다음 글](https://jkng-96.gitbook.io/devlog/home/3d-1)에서 다루도록 하겠다.





### 빌더의 필요성

DOMS 제작 초기에는 사업을 진행하기 위해 각 대시보드를 구성하는 메타 정보를 수정할 때, JS 파일을 직접 수정하고 빌드했다. 그러나 이 방식은 개발자만 할 수 있으며, 실제 구축 현장에서 엔지니어들이 핸들링하기 어렵다는 의견이 있었다. 이 문제를 해결하기 위해, 나는 DOMS에 편집 기능(이하 ‘빌더’)이 필요하다고 생각했다. 나는 빌더 기능을 넣자고 제안하였고, 몽고디비와 Express를 약간 사용할 수 있었기 때문에 개발용 임시 서버를 구축했다. 빌더의 전체적인 구조를 먼저 설계하고 UI 개발과 캔버스 영역에 대하여 편집 할 수 있는 기능을 직접 구현하여 보여드렸더니, 팀에서도 이 기능을 구현하자고 동의해주셨다.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>엔지니어 혹은 개발자가 직접 수정해줘야했었던 JS 파일</p></figcaption></figure>

그래서 팀의 백엔드 개발자들이 API를 만들기 전까지, 우리 UI 팀은 내가 만든 데이터 스키마와 API를 사용하여 빌더 기능을 개발하기 시작했다. 대시보드에 대한 CRUD 기능과 메타 정보를 저장하는 데이터 스키마를 만들어, 해당 구조를 [백엔드 선임 개발자(김락현)](https://github.com/LakHyeonKim)에게 전달하였고 Spring으로 API를 동일한 구조로 교체해주었다.

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption><p>현재 서비스 되고 있는 3D 랙 편집 기능 '빌더' </p></figcaption></figure>



### Rack 모델 사고 싶어요

DOMS 초창기에는 3D 랙 모델이 존재하지 않았기 때문에, 정육면체 박스에 이미지를 입히는 형태로 사업을 나갔다. 나는 회사에 새로운 랙 모델을 구매하자고 제안 하고 싶었지만 관련 절차가 시간이 걸리고 복잡하다는 판단하에 상업적으로 사용 가능한 랙 모델 라이센스를 나의 사비를 들여 구매하고 실제 DOMS 프로젝트에서 적용하였다. 내 사비를 쓴 이유는 빠른 시일내로 FBX 모델을 구매해서 현재 우리의 프로젝트에 직접 적용해 보면 어떨지 너무 궁금했기 때문이었다. react-three/drei 의 [useFBX](https://github.com/pmndrs/drei?tab=readme-ov-file#fbx--usefbx) 라고 하는 훅을 사용하면 FBX 모델을 쉽게 Canvas 로 가져 올 수 있었다.

```javascript
import { useFBX } from "@react-three/drei";
```

구매전에 무료 FBX 모델을 사용하여 면밀히 검토해보고 쇼핑을 시작했다.

<figure><img src="../.gitbook/assets/스크린샷 2022-10-05 오전 11.14.51.png" alt="" width="375"><figcaption><p>3D 모델을 판매하는 TURBOSQUID</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (18).png" alt="" width="375"><figcaption><p>참지 못하고 구매하여 적용하였다</p></figcaption></figure>

<figure><img src="../.gitbook/assets/line 색 #888888.png" alt=""><figcaption><p>기존의 DOMS 랙 모델의 형태는 이런식으로 이미지를 매핑한 모델이었다.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption><p>적용된 모습</p></figcaption></figure>





### DOMS 의 완성

현재 서비스되고 있는 DOMS 의 기능은 아래와 같다.&#x20;

1. <mark style="color:purple;">**랙 장비 검색을 통해 3D 영역에서 어디에 위치 되어있는지 식별 가능**</mark>
2. <mark style="color:purple;">**랙 색상을 통해 알람 식별 가능**</mark>
3. <mark style="color:purple;">**전체 알람 통계 데이터 모니터링 가능**</mark>
4. 랙을 클릭 시 장비 확인 가능
5. <mark style="color:purple;">**사용자가 보고싶은대로 카메라 자유자재로 핸들링 가능**</mark>
6. <mark style="color:purple;">**버튼을 통한 카메라 뷰 3D, 2D 변경 가능**</mark>
7. <mark style="color:purple;">**고객사별 PC 에 따라 그래픽 옵션 조정 가능**</mark>
8. <mark style="color:purple;">**좌표계 기준점 변경 가능**</mark>
9. <mark style="color:purple;">**3D 라벨링으로 랙 위치 식별 가능**</mark>
10. 라이센스 관리 가능
11. <mark style="color:purple;">**권한 관리 가능**</mark>
12. <mark style="color:purple;">**사용자 관리 가능**</mark>
13. 장비 예약 기능
14. <mark style="color:purple;">**자산정보 관리 기능**</mark>
15. 파일 관리 기능
16. 데이터 백업 관리 기능

첫 프로젝트였지만, 기간안에 프로젝트를 마무리 짓기 위해서는 하이라이트한 부분들은 내가 개발해야만 했다. JWT 토큰을 활용한 프론트엔드 인증 및 재발행 시스템 구현도 DOMS 에 적용하였는데, 관련된 내용이 궁금하다면 [이 글](https://jkng-96.gitbook.io/devlog/home/jwt)을 참조하길 바란다.



**구현된 DOMS**

<figure><img src="../.gitbook/assets/스크린샷 2022-11-29 오후 4.58.53.png" alt=""><figcaption><p>초창기 DOMS 의 모습</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption><p>새로운 랙 모델 추가와  전체적인 디자인이 개선된 DOMS</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>카메라 버튼으로 2D 뷰 전환</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption><p>예약 모드 장비를 예약 할 수 있는 기능</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption><p>자산 정보 관리 랙을 선택하면 꽂힌 장비 목록을 볼 수 있고, 전체적인 그룹별 랙과 장비를 모두 관리 할 수 있다.</p></figcaption></figure>





### DOMS 의 제품화

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



**3. 3D 데이터 센터 시각화 솔루션 납품 (LG-CNS) \[24년 2월 7일]**

• 상황: LG-CNS의 대규모 데이터 센터 운영 효율성을 높이기 위해 솔루션이 필요했다.\
• 행동: 맞춤형 솔루션을 설계하고, 철저한 테스트와 조정을 통해 안정성을 확보했다.\
• 결과: 최대 규모의 데이터 센터에 성공적으로 솔루션을 납품하며, 대규모 시장에서도 신뢰를 얻었다.



DOMS는 이러한 성과를 바탕으로 다른 사업들에도 꾸준히 판매되었으며, \
회사의 새로운 매출원으로 자리매김하게 되었다. \


\
