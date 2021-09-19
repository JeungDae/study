# 완전탐색
> 완전 탐색이란 컴퓨터의 빠른 계산능력을 이용해 가능한 경우의 수를 일일이 나열하면서 답을 찾는 방법을 말함.   
> 일명 '무식한 ' 알고리즘. 이러한 알고리즘을 완전탐색(exhaustive search) 라고 부름.

## 재귀호출과 완전탐색
> 재귀함수은 자신이 수행할 작업을 유사한 형태의 여러 조각으로 쪼갠 뒤 그중 한 조각을 수행하고, 나머지를 자기 자신을 호출해 실행하는 함수를 말함.

```javascript
// 간단한 반복문을 재귀 함수로 바꿔서 재귀호출을 이용해 보자.
// 해당 코드는 1부터 n까지의 합을 계산하는 반복 함수와 재귀 함수이다.
// 필수조건 n>=1  / 결과 : 1부터 n까지의 합을 반환
int sum(int n) {
	int ret = 0;
	for(int i = 1; i<= n; ++i )
		ret += i;
	return ret;
}

int recursiveSum(int n) {
	if(n == 1) return 1;
 	return n + recursiveSum(n-1);
}
```
- recursiveSum 을 보면 if문이 있음. 
- 해당 if문은 '더이상 쪼개지지 않는' 최소한의 작업에 도달했을 때 답을 곧장 반환하는 조건문을 포함해야 함. 
- 이렇게 가장 작은 작업들을 가리켜 재귀호출의 기저 사례(base case)라고 함.
## 예시1 
> 독일 로또는 {1, 2, ..., 49}에서 수 6개를 고른다.   
> 로또 번호를 선택하는데 사용되는 가장 유명한 전략은 49가지 수 중 k(k>6)개의 수를 골라 집합 S를 만든 다음 그 수만 가지고 번호를 선택하는 것이다.   
> 예를 들어, k=8, S={1,2,3,5,8,13,21,34}인 경우 이 집합 S에서 수를 고를 수 있는 경우의 수는 총 28가지이다.   
> ([1,2,3,5,8,13], [1,2,3,5,8,21], [1,2,3,5,8,34], [1,2,3,5,13,21], ..., [3,5,8,13,21,34])   
> 집합 S와 k가 주어졌을 때, 수를 고르는 모든 방법을 구하는 프로그램을 작성하시오.   
> 입력은 여러 개의 테스트 케이스로 이루어져 있다. 각 테스트 케이스는 한 줄로 이루어져 있다.   
> 첫 번째 수는 k (6 < k < 13)이고, 다음 k개 수는 집합 S에 포함되는 수이다. S의 원소는 오름차순으로 주어진다.   
> 입력의 마지막 줄에는 0이 하나 주어진다.   

```javascript
var numbers = mutableListOf<Int>()
var checkPrint = Array<Boolean>(50){false}
fun main(){
    var lottoNums = mutableListOf<String>()
    while(true){
        var numbers = readLine()
        if(numbers=="0")
        {
            break
        }
        lottoNums.add(numbers.toString())
    }
    var count = lottoNums.size
    lottoNums.forEach {
        var lottoNum = it.split(" ").toList()
        // set을 list로 바꿔준다
        numbers = mutableListOf<Int>()
        checkPrint = Array<Boolean>(50){false}

        //첫번째 수를 제외하고 받는다.
        for(i in 1 until lottoNum.size)
        {
            numbers.add(lottoNum[i].toInt())
        }

        numbers.sort()                                       // 정렬을 한다.

        lotto(0, 0)                    //재귀 함수를 호출한다
        println()
    }
}

fun lotto(start : Int, checkNum : Int){
    //탈출조건
    if(checkNum==6){
        printLotto()
    }else {
        for(i in start until numbers.size)
        {
            checkPrint[numbers[i]] = true
            lotto(i+1, checkNum+1)
            checkPrint[numbers[i]] = false
        }
    }
}
fun printLotto(){
    for(i in 1 until checkPrint.size-1){
        if(checkPrint[i]){
            print("$i ")
        }
    }
    println()
}
```

## 예시2
> 안드로메다 유치원 익스프레스반에서는 다음 주에 율동공원으로 소풍을 갑니다.   
> 원석 선생님은 소풍 때 학생들을 두명씩 짝을 지어 행동하게 하려고 합니다.   
> 그런데 서로 친구가 아닌 학생들끼리 짝을 지어 주면 서로 싸우거나 같이 돌아다니지 않기 때문에,   
> 항상 서로 친구인 학생들끼리만 짝을 지어야 합니다.   
> 각 학생들의 쌍에 대해 이들이 서로 친구인지 여부가 주어질 때,   
> 학생들을 짝지을 수 있는 방법의 수를 계산하는 프로그래을 작성하세요.   
> 예를 들어   
> - (태연,제시카), (써니,티파니), (효연,유리)   
> - (태연,제시카), (써니,유리), (효연,티파니)   
> 프로그램은 1초 내에 실행되어야 하고,  64 MB이하의 메모리만을 사용해야 함.   
> 입력의 첫 줄에는 테스트 케이스의 수 C(C<=50)가 주어지고,각 테스트 케이스의 첫 줄에는 학생 수 n(2<=n<=10)과 친구 쌍의 수 m(0<=m<=n(n-1)/2)이 주어짐.   
> 그 다음 줄에 m개의 정수 쌍으로 서로 친구인 두 학생의 번호가 주어짐.번호는 모두 0부터 n-1사이의 정수이고,같은 쌍은 입력에 두번 주어지지 않음.학생들의 수는 짝수입니다.   
> 예제입력   
> 3   
> 2 1   
> 0 1   
> 4 6   
> 0 1 1 2 2 3 3 0 0 2 1 3   
> 6 10   
> 0 1 0 2 1 2 1 3 1 4 2 3 2 4 3 4 3 5 4 5   
> 예제출력   
> 1   
> 3   
> 4   
> 예제 입출력 설명   
> -첫 번째 입력(2 1)에는 두 학생밖에 없으며 이들은 서로 친구입니다.따라서 딱 한가지 경우가 있게 된다.   
> -두 번째 입력(4 6)에는 네 명의 학생이 모두 서로 친구입니다.이들을 보라둘이,뚜비,나나,뽀 라고 할 때 다음과 같은 세가지 방법이 있습니다.   
>      - (보라돌이,뚜비),(나나,뽀)   
>      - (뚜비,뽀) (보라돌이,나나)   
>      - (나나,뚜비) (뽀,보라돌이)   

- 해당 문제는 아직 짝을 찾지 못한 학생들의 명단이 주어질 때 친구끼리 둘씩 짝짓는 경우의 수를 계산하라 라는 문제임.
- 명단에서 서로 친구인 두 학생을 찾아 이들을 짝지어 주고 남은 학생들을 짝지어 주는 문제도 원래 문제와 같은 형태임.

```javascript
fun main(){
    var testCase = readLine()!!.toInt() // 테스트 케이스 숫자 받기

    repeat(testCase){
        var countPair = readLine()!!.split(" ").map{ it.toInt() }      // 학생 수와 친구의 수
        var freinds = Array(countPair.first()){ false }                         // 학생 수만큼 false 로 채워주기
        var areFreinds = Array(10) { Array(10) { false } }      // 짝을 확인
        var counts = countPair.first()
        var freind = readLine()!!.split(" ")                          // 12, 34, 56 두번씩 친구
        for (i in 0.. freind.size-1 step 2){
            areFreinds[freind[i].toInt()][freind[i+1].toInt()] = true                                           //짝이 있다면 true
            areFreinds[freind[1+i].toInt()][freind[i].toInt()] = true
        }
        var result = countPairings(freinds, counts, areFreinds)
        println(result)
    }
}

fun countPairings(taken : Array<Boolean>, numbers : Int, areFreinds : Array<Array<Boolean>> ):Int {
    //모든 taken(학생이 true이면 끝)
    var firstFree = -1
    for( i in 0 .. taken.size-1){
        if(!taken[i]){              // 학생이 false 이면
            firstFree = i           // 아직 짝을 찾지 못한 학생
            break
        }
    }
    // 기저사례
    if(firstFree == -1) return 1;
    var ret = 0
    for( i in firstFree+1 .. numbers-1){
        if(!taken[i] && areFreinds[firstFree][i]){
            taken[firstFree] = true
            taken[i] = true
            ret += countPairings(taken, numbers, areFreinds)                // 재귀 함수 호출
            taken[i] = false
            taken[firstFree] = false
        }
    }
    return ret
}
```
