### Prvá aplikácia

Vráťme sa k jednoduchej aplikácii z úvodu a pozrime sa na jej jednotlivé časti.

```python
from dash import Dash  
import dash_mantine_components as dmc  
  
app = Dash(__name__)  
  
app.layout = dmc.Text("Moja prvá aplikácia!")  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

Prvým krokom je vždy import všetkých častí ktoré budeme potrebovať. Z knižnice `dash` si importujeme triedu `Dash`, pomocou ktorej skonštruujeme našu aplikáciu. Rovnako si importujeme aj knižnicu `dash_mantine_components` ktorej priradíme synonymum `dmc`. To znamená, že až z tejto knižnice budeme volať jednotlivé komponenty, môžeme použiť túto skratku.

 ```python
app = Dash(__name__)  
```
Tento riadok sa nazýva Dash contructor, ktorý je zodpovedný za inicializáciu našej aplikácie. Týmto spôsobom je inicializovaná každá aplikácia v Dashi, je však možné pridávať rôzne parametre (napríklad pre viac stránkové aplikácie, ktoré si ukážeme neskôr). 

```python
app.layout = dmc.Text("Moja prvá aplikácia!")  
```

Aplikáciu sme si inicializovali v objekte `app`. Touto formuláciou upravíme jeho vlastnosť `layout` (voľným prekladom by sme mohli preložiť ako rozloženie). Ako názov napovedá, touto vlastnosťou určujeme rozloženie aplikácie teda to, ako aplikácia vyzerá. 

V tomto príklade aplikácia obsahuje komponentu Text z knižnice DMC (Dash Mantine Components). Nahliadnutím do [dokumentácie](https://www.dash-mantine-components.com/components/text) sa dozvieme, že táto komponenta vypíše nami zadaný textový reťazec. Môžeme tiež nahliadnuť, že tejto komponente môžeme nastaviť rôzne parametre, ktoré ovplyvnia to, ako bude daný text vyzerať. 

```python
if __name__ == "__main__":  
    app.run(debug=True)
```

Posledné dva riadky sú zodpovedné za spustenie nami definovanej aplikácie. Opäť sa jedná o riadky, ktoré sú skoro vždy identické. Význam parametru `debug=True` si vysvetlíme v sekcii o [ladení](#ladenie-debugging).

### Layout - meníme to, ako naša aplikácia vyzerá

Poďme sa teraz viac povenovať dvom častiam aplikácie, s ktorými pri vývoji strávime najviac času. Prvou z týchto častí je layout. Typicky budeme layout skladať z predpripravených komponent. V našom prípade sme si vybrali knižnicu Dash Mantine Components (DMC). Na úvod je vždy užitočné zoznámiť sa s [dokumentáciou](https://www.dash-mantine-components.com/) a prezrieť si, aké komponenty knižnica obsahuje. Urobíte si tak dobrú predstavu o tom, aké možnosti sú k dispozícii a zároveň vás tento prehľad môže inšpirovať k ešte lepším nápadom ako rozloženie aplikácie vyriešiť.

 Povedzme, že by sme si radi pripravili aplikáciu, v ktorej si zadáme dve čísla a následne po stlačení tlačidla aplikácia vypíše súčet týchto dvoch čísel. Keďže sme nepodcenili prípravu (:))) a prezreli si komponenty, ktoré máme k dispozícii, tak sme zistili, že môžeme použiť napríklad [NumberInput](https://www.dash-mantine-components.com/components/numberinput) na zadanie čísel na vstupe, [Button](https://www.dash-mantine-components.com/components/button) a [Text](https://www.dash-mantine-components.com/components/text) na zobrazenie výsledku. Navyše komponenta [Div](https://dash.plotly.com/dash-html-components/div) z knižnice html plní rolu prázdneho kontajneru bez vlastností.

```python
from dash import Dash, html  
import dash_mantine_components as dmc  
  
app = Dash(__name__)  
  
app.layout = html.Div(  
    [  
        dmc.NumberInput(id="prve-cislo"),  
        dmc.NumberInput(id="druhe-cislo"),  
        dmc.Button(children="Sčítaj!", id="input-button"),  
        dmc.Text(id="output"),  
    ]  
)  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

U všetkých komponent sme pridali ich unikátny identifikátor pod parametrom `id`. Pri tlačidle sme navyše pridali parameter `children`, ktorý určuje nápis, ktorý sa na tlačidle objaví. S parametrom `children` sa stretneme naprieč mnohými komponentami - vďaka jeho špecifickým vlastnostiam je za `children` považovaný objekt na prvom mieste. Teda zápis `dmc.Button("Sčítaj!",  id="input-button")` je ekvivalentný.

### Callback - pridávame interaktivitu

Teraz je na čase našu aplikáciu priviesť k životu. Na vytváranie interaktivity v Dashi slúžia špeciálne funkcie nazývané `callback`. Vytvorme si teraz `callback`, ktorý sa postará o interaktivitu, ktorú sme požadovali.

```python
from dash import Dash, html, callback, Output, Input, State  
import dash_mantine_components as dmc  
  
app = Dash(__name__)  
  
app.layout = html.Div(  
    [  
        dmc.NumberInput(id="prve-cislo"),  
        dmc.NumberInput(id="druhe-cislo"),  
        dmc.Button(children="Sčítaj!", id="vstupne-tlacidlo"),  
        dmc.Text(id="vystup"),  
    ]  
)  
  
  
@callback(  
    Output("vystup", "children"),  
    Input("vstupne-tlacidlo", "n_clicks"),  
    State("prve-cislo", "value"),  
    State("druhe-cislo", "value"),  
)  
def scitaj(n_clicks, prve_cislo, druhe_cislo):
	sucet = prve_cislo + druhe_cislo
    return sucet


if __name__ == "__main__":  
    app.run(debug=True)
``` 

Vytvorili sme callback `scitaj`. Všimnime si, že callback obsahuje 3 typy parametrov: `Output`, `Input` a `State`.

- `Output` - náš callback zmení daný parameter danej komponenty
- `Input` -  pri zmene daného parametra danej komponenty dôjde ku spusteniu tohto callbacku a zároveň callback použije tento parameter ako vstup pre svoj výpočet
- `State` - rovnako ako pri `Input` je tento parameter použitý ako vstup avšak pri jeho zmene nedochádza k spusteniu callbacku

Callback sa spustí pri zmene parametru `n_clicks` tlačidla s identifikátorom `vstupne-tlacidlo` a okrem tohto parametru aplikácia použije ešte stav parametrov `value` z komponent `prve-cislo` a `druhe-cislo`. Výpočet v samotnej funkcie prevedie jednoduchý súčet a vráti ho do parametru `children` komponenty Text s identifikátorom `vystup`. 

### Ladenie - debugging

Ako som sľúbil na začiatku, je čas sa pozrieť na to, akú funkciu má parameter `debug = True`. Aplikácia je hotová, poďme sa na ňu teda konečne pozrieť!

![enter image description here](https://i.ibb.co/LhKCDjk/ladenie-aplikacie-surprised.gif)

Úspešne sme vytvorili našu prvú chybu! Pokiaľ spustíme aplikáciu s parametrom `debug = True`, aplikácia sa spustí v režime ladenia. Všimnime si, že pri spustení aplikácie v prehliadači vidíme v pravom dolnom rohu menu. Toto menu nám umožňuje zobraziť rôzne funkcie, ktoré nám pomôžu ladiť našu aplikáciu. Jednou z týchto funkcií je zobrazovanie chýb, ktoré vzniknú v behu callbackov. 

V našom prípade sme dostali chybu `Callback error updating vystup.children`. Kliknutím na túto chybovú hlášku získame viac detailov: `TypeError: unsupported operand type(s) for +: 'NoneType' and 'NoneType'`. Ako je to možné? Veď sme ešte ani nestihli čokoľvek spraviť! Táto chyba nám vraví, že Python nedokáže sčítať dva objekty bez hodnoty. Ale veď my sme ešte nič sčítať nechceli! Predvoleným správaním callbackov je, že každý callback sa spustí pri prvom spustení aplikácie a presne k tomu došlo v našom prípade. Riešení tohto problému je niekoľko, my ale využijeme možnosť pri tomto callbacku toto správanie vypnúť pridaním parametru `prevent_initial_call=True`.

```python
from dash import Dash, html, callback, Output, Input, State  
import dash_mantine_components as dmc  
  
app = Dash(__name__)  
  
app.layout = html.Div(  
    [  
        dmc.NumberInput(id="prve-cislo"),  
        dmc.NumberInput(id="druhe-cislo"),  
        dmc.Button(children="Sčítaj!", id="vstupne-tlacidlo"),  
        dmc.Text(id="vystup"),  
    ]  
)  
  
  
@callback(  
    Output("vystup", "children"),  
    Input("vstupne-tlacidlo", "n_clicks"),  
    State("prve-cislo", "value"),  
    State("druhe-cislo", "value"),  
    prevent_initial_call=True,  
)  
def scitaj(n_clicks, prve_cislo, druhe_cislo):  
    sucet = prve_cislo + druhe_cislo  
    return sucet  
  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

Po reštartovaní aplikácie a jej otvorení v prehliadači vidíme, že chyba sa už viac nezobrazuje. Môžeme teda vyplniť vstupy, stlačiť tlačidlo "Sčítaj!" a dočkáme sa správneho výsledku!

![enter image description here](https://i.ibb.co/b1yT3DR/funkcna-aplikacia.gif)

Úspešne sme vytvorili našu prvú aplikáciu! 
