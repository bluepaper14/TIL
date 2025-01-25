## [Boj 5341] Pyramids

# 문제

The input will be a sequence of integers, one per line. The end of input will be signaled by the integer 0, and does not represent the base of a pyramid. All integers, other than the last (zero), are positive.

# 접근방법

일단 1층에 벽돌을 쌓을때 1개가 필요하고  2층은 2개가 필요하다. 이렇게 반복되어 더해지게 for문을 사용해서 만들면 될것 같다.(n+1만큼)


# 풀이

```
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while(true) {
            int n = sc.nextInt();
            if (n == 0) break;

            int sum = 0;
            for (int i = 0; i < n; i++) {
                sum = sum + (i + 1);
            }
            System.out.println(sum);
        }
    }
}


// 1층 -> 1개
// 2층 -> 1 + 2개
// 3층 -> 1 + 2 + 3 = 6개
// 4층 -> 1 + 2 + 3 + 4 = 10개
// 반복되는 것에 현재 기존 1에 n + 1 만큼 더해주면 될것 같다.
// for문을 사용해서 층 기준으로 반복문을 사용해야 겠다.

```

# 정리

기본적으로 등차수열을 이용하여 총 블록의 개수 n * (n + 1) / 2 를 이용해서도 풀이가 가능하다. 하지만 for문에서 n에 +1에 더해주는 것이 이해하기 쉬웠다. 그럼 2개의 코드를 비교해보자.

```
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while(true) {
            int n = sc.nextInt();
            if (n == 0) break;

            int sum = n * (n + 1) / 2;
            System.out.println(sum);
        }
    }
}

```
메모리 자체는 크게 차이가 나지는 않지만 위 코드가 코드 길이와 메모리가 더 작게 출력된다. 이렇게 공식으로 줄이는 것도 코드를 더 효율적으로 관리할 수 있다.
