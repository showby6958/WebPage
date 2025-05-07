# 검색 기능

## like
> SELECT * FROM table WHERE title LIKE '%검색어%';
- like 명령어의 경우 Full Scan 방식이기 때문에 많은 데이터를 검색할때 비효율적이다.(느림)

## Full Text Index
> CREATE FULLTEXT INDEX 작명
ON 테이블명(컬럼명) WITH parser ngram;   
*디폴트가 stop-word 파싱이라서 ngram파싱을 할려면, WITH PARSER ngram 추가*

## stop-word parser
공백, 문장기호, 사용자가 정의한 문자열을 기준으로 토큰을 나누는 방식 

|id|content|
|------|---|
|1|He like bread|
|2|The breads are cheap|
|3|Icecream|

|content|id|
|------|---|
|bread|1, 2|
|cheap|2|
|icecream|3|
|he|1|

> 한글, 일본어, 중국어 등의 경우 제대로 작동하지 않을 수 있다.

|id|content|
|------|---|
|1|안녕하세요|
|2|안녕하십니까|
|3|안녕요|

|content|id|
|------|---|
|안녕하세요|1|
|안녕하십니까|2|
|아이스크림|3|
|안녕요|3|

이 경우, "안녕"을 검색했을때, 검색되지 않는 문제가 발생한다.

## n-gram parser
n-gram 기법으로 할당한 토큰의 크기 n만큼씩 데이터를 인덱스로 파싱해서 사용

|id|content|
|------|---|
|1|안녕하세요|
|2|안녕하십니까|
|3|안녕요|

|content|id|
|------|---|
|안녕|1,2,3|
|녕하|2,3|
|하세|1|
|세요|1|
|하십|2|
|십니|2|
|니까|2|
|녕요|3|

이 경우, "안녕"을 검색한다면 id가 1, 2, 3인 content를 모두 꺼내온다.
