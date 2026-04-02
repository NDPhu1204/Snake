#include <iostream>
#include <windows.h>
#include <cstdlib>
#include <conio.h>
using namespace std;
void gotoxy( int column, int line );
// kích thước khung
const int WIDTH = 20;
const int HEIGHT = 20;
struct Point{
    int x,y;
};
class CONRAN{
public:
    struct Point A[100];
    int DoDai;
    CONRAN(){
        DoDai = 3;
        A[0].x = 10; A[0].y = 10;
        A[1].x = 11; A[1].y = 10;
        A[2].x = 12; A[2].y = 10;
    }
    void Ve(){
        for (int i = 0; i < DoDai; i++){
            gotoxy(A[i].x,A[i].y);
            cout<<"X";
        }
    }
    void DiChuyen(int Huong){
        for (int i = DoDai-1; i>0;i--)
            A[i] = A[i-1];
        if (Huong==0) A[0].x = A[0].x + 1;
        if (Huong==1) A[0].y = A[0].y + 1;
        if (Huong==2) A[0].x = A[0].x - 1;
        if (Huong==3) A[0].y = A[0].y - 1;
    }
    bool KiemTraChamTuong(){
        if (A[0].x <= 0 || A[0].x >= WIDTH ||
            A[0].y <= 0 || A[0].y >= HEIGHT)
            return true;
        return false;
    }
};
// vẽ khung
void VeKhung(){
    for(int x = 0; x <= WIDTH; x++){
        gotoxy(x, 0); cout << "#";
        gotoxy(x, HEIGHT); cout << "#";
    }

    for(int y = 0; y <= HEIGHT; y++){
        gotoxy(0, y); cout << "#";
        gotoxy(WIDTH, y); cout << "#";
    }
}

int main()
{
    CONRAN r;
    int Huong = 0;
    char t;

    while (1){
        if (kbhit()){
            t = getch();
            if (t=='a') Huong = 2;
            if (t=='w') Huong = 3;
            if (t=='d') Huong = 0;
            if (t=='s') Huong = 1;
        }
        system("cls");
        VeKhung();
        r.Ve();
        r.DiChuyen(Huong);
        
       if (r.KiemTraChamTuong()){
            gotoxy(5, HEIGHT/2);
            cout << "GAME OVER";
            break;
        }

        Sleep(150);
    }

    return 0;
}


void gotoxy( int column, int line )
  {
  COORD coord;
  coord.X = column;
  coord.Y = line;
  SetConsoleCursorPosition(
    GetStdHandle( STD_OUTPUT_HANDLE ),
    coord
    );
  }
