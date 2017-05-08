# Anket_deneme
import sqlite3
import time
import datetime
import MySQLdb
from tkinter import *

pencere=Tk()
pencere.geometry("270x270+800+300")
pencere.title("ANKET DENEME")

con = sqlite3.connect("Anket.db")
cursor = con.cursor()

def tabloolustur1():
    cursor.execute("Create table if not exists AnketSoru (aid INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT, soru TEXT,sec1 TEXT,sec2 TEXT,sec3 TEXT,sec4 TEXT)")
    con.commit()
tabloolustur1()
# Cevapları metin halinde görmek için kullanılır
def tabloolustur2():
    cursor.execute("Create table if not exists AnketCvp (sid TEXT,aid INT,cvp TEXT)")
    con.commit()
# Hangi şıkkın en çok seçildiğini bulabiliriz
def tabloolustur3():
    cursor.execute("Create table if not exists AnketSec (sid TEXT, aid INT,secimadet INT)")
    con.commit()
#tid, sadece saat bilgisini içerecektir. İstatistik için kullanılabilir
def tabloolustur4():
    cursor.execute("Create table if not exists AnketTime (tid TEXT,aid INT, sid INT, adet INT)")
    con.commit()



def yeniSoru():
    global giris0, giris1, giris2, giris3, giris4
    pencere2 = Toplevel()
    pencere2.geometry("270x270+800+300")
    pencere2.title("ANKET YENİ SORU")

    etiket0 = Label(pencere2,text="Yeni Soru", cursor="bottom_side", fg="black", bg="orange")
    etiket0.pack()
    giris0 = Entry(pencere2)
    giris0.pack()
    etiket1 = Label(pencere2,text="Şık 1", cursor="bottom_side", fg="black", bg="orange")
    etiket1.pack()
    giris1 = Entry(pencere2)
    giris1.pack()
    etiket2 = Label(pencere2,text="Şık 2", cursor="bottom_side", fg="black", bg="orange")
    etiket2.pack()
    giris2 = Entry(pencere2)
    giris2.pack()
    etiket3 = Label(pencere2,text="Şık 3", cursor="bottom_side", fg="black", bg="orange")
    etiket3.pack()
    giris3 = Entry(pencere2)
    giris3.pack()
    etiket4 = Label(pencere2,text="Şık 4", cursor="bottom_side", fg="black", bg="orange")
    etiket4.pack()
    giris4 = Entry(pencere2)
    giris4.pack()

    def Kaydet():
        metin0 = giris0.get()
        metin1 = giris1.get()
        metin2 = giris2.get()
        metin3 = giris3.get()
        metin4 = giris4.get()

        cursor.execute("INSERT INTO AnketSoru (soru,sec1,sec2,sec3,sec4) VALUES(?,?,?,?,?)",(metin0, metin1, metin2, metin3, metin4))
        con.commit()
        # AnketCevap tablosunu doldurrr
        cursor.execute("Select * from AnketSoru")

        maksdata = cursor.fetchall()
        maksno=(len(maksdata))
        cursor.execute("INSERT INTO AnketCvp (sid,aid,cvp) VALUES(?,?,?)", ("a", maksno, metin1))
        cursor.execute("INSERT INTO AnketCvp (sid,aid,cvp) VALUES(?,?,?)", ("b", maksno, metin2))
        cursor.execute("INSERT INTO AnketCvp (sid,aid,cvp) VALUES(?,?,?)", ("c", maksno, metin3))
        cursor.execute("INSERT INTO AnketCvp (sid,aid,cvp) VALUES(?,?,?)", ("d", maksno, metin4))
        con.commit()
        giris0.delete(0, END)
        giris1.delete(0, END)
        giris2.delete(0, END)
        giris3.delete(0, END)
        giris4.delete(0, END)

    btn1 = Button(pencere2, text="Kaydet", command=Kaydet)
    btn1.pack()
    btn2 = Button(pencere2, text="çıkış", command=pencere2.destroy)
    btn2.pack()



btn1 = Button(text="Anket Soru Gir",bg="orange",fg="navy", command=yeniSoru)
btn1.pack(side=LEFT)

# -----------------------------------------------------------------------------------------------------------

def yeniCevap():
    global giris0, giris1, giris2, giris3, giris4
    pencere3 = Toplevel()
    pencere3.geometry("500x500+800+300")
    pencere3.title("ANKET CEVAP GİR")


    def cvpKayıt():
            cursor.execute("Select * from AnketSoru")
            data = cursor.fetchall()

            for i in range(0,len(data)):
                text =str(data[i][0])+"-"+str(data[i][1])+"\n a-"+str(data[i][2])+" b-"+str(data[i][3])+" c-"+str(data[i][4])+" d-"+str(data[i][5])+"\n"
                #print(text)
                metin.insert(END, text)

    def bilgiAktar():

        cursor.execute("INSERT INTO AnketSec (sid,aid,secimadet) VALUES(?,?,?)", (giris6.get(), giris5.get(), 1))


        # Şu anki zamanı verir zaman=time.time()
        zaman = time.time()
        tid = str(datetime.datetime.fromtimestamp(zaman).strftime('%H'))

        cursor.execute("INSERT INTO AnketTime (tid,aid,sid,adet) VALUES(?,?,?,?)", (tid,giris5.get(), giris6.get(),1))
        con.commit()

        giris5.delete(0, END)
        giris6.delete(0, END)

    etiket8 = Label(pencere3,text="SORULAR", cursor="bottom_side", fg="black", bg="orange")
    etiket8.pack()
    metin = Text(pencere3)
    metin.pack()
    etiket5 = Label(pencere3,text="Sırasıyla Soru Sayısını ve Cevap Şıkkını Giriniz", cursor="bottom_side", fg="black", bg="yellow")
    etiket5.pack()
    giris5 = Entry(pencere3)
    giris5.pack()
    giris6 = Entry(pencere3)
    giris6.pack()
    btn8 = Button(pencere3, text="Kaydet", command=bilgiAktar)
    btn8.pack(side=LEFT)
    btn9 = Button(pencere3, text="Çıkış", command=pencere3.destroy)
    btn9.pack(side=RIGHT)

    cvpKayıt()

    # print(len(data)): Kaç tane kayıt olduğunu görebilirim

btn2 = Button(text="Anket Cevap Gir",bg="green",fg="navy", command=yeniCevap)
btn2.pack(side=LEFT)

# --------------------------------------------------------------------------------------------------------------------


def istatistik():
    global giris0, giris1, giris2, giris3, giris4
    pencere4 = Toplevel()
    pencere4.geometry("500x500+800+300")
    pencere4.title("Seçenekler")


    def soruBazlı():
        cursor.execute("Select a.aid,b.soru,a.sid,c.cvp,sum(a.secimadet) from AnketSec a, AnketSoru b, AnketCvp c where a.aid=b.aid and a.aid=c.aid and a.sid=c.sid group by a.aid,a.sid order by a.aid,sum(a.secimadet) DESC")
        ist1 = cursor.fetchall()

        for i in range(0, len(ist1)):
            text = str(ist1[i][0]) + ". Soru: " + str(ist1[i][1]) + " için " + str(ist1[i][2]) + " Şıkkı olan " + str(ist1[i][3])+" "+str(ist1[i][4])+" kez seçildi.\n--------------------------\n"
            # print(text)
            metin.insert(END, text)

    def saatBazlı():
        cursor.execute("Select a.tid,a.tid+1,a.aid,b.soru,a.sid,c.cvp,sum(a.adet) from AnketTime a,AnketSoru b, AnketCvp c where a.aid=b.aid and a.aid=c.aid and a.sid=c.sid group by a.tid,a.aid,a.sid order by a.tid,a.aid,sum(a.adet) DESC ")
        ist2 = cursor.fetchall()

        for i in range(0, len(ist2)):
            text ="Saat "+str(ist2[i][0]) + "-" + str(ist2[i][1]) + " arası Soru " + str(ist2[i][2]) + ": " + str(ist2[i][3]) +" için "+str(ist2[i][4])+" şıkkı olan "+str(ist2[i][5])+" "+str(ist2[i][6])+" kez şeçildi"+"\n-----------------------------------------------------------\n"
            # print(text)
            metin.insert(END, text)


    metin = Text(pencere4)
    metin.pack()
    btn6 = Button(pencere4, text="Soru Bazlı İstatistik", command=soruBazlı)
    btn6.pack(side=LEFT)
    btn7 = Button(pencere4, text="Saat Bazlı İstatistik", command=saatBazlı)
    btn7.pack(side=LEFT)
    btn8 = Button(pencere4, text="Çıkış", command=pencere4.destroy)
    btn8.pack(side=LEFT)

    #maksno = (len(maksdata))

btn3 = Button(text="İstatistik",bg="yellow",fg="navy", command=istatistik)
btn3.pack(side=LEFT)

# -------------------------------------------------------------------------------------------------------------------

btn4 = Button(text = "Çık",bg="red",fg="navy", command=pencere.quit)
btn4.pack(side=LEFT)

mainloop()
