#include <iostream>
#include <windows.h>
#include <cstdlib>
#include <conio.h>
#include <ctime> // 1. Thư viện để dùng hàm random theo thời gian

using namespace std;

void gotoxy(int column, int line);

// Kích thước khung
const int WIDTH = 30;
const int HEIGHT = 20;

struct Point {
    int x, y;
};

Point moi; // 2. Biến mồi toàn cục

class CONRAN {
public:
    struct Point A[100];
    int DoDai;
    CONRAN() {
        DoDai = 3;
        A[0].x = 10; A[0].y = 10;
        A[1].x = 9;  A[1].y = 10; // Chỉnh lại tọa độ ban đầu cho hợp lý
        A[2].x = 8;  A[2].y = 10;
    }
    void Ve() {
        for (int i = 0; i < DoDai; i++) {
            gotoxy(A[i].x, A[i].y);
            if (i == 0) cout << "0"; // Đầu rắn hình số 0
            else cout << "X";        // Thân rắn hình chữ X
        }
    }
    void DiChuyen(int Huong) {
        for (int i = DoDai - 1; i > 0; i--)
            A[i] = A[i - 1];
        if (Huong == 0) A[0].x++; // Phải
        if (Huong == 1) A[0].y++; // Xuống
        if (Huong == 2) A[0].x--; // Trái
        if (Huong == 3) A[0].y--; // Lên
    }
    bool KiemTraChamTuong() {
        return (A[0].x <= 0 || A[0].x >= WIDTH || A[0].y <= 0 || A[0].y >= HEIGHT);
    }

    // 3. THÊM: Kiểm tra tự cắn vào thân
    bool KiemTraTuCanMinh() {
        for (int i = 1; i < DoDai; i++) {
            if (A[0].x == A[i].x && A[0].y == A[i].y) return true;
        }
        return false;
    }

    // 4. THÊM: Kiểm tra ăn mồi
    bool AnMoi(Point pMoi) {
        if (A[0].x == pMoi.x && A[0].y == pMoi.y) {
            DoDai++;
            return true;
        }
        return false;
    }
};

void VeKhung() {
    for (int x = 0; x <= WIDTH; x++) {
        gotoxy(x, 0); cout << "#";
        gotoxy(x, HEIGHT); cout << "#";
    }
    for (int y = 0; y <= HEIGHT; y++) {
        gotoxy(0, y); cout << "#";
        gotoxy(WIDTH, y); cout << "#";
    }
}

// 5. Hàm tạo mồi ngẫu nhiên
void TaoMoi(CONRAN r) {
    moi.x = rand() % (WIDTH - 1) + 1;
    moi.y = rand() % (HEIGHT - 1) + 1;
    for (int i = 0; i < r.DoDai; i++) {
        if (moi.x == r.A[i].x && moi.y == r.A[i].y) {
            TaoMoi(r);
            break;
        }
    }
}

int main() {
    srand(time(NULL));
    CONRAN r;
    int Huong = 0;
    int tocDo = 150;
    char t;

    TaoMoi(r);

    while (1) {
        if (kbhit()) {
            t = getch();
            // Chặn quay đầu 180 độ đột ngột để tránh tự sát nhầm
            if (t == 'a' && Huong != 0) Huong = 2;
            if (t == 'w' && Huong != 1) Huong = 3;
            if (t == 'd' && Huong != 2) Huong = 0;
            if (t == 's' && Huong != 3) Huong = 1;
        }

        system("cls");
        VeKhung();

        // Vẽ mồi hình chữ O
        gotoxy(moi.x, moi.y); cout << "O";

        r.Ve();
        r.DiChuyen(Huong);

        // Xử lý khi ăn mồi
        if (r.AnMoi(moi)) {
            TaoMoi(r);
            tocDo += 10; // Tăng thời gian chờ -> Rắn đi chậm lại
        }

        // Kiểm tra thua cuộc
        if (r.KiemTraChamTuong() || r.KiemTraTuCanMinh()) {
            gotoxy(WIDTH / 2 - 5, HEIGHT / 2);
            cout << "GAME OVER!";
            gotoxy(WIDTH / 2 - 5, HEIGHT / 2 + 1);
            cout << "DIEM: " << r.DoDai - 3;
            break;
        }

        Sleep(tocDo); // Sử dụng biến tocDo thay vì con số cố định 150
    }

    getch(); // Dừng màn hình để xem điểm
    return 0;
}

void gotoxy(int column, int line) {
    COORD coord;
    coord.X = column;
    coord.Y = line;
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), coord);
}
