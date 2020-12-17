---
layout: post
title:  "[BOJ]2208 보석 줍기"
subtitle:   "보석 줍기 문제 해결"
categories: algorithm
tags: ps boj
comments: true
---

## 개요
> 백준 2208 보석 줍기
  
- 목차
	- [문제](#문제)
	- [접근](#접근)
    - [해결](#해결)

## 문제
---
[https://www.acmicpc.net/problem/2208]()

길이가 M 이상인 구간들 중 최댓값을 구하면 되는 문제이다.   
- 가장 간단히 생각할 수 있는 것은 한 보석을 기준으로 M~N만큼의 범위를 탐색해나가며 해당 구간의 최댓값을 구하는 것이다. 이 경우 시간복잡도는 O(N<sup>2</sup>)이 된다.   
- 하지만 주어진 N의 크기가 100,000이기 때문에, 이 문제는 O(N) 또는 O(NLogN)의 시간복잡도 이내에 풀어야 한다.   

## 접근
---

구간합을 구하는 방법 중 하나로는 누적합을 사용하는 것이다.   
예를 들어 $$0<=x<N$$인 한 정수 $$x$$에 대해 그 누적합을 $$psum(x)$$라고 하자.   
구간 [x, y)의 구간합은 `psum(y) - psum(x)`로 나타낼 수 있다.   
그렇다면 y(y>=m)를 기준으로 길이가 m 이상이 되는 구간의 최대값은 어떻게 구할 수 있을까?   
- [y-m, y)의 구간합은 `psum(y) - psum(y-m)`이다.
- [y-m-1, y)의 구간합은 `psum(y) - psum(y-m-1)`이다.
- ...
- [0, y)의 구간합은 `psum(y) - psum(0)`이다.   

위 결과를 점화식으로 나타내면 다음과 같다.   
`sum(y) =  psum(y) - min({psum(y-k) | m<=k<=y})`

이때, 0 ~ m 까지의 누적합을 구하기 위해서는 m만큼 반복을 해야 할까?   
아래의 식을 한번 보자.   

1. sum(y-1) = psum(y-1) - `min(psum(y-m-1), psum(y-m-2), ..., psum(0))`
2. sum(y) = psum(y) - `min(psum(y-m), psum(y-m-1), psum(y-m-2), ..., psum(0))`

강조된 부분을 보면, `psum(y-m-1), psum(y-m-2), ..., psum(0)`이 반복적으로 나타나는 것을 볼 수 있다. 이 부분을 K라고하고 다시 2번 식을 보자.   

2번 식은 sum(y) = `psum(y)` - `min(psum(y-m), K)`로 나타낼 수 있을 것이다.   
즉, [y-m, y)의 구간합에서 psum(y-m)과 K 중 더 작은 값을 빼준 것이 y까지의 최대 구간합이 된다.

## 해결
---

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	static int N, M;
	static int[] gems;
 	public static void main(String[] args) throws Exception {
		BufferedReader br  = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer stk = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(stk.nextToken());
		M = Integer.parseInt(stk.nextToken());
		gems = new int[N];
		for(int i = 0; i<N; i++) {
			gems[i] = Integer.parseInt(br.readLine());
		}
		int answer = 0;//아무 보석도 줍지 않는 경우
		int K = 0;//모든 보석을 줍는 경우
		int totalSum = 0;
		int subSum = 0;
		for(int i = 0; i<M; i++) {
			totalSum += gems[i];
		}
		for(int i = M; i<N; i++) {
			totalSum += gems[i];
			subSum += gems[i-M];
			K = Math.min(K, subSum);
			answer = Math.max(answer, totalSum - K);
		}
		System.out.println(answer);
	}
}
```




