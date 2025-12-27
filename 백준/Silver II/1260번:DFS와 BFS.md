[https://www.acmicpc.net/problem/1260](https://www.acmicpc.net/problem/1260)

```javascript
const input = require('fs')
    .readFileSync(process.platform === 'linux' ? '/dev/stdin' : './input.txt')
    .toString()
    .trim()
    .split('\n');

// 정점 개수, 간선 개수, 탐색 시작 정점 번호
const [N, M, V] = input[0].split(' ').map(Number);

const nodes = Array.from({ length: N }, () => Array(N).fill(0)); // N개의 정점들

// 간선 채우기
for (let i = 1; i <= M; i++) {
    const [start, end] = input[i].split(' ').map((x) => Number(x) - 1); // 연결된 두 정점

    // 양방향이라서 두 곳 모두 체크
    nodes[start][end] = 1;
    nodes[end][start] = 1;
}
// 정점 현황: [ [ 0, 1, 1, 1 ], [ 1, 0, 0, 1 ], [ 1, 0, 0, 1 ], [ 1, 1, 1, 0 ] ]

const answerDFS = [];
const visitedDFS = new Array(N).fill(false);
const answerBFS = [];
const visitedBFS = new Array(N).fill(false);

// DFS
// dfs함수 설계
function dfs(v) {
    // (1) 현재 노드 방문 처리
    visitedDFS[v] = true;

    // (2) 결과 배열에 추가
    answerDFS.push(v + 1); // 이때 +1 해야한다.

    // (3) 연결된 노드들 순회하며 재귀 호출
    for (let i = 0; i < N; i++) {
        // 조건1) 현재 정점과 연결되어 있고  2) 방문하지 않은 정점이라면 재귀
        if (nodes[v][i] === 1 && visitedDFS[i] === false) {
            dfs(i);
        }
    }
}

// 시작 정점부터 어떻게? -> dfs에 넣기
dfs(V - 1);
console.log(answerDFS.join(' '));

// BFS
const queue = [V - 1]; // BFS에 사용될 큐. 시작 정점 넣어놓기
visitedBFS[V - 1] = true; // 방문 표시
while (queue.length > 0) {
    const x = queue.shift(); // 방문한 정점
    answerBFS.push(x + 1); // 방문한 순서대로 정답 배열에 넣기
    for (let i = 0; i < N; i++) {
        // 1) 현재 정점과 연결되어 있거나 2) 방문하지 않은 정점은 큐에 넣기
        if (nodes[x][i] === 1 && visitedBFS[i] === false) {
            visitedBFS[i] = true; // 방문 표시 -> for문 안에서 해야하는 이유가 있나? -> 큐에 중복으로 들어가는 경우를 방지하기 위해서
            queue.push(i); // 방문 예약
        }
    }
}
console.log(answerBFS.join(' '));
```
### 🔗 포인트
1. DFS, BFS마다 정답 배열과 방문 기록 배열을 만들어야 한다.
2. 이 문제에서 방문 기록 배열(visited)은 이중 배열이 아닌, 1차 배열로 만들어야 한다.  
   - 해당 정점의 연결 여부를 묻는 것이 아니라, 방문 여부를 표기하기 위해서이다.
4. BFS경우 노드 방문 표시 시점이 중요하다.  
   - 큐에 중복으로 들어가는 경우를 방지하기 위해서이다. 만약 큐에서 꺼낼 때 방문 표시를 한다면, 다른 노드가 똑같은 노드를 큐에 넣는 경우가 발생한다.
