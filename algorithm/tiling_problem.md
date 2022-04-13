# Tiling problem

![problemimage](https://media.geeksforgeeks.org/wp-content/cdn-uploads/tilingproblem.png)

타일링 문제의 특징은 다음과 같다.
- 정해진 크기의 보드위에 서로다른 크기의 타일을 채우는 경우의 수를 구한다.
- 보드의 일부분을 타일로 채우고 나머지 부분을 타일로 채우는 경우의 수를 구한다.
- 그 나머지 부분의 타일로 채우는 경우의 수 역시 같은 방법으로 구한다.

같은 방법으로 나머지 보드의 경우의 수를 구한다는 점에서 재귀함수를 사용해볼 수 있다. 그러나 이보다 더 효율적인 다이나믹 프로그래밍을 사용한다.

## 백준 문제

[https://www.acmicpc.net/problem/11726](https://www.acmicpc.net/problem/11726)

2×n 크기의 직사각형을 1×2, 2×1 타일로 채우는 방법의 수를 구하는 프로그램을 작성하시오.

아래 그림은 2×5 크기의 직사각형을 채운 한 가지 방법의 예이다.

![problemimage](https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/11726/1.png)

### -입력

첫째 줄에 n이 주어진다. (1 ≤ n ≤ 1,000)

### -출력

첫째 줄에 2×n 크기의 직사각형을 채우는 방법의 수를 10,007로 나눈 나머지를 출력한다.

## 동작과정

1. n=1일 때와 n=2일 때의 경우의 수를 미리 정의
2. n=3일 때부터 n-1과 n-2의 경우의 수를 구함
3. n의 값을 1씩 높여가며 입력으로 받은 N까지 계산


## Code

```python
import sys
n=int(sys.stdin.readline())
if n>2:
    result = 0
    first=1
    second=2
    for i in range(n-2):
        result=(first+second)%10007
        first=second
        second = result
    print(result)
else:
    print(n)
```

