### Ako vznikol Dash
V dobe písania tohto článku je to presne 10 rokov od vydania knižnice Plotly pre Python, ktorá slúži primárne na tvorbu interaktívnych grafov a vizualizácií (grafy natívne umožňujú napríklad skryť/zobraziť jednotlivé časti grafov, zobrazenie dát po priložení myšou, priblíženie a škálovanie osí atď). Vzhľadom k popularite a potenciálu, ktorý ponúkala tvorba interaktívnych dátových aplikácií pre web v Pythone vychádza v roku 2017 Dash.

### Rozloženie a výzor
Programovanie v Dashi rád vysvetľujem pomocou stavebníc LEGO.

<img src="https://i.ibb.co/sKPT9rV/lego-van-gogh.png" width="1000px"/>  |  <img src="https://media2.giphy.com/media/yoJC2MD0BBbw68Oht6/giphy.gif?cid=ecf05e47x1xhnq4vgdcdv9pskpjm90nnq099zziarjvrtf49&ep=v1_gifs_search&rid=giphy.gif&ct=g" width="1000px"/>
:-------------------------:|:-------------------------:

Naša aplikácia je vlastne ako stavebnica. Najjednoduchšími dielikmi, z ktorých môžeme poskladať našu aplikáciu sú html komponenty - nadpisy (`H1` - `H6`), paragraf (`P`), odkazy (`A`, `Link`), tlačidlá (`Button`), obrázky (`Img`), tabuľky (`Table`, `Tr`, `Td`) a mnoho ďalších. Tým z vás, ktorí niekedy vyvíjali weby sú tieto názvy pravdepodobne povedomé - nie je to náhoda. Keďže Dash slúži na tvorbu interaktívnych **webových** aplikácií, umožňuje pracovať práve s týmito webovými komponentami v pythone.

Teraz si predstavte, že by sme z lega stavali napríklad auto. Pokiaľ chceme aby naše auto skutočne dobre vyzeralo, malo kopu detailov, krásne kolesá a prepracované tvary, pravdepodobne nám to zaberie veľké množstvo času. Podobne by to fungovalo, keby ste sa z týchto html komponent pokúsili postaviť nejakú väčšiu aplikáciu - predsa len, stavať každú tabuľku z jednotlivých buniek, nehovoriac o sofistikovanejších komponentách (napríklad vysúvacie menu alebo vyskakovacie okno) je ohromné množstvo práce. Pokiaľ by sme ale dostali napríklad predpripravený motor, podvozok, dvere alebo strechu, dokázali by sme si prácu výrazne uľahčiť.

To si uvedomili aj ľudia ktorí vyvíjali weby. A tak vznikli frameworky - súbory pokročilejších komponent a rôznych programov, štandardne využívaných pri tvorbe webov. A Dash umožňuje prinášať tieto frameworky do pythonu (príklady tu: TODO link). S ich pomocou vieme získať dobre vyzerajúce aplikácie spĺňajúce moderné štandardy v rozumnom čase.

### Pridávame interaktivitu
Auto z lega vyzerá pekne na poličke, pridaním ozubených kolies, osami a motorčekmi ale môžeme dosiahnuť, že sa auto začne hýbať alebo volant zatáčať kolesami. Ako sme spomínali už na začiatku, Plotly vždy excelovalo v pridávaní interaktivity do grafov a vizualizácií. Inak tomu nie je ani u Dashu v pridávaní interaktivity do webových aplikácií. 

Ako spúšťač môže poslúžiť napríklad tlačidlo, po jeho stlačení dôjde k načítaniu aktuálnych dát. Ako výstup nám môže poslúžiť graf, v ktorom tieto dáta zobrazíme. Ako spúšťač a výstup môžeme použiť akúkoľvek komponentu. Medzi vstupom a výstupom môže prebehnúť akýkoľvek výpočet, čo nám dáva mnoho možností. 
