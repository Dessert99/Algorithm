# [문제](https://www.acmicpc.net/problem/18110)
![image](https://github.com/user-attachments/assets/20371a6d-34f9-48bf-bab5-27660f393338)

---
## 문제를 보고 든 생각
- 상위, 하위 15%를 어떻게 제외할까?  shift pop을 사용할까? 슬라이싱을 사용할까?

---
## 내 풀이
```javascript
const input = require('fs')
  .readFileSync(process.platform === 'linux' ? '/dev/stdin' : './input.txt')
  .toString()
  .trim()
  .split(/\r?\n/)
  .map(Number)
  .slice(1);

let Per15 = Math.round(input.length * 0.15); // 뺴야하는 사람 수

let sortedRank = input.sort((a, b) => a - b);

let middleUsers = sortedRank.slice(Per15, input.length - Per15);

let answer = Math.round(middleUsers.reduce((acc, cur) => acc + cur, 0) / middleUsers.length);

console.log(input.length === 0 ? 0 : answer);
```
`round`으로 빼야할 사람 수를 구한다. 이후 `middle` 구간을 구해서 평균을 낸다. 아주 쉽다.
