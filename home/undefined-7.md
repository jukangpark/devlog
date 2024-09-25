---
description: CONTRIBUTING 문서와 CODE CONVENTION 을 작성해보자
cover: >-
  https://images.unsplash.com/photo-1716643406202-f3a5f212f827?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHJhbmRvbXx8fHx8fHx8fDE3MjM2MjMxMjl8&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 협업 문서 작성하기

### 개요

개발을 하다 보면, 각자의 코드 스타일이 조금씩 달라 다른 사람의 코드를 이해하는 데 예상보다 많은 리소스가 소모될 때가 있다. 이러한 비효율을 줄이기 위해, 팀 내 프론트엔드 개발자들 간의 협업을 원활하게 하고자 기여 관련 문서와 코드 컨벤션 문서를 작성하여 팀에 공유했다. 이 문서는 팀원들이 일관된 코드 스타일을 유지하고, 효율적인 협업을 지원하기 위해 마련한 것이다. 이를 통해 코드 리뷰 과정에서 발생할 수 있는 불필요한 논의나 오해를 줄이고, 팀의 전반적인 생산성을 높이는 데 기여할 수 있을 것이다.



## 💻 Contributing

### ✏️ 기여 방법

lucida-doms 는 MR 을 통해 기여를 받습니다. 이 문서는 커밋 메시지 형식, 개발자 연락처, 그리고 lucida-doms에 기여하는 데 도움이 되는 기타 리소스에 대한 규칙을 설명합니다.

#### 📞 연락처

기여 중에 궁금한 사항이 있으면 언제든지 문의해 주세요.

* Slack: [lucida-doms](https://3-itp8560.slack.com/archives/C0682QN51U5)

#### 📃 시작하기

* 이슈를 작성하여 기여하고자 하는 내용을 설명
* 이슈를 해결하기 위한 브랜치를 생성
* 변경 사항을 커밋
* MR을 생성
* MR을 검토하고 다른 개발자들에게 리뷰 받기
* MR을 병합

### 👤 기여 흐름

기여자의 워크플로우는 대략적으로 다음과 같습니다:

* 기여할 브랜치를 기반으로 브랜치를 생성하세요. 보통 develop 에서 시작합니다. ( 브랜치 이름이나 형식은 자유롭게 하되, 다른 개발자들이 이해하기 쉽게 작성하세요. )
* 논리적인 단위로 커밋을 만드세요. ( rebase 와 squash 를 통해 커밋을 정리할 수 있습니다. 다른 개발자들이 이해하기 쉽게 작성하세요. )
* 커밋 메시지가 올바른 형식으로 작성되었는지 확인하세요.
* lucida-doms 에 MR 을 제출하세요.
* MR 은 다른 개발자들에게 리뷰를 받아야 합니다. (변경사항이 올바르게 동작하는지 확인하고, 코드 컨벤션에 맞는지 확인합니다. 추가적으로 더 좋은 접근 방법이 있을 경우 공유합니다.)
* 다른 개발자들이 looks good to me 라는 댓글을 남기면 MR 을 병합할 수 있습니다. ( 모두가 확인하는게 어려울 경우, 최소 1명의 개발자가 확인하면 됩니다. )

**\[중요] 왠만하면 자기 자신이 Merge 하지 않도록 하고, 다른 개발자가 Merge 하도록 합니다.**

Thanks for contributing!

#### 🔨 코드 컨벤션

코드 컨벤션은 다음의 문서를 참고하세요. Code Convention

#### ⚙️ 커밋 메시지 형식

우리는 러프한 커밋 메시지 규칙을 따릅니다. 제목 줄에는 전체적인 커밑 내용을 요약을 작성하고, 한 줄 띄운 후에는 자세한 내용을 작성합니다.\


```
axios 인터셉터 관련로직 리팩토링

- 인터셉터 모듈 분리 및 리팩토링
- 인터셉터 관련 로직 ts 로 마이그레이션
- 인터셉터 관련 테스트 코드 작성
```

첫 번째 줄은 제목이며 70자 이내로 작성해야 하고, 두 번째 줄은 항상 비워둬야 합니다. 다른 줄은 80자 이내로 줄바꿈해야 합니다.





### CODE CONVENTION



## ✏️ Coding Conventions

### 📄 목차

* ✏️ Coding Conventions
  * 📄 목차
    * 명명 규칙
    * 이벤트 핸들러
    * 불린 반환 함수 : 반환 값이 boolean 인 경우에는 is 로 시작
    * 전역변수를 최대한 지양합니다.
    * var 는 절대 사용하지 않으며, 값이 변하지 않는 변수를 let 대신 const 로 선언합니다.
    * 함수 표현식 대신 화살표 함수를 사용한다.
    * 암시적 반환을 최대한 활용한다.
    * Destructuring
    * 템플릿 문자열
    * 조건 확인하기
    * 함수 선언할 때 JSDoc을 작성합니다.
    * 타입정의
    * 배열 타입 지정 하는 방법
    * 타입 추론 ( Type Inference ) 를 최대한 활용합니다.
    * 함수 작성 시 주의사항
    * 테스트 코드 모듈 관리
    * AJV 를 사용하여 API 요청 하여 외부 시스템과 동기화 할 때, validation 을 수행합니다.
    * if 문 사용 시, 중첩 if 문을 최대한 줄입니다.
    * 함수 작성시 결합도를 줄입니다.
    * 마무리하며

**코딩 컨벤션**은 **읽고, 관리하기 쉬운 코드를 작성하기 위한 일종의 코딩 스타일 규약**입니다. 특히 자바스크립트는 다른 언어에 비해 유연한 문법 구조 (동적 타입, this 바인딩, 네이티브 객체 조작 가능)를 가지기 때문에 개발자간 통일된 규약이 없다면 코드의 의도를 파악하거나 오류를 찾기 어렵습니다. 코딩 컨벤션을 준수하면 가독성이 좋아지고, 잠재적 위험 요소를 줄여줍니다. 특히 규모가 큰 프로젝트일수록, 유지보수 비용을 줄여줍니다.

이 문서는 **프로그램의 성능을 해치지 않는 범위 내에서 가독성과 관리 용이성을 우선**하여 작성하였으며, ESLint 와 같은 **린터를 사용한다는 가정하에 린터로 검출할 수 없는 모호한 부분을 가이드합니다**.

#### 명명 규칙

(매개) **변수,함수에는 카멜 케이스를 사용합니다**. 변수와 함수의 이름에는 카멜 케이스를 사용합니다. **예약어는 절대 사용하지 않습니다.**

```javascript
// Bad
let class;
let enum;
let extends;
let super;
let const;
let export;
let import;


// Bad
function example(this) {
    // 사용이 가능하긴 하지만 권장하지 않습니다.
    console.log(this);
}

example('hello'); // 'hello' 출력

```

절대 변경되지 않는 개발자들끼리의 약속한 **상수**의 경우에는 영문 대문자 **스네이크 표기법**을 사용합니다.

```javascript
const FONT_FAMILY = "Spoqa Han Sans Neo" // 전역으로 사용될 폰트
```

생성자는 대문자 카멜 케이스를 사용합니다.

```javascript
class ConstructorName {
    //
}
```

#### 이벤트 핸들러

```javascript
// handleClick 을 채택하였습니다.
const handleClick = () => {
    // 이벤트 핸들러
}

// onClick 은 사용하지 않습니다.
// const onClick = () => {};
```

#### 불린 반환 함수 : 반환 값이 boolean 인 경우에는 is 로 시작

```javascript
function isEven(num) {
    return num % 2 === 0;
}
```

#### 전역변수를 최대한 지양합니다.

자바스크립트는 전역 변수에 기반을 둡니다. 모든 컴파일 단위는 하나의 공용 전역 객체 (window) 에 로딩 되기 때문에 전역변수는 언제든지 프로그램의 모든 부분에서 접근할 수 있기 때문에, 편하지만 위험합니다. 저희는 최대한 전역변수를 사용하지 않도록 노력합니다.

```javascript
// Bad
const myglobal = "hello";
```

#### var 는 절대 사용하지 않으며, 값이 변하지 않는 변수를 let 대신 const 로 선언합니다.

```javascript
// Bad
var a = 1;

// Good
const a = 1;
```

#### 함수 표현식 대신 화살표 함수를 사용한다.

화살표 함수는 별도의 this 바인딩 없이 상위 컨텍스트에 바인딩되기 때문에 함수 표현식보다 혼란이 적으며 덜 장황하고 추론이 쉽습니다.

```javascript
// Bad
[1, 2, 3].map(function (x) {
  const y = x + 1;
  return x * y;
});

// Good
[1, 2, 3].map(x => {
  const y = x + 1;
  return x * y;
});

// 예시 코드일 뿐입니다. 재사용되거나, 함수가 길어질 경우 콜백함수는 따로 분리하는 것이 좋습니다.
```

#### 암시적 반환을 최대한 활용한다.

함수의 본체가 하나의 표현식이면 중괄호를 생략하고 암시적 반환을 사용할 수 있다. 그 외에는 return문을 명시해야 합니다.

```javascript
// Bad
[1, 2, 3].map(number => {
  const nextNumber = number + 1;
  `A string containing the ${nextNumber}.`;
});
// 위 처럼 작성하면, 객체를 리턴한다는걸 조금 자세히 봐야 합니다. 이런 경우에는 return을 명시하는 것이 좋습니다.

// Good - 암시적 return을 사용 (return 하는 데이터가 객체가 아닌 경우) 이렇게 작성해도 무관합니다.
[1, 2, 3].map(number => `A string containing the ${number + 1}.`);
```

#### Destructuring

오브젝트의 프로퍼티에 접근할 때는 최대한 비구조화 할당을 사용을 지향합니다.

```javascript
// Bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// Bad
const first = arr[0];
const second = arr[1];

// Good
function getFullName(obj) {
  const {firstName, lastName} = obj;

  return `${firstName} ${lastName}`;
}

// Good
const [first, second] = arr;

// Good
function getFullName({firstName, lastName}) {
  return `${firstName} ${lastName}`;
}
```

새로운 이름으로 변수에 할당 할 때는 꼭 Destructuring을 사용하지 않아도 된다. (둘다 상관 없습니다. 너무 피곤해지니까..)

```javascript
// Good
const changeFirstName = user.firstName;

// Good
const {firstName: changeFirstName} = user;
```

#### 템플릿 문자열

**변수 등을 조합해서 문자열을 생성하는 경우 템플릿 문자열을 이용합니다.** 자바스크립트에서 문자열을 보다 쉽고 명확하게 다룰 수 있어 코드 복잡도가 낮아집니다.

```javascript
// Bad
function sayHi(name) {
  return "How are you, " + name + "?" ;
}

// Bad
function sayHi(name) {
  return ["How are you, ", name, "?"].join();
}

// Bad - 일반적인 경우, 쌍따옴표를 사용
function sayHi(name) {
  return "How are you name?";
}

// Good
function sayHi(name) {
  return `How are you, ${name}?`;
}
```

#### 조건 확인하기

**삼중 등호 연산자인 ===, !==만 사용합니다.** 이건 지향하는게 아니라 **필수**입니다. ==이나 !=는 암묵적 캐스팅으로 타입에 관계없이 판단되어 조건문의 의도를 파악하기 어렵고 버그로 이어집니다.

```javascript

const numberB = 777;

// Bad - 절대 이렇게 작성하지 않도록 합니다.
if (numberB == '777') {
  ...
}

// Good
if (numberB === 777) {
  ...
}
```

#### 함수 선언할 때 JSDoc을 작성합니다.

**함수를 작성할 때, JSDoc을 작성하여 함수의 역할과 파라미터, 반환값에 대한 설명을 작성합니다.** TS 타입이 존재하는 경우, 타입을 명시하고, 그렇지 않을 경우 데이터 타입을 명시합니다. 아래 예시 코드를 보면 selectedNodeIds는 string\[] 타입이고, project는 Project 타입이라는 것을 알 수 있습니다.

```javascript
import Project from "@builderTypes/builder/Project";
import getCopiedNodes from "./getCopiedNodes";

/**
    @param {boolean} isSelectedNothing - 선택된 노드가 없는지 여부
    @param {string[]} selectedNodeIds - 선택된 노드 아이디 배열
    @param {Project} project - 전역 상태 관리 객체
    @description - 선택된 노드가 없으면 아무것도 하지 않고, 선택된 노드가 있으면 선택된 노드를 복사하여 localStorage에 저장한다.
 */
const handleCopy = (
  isSelectedNothing: boolean,
  selectedNodeIds: string[],
  project: Project
) => {
  if (isSelectedNothing) return;

  const newCopiedNodes = getCopiedNodes(selectedNodeIds, project.nodeTree);

  localStorage.setItem("copiedNodes", JSON.stringify(newCopiedNodes));
};

export default handleCopy;

```

#### 타입정의

중복된 타입 정의를 최대한 줄이고, 타입을 재사용할 수 있도록 노력합니다. 상속을 통해 타입을 재사용할 수 있습니다. **Interface 로 정의된 타입은 prefix 로 I 를 붙이고, Type 으로 정의된 타입은 prefix 로 T 를 붙입니다.** 상속이 필요한 경우, 타입을 정의할 때 interface 를 사용합니다. type 은 상속이 불가능합니다. 상속이 필요하지 않은 경우 type 을 사용하도록 지향합니다. (절대적 규칙은 아닙니다. 개발자가 만약 나중에 상속이 필요하다는 판단이 들면 interface 로 작성해도 무방합니다.)

```javascript
// Bad
interface Project {
  id: string;
  name: string;
  description: string;
}

// Good
interface IProject {
  id: string;
  name: string;
  description: string;
}

// Bad
type Project = {
  id: string;
  name: string;
  description: string;
};

// Good
type TProject = {
  id: string;
  name: string;
  description: string;
};
```

**전역적으로 사용하게 되는 타입은 types 폴더에 정의하여 사용합니다.**

```javascript
// types/IProject.ts
interface IProject {
  id: string;
  name: string;
  description: string;
}

export default IProject;
```

전역적으로 사용하지 않는 타입은 해당 파일 내에서 작성하거나, 해당 파일이 포함된 폴더 하위에서 작성합니다. (전역적으로 사용하지 않는 타입은 해당 파일 내에서 작성하는 것이 좋습니다.) 헥사곤 차트에서만 사용될 타입들을 types.ts 파일에 모두 작성하거나, 해당 파일 내부에 작성합니다. **이건 컨벤션으로 모든 경우의 수를 세세하게 다루기 어렵기 때문에,** 개발자가 생각하였을 때 타입이 너무 많아 모듈로 분리해야될 경우에는 분리하는 것이 좋습니다. 만약 타입이 너무 많지 않다면, 해당 파일 내부에 작성하는 것이 좋습니다.

#### 배열 타입 지정 하는 방법

배열에는 타입을 지정하는 방법이 여러개가 있습니다. 그 중에서도 가장 많이 사용되는 방법은 아래와 같습니다.

```javascript
// Bad
const arr: Array<string> = ['a', 'b', 'c'];

// Good
const arr: string[] = ['a', 'b', 'c']; // 이 방법을 사용하도록 합니다ㅏ.
```

#### 타입 추론 ( Type Inference ) 를 최대한 활용합니다.

타입 스크립트는 타입 추론을 통해 변수의 타입을 추론합니다. 타입 추론을 최대한 활용하여 코드를 작성합니다. 굳이 명시하지 않아도 되는 타입은 명시하지 않습니다. (경제성)

```javascript
// Bad
const a: number = 1;
const b: number = 2;
const c: number = a + b;


// Good
const a = 1;
const b = 2;
const c = a + b;
```

#### 함수 작성 시 주의사항

**함수의 매개변수를 최대한 줄이고, 하나의 역할만 수행하도록 작성합니다.** 함수의 역할이 많아지면, 함수를 분리하여 작성합니다. 함수는 **순수 함수**로 작성합니다. 함수 내부에서 외부의 상태를 변경하지 않도록 주의합니다. **SOLID 원칙**을 준수하도록 코드를 작성합니다. 코드리뷰 시 SOLID 원칙을 준수하는지 확인합니다.

참고자료 : https://fe-developers.kakaoent.com/2023/230330-frontend-solid/

#### 테스트 코드 모듈 관리

테스트 코드는 최대한 작성하도록 노력합니다. 테스트 코드는 코드의 안정성을 보장하고, 코드 리팩토링 시 안정성을 보장합니다. **작성된 모듈에 대한 테스트 코드를 바로 옆에 두도록 합니다.** (테스트 코드는 해당 모듈과 같은 폴더에 작성합니다.) 글로벌 패턴의 장점은 테스트 커버리지를 한눈에 볼 수 있다는 장점이 존재하고, 테스트 코드 작성하게 될 때, 실제 함수를 import 해야하는데, 경로가 길어지면 테스트 코드 작성이 어려워질 수 있기 때문에, 글로벌 패턴을 사용합니다.

저도 이 부분을 고민을 많이 했었는데, 현직자들의 의견을 취합하여 작성하였습니다. 더 많은 정보를 원하시면 아래 링크를 참고해주세요.

https://careerly.co.kr/qnas/4968

#### AJV 를 사용하여 API 요청 하여 외부 시스템과 동기화 할 때, validation 을 수행합니다.

**로직에 스키마를 확인하는 코드를 작성하지 않습니다.** AJV 를 사용하여 우선 해당 함수에서 인자로 받은 데이터에 대한 데이터 유효성 검사를 수행한 이후에 로직을 수행하도록 합니다.

```javascript
// Bad 이렇게 작성하지 않도록 합니다. 로직에 스키마를 확인하는 코드를 작성하지 않습니다.
function createProject(project) {
  if (!project.id) {
    throw new Error("id is required");
  }
  if (!project.name) {
    throw new Error("name is required");
  }
  if (!project.description) {
    throw new Error("description is required");
  }

  // 로직 수행
}

// Good
import Ajv from "ajv";
import IProject from "./types/IProject";

const ajv = new Ajv();

const schema = {
  type: "object",
  properties: {
    id: { type: "string" },
    name: { type: "string" },
    description: { type: "string" },
  },
  required: ["id", "name", "description"],
  additionalProperties: false,
};

const validate = ajv.compile(schema);

function createProject(project: IProject) {
  const valid = validate(project);
  if (!valid) {
    throw new Error("invalid project");
  }

  // 로직 수행
}

```

#### if 문 사용 시, 중첩 if 문을 최대한 줄입니다.

**if 문을 사용할 때, 중첩 if 문을 최대한 줄이도록 노력합니다.** 중첩 if 문은 가독성을 떨어뜨리고, 코드의 복잡도를 높입니다. 중첩 if 문을 최대한 줄이도록 노력합니다.

```javascript
// Bad

if (condition1) {
  if (condition2) {
    // 로직 수행
  }
}

// Good
if (condition1 && condition2) {
  // 로직 수행
}
```

#### 함수 작성시 결합도를 줄입니다.

함수를 작성할 때, 함수의 결합도를 줄이도록 노력합니다. 함수의 결합도가 높으면, 함수의 재사용성이 떨어지고, 유지보수가 어려워집니다. 함수의 결합도를 줄이도록 노력합니다.

```javascript
// Bad
function createProject(project) {
  if (!project.id) {
    throw new Error("id is required");
  }
  if (!project.name) {
    throw new Error("name is required");
  }
  if (!project.description) {
    throw new Error("description is required");
  }

  // 로직 수행
}

// Good
function validateProject(project) {
  // validation 도 ajv 를 사용하여 수행합니다.
  // 그리고 발리데이션을 수행하는 로직을 아예 따로 분리하도록 합니다.
  // 하나의 함수는 최대한 하나의 역할만 수행하도록 합니다. SRP 원칙을 준수합니다.
}

function createProject(project) {
  validateProject(project);

  // 로직 수행
}
```

#### 마무리하며

컨벤션보다 더 중요한 원칙은 **코드의 일관성**과 **가독성**을 높이는 것입니다. 코드를 작성할 때, 다른 개발자가 코드를 읽었을 때, 이해하기 쉽고, 유지보수가 쉽도록 작성하는 것이 중요합니다. 코드 컨벤션은 이러한 원칙을 지키기 위한 **도구로 사용되어야 합니다**. 코드 컨벤션과 관련하여 더 좋은 접근 방법이 있다면, 언제든지 변경할 수 있습니다. 코드 컨벤션의 가장 중요한 목적은 코드의 일관성과 가독성을 높이는 것입니다. 그리고 무엇보다 경제적인 측면에서 목적을 달성하기 위해 최대한 리소스를 절약하는데 포커스를 둡니다. 코드 컨벤션은 규약일 뿐이며, 법칙이 아니기 때문에 최소한의 가이드라인을 제시하고, 개발자가 이를 따르도록 유도하는 것이 중요합니다.

컨벤션 작성자 : [박주강](https://github.com/jukangpark)
