# 🔴다익스트라(dijkstra)

---

먼저 알아두어야 할 것이, 다익스트라는 출발지에서 도착지까지 가는 최단거리를 찾는 것이고, 크루스칼은 모든 노드를 순회하는데 필요한 최소의 거리를 찾는 것이다.

"오 최단거리는 bfs 아닌가?" 라는 의문이 들 수 있다. 물론 다음 노드로 가는 비용이 모두 같을 경우에는 bfs로 최단거리를 구할 수 있다. 하지만, 비용이 제각각인 경우에는 bfs가 아닌 다익스트라 알고리즘을 사용해야만 최단거리를 찾아낼 수 있다.

즉, 다익스트라는 특정한 시작 지점과 특정한 도착 지점을 받게 된다.

먼저, 입력 부분이다. 보통 (시작점, 도착점, 비용) 을 하나의 세트로 여러 개의 edge들을 받게 된다.

N개의 노드가 있고 1번 부터 N번까지의 노드를 활용한다고 가정한다면 graph 선언부는 다음과 같은 모양을 가질 것이다.

```javascript
let graph = Array.from(new Array(N+1), () => new Array())
```

다음은 edge에 대한 입력 부분이다. edge의 개수는 E개 라고 가정한다.

```javascript
for(let i=0;i<E;i++){
	const [from, to, cost] = input().split(' ').map(Number)
    graph[from].push({ 'to': to, 'cost': cost })
}
```

다음은 다익스트라의 함수이다.

```javascript
// 가장 처음에는 최대값으로 distance 배열을 채워놓고 시작해야 한다.
let distance = new Array(N + 1).fill(Infinity)

	// 시작점을 x로 받는다.
    function bfs(x) {
        distance[x] = 0
        let queue = []
        queue.push({ currNode: x, currCnt: 0 })
        while (queue.length) {
            // 매번 cost를 기준으로 정렬이 필요하다.
            queue.sort((a, b) => {
                return a.currCnt - b.currCnt
            })

            let { currNode, currCnt } = queue.shift()
			
            // 해당 노드에서 갈 수 있는 길을 모두 검사한다. 해당 노드까지 오는데 든 비용 + 다음 노드로 가는데 드는 비용이 distance[가려고하는 노드 번호] 값보다 작다면 갱신하고 queue에 넣는다.
            for (let i = 0; i < graph[currNode].length; i++) {
                if (currCnt + graph[currNode][i]['cost'] < distance[graph[currNode][i]['to']]) {
                    distance[graph[currNode][i]['to']] = currCnt + graph[currNode][i]['cost']
                    queue.push({ currNode: graph[currNode][i]['to'], currCnt: currCnt + graph[currNode][i]['cost'] })
                }
            }
        }
    }
```

다익스트라 함수 호출 이후에는 distance 배열에 시작점 x에서 각 노드로 가는 최소의 비용들이 저장되어 있을 것이다. 

만약에 1번에서 4번으로 가는 최소의 비용이 필요하다면 

```javascript
bfs(1)

console.log(distance[4])
```

이런 식으로 호출, 출력하면 된다.