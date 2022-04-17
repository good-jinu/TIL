# 미로 탈출

[https://programmers.co.kr/learn/courses/30/lessons/81304#](https://programmers.co.kr/learn/courses/30/lessons/81304#)

![img](https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/0015adcc-d76e-40e3-8004-70dd8deff2ec/Maze.png)

최단 경로 시간을 찾는 문제이므로 다익스트라를 활용해볼 수 있지만 이 문제에는 함정 방이라는 변수가 존재한다.

## 아이디어

> 다익스트라 알고리즘은 특정한 하나의 정점에서 다른 모든 정점으로 가는 최단 경로를 알려줍니다.

기존의 다익스트라 알고리즘은 이미 방문한 노드에 대해서 더 짧은 경로를 찾지 못한 경우에는 최단 시간을 
갱신 하지 않는다. 이 문제에서는 조건을 추가해서 모든 함정방들의 활성화 상태가 동일할 경우에 대해서만 시간을 
비교하고 그렇지 않은 경우에는 그 상태에 대해서 시간을 새롭게 갱신한다.

## 알고리즘 작동

1. (n,n)의 2차원 리스트에 roads의 상태를 저장, traps 정렬, trap 상태에 대한 정보 초기화
2. BFS를 위한 nodequeue 초기화와 방문한 노드는 상태와 함께 딕셔너리 키로 시간과 함께 저장
3. nodequeue가 비워질때까지 BFS 시행
4. 현재 위치에서 이동할 때 현재 노드와 이동할 노드의 함정 활성화 상태에 따라서 이동 road 변경
5. end 노드에 도착 했을 경우 그 시간을 endvalue에 저장
6. 반복문을 계속 시행하다가 endvalue보다 시간이 길어지면 해당 경로는 건너뜀


## Code

```python
from collections import deque


def solution(n, start, end, roads, traps):
    roads_mat = [[0] * n for i in range(n)]
    for i in roads:
        if roads_mat[i[0] - 1][i[1] - 1] == 0:
            roads_mat[i[0] - 1][i[1] - 1] = i[2]
        elif roads_mat[i[0] - 1][i[1] - 1] > i[2]:
            roads_mat[i[0] - 1][i[1] - 1] = i[2]
    traps = tuple(sorted(map(lambda x: x - 1, traps)))
    trapstatusindex = [-1] * n
    trapindexcount = 0
    for i in range(n):
        if i in traps:
            trapstatusindex[i] = trapindexcount
            trapindexcount += 1
    nodequeue = deque()
    nodequeue.append((start - 1, tuple([False] * len(traps))))
    usednode = {nodequeue[0]: 0}
    endvalue = -1

    while len(nodequeue) > 0:
        curnode = nodequeue.popleft()
        curtime = usednode[curnode]
        iscuract = curnode[1][trapstatusindex[curnode[0]]] if trapstatusindex[curnode[0]] > -1 else False
        for i in range(n):
            isiact = curnode[1][trapstatusindex[i]] if trapstatusindex[i] > -1 else False
            if iscuract ^ isiact:
                road_to_d = roads_mat[i][curnode[0]]
            else:
                road_to_d = roads_mat[curnode[0]][i]
            if road_to_d <= 0:
                continue
            if -1 < endvalue < (curtime + road_to_d):
                continue
            newtrapsnapshot = list(curnode[1])
            if trapstatusindex[i] > -1:
                newtrapsnapshot[trapstatusindex[i]] = False if newtrapsnapshot[trapstatusindex[i]] else True
            newnode = (i, tuple(newtrapsnapshot))
            if newnode not in usednode or usednode[newnode] > curtime + road_to_d:
                if newnode[0] == (end - 1):
                    endvalue = curtime + road_to_d
                usednode[newnode] = curtime + road_to_d
                nodequeue.append(newnode)
    return endvalue
```

