# personelkayitsistemi

#include <stdio.h>

#include <string.h>

#define MAX_PERSONEL 100

#define DOSYA_ADI "personel.txt"

struct Personel
{
    char tcKimlikNo[12];
    char isim[16];
    char soyisim[16];
    char telefon[12];
    char calistigiBirim[31];
};

 // Yardımcı fonksiyonlar
int tcKimlikNoKontrol(const char *tcKimlikNo) 
{
    FILE *dosya = fopen(DOSYA_ADI, "r");
    
    if (dosya == NULL)
    {
        return 0; // Dosya açılamadı
    }

    struct Personel personel;
    while (fscanf(dosya, "%11s %15s %20s %20s %30s\n",
                  personel.tcKimlikNo, personel.isim, personel.soyisim,
                  personel.telefon, personel.calistigiBirim) == 5) {
        if (strcmp(personel.tcKimlikNo, tcKimlikNo) == 0) {
            fclose(dosya);
            return 1; // TC kimlik numarası kullanılıyor
        }
    }

    fclose(dosya);
    return 0; // TC kimlik numarası kullanılmıyor
}

void listelePersonel() 
{
    FILE *dosya = fopen(DOSYA_ADI, "r");
    
    if (dosya == NULL) 
    {
        printf("Dosya açılamadı.\n");
        return;
    }

    struct Personel personel;
    printf("\n----- Personel Listesi -----\n");
    while (fscanf(dosya, "%11s %15s %15s %11s %30s\n",personel.tcKimlikNo, personel.isim, personel.soyisim,
                  personel.telefon, personel.calistigiBirim) == 5)
                   {
        printf("TC Kimlik No: %s\n", personel.tcKimlikNo);
        printf("Isim: %s %s\n", personel.isim, personel.soyisim);
        printf("Telefon: %s\n", personel.telefon);
        printf("Calistigi Birim: %s\n", personel.calistigiBirim);
        printf("-------------------------\n");
    }

    fclose(dosya);
}

void eklePersonel() 
{
    FILE *dosya = fopen(DOSYA_ADI, "a");
    
    if (dosya == NULL) 
    {
        printf("Dosya açılamadı.\n");
        return;
    }

    struct Personel personel;
    printf("\n----- Personel Bilgileri Ekleme -----\n");
    printf("TC Kimlik No: ");
    scanf("%11s", personel.tcKimlikNo);

    if (tcKimlikNoKontrol(personel.tcKimlikNo)) {
        fclose(dosya);
        printf("Bu TC Kimlik No zaten kayıtlı!\n");
        return;
    }

    printf("Isim: ");
    scanf("%15s", personel.isim);
    printf("Soyisim: ");
    scanf("%15s", personel.soyisim);
    printf("Telefon: ");
    scanf("%11s", personel.telefon);
    printf("Calistigi Birim: ");
    scanf("%30s", personel.calistigiBirim);

    fprintf(dosya, "%-11s %-15s %-15s %-11s %-30s\n",
            personel.tcKimlikNo, personel.isim, personel.soyisim,
            personel.telefon, personel.calistigiBirim);

    printf("Personel başarıyla eklendi.\n");
    fclose(dosya);
}

void silPersonel() 
{
    FILE *dosya = fopen(DOSYA_ADI, "r");
    
    if (dosya == NULL) 
    {
        printf("Dosya açılamadı.\n");
        return;
    }

    FILE *tempDosya = fopen("temp.txt", "w");
    if (tempDosya == NULL) {
        printf("Dosya açılamadı.\n");
        fclose(dosya);
        return;
    }

    char silinecekTC[12];
    printf("\n----- Personel Silme -----\n");
    printf("Silinecek Personelin TC Kimlik No'su: ");
    scanf("%11s", silinecekTC);

    struct Personel personel;
    int silindi = 0;

    while (fscanf(dosya, "%11s %15s %15s %11s %30s\n",
                  personel.tcKimlikNo, personel.isim, personel.soyisim,
                  personel.telefon, personel.calistigiBirim) == 5) {
        if (strcmp(personel.tcKimlikNo, silinecekTC) != 0) {
            fprintf(tempDosya, "%-11s %-15s %-15s %-11s %-30s\n",
                    personel.tcKimlikNo, personel.isim, personel.soyisim,
                    personel.telefon, personel.calistigiBirim);
        } else {
            silindi = 1;
        }
    }

    fclose(dosya);
    fclose(tempDosya);

    remove(DOSYA_ADI);
    rename("temp.txt", DOSYA_ADI);

    if (silindi) {
        printf("Personel başarıyla silindi.\n");
    } else {
        printf("Silinecek personel bulunamadı.\n");
    }
}

void guncellePersonel() 
{
    FILE *dosya = fopen(DOSYA_ADI, "r");
    
    if (dosya == NULL)
    {
        printf("Dosya açılamadı.\n");
        return;
    }

    FILE *tempDosya = fopen("temp.txt", "w");
    
    if (tempDosya == NULL) {
        printf("Temp dosya açılamadı.\n");
        fclose(dosya);
        return;
    }

    char guncellenecekTC[12];
    printf("\n----- Personel Güncelleme -----\n");
    printf("Güncellenecek Personelin TC Kimlik No'su: ");
    scanf("%11s", guncellenecekTC);

    struct Personel personel;
    int guncellendi = 0;

    while (fscanf(dosya, "%11s %15s %15s %11s %30s\n",
                  personel.tcKimlikNo, personel.isim, personel.soyisim,
                  personel.telefon, personel.calistigiBirim) == 5) 
        {
        if (strcmp(personel.tcKimlikNo, guncellenecekTC) == 0) 
        {
            printf("Yeni Isim: ");
            scanf("%15s", personel.isim);
            printf("Yeni Soyisim: ");
            scanf("%15s", personel.soyisim);
            printf("Yeni Telefon: ");
            scanf("%11s", personel.telefon);
            printf("Yeni Calistigi Birim: ");
            scanf("%30s", personel.calistigiBirim);
            guncellendi = 1;
        }
        fprintf(tempDosya, "%-11s %-15s %-15s %-11s %-30s\n",
                personel.tcKimlikNo, personel.isim, personel.soyisim,
                personel.telefon, personel.calistigiBirim);
    }

    fclose(dosya);
    fclose(tempDosya);

    remove(DOSYA_ADI);
    rename("temp.txt", DOSYA_ADI);

    if (guncellendi) {
        printf("Personel bilgileri başarıyla güncellendi.\n");
    } else {
        printf("Güncellenecek personel bulunamadı.\n");
    }
}

void araPersonel() 
{
    FILE *dosya = fopen(DOSYA_ADI, "r");
    
    if (dosya == NULL) 
    {
        printf("Dosya açılamadı.\n");
        return;
    }

    char anahtar[50];
    printf("\n----- Personel Arama -----\n");
    printf("Aranacak Anahtar Kelime: ");
    scanf("%s", anahtar);

    struct Personel personel;
    int bulundu = 0;

    while (fscanf(dosya, "%11s %15s %15s %11s %30s\n",
                  personel.tcKimlikNo, personel.isim, personel.soyisim,
                  personel.telefon, personel.calistigiBirim) == 5) {
        if (strstr(personel.tcKimlikNo, anahtar) != NULL ||
            strstr(personel.isim, anahtar) != NULL ||
            strstr(personel.soyisim, anahtar) != NULL ||
            strstr(personel.telefon, anahtar) != NULL ||
            strstr(personel.calistigiBirim, anahtar) != NULL) {
            printf("TC Kimlik No: %s\n", personel.tcKimlikNo);
            printf("Isim: %s %s\n", personel.isim, personel.soyisim);
            printf("Telefon: %s\n", personel.telefon);
            printf("Calistigi Birim: %s\n", personel.calistigiBirim);
            printf("-------------------------\n");
            bulundu = 1;
        }
    }

    fclose(dosya);

    if (!bulundu) {
        printf("Aranan kriterlere uygun personel bulunamadı.\n");
    }
}

int main() 
{
    int secim;

    do {
    
        printf("\n--- Personel Kayit Programi ---\n");
        printf("1. Kayit Listele\n");
        printf("2. Kayit Ekle\n");
        printf("3. Kayit Sil\n");
        printf("4. Kayit Guncelle\n");
        printf("5. Kayit Ara\n");
        printf("0. Cikis\n");
        printf("Seciminizi yapin: ");
        scanf("%d", &secim);

        switch (secim) {
            case 1:
                listelePersonel();
                break;
            case 2:
                eklePersonel();
                break;
            case 3:
                silPersonel();
                break;
            case 4:
                guncellePersonel();
                break;
            case 5:
                araPersonel();
                break;
            case 0:
                printf("Programdan çıkılıyor...\n");
                break;
            default:
                printf("Geçersiz seçim! Lütfen tekrar deneyin.\n");
        }
    } while (secim != 0);

    return 0;
}
