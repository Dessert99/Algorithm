# [문제](https://school.programmers.co.kr/learn/courses/30/lessons/72410)
![image](https://github.com/user-attachments/assets/f7d53e0a-be23-4c4c-be1b-6104c54863b3)  

---
## 문제를 보고 든 생각
문자열을 수정하는 조건이 많아서 정규 표현식을 사용해야겠다고 생각했다.

---
## 내 풀이
```javascript
function solution(new_id) {
  let answer = new_id
    .toLowerCase()
    .replace(/[^a-z0-9-_.]/g, '')
    .replace(/\.+/g, '.')
    .replace(/^[.]|[.]$/g, '');

  if (answer.length === 0) answer += 'a';
  if (answer.length > 15) answer = answer.slice(0, 15).replace(/[.]$/, '');
  if (answer.length <= 2) {
    while (answer.length < 3) {
      let last = answer.slice(-1);
      answer += last;
    }
  }
  return answer;
}
```
- 문자열은 총 7단계의 필터링 과정을 거친다. 4단계까지는 `replace`메서드를 체이닝하여 처리했지만, 5단계부터는 `if`문을 사용했다.
- 정규 표현식 정리
  -  `replace(/[^a-z0-9-_.]/g, '')`  
      알파벳 소문자(`[a-z]`), 숫자(`[0-9]`), 특수문자(`[-_.]`)를 제외한(`^`) 다른 문자들(`g`)을 빈문자(`''`)로 교체한다.
  - `replace(/\.+/g, '.')`  
    문자열 `\.`이 반복되면(`+`) 모두(`g`) `.`로 교체한다.
  - `replace(/^[.]|[.]$/g, '')`  
    문자열이 `.`으로(`[.]`) 시작(`^`)하거나(`|`) `.`으로(`[.]`) 끝나면(`$`), 모두(`g`) 빈문자열(`''`)로 교체한다.

---
## 다른 사람 풀이
```javascript
function solution(new_id) {
    const answer = new_id
        .toLowerCase() // 1
        .replace(/[^\w-_.]/g, '') // 2
        .replace(/\.+/g, '.') // 3
        .replace(/^\.|\.$/g, '') // 4
        .replace(/^$/, 'a') // 5
        .slice(0, 15).replace(/\.$/, ''); // 6
    const len = answer.length;
    return len > 2 ? answer : answer + answer.charAt(len - 1).repeat(3 - len);
}
```
내가 `if`문으로 해결했던 단계를 누군가 체이닝으로 이어서 간결하게 풀었다.

- `replace(/^$/, 'a')`  
  빈문자열(`^$`)이라면 `a`로 교체한다.
- `slice(0, 15).replace(/\.$/, '');`  
  문자열 길이를 15이하로 조정하고(`slice(0,15)`) 만약 조절한 문자열의 끝이 `.`라면(`\.$`) `''`빈문자열로 교체한다.
- `return len > 2 ? answer : answer + answer.charAt(len - 1).repeat(3 - len)`  
  만약 문자열의 길이가 2보다 크다면 그대로 반환하고, 2이하라면 문자열의 마지막 문자를(`charAt(len -1)`) 길이가 3이 될 때까지 반복하여 이어붙인(`repeat(3 - len)`) 문자열을 반환하라.
