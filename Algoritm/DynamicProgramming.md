# 동적 프로그래밍(Dynamic Programming)

- 키워드
> 작게 분할, 메모이제이션, 일종의 전략, 공식처럼 사용하자. 손이 많이간다. 머리를 많이 써야한다.

- 다이나믹 프로그래밍
> 주어진 문제를 해결하기 위해, 문제를 여러 개의 하위 문제로 나누어 푼 다음,
> 그것을 결합하여 최종적인 목적에 도달하는 것

- 상향식 접근법
> 가장 하위의 문제를 먼저 해결한 다음 값을 저장하고 해당 값으로 상위 문제를 풀어간다.
> 풀고 -> 값도출 -> 풀고 -> 값도출 -> 풀고 -> 값도출... ->최종 알고리즘

- 메모이제이션(memoization)
> 컴퓨터 프로그램이 동일한 계산을 반복해야할 때, 이전에 계산한 값을 메모리에 저장함으로써
> 동일한 계산의 반복 수행을 제거하여 프로그램 실행 속도를 빠르게 하는 기술.


## 예제1 (java)
- 피보나치 수열
// JAVA
// 첫째 및 둘째 항이 1이며, 그 뒤의 모든 항은 바로 앞 두항의 합인 수열
public class Dynamic{
  public int funcDynamic(int number){
    Integer[] value = new Integer[number+1]; //0부터 시작, 6까지 계산하기 위해 +1을 함.
    value[0] = 0;
    value[1] = 1;
    for(int index =2; index <number+1; index++ ){
      value[index] = value[index-1] + value[index-2];
    }
    
    retunr value[number];
  }
}
```

## 예제2 (kotlin)
```kotlin
/**
 * 정수 4를 1, 2, 3의 합으로 나타내는 방법은 총 7가지가 있다. 합을 나타낼 때는 수를 1개 이상 사용해야 한다.
 * 1+1+1+1
 * 1+1+2
 * 1+2+1
 * 2+1+1
 * 2+2
 * 1+3
 * 3+1
 * 정수 n이 주어졌을 때, n을 1, 2, 3의 합으로 나타내는 방법의 수를 구하는 프로그램을 작성하시오.
 *
 *
 * 첫째 줄에 테스트 케이스의 개수 T가 주어진다. 각 테스트 케이스는 한 줄로 이루어져 있고, 정수 n이 주어진다. n은 양수이며 11보다 작다.
 *
 * 각 테스트 케이스마다, n을 1, 2, 3의 합으로 나타내는 방법의 수를 출력한다.
 */
fun main(){
    var testCase = readLine()!!.toInt()
    var case = mutableListOf<Int>()

    var solutionCase = Array<Int>(11){0}    //n 은 11보다 작음

    repeat(testCase){
        var cases = readLine()!!.toInt()
        case.add(cases)
    }

    solutionCase[1]=1       // 1 은 1로만 나타낼 수 있음
    solutionCase[2]=2       // 2 는 1+1 , 2
    solutionCase[3]=4       // 3은 1+1+1, 1+2, 2+1, 3

    for(i in 4 .. 10){
        solutionCase[i] = solutionCase[i-1]+solutionCase[i-2]+solutionCase[i-3]
    }
    case.forEach {
        println(solutionCase[it])
    }
}
```
