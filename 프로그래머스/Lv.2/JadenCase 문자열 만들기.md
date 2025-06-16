# [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12951)
![image](https://github.com/user-attachments/assets/b8c6b5ca-2b53-4c78-9fa4-2c8ab6e1f973)

---
## 문제를 보고 든 생각
- 맨 앞글자 숫자 판단은 어떻게 할까/ -> `isNaN()`

## 내 풀이 - 오답
```javascript
function solution(s) {
    var answer = [];
    let arr = s.split(' ')
    
    for (let word of arr){
		    // isNaN()은 value를 Number로 바꾼 뒤, NaN이면 true 반환
        if (isNaN(word[0])) {
            let firstWord = word[0].toUpperCase()
            answer.push(firstWord+word.slice(1).toLowerCase())
        } else {
	        // 앞글자가 숫자라면 Number가 되어 false 반환
            answer.push(word.toLowerCase())
        }
    }
    return answer.join(' ');
}
```
몇 가지 케이스에서 런타임 에러가 나왔다.  
문제 조건 중 `공백문자가 연속해서 나올 수 있습니다`를 발견했다. 
공백문자에 어떤 메서드가 잘못 쓰여져서 에러가 나온 것은 아닐까?

---
## JS에서 빈 문자열
빈 문자열(`""`)도 길이가 0인 유효한 문자열이기 때문에, `String prototype`에 있는 거의 모든 메서드는 에러 없이 동작한다. `isNaN()`이나 `toUpperCase()`에도 메서드 특성에 따른 기본값이 반환되겠지만 작동되긴 한다.

---
## 문제점
공백에 `toUpperCase()`를 호출해도 문제는 없지만 `undefined`에 호출한다면 에러가 발생한다.  
위 코드 `word = ''`에서 `word[0]`의 결과값은 `undefined`이다.  
그 위에 `toUpperCase()`와 `toLowerCase()`를 바로 쓰게 된다면 런타임 에러가 발생한다.

---
## 개선 코드 - 정답
```javascript
function solution(s) {
    var answer = [];
    let arr = s.split(' ')
    for (let word of arr){
        if (isNaN(word[0])) {
            let firstWord = word.charAt(0).toUpperCase() // charAt으로 변경
            answer.push(firstWord+word.slice(1).toLowerCase())
        } else {
            answer.push(word.toLowerCase())
        }
    }
    return answer.join(' ');
}
```
인덱싱 부분을 `charAt()`으로 바꿨다.  
`charAt(0)`는 빈문자열이라도 공백 그대로를 반환해서 런타임 에러가 발생하지 않는다.
