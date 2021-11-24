# 사파리에서 SVG파일이 흐려보인다면
업무를 하는 도중 img 태그 내 src의 확장자가 `.svg`인 파일이 테두리가 깨져서 흐릿해보이는 이슈를 만났다. 
크롬에서는 이상이 없었지만, 사파리에서만 해당 이슈가 재현되었다. 

### svg의 테두리 픽셀이 깨져서 흐릿하게 보일 때
1. [shape-rendering](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/shape-rendering)을 이용하거나
2. svg 태그 안의 소수점을 모두 제거


### svg의 filter의 x, width가 % 로 설정되어 있을 때
크롬의 경우 `width="80"` 처럼 숫자에 단위가 없을 경우, 153px로 인식하는 반면, safari는 15300%로 인식함에 따라
과하게 블러 처리를 한다. 이는 전부 % 값을 바꾸어 해결할 수 있다.


### 기타 해결법

> `<object>`, `<iframe>`, `<embed>` 태그로 svg 파일을 렌더하면 된다. 

[참고]
https://dlwnsdud205.tistory.com/246
https://stackoverflow.com/questions/27245673/svg-image-element-not-displaying-in-safari/27249490#27249490
