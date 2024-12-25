# dynamic-and-static-password-system
A Python program that generates passwords based on dynamic date and time.
# Dynamic Password System

This project is a Python program that generates dynamic passwords based on user-selected date and time components. It provides a user interface using the **Tkinter** library.

## 📋 Features

- **Dynamic Password Generation:**  
  - Users can select the order of components such as day, month, year, hour, and minute.  
  - A password is generated based on the current date and time according to the selected order.

- **User Security:**  
  - The system blocks access after 3 incorrect password attempts.  
  - The blocking duration increases progressively (30 seconds, 1 minute, 1.5 minutes, etc.) with each additional 3 failed attempts.

- **Background Image:**  
  - A visually appealing background image is added to the application window.

## 🛠️ Technologies Used

- **Python 3.x**
- **Tkinter:** For the Graphical User Interface (GUI).
- **Time and Datetime Libraries:** For handling date and time operations.

'''
## 🚀 How to Use

1. When the program starts, select the order of date and time components.
2. Use the system-generated password to log in.
3. If the password is entered incorrectly 3 times consecutively, the system will block access, and the blocking duration will increase incrementally.

## 📂 File Structure
re
- **main.py** - The main program file.
- **assets/** - Contains the background image and other graphical elements.

## 💡 How It Works

1. The program generates a password based on the current time and the user-selected component order.  
2. Password input and validation are handled interactively.  
3. If the password is entered incorrectly, the blocking mechanism activates to enhance security.

## 📜 License

This project is licensed under the [MIT License](LICENSE). Refer to the LICENSE file for more details.

## 🌟 Contact

If you have any questions about the project, feel free to reach out:

- **Email:** elmincfrlu@gmail.com  
- **GitHub:** [MyGitHubProfile](https://github.com/eLmincfrLu))'''



------------------------------------------------------------------CODE------------------------------------------------------------------------------------------


  
import tkinter as tk
from tkinter import messagebox    # messagebox ve datetime in ozunu daxil etmisemki daha birde datetime ve tk yazmayim..
from datetime import datetime, timedelta
import os   #  faylarla islemek ucun daxil olunur
import json   #    Faylarin pytonda isliye bilmesi ucun   (yeni pyton ucun bir obyekte cevririr)..
import subprocess   #   bu moduldan notepad proqramini birbasa acmaqcun istifade edeceyik

import re    # emailde ve parolda mehdudiyyet qoymaq ucun istifade edecem

from PIL import Image, ImageTk  # Pillow kitabxanası


# umumi deyisenlerimiz
istifadeci_melumatlari = "users.json"           # İstifadəçi məlumatlarını saxlamaq üçün olan fayl
arxiv_fayli = "arxiv.txt"               # Ümumi arxiv məlumatları üçün fayl
tehlukesizlik_kodu = "Baku2024"

uğursuz_cəhdlər = dict()  #  hansi emaillerde ugursuz cehdler olubsa bura atib sayini tapacam
bloklanmış_istifadəçilər =dict()   # Email üzrə bloklama bitmə vaxtı

# İstifadəçi məlumatlarını oxumaqcun funksiya
def melumat_oxu(fayl_adi):
    if os.path.exists(fayl_adi):
        with open(fayl_adi, "r") as fayl:
            return json.load(fayl)
    return dict()

# İstifadəçi məlumatlarını yazmaqcun funksiya 
def melumat_yaz(fayl_adi, melumat):
    with open(fayl_adi, "w") as fayl:
        json.dump(melumat, fayl)

# Yeni istifadəçini həm arxivə, həm şəxsi fayla yazmaqcun funksiya
def arxiv_yaz(email, sıralama, sabit_parol=None):
    # Ümumi arxiv faylına yazırıq
    now = datetime.now()
    gun = str(now.day)
    ay = str(now.month)
    il = str(now.year)
    saat = str(now.hour)
    deqiqe = str(now.minute)

    # bir dict yaradiram ve bu dict den ist ederek dinamik parollari yaradiram
    components = { 1: ("gun", gun) , 2: ("ay", ay) , 3: ("il", il) , 4: ("saat", saat) , 5: ("deqiqe", deqiqe)} 
        
    
       

    # Seçilən sıralamaya uyğun parol yaradılır
    parol_str = ""
    dinamik_parol = ""
    for sira in sıralama:
        if sira != 0:
            parol_str += f"{components[sira][0]}"  # Parol sırasını arxivə yazırıq
            dinamik_parol += components[sira][1]  # Dinamik parolu şəxsi fayla yazırıq

    # Sabit şifrə varsa, onu əlavə edirik
    if sabit_parol:
        parol_str = sabit_parol  # Arxivdə sabit parolu qeyd edirik
        dinamik_parol = sabit_parol  # Dinamik parolu sabit parol ilə əvəz edirik

    # Arxiv faylına yazırıq
    with open(arxiv_fayli, "a") as fayl:
     fayl.write(f"Email: {email}, Parol: {parol_str}")  # Parolu burada sıralama ilə yazırıq
    print(f"Arxivə yazıldı: Email: {email}, Parol: {parol_str}")  # Debug mesajı


    # Şəxsi fayl yaradırıq
    user_fayli = f"{email.replace('@', '_').replace('.', '_')}.txt"
    try:
      with open(user_fayli, "w") as fayl:
        fayl.write(f"Email: {email},Parol: {sabit_parol if sabit_parol else dinamik_parol}\n")
    except Exception as problem:
       print(f"Xəta: Şəxsi fayl yaradılarkən problem yaranıb: {problem}")


# Qeydiyyata alinma funksiyasi
def qeydiyyat_ac():
    def qeydiyyat_saxla():
        email = email_giris.get()
        sira_1 = sira_1_var.get()
        sira_2 = sira_2_var.get()
        sira_3 = sira_3_var.get()
        sira_4 = sira_4_var.get()
        sira_5 = sira_5_var.get()
        sabit_parol = sabit_parol_giris.get()  # Sabit şifrə girişini alırıq


         #emaile qoyulan mehdudiyyetler
        if not re.search("[a-z]", email):
            return messagebox.showerror("Xəta", "Emailde kicik herflerde olmalidir!")
        elif not re.search("[$#@]", email):
            return messagebox.showerror("Xəta", "Emailde xusui simvollarda (@) olmalidir!")



        # sabit sifre xanasi secilendeki telebler-->
        if sabit_sifrə_var.get() == 1:
            if sabit_parol == "":
                return messagebox.showerror("Xəta", "Sabit şifrə daxil edilməlidir!")
            elif not re.search("[a-z]", sabit_parol):
                return messagebox.showerror("Xəta", "Parolda kicik herflerde olmalidir!")
            elif not re.search("[A-Z]", sabit_parol):
                return messagebox.showerror("Xəta", "Parolda boyuk herflerde olmalidir!")
            elif not re.search("[0-9]", sabit_parol):
                return messagebox.showerror("Xəta", "Parolda reqemlerde olmalidir!")

            

        if email:
            melumatlar = melumat_oxu(istifadeci_melumatlari)
            if email in melumatlar:
                messagebox.showerror("Xəta", "Bu email artıq qeydiyyatdan keçib!")
            else:
                sıralama = [sira_1, sira_2, sira_3, sira_4, sira_5]
                melumatlar[email] = {"sira": sıralama, "sabit_parol": sabit_parol if sabit_sifrə_var.get() == 1 else None}
                melumat_yaz(istifadeci_melumatlari, melumatlar)

                # Arxivə və şəxsi fayla yazırıq
                arxiv_yaz(email, sıralama, sabit_parol if sabit_sifrə_var.get() == 1 else None)

                messagebox.showinfo("Uğur", "Qeydiyyat tamamlandı!")
                qeydiyyat_pencere.destroy()
        else:
            messagebox.showerror("Xəta", "E-mail daxil edilməlidir!")

    qeydiyyat_pencere = tk.Toplevel()
    qeydiyyat_pencere.title("Qeydiyyat")
       #qeydiyyat_pencere.geometry("400x400")
    qeydiyyat_pencere.state('zoomed')
    tk.Label(qeydiyyat_pencere, text="E-mail:", font=("Arial", 12)).pack(pady=5)
    email_giris = tk.Entry(qeydiyyat_pencere, font=("Arial", 12))
    email_giris.pack(pady=5)

    tk.Label(qeydiyyat_pencere, text="Parol Sırasını Seçin:", font=("Arial", 12)).pack(pady=10)


    
    sira_1_var = tk.IntVar()
    sira_2_var = tk.IntVar()
    sira_3_var = tk.IntVar()    # intvar xananin secilib secilmediyini izleyir...
    sira_4_var = tk.IntVar()     # 0 deyer ve ya basqa reqem qaytarir 0 qaytarsa demeli secilmiyib..
    sira_5_var = tk.IntVar()
    tk.Checkbutton(qeydiyyat_pencere, text="Gün", variable=sira_1_var, onvalue=1, offvalue=0).pack()
    tk.Checkbutton(qeydiyyat_pencere, text="Ay", variable=sira_2_var, onvalue=2, offvalue=0).pack()
    tk.Checkbutton(qeydiyyat_pencere, text="İl", variable=sira_3_var, onvalue=3, offvalue=0).pack()
    tk.Checkbutton(qeydiyyat_pencere, text="Saat", variable=sira_4_var, onvalue=4, offvalue=0).pack()
    tk.Checkbutton(qeydiyyat_pencere, text="Dəqiqə", variable=sira_5_var, onvalue=5, offvalue=0).pack()




    # Sabit şifrə seçimi
    sabit_sifrə_var = tk.IntVar()
    tk.Checkbutton(qeydiyyat_pencere, text="Sabit Şifrə ilə qeydiyyatdan keç", variable=sabit_sifrə_var, onvalue=1, offvalue=0).pack(pady=10)

    sabit_parol_giris = tk.Entry(qeydiyyat_pencere, font=("Arial", 12))
    sabit_parol_giris.pack(pady=5)
    sabit_parol_giris.config(state=tk.DISABLED)  # İlk olaraq sabit şifrəni bağlayırıq

    def sabit_parol_seçimi():
        if sabit_sifrə_var.get() == 1:
            sabit_parol_giris.config(state=tk.NORMAL)  # Sabit şifrəni aktivləşdiririk
        else:
            sabit_parol_giris.config(state=tk.DISABLED)  # Sabit şifrəni deaktivləşdiririk

    sabit_sifrə_var.trace_add("write", lambda *args: sabit_parol_seçimi())

    tk.Button(qeydiyyat_pencere, text="Qeydiyyatdan Keç", command=qeydiyyat_saxla, font=("Arial", 12), bg="green", fg="white").pack(pady=20)



# Daxil olma funksiyasi
def daxil_ol():

    def parol_goster_gizle():
     # Parolun göstərilməsi/gizlədilməsi
     if parol_giris.cget('show') == '*':  # Əgər parol gizlidirsə
        parol_giris.config(show='')  # Parolu göstər
        parol_goster_düyməsi.config(text="Parolu Gizlət")  # Düymə mətni "Gizlət" et
     else:
        parol_giris.config(show='*')  # Parolu gizlət
        parol_goster_düyməsi.config(text="Parolu Göstər")  # Düymə mətni "Göstər" et

    def daxil_ol_yoxla():
        email = email_giris.get()
        parol = parol_giris.get()

        global uğursuz_cəhdlər, bloklanmış_istifadəçilər  # global ---- umumi deyisen oldugunu gosterir...

        # Cari zaman
        indiki_vaxt = datetime.now()

        # Bloklanmış istifadəçi yoxlaması (email)
        if email in bloklanmış_istifadəçilər:
            blok_vaxtı = bloklanmış_istifadəçilər[email]
            qalan_vaxt = (blok_vaxtı - indiki_vaxt).total_seconds()
            if qalan_vaxt > 0:
                # Bloklanmışdır, qalan vaxtı göstəririk
                return messagebox.showerror("Bloklanıb", f"Hazırda blokdasınız. Qalan vaxt: {int(qalan_vaxt)} saniyə")
            else:
                # Blok bitibse , silinir bloklanmislardan
                del bloklanmış_istifadəçilər[email]

        melumatlar = melumat_oxu(istifadeci_melumatlari)

        if email in melumatlar:
            now = datetime.now()
            gun = str(now.day)
            ay = str(now.month)
            il = str(now.year)
            saat = str(now.hour)
            deqiqe = str(now.minute)

            sıralama = melumatlar[email]["sira"]
            components = {1: gun, 2: ay, 3: il, 4: saat, 5: deqiqe}
            

            dinamik_parol = ""
            for sira in sıralama:
                # 0 olmayan sıralama dəyərləri ilə işləyirik
             if sira != 0:
              dinamik_parol = dinamik_parol + components[sira]


            # Sabit şifrə yoxlaması
            sabit_parol = melumatlar[email].get("sabit_parol")

            # Dinamik parol və sabit parol yoxlaması
            if parol == dinamik_parol or (sabit_parol and parol == sabit_parol):
                messagebox.showinfo("Uğur", "Daxil oldunuz!")
                uğursuz_cəhdlər[email] = 0  # Uğurlu girişdə  cehdlerin sayi sıfırlanır

                # Şəxsi notepad faylını açırıq
                user_fayli = f"{email.replace('@', '_').replace('.', '_')}.txt"  # fayl adinda bu simvollardan istifade etmek
                                                                                 #olmadigi ucun replace olunur....
                if os.path.exists(user_fayli):
                    subprocess.run(["notepad.exe", user_fayli])
                else:
                    messagebox.showerror("Xəta", "Şəxsi fayl tapılmadı!")
                
            else:
                # Parol səhvdir, qalan cəhdləri göstəririk
                uğursuz_cəhdlər[email] = uğursuz_cəhdlər.get(email, 0) + 1
                qalan_cehdler = 3 - uğursuz_cəhdlər[email]

                if qalan_cehdler > 0:
                    messagebox.showwarning("Xəta", f"Parol səhvdir! Qalan cəhdlər: {qalan_cehdler}")
                else:
                    # 3 cəhddən sonra bloklama tətbiq edilir
                    blok_vaxtı = indiki_vaxt + timedelta(seconds=30)
                    bloklanmış_istifadəçilər[email] = blok_vaxtı
                    qalan_vaxt = (blok_vaxtı - indiki_vaxt).total_seconds()
                    messagebox.showerror("Bloklanıb", f"30 saniyəlik bloklandınız. Qalan vaxt: {int(qalan_vaxt)} saniyə")

        else:
            messagebox.showerror("Xəta", "E-mail tapılmadı!")
          



  
        
    daxil_ol_pencere = tk.Toplevel()
    daxil_ol_pencere.title("Daxil Ol")
    daxil_ol_pencere.state('zoomed')
    



    # E-mail və parol giriş sahələrini göstərmək
    tk.Label(daxil_ol_pencere, text="E-mail:", font=("Arial", 12)).pack(pady=5)
    email_giris = tk.Entry(daxil_ol_pencere, font=("Arial", 12))
    email_giris.pack(pady=5)
    


    tk.Label(daxil_ol_pencere, text="Parol:", font=("Arial", 12)).pack(pady=5)
    parol_giris = tk.Entry(daxil_ol_pencere, font=("Arial", 12), show="*")
    parol_giris.pack(pady=5)

                 # Parolun göstərilməsini/gizlədilməsini tənzimləyən 
    parol_goster_düyməsi = tk.Button(daxil_ol_pencere, text="Parolu Göstər", command=parol_goster_gizle, font=("Arial", 12), bg="black", fg="white")
    parol_goster_düyməsi.pack(pady=5)



    tk.Button(daxil_ol_pencere, text="Daxil ol", command=daxil_ol_yoxla, font=("Arial", 12), bg="blue", fg="white").pack(pady=20)


# Arxiv düyməsi
def arxiv_goster():
    def tehlukesizlik_kodu_yoxla():
        kod = kod_giris.get()
        if kod == tehlukesizlik_kodu:
            if os.path.exists(arxiv_fayli):
                subprocess.run(["notepad.exe", arxiv_fayli])
            else:
                messagebox.showerror("Xəta", "Arxiv faylı mövcud deyil.")
            kod_pencere.destroy()
        else:
            messagebox.showerror("Xəta", "Yanlış təhlükəsizlik kodu.")

    kod_pencere = tk.Toplevel()
    kod_pencere.title("Təhlükəsizlik Kodu")
        #kod_pencere.geometry("300x150")
    kod_pencere.state('zoomed')
    tk.Label(kod_pencere, text="Təhlükəsizlik Kodunu Daxil Edin:", font=("Arial", 12)).pack(pady=10)
    kod_giris = tk.Entry(kod_pencere, font=("Arial", 12))
    kod_giris.pack(pady=10)

    tk.Button(kod_pencere, text="Göstər", command=tehlukesizlik_kodu_yoxla, font=("Arial", 12), bg="orange", fg="black").pack(pady=20)







# Əsas pəncərə  (((((--- yeni giris penceresi
pencere = tk.Tk()
pencere.title("Qeydiyyat və Daxil Olma Sistemi")
    # pencere.geometry("500x500+500+350")   +500+350 pencerenin merkezde acilmasi ucundur.
pencere.state('zoomed')



# sekil qoymaq
sekil=Image.open("C:\\Users\\User\\Desktop\\notepadlogo.jpg")
sekil2=ImageTk.PhotoImage(sekil.resize((2000,1000),Image.Resampling.LANCZOS))
sekillabel=tk.Label(pencere,image=sekil2)
sekillabel.place(x=0,y=0)




tk.Label(text="Qeydiyyat və Giriş sistemi", font=('Times', 25,'bold'),fg='black').pack()
tk.Button(pencere, text="Qeydiyyat", command=qeydiyyat_ac, font=("Times", 20), bg="green", fg="white").pack(pady=20)
tk.Button(pencere, text="Daxil Ol", command=daxil_ol, font=("Times", 20), bg="blue", fg="white").pack(pady=20)
tk.Button(pencere, text="Arxiv", command=arxiv_goster, font=("Times", 20), bg="red", fg="black").pack(pady=20)
 


pencere.mainloop()

