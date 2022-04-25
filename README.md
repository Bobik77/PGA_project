# PGA_project
PGA project shared directory
Maked by: Hodák Dominik & Vaněk Pavel
Rozdělení práce:
1. Nákres modelu dávkovače - Dominik
2. SCADA - Dominik
3. ADDon Převod BCD + alarmy - Pavel
4. ADDon Ventilátory - Pavel
5. ADDon Řízení válce - Pavel 
6. Main rutine - pavel
7. Dokumnetace - OBA


## Nákres modelu - Dominik


## Vizualizace SCADA - Dominik
Vizualizace obsahuje dávkovač a ovládací panel. 
Na ovládavcím panelu jsou tlačítka Start, Stop spolu s červenou a zelenou led. Ovladácí panel obsahuje kontrolky pro všechny alarmy. Otočné číslicové snímače ovládací panel neobsahuje z důvodu interpretace zadání. Volba požadovaného počtu kuliček probíhá přímím nastavovaním logické hodnoty na vstupy PLC pomocí Studio 5000 Logix Emulate.
Kuličky v dávkovači jsou pouze pro představu. Pro jejich pohyb by bylo potřeba úpravy kódu kterou by reálné zařízení nevyužilo. Každý válec má dva číslicové indikátory, horní pro zvolený počet kuliček, druhý ukazuje aktuální počet nadávkovaných kuliček. Dále má každý válec dvě tlačítka - pro horní snímač a zpětné hlášení pro ventilátor.
Na ovládavcím panelu jsou tlačítka Start, Stop spolu s červenou a zelenou led. Ovladácí panel obsahuje kontrolky pro všechny alarmy. Otočné číslicové snímače ovládací panel neobsahuje z důvodu interpretace zadání. Volba požadovaného počtu kuliček probíhá přímím nastavovaním logické hodnoty na vstupy PLC pomocí Studio 5000 Logix Emulate.
Kuličky v dávkovači jsou pouze pro představu. Pro jejich pohyb by bylo potřeba úpravy kódu kterou by reálné zařízení nevyužilo. Každý válec má dva číslicové indikátory, horní pro zvolený počet kuliček, druhý ukazuje aktuální počet nadávkovaných kuliček. Dále má každý válec dvě tlačítka - pro horní snímač a zpětné hlášení pro ventilátor.
![Vizualizace SCADA](/SCADA.jpg "SCADA")

## Add_on bcd_to_dec (FBD) - Pavel 
Přímo přepisuje bity BCD kódu do DINT. Jsou zde aplikovány alarmy indikující přetečení hodnoty z požadovaného rozmezí. V tomto projektu prakticky nemůže nastat alarm_l.


## Add_on ventilator (FBD) - Pavel 
Kontroluje spravnou funkci ZH od stykače ventilátoru. Pokud stykač do doby **TZH** po zapnutí/vypnutí nesepne/nerozepne, funkce setuje **fault** flag. Chybový flag můžeme resetovat  setováním **clr_fault**.

## Add_on cilinder_control - Pavel 
Obsahuje kompletní řízení západek válce - odpočítá potřebný počet kuliček.
VSTUPY:
-**senzor** připojení senzoru snímače na vrchu válce
-**num_balls** DINT potřebného počtu kuliček, který se má odpočítat
-**start** spuštění stroje - fáze plnění válců
-**stop** vypnutí stroje - čekání na vyprazdnění válců
-**activate** spuštění cyklu dávkování míčků
-**dose_period** délka trvnání dávkování jednoho míčku
VÝSTUPY:
-**zapadka_h** horní západka
-**zapadka_l** dolní západka
-**ball_cntr** počet odpočítaných míčků
-**ready** připraveno pro cyklus dávkování (zakrytý snímač)
-**run** probíhající dávkování
-**complete** odpočítány všechny míčky
-**state** informační proměná stavu FSM

# Main rutine- Pavel
Je psána v FBD. Schéma je rozdělené na tři listy. Na prvním se věnuji kompletnímu řízení válců a spouštění fází plnění a dávkování. Druhý list se věnuje převodu BDC na dec. a ošetření alarmů. Třetí list se věnuje správné funkci ventilátorů.
## Indikace
Podle zadání je indikován zelenou LED stav splnění všech podmínek pro splění dávkování (sepnutý S4 a naplněny všechny válce). Pokud dojde k zastavení stroje rozsvítí se červená LED na znamení, že stroj vyčkává na opětovné stisknutí STOP, čímž dojde vyčištění válců do odpadní krabice. Tento stav uživatel může také opustit opětovným stiskem START, čímž započne znovu fáze naplnění válců.
## Poruchy
Jediná detekovatelná porucha může být způsobená ZH od stykačů ventilátoru. Při chybě se setuje **fault** flag a stroj zastaví dávkování a přejde do stavu, kdy čeká na vyčištění válců. Chyba může být resetována stisknutím STOP talčítka.
## Alarmy BCD
Pokud je detekován alarm špatné hodnoty BCD, stroj nelze spustit.


