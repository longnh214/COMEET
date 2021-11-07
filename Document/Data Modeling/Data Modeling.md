## Data Modeling

## Reference data

- [포스트 코로나 데이터 시각화 경진대회](https://dacon.io/competitions/official/235618/data/)

- [서울 자치구별 확진자 현황](https://www.seoul.go.kr/coronaV/coronaStatus.do)

- [서울시 유동인구 데이터](https://www.bigdatahub.co.kr/product/list.do?menu_id=1000157) : 2019.03 ~ 2021.01


## Table 

**1. SKT 유동인구 데이터**

<table>
    <thead>
        <tr>
          <th>Name</th>
          <th>일자</th>
          <th>시간</th>
          <th>나이대</th>
          <th>성별</th>
          <th>지역</th>
          <th>구 이름</th>
          <th>유동인구수</th>
        </tr>
    </thead>
    <tbody>
        <tr>
          <th>예시</th>
          <td>8자리 날짜</td>
          <td>00시 ~ 23시</td>
          <td>10대 ~ 60대 이상</td>
          <td>남성, 여성</td>
          <td>서울 지역 내</td>
          <td>서울 지역 구 이름</td>
          <td>만 단위 자릿수</td>
        </tr>
    </tbody>
    <tbody>
        <tr>
          <th>예시</th>
          <td>20210115</td>
          <td>01</td>
          <td>70</td>
          <td>남자</td>
          <td>서울</td>
          <td>성북구</td>
          <td>38000</td>
        </tr>
    </tbody>
</table>

<p>

**2. adstrd_master.csv : 8자리 행정동 코드 데이터**

<table>
    <thead>
        <tr>
          <th>Name</th>
          <th>adstrd_code</th>
          <th>adstrd_nm</th>
          <th>brtc_nm</th>
          <th>signgu_nm</th>
        </tr>
    </thead>
    <tbody>
        <tr>
          <th>예시</th>
          <td>행정동 코드</td>
          <td>행정동명</td>
          <td>시도명</td>
          <td>시군구명</td>
        </tr>
    </tbody>
    <tbody>
        <tr>
          <th>예시</th>
          <td>110111</td>
          <td>영등포구</td>
          <td>서울특별시</td>
          <td>영등포구</td>
        </tr>
    </tbody>
</table>
<p>

**3. card.csv : 업종 별 결제금액 데이터(카드내역)**
<table>
    <thead>
        <tr>
          <th>Name</th>
          <th>receipt_dttm</th>
          <th>adstrd_code</th>
          <th>adstrd_nm</th>
          <th>mrhst_induty_cl_code</th>
          <th>mrhst_induty_cl_nm</th>
          <th>selng_cascnt</th>
          <th>salamt</th>
        </tr>
    </thead>
    <tbody>
        <tr>
          <th>예시</th>
          <td>카드사용내역을 접수한 일자</td>
          <td>가맹점 위치 기준 행정동 코드</td>
          <td>가맹점 위치 기준 행정동</td>
          <td>가맹점 업종코드</td>
          <td>가맹점 업종명</td>
          <td>매출발생건수</td>
          <td>(하루기준)매출발생금액</td>
        </tr>
    </tbody>
</table>
<p>

**4. index.csv : 품목 별 소비지수 데이터**
<table>
    <thead>
        <tr>
          <th>Name</th>
          <th>period</th>
          <th>catl</th>
          <th>catm</th>
          <th>age</th>
          <th>gender</th>
          <th>sido</th>
          <th>sigungu</th>
          <th>cgi</th>
        </tr>
    </thead>
    <tbody>
        <tr>
          <th>예시</th>
          <td>기준월</td>
          <td>대분류</td>
          <td>중분류</td>
          <td>나이대</td>
          <td>성별명</td>
          <td>지역</td>
          <td>세부지역</td>
          <td>카테고리성장지수</td>
        </tr>
    </tbody>
</table>

<p></p>
- 카테고리성장지수 (2018년 월평균 대비 매출 성장 비율, 100을 기준으로 이상이면 매출 상승, 이하면 하락)
<p>

**5. corona :서울 자치구별 코로나 확진자 현황(개인정보의 이유로 확진자 동선은 2주뒤에 삭제됩니다.)**
<table>
    <thead>
        <tr>
          <th>Name</th>
          <th>brtc_nm</th>
          <th>signgu_nm</th>
          <th>date</th>
          <th>status</th>
        </tr>
    </thead>
    <tbody>
        <tr>
          <th>예시</th>
          <td>시도명</td>
          <td>걸린 날짜</td>
          <td>퇴원 여부</td>
        </tr>
    </tbody>
</table>
<p></p>

* 파싱 주기 : 12시에 업데이트되므로 매일 12시 마다 일괄 업데이트 
<p>

**6. score : 점수 부여 기준**

- 시군구명
- 점수

* 점수 가중치 요소

- 퇴원여부 : 사망, 퇴원, 빈칸(퇴원X, 치료중)
- 확진자수
    - 상대값 가중치 : 하루 전 혹은 일주일 전을 기준으로 현재 확진자 수의 추이 변화를 기준으로가중치를 둔다.
                      ( 구마다 걸린 확진자 수의 평균을 잡아서 평균보다 많으면 위험지역, 적으면 안전지역 )
    - 절대값 가중치 : 5명 단위로 끊어서 현재 확진자 수를 기준으로 가중치를 둔다. (구 별 확진자 기준)

- 유동인구 : 유동인구 대비 확진자 수를 고려하여 비율이 상대적으로 작으면,
             (예측 유동인구를 두어 예상추이를 생각해본다.)
- 카드사용량 - 매출발생건수를 기준으로 유동인구 대비 비율의 높고 낮음을 판별하여 가중치를 둔다.

**7. user : 회원**

- _id : ex) 1234
- email : ex) "test@test.com"
- password : ex) "password"
- nickname : ex) comeet
- address : ex) "서울특별시 강남구"
- emailToken(이메일 인증을 위한 토큰) : ex) "19ADVC3DS"
- flag(인증을 받은 사용자인지 아닌지 판단) : ex) true
- quitFlag(탈퇴여부) : ex) false
- shortcuts(회원 검색 로그) : 

```
"user" : {
	"_id" : "",
	"email" : "test@test.com",
	"password" : "암호화된 패스워드",
	"nickname" : "test",
	"address" : "",
	"emailToken" : "", //이메일 인증을 위한 토큰
	"flag" : true, //인증을 받은 사용자인지 아닌지 flag
	"quitFlag" : false, // 탈퇴 flag
	"shortcuts" : [
		{"_id" : "Object_id("602d0fc3628e395b21169645")","nickname" : "낙훈", "address" : "경기도 고양시 일산동구"},
		{"_id" : "Object_id("602d0fc3628e395b21167645")","nickname" : "세준", "address" : "서울특별시 관악구"},
		{"_id" : "Object_id("602d0fc3628e395b21163645")","nickname" : "덕인", "address" : "서울특별시 영등포구"}
	]
}
```
