## ☕️ [C++] 배열의 크기제한 없이 입력

### 📌 `fixed`와 `setprecision` 사용 (`<iostream>`, `<iomanip>` 라이브러리)
C++에서는 `std::fixed`와 `std::setprecision(n)`을 사용하여 원하는 소수점 자리수까지 출력할 수 있습니다.

### ✅ 코드 예제
```cpp
#include <iostream>
#include <iomanip>

int main() {
    double num = 3.14159;
    std::cout << std::fixed << std::setprecision(1) << num << std::endl;
    return 0;
}
```
