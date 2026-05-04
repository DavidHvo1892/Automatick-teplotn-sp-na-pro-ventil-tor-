# Automatick-teplotn-sp-na-pro-ventil-tor-
# 🌡️ Automatický teplotní spínač pro ventilátor (12V)

Tento ročníkový projekt představuje automatický systém chlazení, který spíná 12V ventilátor na základě okolní teploty. Oproti jednodušším spínačům využívá tento systém operační zesilovač v roli komparátoru. Tím je zajištěno přesné sepnutí a zabrání se částečnému otevírání tranzistoru při hraničních teplotách, což by vedlo k jeho zbytečnému a nebezpečnému zahřívání.

## 📋 Hlavní vlastnosti

*   **Přesné spínání (0 nebo 1):** Komparátor zajistí okamžité zapnutí ventilátoru na 100 % výkonu bez zpoždění.
*   **Ochrana elektroniky:** Integrovaná ochranná dioda chrání systém před napěťovými špičkami (zpětnými proudy) z indukční zátěže motorku ventilátoru.
*   **Nastavitelná citlivost:** Možnost přesného nastavení prahové teploty sepnutí pomocí trimru.
*   **Univerzálnost:** Možnost využití standardních 12V PC ventilátorů.

## 🛠 Seznam součástek

| Součástka | Specifikace | Funkce |
| :--- | :--- | :--- |
| **Napájecí adaptér** | 12 V / 1 A | Zdroj energie pro systém |
| **Tranzistor** | N-MOSFET FQP30N06 | Výkonový spínač ovládaný napětím |
| **Senzor** | NTC termistor 10 kΩ | Detekce teploty (odpor klesá s rostoucí teplotou) |
| **Integrovaný obvod**| LM358 | Operační zesilovač fungující jako komparátor napětí |
| **Trimr** | 10 kΩ lineární | Nastavení teplotní hranice sepnutí (referenční napětí) |
| **Rezistor děliče** | 10 kΩ | Společně s NTC tvoří napěťový dělič |
| **Rezistor hradla** | 1 kΩ | Ochrana hradla (Gate) tranzistoru |
| **Dioda** | 1N4007 | "Flyback" ochranná dioda zapojená paralelně k ventilátoru |
| **Zátěž** | 12 V PC ventilátor | Aktivní prvek chlazení |

## ⚙️ Technický princip (Shrnutí funkce)

Systém neustále porovnává dvě různá napětí a na základě výsledku skokově otevírá výkonový tranzistor.
1.  **Snímání teploty:** NTC termistor a pevný rezistor tvoří první napěťový dělič. S rostoucí teplotou se mění úroveň napětí v tomto uzlu.
2.  **Referenční napětí:** Trimr tvoří druhý napěťový dělič a určuje referenční napětí (teplotu, při které se má začít chladit).
3.  **Porovnání:** Integrovaný obvod LM358 funguje jako komparátor, který neustále porovnává napětí ze senzoru s napětím z trimru.
4.  **Sepnutí:** Jakmile napětí ze senzoru překročí referenční hodnotu z trimru, komparátor pošle na výstup plné napětí, čímž otevře MOSFET tranzistor a ventilátor se roztočí.

## 🧠 Rozbor funkce jednotlivých částí obvodu

Pro snazší pochopení detailního principu lze celé zapojení rozdělit do čtyř logických bloků:

### 1. Snímací blok (Teplotní napěťový dělič)
NTC termistor a pevný rezistor 10 kΩ jsou zapojeny v sérii mezi napájecí napětí (+12 V) a zem (GND) a tvoří napěťový dělič. NTC termistor má vlastnost, že s rostoucí teplotou jeho elektrický odpor klesá. Když se termistor zahřeje, jeho odpor se zmenší, což způsobí, že na spojnici mezi ním a pevným rezistorem (uzel připojený na Pin 3) stoupne napětí. Tento uzel nám tedy dává proměnlivé napětí odpovídající aktuální teplotě.

### 2. Referenční blok (Nastavení citlivosti)
Trimr (10 kΩ) funguje jako nastavitelný napěťový dělič v jedné součástce. Otáčením běžce (prostřední nožičky připojené na Pin 2) si lze manuálně nastavit libovolné pevné napětí mezi 0 V a 12 V. Toto napětí slouží jako prahová hodnota, která určuje, při jaké teplotě se má obvod aktivovat.

### 3. Vyhodnocovací blok (Komparátor LM358)
Obvod LM358 funguje jako rozhodovací prvek, který obě hodnoty z děličů neustále porovnává. Pokud je napětí ze senzoru menší než nastavené napětí z trimru, výstup zůstane na 0 V (ventilátor stojí). Jakmile se senzor zahřeje a napětí ze senzoru překročí referenční napětí z trimru, komparátor okamžitě překlopí svůj výstup na téměř +12 V.

### 4. Výkonový a ochranný blok (Spínač a zátěž)
Výstup z komparátoru vede přes ochranný 1 kΩ rezistor na hradlo (Gate) MOSFETu. Rezistor omezuje nárazový proud při nabíjení vnitřní kapacity hradla. Přivedené napětí tranzistor plně otevře a propojí ventilátor se zemí (GND). Ventilátor (indukční zátěž s cívkami) generuje při vypnutí vysokou napěťovou špičku s opačnou polaritou, která by mohla tranzistor zničit. Ochranná "flyback" dioda (1N4007), zapojená paralelně k ventilátoru v nepropustném směru, tuto špičku bezpečně zkratuje a energii neškodně vybije.

## 🔌 Schéma zapojení

```text
                     +12V (VCC z adaptéru)
                               │
       ┌───────────┬───────────┼───────────┬──────────────┐
       │           │           │           │              │
      [NTC]        │       Pin 8 (VCC)     │              │
       │           │         LM358         │             (+)
       ├───────► Pin 3                     │          Ventilátor
       │         (IN+)                     │             (-)
      [R 10k]      │                       │              │
       │           │         LM358       Katoda ──[Dioda]─┤
      GND          │      Pin 1 (OUT)    Anoda            │
                   │           │                          │
      +12V         │           ├────[R 1k]─────► Gate (G) │
       │           │           │                          │
     [Trimr] ──► Pin 2         │                        Drain (D)
     (10k)       (IN-)         │                         MOSFET
       │                       │                        Source (S)
      GND                  Pin 4 (GND)                    │
                             LM358                        │
                               │                          │
       └───────────┴───────────┴──────────────────────────┘
                               │
                       GND (0V z adaptéru)
