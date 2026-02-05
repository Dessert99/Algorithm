[https://www.acmicpc.net/problem/20046](https://www.acmicpc.net/problem/20046)
```javascript
const input = require('fs')
    .readFileSync(process.platform === 'linux' ? '/dev/stdin' : './input.txt')
    .toString()
    .trim()
    .split('\n');

let inputIndex = 0;

// 우선순위 큐
class MinHeap {
    constructor() {
        this.heap = [];
    }
    // 유틸
    getParentIndex(curIndex) {
        return Math.floor((curIndex - 1) / 2);
    }

    getLeftChildIndex(curIndex) {
        return curIndex * 2 + 1;
    }
    getRightChildIndex(curIndex) {
        return curIndex * 2 + 2;
    }

    swap(i1, i2) {
        [this.heap[i1], this.heap[i2]] = [this.heap[i2], this.heap[i1]];
    }

    // 삽입
    insert(value) {
        this.heap.push(value);
        this.heapifyUp();
    }

    heapifyUp() {
        // 뒤에 넣었던 녀석 인덱스
        let curIndex = this.heap.length - 1;

        while (curIndex > 0) {
            // 부모 인덱스
            const parentIndex = this.getParentIndex(curIndex);

            // 비교
            if (this.heap[parentIndex][0] <= this.heap[curIndex][0]) {
                // 종료 조건
                break;
            }

            // 끌어내리기
            this.swap(parentIndex, curIndex);
            curIndex = parentIndex;
        }
    }

    extractMin() {
        if (this.heap.length === 0) {
            return null;
        }
        if (this.heap.length === 1) {
            return this.heap.pop();
        }

        const min = this.heap[0];

        // 맨 밑
        this.heap[0] = this.heap.pop();

        this.heapifyDown();

        return min;
    }

    heapifyDown() {
        // 초기값: 맨 위
        let curIndex = 0;

        const length = this.heap.length;

        // 자식보다 크면 내려간다.
        while (true) {
            let minIndex = curIndex;
            const leftChildIndex = this.getLeftChildIndex(curIndex);
            const rightChildIndex = this.getRightChildIndex(curIndex);

            if (leftChildIndex < length && this.heap[leftChildIndex][0] < this.heap[minIndex][0]) {
                minIndex = leftChildIndex;
            }
            if (rightChildIndex < length && this.heap[rightChildIndex][0] < this.heap[minIndex][0]) {
                minIndex = rightChildIndex;
            }

            if (curIndex === minIndex) {
                break;
            }

            this.swap(minIndex, curIndex);

            curIndex = minIndex;
        }
    }
}

//행, 열 (1~1000)
const [m, n] = input[inputIndex++].split(' ').map(Number);

// 지도
const maps = [];
for (let y = 0; y < m; y++) {
    maps.push(input[inputIndex++].split(' ').map(Number));
}

// 가중치 배열
const dist = Array.from({ length: m }, () => Array(n).fill(Infinity));

// 우선 순위 힙
const pq = new MinHeap();

const dy = [-1, 0, 1, 0];
const dx = [0, 1, 0, -1];

// 초기값. 첫 위치가 -1이 아니어야 함.
if (maps[0][0] !== -1) {
    dist[0][0] = maps[0][0];
    // [비용, y, x]
    pq.insert([dist[0][0], 0, 0]);
}

while (pq.heap.length > 0) {
    // 현재 위치. 항상 최소 비용 좌표가 나온다?
    const [curCost, curY, curX] = pq.extractMin();

    // 더 비싼 경로면 무시
    if (dist[curY][curX] < curCost) {
        continue;
    }

    // 상하좌우 탐색
    for (let i = 0; i < 4; i++) {
        const ny = dy[i] + curY;
        const nx = dx[i] + curX;

        // 이동 조건1) 유효한 범위 2) -1이 아닌 곳
        if (ny >= 0 && ny < m && nx >= 0 && nx < n && maps[ny][nx] !== -1) {
            // 이곳으로 이동했을 때 비용 고려
            const nextCost = curCost + maps[ny][nx];

            // 큐에 넣는 조건: 더 효율적인 경로를 발견했을 때
            if (nextCost < dist[ny][nx]) {
                // 최단 비용을 갱신한다. (초기에는 Infinity로 되어있음)
                dist[ny][nx] = nextCost;
                pq.insert([nextCost, ny, nx]);
            }
        }
    }
}

console.log(dist[m - 1][n - 1] === Infinity ? -1 : dist[m - 1][n - 1]);
```
### 🔗 풀이
1. 💡 조건
    - 검정색: 단위도로 : 0
    - 흰색:  도로 없었거나 or 유실된 상태 : 1, 2
    - X: 건설불가: -1
    - 도로 건설 비용: 1 or 2

2. 🤔 어떻게 도로 건설 비용을 계산할까?
    - 위치 정보에 누적 건설 비용 정보를 묶을까?
    - ⭐️ 정답은 "다익스트라알고리즘"을 사용해야 한다.
        1. 가중치 배열 dist
            - 출발지 빼고 모두 Infinity로 초기화한다.
            - 최소 비용 기록
        2. 우선 순위 큐
            - 도입하여 비용이 가장 적은 곳을 먼저 방문한다.
        3. 비교 및 갱신
            - "지금 내 위치를 거쳐서 가는 게, 기존에 알고 있던 길보다 더 싼가?" 를 확인

3. 🤔 이동한 다음에 해야할 일은?
    - 그곳으로 갔을 때, 비용을 고려한다.
    - 더 싼 길인지 확인하고, dist를 고친다.

### 🔗 배운점
1. 왜 그냥 큐가 아닌, 우선순위 큐로 만들어야 할까?
    - "한 번 방문한 놈은 두 번 다시 안 건드린다"를 보장해준다.
    - 다익스트라 알고리즘의 `dist`배열은 한 번만 방문하는 것이 아니라, 여러번 방문하면서 최솟값으로 대체하는 방식이다. 만약 우선순위 큐가 아닌 일반 큐라면 값이 여러번 덮여씌워지기 때문에 무척 비효율적이다. 우선순위 큐를 사용하여 최솟값으로 대체한다면 이후에 값을 덮어씌워야할 상황은 만들어지지 않는다.

2. dist 배열의 값은 계속 바뀌는가?
    - 바뀐다. 단, 점점 값이 작아진다.
      1. 처음엔 삥 돌아가는 비싼 길을 발견해서 dist가 100으로 바뀔 수 있다.
      2. 근데 탐색하다 보니 지름길을 발견했네? 그럼 100을 지우고 30으로 바꾼다.
      3. 또 탐색하다가 더 싼 10짜리 길을 발견하면? 또 10으로 바꾼다.
    - 위 예시는 무척 비효율적이다. 100 -> 30 -> 10 처럼 여러번 바뀌었기 때문이다. 우선순위 큐를 사용하면 100 -> 10 처럼 한 번에 최솟값으로 대체할 수 있다.
