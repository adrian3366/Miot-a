package miotla;
import java.awt.Color;
import java.awt.Dimension;
import java.awt.EventQueue;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.geom.Ellipse2D;
import java.awt.geom.Line2D;
import java.io.*;
import javax.swing.JFrame;
import javax.swing.JPanel;
import static miotla.Miotla.Q;
import static miotla.Miotla.S;
import static miotla.Miotla.wymiar;

class okno extends JFrame {
	public okno() {
		super("Geometria obliczeniowa");
                        EventQueue.invokeLater(new Runnable() {
                        @Override
                        public void run() {
                            setLocation(400, 20);
                            JPanel panel = new rys();
                            add(panel);
                            pack();
                            setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
                            setVisible(true);
} }); } }

class rys extends JPanel {
    public rys() {
            setPreferredSize(new Dimension(wymiar+10, wymiar+10));
    }
    
    public void rys1(Graphics2D g2d, int S[][]) {
        for(int i=0; i<S.length; i++){
                    int xx =S[i][0],    yy = wymiar-S[i][1];
                    Ellipse2D circle = new Ellipse2D.Double(xx-5,yy-5,10,10);
                    g2d.draw(circle);
                    g2d.drawString("P"+i, xx + 10, yy);
                }
    }
    @Override
    protected void paintComponent(Graphics g) {
	Graphics2D g2d = (Graphics2D) g;
                    g2d.setColor(Color.gray);      //osie x y
                    for(int i=0; i<wymiar; i=i+wymiar/8){       // wymiar/8 = 400/8 = 50
                    Line2D os_x = new Line2D.Double(i,0,i,wymiar);
                    g2d.draw(os_x);
                    Line2D os_y = new Line2D.Double(0,i,wymiar,i);
                    g2d.draw(os_y);
                    if(true){
                        g2d.drawString(" "+i, i , wymiar);
                        g2d.drawString(""+i, 10,wymiar-(i+5));
                    }
                    }
                    g2d.setColor(Color.BLUE);        //punkty 
                    rys1(g2d, S) ;
                    g2d.setColor(Color.BLACK);      // linie
                    for(int i = 0; i<S.length-1;i=i+2){
                        int x1 = S[i][0],          x2=S[i+1][0];
                        int y1 = wymiar-S[i][1],   y2=wymiar-S[i+1][1];
                        Line2D linia2 = new Line2D.Double(x1,y1,x2,y2);
                        g2d.draw(linia2);
                    }
                    g2d.setColor(Color.RED);        //punkty 
                    rys1(g2d, Q) ;
                    g2d.setColor(Color.lightGray);        //punkty 
                    Ellipse2D circle = new Ellipse2D.Double(-5,wymiar-5,10,10);
                    g2d.draw(circle);
    }
}

public class Miotla {
    public static int S[][];    //tablica odcinkow
    public static int Q[][];    //wspolrzedne przeciec
    public static int wymiar = 650; //wielkosc okna na proste
    public static int ilosc_wymiarow=2;
    public static int licznik=0;    // liczba przecięć
    public static void tworzenie_punktow(){  
        S= new int[][]{ {50, 20},         {600, 600},     //0 - 1
                                    {534,260},      {205,355},      //2 - 3
                                    {100,300},      {500,50},      //4 - 5
                                    {200,250},       {530,100},      //6 - 7
                                    {40,40},            {500,300}         //8 - 9 
                                   ,{60,60},            {580,200},  
                                    };
    }
    
    public static void zamiana_punktów(int t[], int tt[]){
        int zmienna_x=t[0],     zmienna_y=t[1];
        t[0]=tt[0];                     t[1]=tt[1];
        tt[0]=zmienna_x;        tt[1]=zmienna_y;
    }
    
    public static void sortowanie_punktow(int t[][]){
    //Proste ze zbioru S są sortowane i wypisane kolejno. Parzysty numer to pierwszy punkt lini, a kolejny nieparzysty to drugi.
        int y=1;
        for (int i = 0; i < t.length; i=i+2) {
            //zakladam ze nie ma poziomych punktow
            if(t[i][y]<t[i+1][y])
                zamiana_punktów(t[i],t[i+1]);
        }
        for (int j = 0; j < t.length; j=j+2)
            for (int i = 0; i < t.length-2; i=i+2) {
                if(t[i][y]<t[i+2][y])
                {
                    zamiana_punktów(t[i],t[i+2]);
                    zamiana_punktów(t[i+1],t[i+3]);
    }       }   }
    
    public static double tabela(int p[],int q[], int a[]){  //wyznacznik
        return p[0]*q[1]+q[0]*a[1]+a[0]*p[1] - a[0]*q[1]-q[0]*p[1]-p[0]*a[1];
    }
    
    public static boolean spr(double det, int p[], int a[], int q[]){   //wyznacznik=0 i p<a<q
        return det==0 && p[0]<=a[0] && a[0]<=q[0] && p[1]<=a[1] && a[1]<=q[1];
    }
    
    public static void liczenie_przeciec(int b[],int a[],int q[], int p[], int przecina){
        double pqa  = tabela(p,q,a);         double pqb  = tabela(p,q,b);   //wyliczone wyznaczniki
        double abp  = tabela(a,b,p);         double abq  = tabela(a,b,q);
        boolean wyz = pqb*pqa<0     &&      abp*abq<0;
        if( wyz  || spr(pqa,p,a,q) ||spr(pqb,p,b,q) ||spr(abp,a,p,b) ||spr(abq,a,q,b))  //wzor na przeciecia
                System.out.println("przecina sie miedzy " + przecina + " a " + (przecina + 1) +
                                                  ", liczba przeciec: " + (++licznik)+ punkt_przeciecia(a,b,p,q));
        else
                System.out.println("nie przecinaja sie");
    }
    
    public static String punkt_przeciecia(int p1[], int p2[], int q1[], int q2[]){
    //nie mogą byc pionowe i poziome odcinki, dzielenie przez 0
        int x=0, y=1;
        double a1 = (double)(p1[y]-p2[y])/(p1[x]-p2[x]);
        double b1 = (double)p1[y] - a1 * p1[x];
        double a2 = (double)(q1[y]-q2[y])/(q1[x]-q2[x]);
        double b2 = (double)q1[y] - a2 * q1[x];
        double y1 = (b1*a2-b2*a1)/(a2-a1);
        double x1 = (y1-b1)/a1;
        Q[licznik-1][x]=(int)x1;
        Q[licznik-1][y]=(int)y1;
        return "\n   x= " +(int)x1+ ", y= " +(int)y1;
    }
    
    public static void Wyznaczanie_przeciec(){
        for (int j = 0; j < S.length-2; j=j+2){
            System.out.println("\n\tp = (" + j +"," + (j+1)+")");
            for (int i = j+2; i < S.length; i=i+2) {
                System.out.println("  punkt" + i);  //if(S[j][1]!=S[i][1] && S[j][0]!=S[i][0])
                liczenie_przeciec(S[j],S[j+1],S[i],S[i+1],i);
                if(S[j+1][1]>S[i][1])
                    i=S.length;
    }   }   }
    
    public static void main(String[] args) throws IOException  {
        tworzenie_punktow();
        sortowanie_punktow(S);
        Q = new int[S.length][ilosc_wymiarow];  //tworzenie tablicy przeciec
        Wyznaczanie_przeciec();
        java.awt.EventQueue.invokeLater(new Runnable() {//rysowanie
            public void run() {     new okno().repaint();    }
        });}
}
