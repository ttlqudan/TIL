
## 인프런 듣기전 n+1 을 제거하기 위한 공부

### 몇줄 요약
* eager 는 거의 안써야 함, lazy를 써서 나중에 필요 시 조합해서 쓸 수 있게 (확장 가능하게) 기본 설계
* fetch join 을 이용해서 n+1 문제를 해결한다.



### 추가 확인해야 될 사항
```
select()
	.from(A)
	.leftJoin(B).fetchJoin()
	.where( A.B.id == "1" )
```
로 하면 안되고
```
select()
	.from(A)
	.leftJoin(B).fetchJoin()
	.where( B.id == "1" )
```
로 바꿔서 사용해야 쿼리가 줄어듬


### 참고: 인프런의 주옥같은 질문들과 답변들.
https://www.inflearn.com/questions/59632
https://www.inflearn.com/questions/39516
https://www.inflearn.com/questions/30446
https://www.inflearn.com/questions/15876