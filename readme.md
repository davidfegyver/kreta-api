# e-Kréta  v3 API dokumentáció

*Krétás API-knak nem hivatalos gyűjteménye*

## Endpoints
```js
const userAgent = "hu.ekreta.student/1.0.5/Android/0/0";
const clientID = "kreta-ellenorzo-mobile";
const eugyintezes = "https://eugyintezes.e-kreta.hu";
const idp = "https://idp.e-kreta.hu";
const inst = "https://huszargaliskola.e-kreta.hu";

const messageEndpoints = {
    sendMessage: `${eugyintezes}/api/v1/kommunikacio/uzenetek`,
    messages: `${eugyintezes}/api/v1/kommunikacio/postaladaelemek/`, // endpoint
    message: `${eugyintezes}/api/v1/kommunikacio/postaladaelemek/`, // id
    recipientCategories: `${eugyintezes}/api/v1/adatszotarak/cimzetttipusok`,
    availableCategories: `${eugyintezes}/api/v1/kommunikacio/cimezhetotipusok`,
    recipientsTeacher: `${eugyintezes}/api/v1/kreta/alkalmazottak/tanar`,
    downloadAttachment: `${eugyintezes}/api/v1/dokumentumok/uzenetek/$id`,
    trashMessage: `${eugyintezes}/api/v1/kommunikacio/postaladaelemek/kuka`,
    deleteMessage: `${eugyintezes}/api/v1/kommunikacio/postaladaelemek/torles`
};

const commonEndpoints= {
    login:`${idp}/connect/token`,
    notes: `${inst}/ellenorzo/V3/Sajat/Feljegyzesek`,
    events: `${inst}/ellenorzo/V3/Sajat/FaliujsagElemek`,
    student: `${inst}/ellenorzo/V3/Sajat/TanuloAdatlap`,
    evaluations: `${inst}/ellenorzo/V3/Sajat/Ertekelesek`,
    absences: `${inst}/ellenorzo/V3/Sajat/Mulasztasok`,
    groups: `${inst}/ellenorzo/V3/Sajat/OsztalyCsoportok`,
    timetable: `${inst}/ellenorzo/V3/Sajat/OrarendElemek`,
    exams: `${inst}/ellenorzo/V3/Sajat/BejelentettSzamonkeresek`,
    homeworks: `${inst}/ellenorzo/V3/Sajat/HaziFeladatok`,
    homeworkDone: `${inst}/ellenorzo/V3/Sajat/HaziFeladatok/Megoldva`,
    capabilities: `${inst}/ellenorzo/V3/Sajat/Intezmenyek`
};
```
## Iskolák lekérdezése
#### Az összes iskola ahol be van vezetve az e-Kréta:  
```bash
curl -H "apiKey: 7856d350-1fda-45f5-822d-e1a2f3f1acf0"  https://kretaglobalmobileapi2.ekreta.hu/api/v1/Institute
```
#### A szerver válasza:  
```json
[
  {
    "instituteId": 7458,
    "instituteCode": "appteszt",
    "name": "PedApp Teszt Intézmény",
    "city": "Budapest",
    "url": "https://appteszt.e-kreta.hu",
    "advertisingUrl": "",
    "informationImageUrl": "https://kretamobile.blob.core.windows.net/advertisement/nati_app.gif",
    "informationUrl": "",
    "featureToggleSet": {}
},
  ...
]
```
#### Egy iskola adatainak lekérése ID alapján:
```bash
curl -H "apiKey: 7856d350-1fda-45f5-822d-e1a2f3f1acf0"  https://kretaglobalmobileapi2.ekreta.hu/api/v1/Institute/3928
```

#### A szerver válasza:  
```json
{
    "instituteId": 7458,
    "instituteCode": "appteszt",
    "name": "PedApp Teszt Intézmény",
    "city": "Budapest",
    "url": "https://appteszt.e-kreta.hu",
    "advertisingUrl": "",
    "informationImageUrl": "https://kretamobile.blob.core.windows.net/advertisement/nati_app.gif",
    "informationUrl": "",
    "featureToggleSet": {}
}
```
## API linkek lekérdezése
#### Lekéri a KRÉTA API linkjét:
```bash
curl http://kretamobile.blob.core.windows.net/configuration/ConfigurationDescriptor.json
```
* Egy sima böngészőből is [elérhető](http://kretamobile.blob.core.windows.net/configuration/ConfigurationDescriptor.json)

#### A szerver válasza:  
```json
{
  "GlobalMobileApiUrlDEV": "https://kretaglobalmobileapidev.ekreta.hu/",
  "GlobalMobileApiUrlTEST": "https://kretaglobalmobileapitest.ekreta.hu",
  "GlobalMobileApiUrlUAT": "https://kretaglobalmobileapiuat.ekreta.hu",
  "GlobalMobileApiUrlPROD": "https://kretaglobalmobileapi2.ekreta.hu"
}
```
## Bejelentkezés
### Lekér egy Bearer kódot amit majd azonosításra fogunk használni később

```bash
curl --data "institute_code=xxxxxxxxxxx&userName=xxxxxxxxxxx&password=xxxxxxxxxxx&grant_type=password&client_id=kreta-ellenorzo-mobile" https://idp.e-kreta.hu/connect/token -H "Content-Type: application/x-www-form-urlencoded; charset=utf-8"
```

* institute_code: az intézmény azonosítója
* userName: a felhasználó azonosítója
* password: a felhasználó jelszava
* grant_type: lehet
   * **Password** `grant_type=password`
   * **Refresh Token** `grant_type=refresh_token`
   Amikor Password-el lekérjük az access_token-t akkor egy refresh_token-t is kapunk, amivel később a jelszó nélkül is frissíthetjük az access_token-ünket.
* client_id: `kreta-ellenorzo-mobile`

### A Bearer token-ról:
A bearer igazából egy JWT, decodeolva ezt kapjuk: 
```json
{
  "nbf": 00000,
  "exp": 00000,
  "iss": "https://idp.e-kreta.hu",
  "aud": [
    "https://idp.e-kreta.hu/resources",
    "kozelkep-webapi",
    "kreta-ellenorzo-webapi",
    "kreta-fileservice-webapi",
    "kreta-mobile-global-webapi"
  ],
  "client_id": "kreta-ellenorzo-mobile",
  "sub": "000000",
  "auth_time": 0000000000,
  "idp": "local",
  "name": "Fegyver Dávid Bátor",
  "kreta:user_name": "00000000000",
  "kreta:institute_code": "XXXXXXXXXXXXXXXX",
  "kreta:institute_user_id": "XXXXXX",
  "kreta:institute_user_unique_id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX",
  "kreta:institute_user_idp_unique_id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX",
  "kreta:school_year_id": "0000",
  "kreta:school_year_unique_id": "00",
  "kreta:institute_unique_id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX",
  "kreta:user_type": "Tanulo",
  "role": "Tanulo",
  "scope": [
    "kozelkep-webapi.public",
    "kreta-ellenorzo-webapi.public",
    "kreta-fileservice-webapi.public",
    "kreta-mobile-global-webapi.public",
    "offline_access"
  ],
  "amr": [
    "password"
  ]
}


```

#### A szerver válasza:
```json
{
 "access_token":"XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
 "refresh_token": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
 "expires_in":1800,
 "scope":"kozelkep-webapi.public kreta-ellenorzo-webapi.public kreta-fileservice-webapi.public kreta-mobile-global-webapi.public offline_access"
 }
```

### `access_token` frissítése
```bash
curl --data "institute_code=xxxxxxxxxxx&refresh_token=xxxxxxxxxxx&grant_type=refresh_token&client_id=kreta-ellenorzo-mobile" https://idp.e-kreta.hu/connect/token -H "Content-Type: application/x-www-form-urlencoded; charset=utf-8"
```
* ugyanúgy működik, mint a Bearer kód lekérdezése
* itt is kapunk egy refresh_token-t amit újra fel tudunk használni a következő frissítéshez


## Felhasználó adatainak lekérdezése
* a mobil alkalmazás használja
* kell hozzá a Bearer azonosító (lásd: bejelentkezés)

```bash
curl -H "Authorization: Bearer XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -H "User-Agent: hu.ekreta.student/1.0.5/Android/0/0" https://xxxxxxxxxxx.e-kreta.hu/ellenorzo/V3/Sajat/TanuloAdatlap
```

#### A szerver válasza:
```json
{
  "AnyjaNeve": "",
  "Cimek": [
    "Lakcímeid"
  ],
  "Gondviselok": [
    {
      "EmailCim": null,
      "Nev": "",
      "Telefonszam": "",
      "Uid": ""
    },
    ...
  ],
  "IntezmenyAzonosito": "xxxxxxxxxxx",
  "IntezmenyNev": "Iskola teljes neve",
  "Nev": "Fegyver Dávid Bátor",
  "SzuletesiDatum": "2006-08-01T22:00:00Z",
  "SzuletesiHely": "Debrecen",
  "SzuletesiNev": "Fegyver Dávid Bátor",
  "TanevUid": "xxxx",
  "Uid": "xxxxxx",
  "Bankszamla": {
    "BankszamlaSzam": "",
    "BankszamlaTulajdonosTipusId": null,
    "BankszamlaTulajdonosNeve": null,
    "IsReadOnly": false
  },
  "Intezmeny": {
    "Uid": "3683",
    "RovidNev": "Iskola rovid neve",
    "Rendszermodulok": [
      {
        "IsAktiv": true,
        "Tipus": "Eugyintezes"
      },
      ...
    ],
    "TestreszabasBeallitasok": {
      "IsDiakRogzithetHaziFeladatot": false,
      "IsTanorakTemajaMegtekinthetoEllenorzoben": true,
      "IsOsztalyAtlagMegjeleniteseEllenorzoben": false,
      "ErtekelesekMegjelenitesenekKesleltetesenekMerteke": 0
    }
  }
}
```
## Jegyek lekérdezése

* a mobil alkalmazás használja
* kell hozzá a Bearer azonosító (lásd: bejelentkezés)

```bash
curl -H "Authorization: Bearer XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -H "User-Agent: hu.ekreta.student/1.0.5/Android/0/0" https://xxxxxxxxxxx.e-kreta.hu/ellenorzo/V3/Sajat/Ertekelesek?DatumTol=xxxx-xx-xx&DatumIg=xxxx-xx-xx
```

#### A szerver válasza:
```json
[{
    "ErtekeloTanarNeve": "",
    "ErtekFajta": {
      "Uid": "1,Osztalyzat",
      "Leiras": "Elégtelen (1) és Jeles (5) között az öt alapértelmezett érték",
      "Nev": "Osztalyzat"
    },
    "Jelleg": "Ertekeles",
    "KeszitesDatuma": "2020-09-22T06:38:48Z",
    "LattamozasDatuma": null,
    "Mod": {
      "Uid": "1516,orai_munka",
      "Leiras": "Órai munka",
      "Nev": "orai_munka"
    },
    "RogzitesDatuma": "2020-09-21T22:00:00Z",
    "SulySzazalekErteke": 100,
    "SzamErtek": 5,
    "SzovegesErtek": "Jeles(5)",
    "SzovegesErtekelesRovidNev": null,
    "Tantargy": {
      "Uid": "xxxxxx",
      "Kategoria": {
        "Uid": "1224,enek_zene",
        "Leiras": "Ének-zene",
        "Nev": "enek_zene"
      },
      "Nev": "Ének-zene"
    },
    "Tema": "katonadalok éneklése",
    "Tipus": {
      "Uid": "1518,evkozi_jegy_ertekeles",
      "Leiras": "Évközi jegy értékelés",
      "Nev": "evkozi_jegy_ertekeles"
    },
    "OsztalyCsoport": {
      "Uid": "xxxxxx"
    },
    "SortIndex": 0,
    "Uid": "25830018,Ertekeles"
  },
  ...
]
```
## Bejelentett számonkérések lekérése
* a mobil alkalmazás használja
* kell hozzá a Bearer azonosító (lásd: bejelentkezés)

```bash
curl https://xxxxxxxxxx.e-kreta.hu/ellenorzo/V3/Sajat/BejelentettSzamonkeresek?DatumTol=null&DatumIg=null -H "Authorization: Bearer XXXXXXXXXXXXXXX" -H "User-Agent: hu.ekreta.student/1.0.5/Android/0/0" 
```

A szerver válasza:

```json
[
  {
    "BejelentesDatuma": "2020-11-10T11:05:31Z",
    "Datum": "2020-11-23T23:00:00Z",
    "Modja": {
      "Uid": "1514,hazi_dolgozat",
      "Leiras": "Házi Dolgozat",
      "Nev": "hazi_dolgozat"
    },
    "OrarendiOraOraszama": 5,
    "RogzitoTanarNeve": "",
    "Tantargy": {
      "Uid": "399368",
      "Kategoria": {
        "Uid": "1202,angol_nyelv",
        "Leiras": "Angol nyelv",
        "Nev": "angol_nyelv"
      },
      "Nev": "Angol nyelv"
    },
    "TantargyNeve": "Angol nyelv",
    "Temaja": "",
    "OsztalyCsoport": {
      "Uid": ""
    },
    "Uid": ""
  },
  ...
]
```
## Órarend lekérése
### Lekéri két adott időpont között megtartott (vagy elmaradt) tanórákat

* a mobil alkalmazás használja
* kell hozzá a Bearer azonosító (lásd: bejelentkezés)
* DatumTol: a vizsgált időintervallum kezdete (ÉÉÉÉ-HH-NN)
* DatumIg: a vizsgált időintervallum vége (ÉÉÉÉ-HH-NN)

```bash
curl -H "Authorization: Bearer XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" https://xxxxxxxxxxx.e-kreta.hu/ellenorzo/V3/Sajat/OrarendElemek?DatumTol=2020-11-23&DatumIg=2020-11-28 -H "User-Agent: hu.ekreta.student/1.0.5/Android/0/0" 
```

#### A szerver válasza:
```json
[
  {
    Allapot: {
      Uid: '2,Nem_naplozott', 
      Leiras: 'Nem naplózott',
      Nev: 'Nem_naplozott'
    },
    BejelentettSzamonkeresUids: [],
    BejelentettSzamonkeresUid: null,
    Datum: '2020-11-26T23:00:00Z',
    HelyettesTanarNeve: null,
    IsTanuloHaziFeladatEnabled: false,
    KezdetIdopont: '2020-11-27T11:15:00Z',
    Nev: 'Testnevelés',
    Oraszam: 6,
    OraEvesSorszama: null,
    OsztalyCsoport: { Uid: 'xxxxxx', Nev: 'Testnevelés 8.a/ 8.b fiúk' },
    HaziFeladatUid: null,
    IsHaziFeladatMegoldva: false,
    TanarNeve: '',
    Tantargy: { Uid: '399380', Kategoria: [Object], Nev: 'Testnevelés' },
    TanuloJelenlet: { Uid: '1497,Na', Leiras: 'Nem definiált', Nev: 'Na' },
    Tema: null,
    TeremNeve: 'Tornaterem',
    Tipus: {
      Uid: '1,OrarendiOra',
      Leiras: 'Órarendi óra',
      Nev: 'OrarendiOra'
    },
    Uid: '3181798,OrarendiOra,2020-11-26T23:00:00Z',
    VegIdopont: '2020-11-27T11:55:00Z'
  },
  ...
]



# Még sok van hátra, ha tudsz, segíts a munkámban egy pull requesttel
