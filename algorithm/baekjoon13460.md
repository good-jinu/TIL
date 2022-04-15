# 구슬 탈출 2

```
#####
#..B#
#.#.#
#RO.#
#####
```

이 게임의 목표는 벽(#)으로 둘러싸인 보드를 상하좌우로 기울여서 파란 구슬(B)를 구멍(O)에 빠뜨리지 않으며
빨간 구슬(R)만을 구멍(O)에 넣어야한다. 

이때, 목표를 가장 빠르게 달성했을 때 행동(한방향 기울이기 당 1번의 행동)의 횟수를 출력하는 것이다.

다만, 10회를 초과하면 -1을 출력한다.

## 백준 문제

[https://www.acmicpc.net/problem/13460](https://www.acmicpc.net/problem/13460)

## 풀이

- 최초로 행동을 할 때 총 4가지(상하좌우)의 행동을 한다.
- 그 다음 행동은 이전 행동이 상하 였을 경우 좌우로만 행동할 수 있고, 좌우였을 경우 상하로만 행동할 수 있다.
- 다만, 행동을 하였을 때 그 행동이 파란 구슬을 구멍에 떨어뜨리거나 이전에 방문했던 위치에 똑같이 방문했을 때 
유효하지 않은 행동이므로 다음 행동을 큐에 새로 넣지 않고 넘긴다.


## Code

```python
import sys

# 입력 받고 초기화
boardmap = []
N, M = map(int, sys.stdin.readline().split())
for i in range(N):
    getstr = list(sys.stdin.readline().strip())
    if 'R' in getstr:
        R = (i, getstr.index('R'))
        getstr[R[1]] = '.'
    if 'B' in getstr:
        B = (i, getstr.index('B'))
        getstr[B[1]] = '.'
    if 'O' in getstr:
        O = (i, getstr.index('O'))
        getstr[O[1]] = '.'
    boardmap.append(getstr)

visited = [[[[False]*M for _2 in range(N)] for _1 in range(M)] for _ in range(N)]
visited[R[0]][R[1]][B[0]][B[1]] = True
# 결과 변수 빛 상태 큐 초기화
result = -1
# 상태를 저장하는 큐에는 R, B, 이동할 방향이 주어짐
new_state_queue = [(R, B, 0), (R, B, 1), (R, B, 2), (R, B, 3)]
# 0: left, 1: up, 2: right, 4:down
directionByState = (
    lambda x: (x[0], x[1] - 1), lambda x: (x[0] - 1, x[1]), lambda x: (x[0], x[1] + 1), lambda x: (x[0] + 1, x[1]))

# 10단계까지만 진행
for i in range(1, 11):
    # state_queue는 현재 진행중인 큐 new_state_queue는 다음 단계에 진행할 예정인 큐
    state_queue = new_state_queue
    new_state_queue = []
    while len(state_queue) > 0:
        result = -1
        getstate = state_queue.pop()
        # isfailed는 파란 구슬이 구멍에 들어간 경우
        isfailed = False
        # nextR, nextB는 다음 큐에 삽입예정인 위치들
        nextR = getstate[0]
        nextB = getstate[1]
        # newRpos와 newBpos는 유효한 위치인지 체크해볼 예정인 위치들
        newRpos = directionByState[getstate[2]](nextR)
        newBpos = directionByState[getstate[2]](nextB)
        # 이동하기전 위치에 R과 B를 넣어서 구슬이 서로 겹치지 않도록 함
        boardmap[nextR[0]][nextR[1]] = 'R'
        boardmap[nextB[0]][nextB[1]] = 'B'
        #isRin은 빨간 구슬(R)이 구멍에 들어갔느지의 여부
        isRin = False
        while True:
            if newBpos == O:
                # 파란구슬 골인
                result = -1
                isfailed = True
                break
            if newRpos == O and not isRin:
                # 빨간구슬 골인 그러나 파란구슬도 들어올 수 있기 때문에 계속진행
                isRin = True
                boardmap[nextR[0]][nextR[1]] = '.'
                result = i

            #빨간 구슬과 파란구슬이 움직일 수 없으면 True 있으면 False
            Rcantmove = True
            Bcantmove = True

            if boardmap[newRpos[0]][newRpos[1]] == '.' and not isRin:
                # 빨간구슬이 구멍이 들어가지도 않아고 이동할 수 있는 상태라면
                boardmap[nextR[0]][nextR[1]] = '.'
                nextR = newRpos
                boardmap[nextR[0]][nextR[1]] = 'R'
                newRpos = directionByState[getstate[2]](nextR)
                Rcantmove = False
            if boardmap[newBpos[0]][newBpos[1]] == '.':
                # 파란구슬이 이동할 수 있는 상태라면
                boardmap[nextB[0]][nextB[1]] = '.'
                nextB = newBpos
                boardmap[nextB[0]][nextB[1]] = 'B'
                newBpos = directionByState[getstate[2]](nextB)
                Bcantmove = False

            #R과 B가 모두 움직일 수 없는 경우 반복문 탈출
            if Rcantmove and Bcantmove:
                break
        boardmap[nextR[0]][nextR[1]] = '.'
        boardmap[nextB[0]][nextB[1]] = '.'
        tmpvisit = visited[nextR[0]][nextR[1]][nextB[0]][nextB[1]]
        visited[nextR[0]][nextR[1]][nextB[0]][nextB[1]] = True
        # 파란구슬이 구멍에 들어갔거나 빨간구슬과 파란구슬이 위치에 변동이 없을 경우 다읔 큐 생성X
        if isfailed:
            continue
        if result > 0:
            break
        if tmpvisit:
            continue
        #if nextR == getstate[0] and nextB == getstate[1]:
        #    continue
        # 다음단계에 진행할 상태를 큐에 삽입
        # 이번에 왼쪽 또는 오른쪽으로 진행 했다면 다음 단계에서는 위아래로 진행, 반대도 성립
        new_state_queue.append((nextR, nextB, (getstate[2] + 1) % 4))
        new_state_queue.append((nextR, nextB, (getstate[2] + 3) % 4))
    if result > 0:
        break
print(result)
```

