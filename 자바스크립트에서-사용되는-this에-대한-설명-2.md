> 이 문서는 https://rainsoft.io/gentle-explanation-of-this-in-javascript/ 를 번역한 내용입니다.

## 목차


1. [this에 대한 미스터리](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#1-this%EC%97%90-%EB%8C%80%ED%95%9C-%EB%AF%B8%EC%8A%A4%ED%84%B0%EB%A6%AC)
1. [함수 실행](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#2-%ED%95%A8%EC%88%98-%EC%8B%A4%ED%96%89)
  1. [함수 실행에서의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#22-%EC%97%84%EA%B2%A9-%EB%AA%A8%EB%93%9C%EC%97%90%EC%84%9C-%ED%95%A8%EC%88%98-%EC%8B%A4%ED%96%89%EC%97%90%EC%84%9C%EC%9D%98-this)
  1. [엄격 모드에서 함수 실행에서의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#22-%EC%97%84%EA%B2%A9-%EB%AA%A8%EB%93%9C%EC%97%90%EC%84%9C-%ED%95%A8%EC%88%98-%EC%8B%A4%ED%96%89%EC%97%90%EC%84%9C%EC%9D%98-this)
  1. [실수: 내부 함수에서의 this를 사용할 때](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#23-%EC%8B%A4%EC%88%98-%EB%82%B4%EB%B6%80-%ED%95%A8%EC%88%98%EC%97%90%EC%84%9C%EC%9D%98-this%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%A0-%EB%95%8C)
1. [메소드 실행](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#3-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%8B%A4%ED%96%89)
  1. [메소드 실행에서의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#31-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%8B%A4%ED%96%89%EC%97%90%EC%84%9C%EC%9D%98-this)
  1. [실수: 객체로부터 메소드를 분리할 때](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#32-%EA%B0%9D%EC%B2%B4%EB%A1%9C%EB%B6%80%ED%84%B0-%EB%A9%94%EC%86%8C%EB%93%9C%EB%A5%BC-%EB%B6%84%EB%A6%AC%ED%95%A0-%EB%95%8C)
1. [생성자 실행](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#4-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%8B%A4%ED%96%89) 
  1. [생성자를 실행할 때의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#41-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%8B%A4%ED%96%89%EC%97%90%EC%84%9C%EC%9D%98-this)
  1. [실수: new 깜빡할 때](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#42-%EC%8B%A4%EC%88%98-new-%EA%B9%9C%EB%B9%A1%ED%95%A0-%EB%95%8C)
1. 간접 실행 
  1. 간접 실행에서의 this
1. 바인딩 함수
  1. 바인딩 함수에서의 this
1. 화살표 함수
  1. 화살표 함수에서의 this
  1. 실수: 화살표 함수로 메소드를 정의할 때
1. 결론


## 5. 간접 실행