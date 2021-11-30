## HTML tag option selected 버그를 찾아서..

* 문제점: select tag 안에 option 의 selected 후 submit 을 하고 나면 캐시에 남게됨
  * 그래서 다른 페이지 혹은 modal 을 열어도 동일한 option 에 selected 가 된 현상을 보게됨

* 해결
  * browser cache 라고 생각해서 검색해봤지만 별 도움안됨
  * jquery 혹은 modal 혹은 bootstrap 문제인가 확인해봤지만 아님
  * 그러다.. 결국 이런 stack overflow 발견 : https://stackoverflow.com/questions/8791935/browser-caching-select-tag-state-and-ignoring-selected-true
  * https://www.dofactory.com/html/select/autocomplete
    * 여기를 살펴보니 이전에 selected 한걸 남긴다길래 .. <select name="color" autocomplete="off">
    * off 를 하고 나니 이전에 설정한 selected 가 없어지고 새로 지정한게 잘 지정되었다.
