
최대 플로우 문제에서 비용이라는 제약이 추가된 형태의 문제이다.

말 그대로 최대 플로우를 흘리는데 드는 비용을 최소화해야한다.

기본적인 로직은 간단하다.

1. 최소 비용(최소 가중치)으로 목적지에 도달할 수 있는 증가 경로를 찾는다.
2. 증가 경로로 플로우를 흘린다.
3. 더 이상 증가 경로를 찾지 못할 때 까지 반복한다.

어떤 간선 $(u,v)$ 에 비용(가중치)을 설정해줄 때, 용량이 0인 반대방향의 간선 $(v,u )$ 에 대하여 $(u, v)$ 간선의 가중치에 -1을 곱한 음수 가중치를 설정해줌으로 이미 flow가 있는 간선을 취소할 때의 비용도 정확하게 계산할 수 있다.

그렇지만 위의 방법을 사용할 경우 음의 가중치를 포함한 최소 비용 경로를 찾아야하기 때문에 벨만포드 알고리즘처럼 음의 가중치를 허용하는 알고리즘을 사용해야한다.

일반적으로는 SPFA 가 가장 많이 사용된다고 한다.

이 알고리즘의 로직은 간단하다.

```plain text
procedure Shortest-Path-Faster-Algorithm(_G_, _s_)
  1    for each vertex _v_ ≠ _s_ in _V_(_G_)  // 시작점을 제외한 모든 간선의 거리를 INF로 설정
  2        d(_v_) := ∞  
  3    d(_s_) := 0 // 시작점의 거리는 0으로 설정
  4    push _s_ into _Q_ // 시작점을 q 에 넣음
  5    while _Q_ is not empty do // bfs로 최소 비용 경로 탐색
  6        _u_ := poll _Q_
  7        for each edge (_u_, _v_) in _E_(_G_) do
  8            if d(_u_) + w(_u_, _v_) < d(_v_) then
  9                d(_v_) := d(_u_) + w(_u_, _v_)
 10                if _v_ is not in _Q_ then // 큐에 들어있지 않을 때만 큐에 삽입 (우선순위 큐가 아니기 때문에 필요)
 11                    push _v_ into _Q
```



에드몬드-카프 알고리즘처럼 8번 조건문을 통과했을 때 검사하는 정점의 이전 정점을 현재 정점으로 갱신해줌으로 플로우를 흘릴 경로를 쉽게 찾을 수 있다.

이를 c++ 코드로 다음과 같이 작성할 수 있다.

```cpp
int mcmf() {  
  
    int res = 0;  
    int total_weight = 0;
    queue<int> q;  
  
    bool inQ[802];  
    memset(inQ, false, sizeof(inQ));  
    while(true) {  
        q.push(s);  
        inQ[s] = true;  
        memset(pre, -1, sizeof(pre));  
        fill(dist, dist + siezof(dist), INF);  
        dist[s] = 0;  
        while (!q.empty()) {  
            int curr = q.front();  
            q.pop();  
            inQ[curr] = false;  
            for (auto& e: g[curr]) {  
                if (cap[curr][e] - fl[curr][e] > 0 && dist[e] > dist[curr] + w[curr][e]) {  
                    pre[e] = curr;  
                    dist[e] = dist[curr] + w[curr][e];  
                    if (!inQ[e]) {  
                        q.push(e);  
                        inQ[e] = true;  
                    }  
                }  
            }  
        }  
  
        if (pre[t] == -1) break;  
  
        int flow = INF;  
        for (int i = t; i != s; i = pre[i]) {  
            flow = min(flow, cap[pre[i]][i] - fl[pre[i]][i]);  
        }  
        for (int i = t; i != s; i = pre[i]) {  
            fl[pre[i]][i] += flow;  
            fl[i][pre[i]] -= flow;  
            total_weight += flow * w[pre[i]][i];  
        }  
        res += flow;  
    }  
    return res;  
}
```



기본적으로 경로를 찾을 때 벨만포드 알고리즘을 사용하기 때문에 시간 복잡도는 $O(|V||E|f)$ 이다. 