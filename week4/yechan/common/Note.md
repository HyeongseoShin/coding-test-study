## ✨2048(Easy)

### 🧩 1️⃣ 얕은 복사(map.clone())로 인한 상태 공유 문제

❌ 잘못된 코드  
int[][] copy_map = map.clone();  
→ 얕은 복사로 인해 내부 2차원 배열이 같은 참조를 가리킴  
→ copy_map[i][j]를 수정하면 원본 map[i][j]도 함께 변경됨

✅ 개선 코드  
private static int[][] deepCopy(int[][] board) {  
 int[][] copy = new int[N][N];  
 for (int i = 0; i < N; i++) {  
  copy[i] = board[i].clone(); // 각 행을 개별 복사 (깊은 복사)  
 }  
 return copy;  
}

💡 이렇게 하면 DFS에서 각 단계별 보드 상태가 완전히 독립적으로 유지된다.  
이는 **백트래킹 시뮬레이션의 기본기**로,  
“이전 상태로 복귀하는 구조”에서 필수적인 개념이다.

---

### 🧩 2️⃣ move_left, move_right 등의 이동 로직 설계 방향

❌ 잘못된 접근 (무한 루프 + 병합 규칙 위반 가능성)  
while (true) {  
 if(copy_map[i][j] == copy_map[i][j+1]) ...  
 if(copy_map[i][j] == 0) ...  
 if(copy_map[i][0] != 0) break; // 부분 값만 검사 → 루프 종료 불가  
}

### ❌ 문제점

1. **무한 루프 위험** → break 조건이 행 전체가 아니라 부분 값만 검사함
2. **2048 병합 규칙 위반** → 한 번 병합된 블록이 다시 병합될 수 있음

---

✅ 개선: 리스트 압축 방식 (newRow, prev, idx 활용)

private static void move_left(int[][] board) {  
 for (int i = 0; i < N; i++) {  
  int[] newRow = new int[N];  
  int idx = 0;  
  int prev = 0;

for (int j = 0; j < N; j++) {  
   if (board[i][j] == 0) continue; // 0은 건너뛰기

if (prev == 0) { // 첫 숫자 저장  
    prev = board[i][j];  
   } else if (prev == board[i][j]) { // 같은 수 → 병합  
    newRow[idx++] = prev \* 2;  
    prev = 0; // 병합 후 prev 리셋  
   } else { // 다른 수 → prev를 newRow에 넣고 현재값 저장  
    newRow[idx++] = prev;  
    prev = board[i][j];  
   }  
  }

if (prev != 0) newRow[idx] = prev; // 마지막 남은 수 처리  
  board[i] = newRow; // 결과 행 교체  
 }  
}

💡 “이동 + 병합”을 한 흐름으로 처리할 수 있어  
불필요한 반복문이나 복잡한 조건문이 필요 없다.  
이 방식은 **모든 이동 시뮬레이션류 문제의 정석 패턴**이다.
