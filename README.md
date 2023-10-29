#pgzero
import random


WIDTH = 600
HEIGHT = 450

TITLE = "Uzay Yolculuğu"
FPS = 30

# Değişkanler ve Nesneler
gemi = Actor("gemi", (300, 400))
uzay = Actor("uzay")
dusmanlar = []
gezegenler = [Actor("gezegen1", (random.randint(0, 600), -100)), Actor("gezegen2", (random.randint(0, 600), -100)), Actor("gezegen3", (random.randint(0, 600), -100))]
meteorlar = []
mod = 'menü'
gemi1 = Actor("gemi1",(100,300))
gemi2 = Actor("gemi2",(300,300))
gemi3 = Actor("gemi3",(500,300))
fuzeler = []
puan = 0


# Düşmanlar listesini oluşturmak
for i in range(10):
    x = random.randint(0, 600)
    y = random.randint(-450, -50)
    dusman = Actor("düşman", (x, y))
    dusman.speed = random.randint(2, 10)
    dusmanlar.append(dusman)
    
# Meteorlar listesini oluşturmak
for i in range(10):
    x = random.randint(0, 600)
    y = random.randint(-450, -50)
    meteor = Actor("meteor", (x, y))
    meteor.speed = random.randint(2, 10)
    meteorlar.append(meteor)

# Çizimler
def draw():
    # Menü modu
    if mod == "menü":
        uzay.draw()
        gemi1.draw()
        gemi2.draw()
        gemi3.draw()
        screen.draw.text("GEMİNİZİ SEÇİNİZ",center=(300,100),color="white",fontsize=36)
       
    
    # Oyun Modu
    if mod == 'oyun':
        uzay.draw()
        gezegenler[0].draw()
        
        # Meteorların Çizilmesi
        for i in range(len(meteorlar)):
            meteorlar[i].draw()
        gemi.draw()
        
        # Düşmanların Çizilmesi
        for i in range(len(dusmanlar)):
            dusmanlar[i].draw()
            
        # Füzelerin çizilmesi
        for i in range(len(fuzeler)):
            fuzeler[i].draw()
        
        #Puanı sol üst köşede göstermek    
        screen.draw.text("Puan: " + str(puan), topleft=(10, 10), color="white", fontsize=24)    
            
    # Oyun Bitti ekranı
    elif mod == 'son':
        uzay.draw()
        screen.draw.text("OYUN BİTTİ!", center = (300, 200), color = "white", fontsize = 36)
        
    
# Kontroller
def on_mouse_move(pos):
    gemi.pos = pos

# Yeni Düşmanların Eklenmesi
def yeni_dusman():
    x = random.randint(0, 400)
    y = -50
    dusman = Actor("düşman", (x, y))
    dusman.speed = random.randint(2, 8)
    dusmanlar.append(dusman)

# Düşmanların Hareketleri
def dusman_gemisi():
    for i in range(len(dusmanlar)):
        if dusmanlar[i].y < 650:
            dusmanlar[i].y = dusmanlar[i].y + dusmanlar[i].speed
        else:
            dusmanlar.pop(i)
            yeni_dusman()

# Gezegenlerin Hareketleri
def gezegen():
    if gezegenler[0].y < 550:
            gezegenler[0].y = gezegenler[0].y + 1
    else:
        gezegenler[0].y = -100
        gezegenler[0].x = random.randint(0, 600)
        birinci = gezegenler.pop(0)
        gezegenler.append(birinci)

# Meteorların Hareketleri
def meteorlar_hareket():
    for i in range(len(meteorlar)):
        if meteorlar[i].y < 450:
            meteorlar[i].y = meteorlar[i].y + meteorlar[i].speed
        else:
            meteorlar[i].x = random.randint(0, 600)
            meteorlar[i].y = -20
            meteorlar[i].speed = random.randint(2, 10)

# Çarpışmalar
def carpismalar():
    global mod, puan
    for i in range(len(dusmanlar)):
        if gemi.colliderect(dusmanlar[i]):
            mod = 'son'
        for j in range(len(fuzeler)):
            if fuzeler[j].colliderect(dusmanlar[i]):
                dusmanlar.pop(i)
                fuzeler.pop(j)
                yeni_dusman()
                puan += 1                                          
                break
            

def update(dt):
    if mod == 'oyun':
        dusman_gemisi()
        carpismalar()
        gezegen()
        meteorlar_hareket()
        #Füzelerin hareketi
        for i in range(len(fuzeler)):
            if fuzeler[i].y < 0:
                fuzeler.pop(i)
                break
            else:
                fuzeler[i].y = fuzeler[i].y - 10
#
#Oyunu yeniden başlatmak için
def yeniden_baslat():
    global mod, puan, fuzeler, dusmanlar, meteorlar, gezegenler

    # Oyun modunu "oyun" olarak ayarla
    mod = "oyun"

    # Puanı sıfırla
    puan = 0

    # Füzeleri temizle
    fuzeler = []

    # Düşmanları temizle ve başlangıç düşmanlarını ekle
    dusmanlar = []
    for i in range(5):
        x = random.randint(0, 600)
        y = random.randint(-450, -50)
        dusman = Actor("düşman", (x, y))
        dusman.speed = random.randint(2, 8)
        dusmanlar.append(dusman)

    # Meteorları temizle
    meteorlar = []
    for i in range(5):
        x = random.randint(0, 600)
        y = random.randint(-450, -50)
        meteor = Actor("meteor", (x, y))
        meteor.speed = random.randint(2, 10)
        meteorlar.append(meteor)

# Boşluk tuşuna basıldığında oyunu yeniden başlat
def on_key_down(key):
    if key == keys.SPACE and mod == "son":
        yeniden_baslat()                
        
def on_mouse_down(button,pos):
    global mod, gemi
    if mod == "menü" and gemi1.collidepoint(pos):
        gemi.image = "gemi1"
        mod = "oyun"
        
    elif mod == "menü" and gemi2.collidepoint(pos):
        gemi.image = "gemi2"
        mod = "oyun"
    
    elif mod == "menü" and gemi3.collidepoint(pos):
        gemi.image = "gemi3"
        mod = "oyun"
        
    # Ateş etmek
    elif mod == "oyun" and button == mouse.LEFT:
        fuze1 = Actor("füzeler")
        fuze2 = Actor("füzeler")
        
        
        fuze1.pos = (gemi.x -20, gemi.y)
        fuze2.pos = (gemi.x +20, gemi.y)
        
        fuzeler.append(fuze1)
        fuzeler.append(fuze2)
        
        
