---
layout: post
title: "[백준 - Dijkstra] 1753 - 최단경로 - 파이썬"
date: 2019-12-06 16:00:00
author: Dojin Kim
categories: Problem_Solving
tags: ps algorithm python
cover:  "/assets/instacode.png"
---

# 1753 - 최단경로 [Dijkstra]

### 출처 : <a href="https://www.acmicpc.net/problem/1753"> 백준 1753 최단경로</a>

더 많은 문제들에 대한 Python3 풀이는 [Github Repo](https://github.com/dojinkimm/AlgorithmPractice)에서 찾아볼 수 있다

## 문제
단방향 그래프 정보와 시작점이 주어진다. 시작점에서 다른 노드들 까지의 최단거리를 구하는 것이 문제이다. 최단거리를 구하는 알고리즘은 많지만 가장 많이 사용되는 다익스트라(Dijkstra) 알고리즘을 사용해서 문제를 풀었다.

Python 리스트의 index는 0부터 시작하기 때문에, 각 노드들에 대한 정보가 주어졌을 때 -1씩 해줬다. 그러고 나서 그래프 노드들에 대한 정보를 다익스트라 알고리즘에 적용했다. 다익스트라 알고리즘은 우선순위 큐를 사용해서 최단거리에 있는 노드를 선택하고 그 노드와 인접된 노드까지의 거리를 계산한다. 계산이 끝나고 현 노드에서 인접한 노드까지의 거리가 최소라면 그 인접한 노드를 우선순위 큐에 추가한다. 

다익스트라 알고리즘은 한 노드에서 모든 다른 노드까지의 최단거리를 구하는 알고리즘이다. Python3에서 우선순위큐를 사용하기 위해서 `heapq`라는 모듈을 import해서 사용했다. 


## 풀이
```python
# BOJ 1753 - 최단경로
import sys
from heapq import heappush, heappop
r = sys.stdin.readline
INF = 1e9


def dijkstra(v, k, g):
    dist = [INF] * v
    dist[k - 1] = 0
    q = []
    heappush(q, [0, k-1])

    while q:
        cost, pos = heappop(q)

        for p, c in g[pos]:
            c += cost
            if c < dist[p]:
                dist[p] = c
                heappush(q, [c, p])

    return dist


V, E = map(int, r().split())
K = int(r())
graph = [[] for _ in range(V)]
for _ in range(E):
    u, v, w = map(int, r().split())
    graph[u-1].append([v-1, w])

for d in dijkstra(V, K, graph):
    print(d if d != INF else "INF")

```