## ☕️ [C++] 배열 동적할당
문제를 풀다보면 변수를 설정하여 해당 변수의 입력을 통해 배열의 크기를 설정해야할 때가 있다. 이때, 문제에 대한 조건을 무시하고 크기를 설정하게 된다면 **런타임 에러 (OutOfBounds)** 가 발생될 수 있다. 그러므로 해당 배열을 동적할당 되도록 해보자.

아래 코드를 보자

```
#include <iostream>
using namespace std;

int main() {
    int T; // 복통 일수
    int M; // 스트레스 양 기준

    cin >> T >> M;

    int* arr = new int[T]; // 동적 배열 할당
    int result = 0;
    int cnt = 0;

    for (int i = 0; i < T; i++) {
        cin >> arr[i];

        result += arr[i];
        if (result < 0) {
            result = 0;
        }
        if (result >= M) {
            cnt++;
        }
    }

    cout << cnt << endl;

    delete[] arr; // 동적 배열 해제 (메모리 누수 방지)
    return 0;
}

```
다른건 재처두고 해당 복통일수 변수 T를 입력받고 동적 배열을 할당된 것을 볼 수 있다.

```
int* arr = new int[T];

delete[] arr; // 동적 배열 해제 (메모리 누수 방지)
```

new 키워드를 사용해 int형 배열을 동적으로 생성한다.
T의 값이 실행 중 결정되므로 가변적인 크기의 배열을 생성할 수 있다.
배열이 할당되면 arr은 해당 배열의 첫 번째 요소를 가리키는 포인터가 된다.
