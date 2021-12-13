## Javascript bundle diet

> 라이브러리를 사용하는 관점에서 Bundle size가 커질 수 있는 상황과 해결 방법

웹사이트 속도는 이탈률에 큰 영향을 준다. 2초 이내 로드시 9%가 이탈하며, 5초 초과시 사용자의 38%가 이탈한다. 
사용자을 잃지 않으려면, 웹페이지 속도가 중요하다. 
웹사이트는 API call이 너무 많거나 critical rendering path에 해당하는 리소스처럼 큰 것처럼 다양한 원인으로 느려질 수 있다.

- 이 발표는 그 중 번들 사이즈에 초점을 맞춤

- byte별로 보면 JS는 같은 크기의 이미지나 웹폰트보다 브라우저가 처리하는 비용이 많이 든다 
  - 이미지는 파일 다운로드 후 디코딩만하면 됨 
  - js는 다운로드 후 파싱을 하고, 컴파일을 하고, 마지막으로 실행까지 여러 단계를 거침. 
  - 그래서 같은 용량이라도 JS 가 처리비용이 더 높다 



### 번들 사이즈를 줄여보자

1. 원인 찾기 

   - 웹팩을 기준으로 webpack Analyse, webpack visualizer, webpack bundle analyzer

   - webpack bundle analyzer를 사용하면 용량별로 시각화해주기 때문에 어떠한 라이브러리가 많이 사용되고 있는지 확인할 수 있으며, 사이드바를 이용해 검색과 필터링도 가능하다 . 게다가 사용중인 라이브러리의 디펜던시들도 찾을 수 있어 직접 사용하지 않지만 번들에 포함된 라이브러리들을 확인할 수 있다 

2. 용량이 큰 라이브러리는 더 가벼운 라이브러리로 대체하고, 용도가 겹치는 라이브러리는 하나의 라이브러리로 통일하는 게 번들 용량을 줄이는 시작이다 

   - 종종 같은 라이브러리지만 버전이 다른 경우를 발견할 수 있다 

   - 왜 이런일이 생기고 어떻게 피할 수 있을까
     - npm의 특성때문임

   - 중복된 모듈을 줄일 수 있는 명령어 
     - npm dedupe
     - yarn deduplicate
     - yarn dedupe(yarn 2) 

- webpack의 alias 기능 

  - 직접 lodash를 사용하지 않더라도 다른 서드파티 라이브러리로 인해 lodash가 번들에 포함되는 경우가 많음.  lodash는 다양한 버전의 라이브러리가 있어 문제임.

    - cjs, esm 외에 lodash.get 처럼 기능별로 단독 패키지도 존재 

    - webpack의 alias 기능을 통해 이와 같은 라이브러리를 lodash만 이용하도록 만들 수 있음

      ```
      {
      	resolve: {
      		alias: {
      			'lodash-es': 'lodash',
      			'lodash.get': 'lodash/get',
      			'lodash.isplainobject': 'lodash/isPlainObject'
      		}
      	}
      }
      ```

- babel-plugin-transform-imports 

  - 라이브러리의 중복을 피했다면 꼭 필요한 코드만 사용하도록 수정해야함.
  - 모든 라이브러리가 tree-shaking을 지원하지는 않음 (lodash)
  - babel-plugin-transform-imports를 사용하여 소스코드를 수정하지 않고도 결과물을 최적화할 수 있음 

- 더 가벼운 라이브러리라 추가해봤더니 polyfill을 추가하는 경우로 더 많은 용량을 차지하는 경우가 생기기도 함 → 명시적으로 끄거나 브라우저 판단을 더 잘해주는 라이브러리 사용을 권함 (CRA는 명시적으로 꺼둔 node polyfill 설정을 가지고 있음) 

- 설치전 라이브러리를 잘 고르고 비교하기 위해 Bundle Phobia 사이트 추천 
  - gzip 용량, 다운로드 속도, 각 버전별 용량, 라이브러리 디펜던시를 쉽게 확인 가능 
  - 사용하려는 라이브러리 용량이 조금 크더라도 이미 사용하고 있는 라이브러리와 많이 겹친다면 용량 변화는 미미할테니 고려해볼 수 있음
  - 비슷한 용량의 라이브러리를 추천해주기도 함
  - exports analysis → tree shaking 되었을때 함수별로 용량을 얼마나 차지할지 미리 확인해 볼 수 있다 

- 더 가벼운 라이브러리 만들기!
  - 정적 분석을 통해 필요한 코드만 가져오는 기술 → tree-shaking 
  - 나무를 흔들면 열매가 떨어지는 것처럼 불필요한 코드를 정적분석을 통해 제거하는 기술 
  - 정말로 사용중인 코드만 번들에 포함되기 때문에 더 가벼운 번들을 만들 수 있음 


### Reference
https://toss.im/slash-21/sessions/3-2
