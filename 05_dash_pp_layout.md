# Tvoríme osobné stránky - rozloženie aplikácie

V tejto kapitole sa zameriame na spôsoby, ako môžeme zabezpečiť rozmiestnenie jednotlivých komponent na stránke. Pripomeňme si 2 aplikácie, ktoré sme už videli:

 ![enter image description here](https://i.ibb.co/b1yT3DR/funkcna-aplikacia.gif)
 ![enter image description here](https://i.ibb.co/NKSYxZv/pp-multipage-first.gif)

Na oboch aplikáciách je vidieť, že prednastavené správanie jednotlivých komponent je, že sa snažia zabrať celú šírku svojho nadradeného kontajneru (parent container), ktorú majú k dispozícii. 

### Rozloženie využitím `dmc.Grid` a `dmc.Col`

Na vytvorenie správneho rozloženia komponent sa v DMC používajú komponenty zo sekcie layout. Základnou dvojicou sú `dmc.Grid` a `dmc.Col`. Myšlienka je jednoduchá: `dmc.Grid` tvorí jeden riadok nášho kontajneru, ktorý je rozdelený na 12 stĺpcov. Do tohto riadku umiestnime ľubovoľný počet  komponent `dmc.Col` u ktorých pomocou parametru `span` nastavíme rozpätie, cez koľko stĺpcov sa má `dmc.Col` rozprestierať. Tieto komponenty je možné do seba vnárať (tzn. `dmc.Col` je možné zase pomocou kombinácie `dmc.Grid` a `dmc.Col` rozdeliť na ďalšie časti) a tak vytvárať sofistikované rozloženia.

Demonštrujme si to na jednoduchom príklade - vytvoríme si rozloženie na stránke 'O mne'. Najprv si na stránke vytvoríme `dmc.Col` s rozpätím 6 stĺpcov uprostred obrazovky a následne tento stĺpec rozdelíme na časti s rozpätím 8 a 4 stĺpce. Jeden bude obsahovať text s našou charakteristikou a druhý našu profilovú fotku.

`o-mne.py`:

```python
from dash import register_page  
import dash_mantine_components as dmc  
  
register_page(__name__)  
  
o_mne_text = (  
    "Prvý kontakt s programovaním som mal ešte v študentských časoch a odvtedy sa moje kódovanie stalo "  
 "súčasťou môjho DNA. Mojím poslaním je prinášať riešenia prostredníctvom kódu, ktoré majú skutočný dopad na svet. " "Vo svojej voľnej chvíli ma nájdete buď hlboko ponoreného do nového projektu alebo hľadajúceho inšpiráciu v " "prírode a technológii.")  
  
layout = dmc.Grid(  
    [  
        dmc.Col(  
            [  
                dmc.Grid(  
                    [  
                        dmc.Col([dmc.Text(o_mne_text, align="justify")], span=8),  
                        dmc.Col(  
                            [dmc.Image(src="/assets/profile_picture_pingu.svg")], span=4  
  ),  
                    ]  
                )  
            ],  
            span=6,  
            offset=3,  
        )  
    ]  
)
```

- U `dmc.Col` je okrem parametru `span` možné definovať parameter `offset` ktorým vytvoríme odsadenie o daný počet stĺpcov.
- U `dmc.Text` sme pomocou `align="justify"` text rozdelili rovnomerne medzi okrajmi.
- V parametri `src` v `dmc.Image` definujeme cestu k obrázku. Priečinok assets zvyčajne slúži práve na ukladanie takéhoto obsahu, preto sme obrázok uložili v ňom. 

![enter image description here](https://i.ibb.co/cCBBKR1/pp-layout-grid.png)

### Ďalšie možnosti

S ďalšou možnosťou na usporiadanie komponent sme sa stretli už v predchádzajúcej kapitole. Pomocou `dmc.Group` je možné usporiadať viacero komponent vedľa seba. Rovnaké rozloženie je možné vytvoriť aj pomocou `dmc.Grid` a `dmc.Col` s parametrom `span="content"` (prispôsobí šírku šírke obsahu kontajneru) avšak využitím `dmc.Group` získame jednoduchší zápis. Pokiaľ by sme radi usporiadali viacero komponent vertikálne môžeme využiť `dmc.Stack`.

Poďme rozšíriť stránku O mne o sekciu s technickými a mäkkými schopnosťami. Aby sme naše predstavenie a schopnosti oddelili vytvoríme navyše nadpisy jednotlivých sekcií.

`o-mne.py`:
```python
from dash import register_page  
import dash_mantine_components as dmc  
  
register_page(__name__)  
  
o_mne_text = (  
    "Prvý kontakt s programovaním som mal ešte v študentských časoch a odvtedy sa moje kódovanie stalo "  
 "súčasťou môjho DNA. Mojím poslaním je prinášať riešenia prostredníctvom kódu, ktoré majú skutočný dopad na svet. " "Vo svojej voľnej chvíli ma nájdete buď hlboko ponoreného do nového projektu alebo hľadajúceho inšpiráciu v " "prírode a technológii.")  
  
tech_schopnosti = [  
    "Python",  
    "Django",  
    "Pandas",  
    "Matplotlib",  
    "Scikit-learn",  
]  
  
makke_schopnosti = [  
    "Silné analytické a riešiteľské schopnosti",  
    "Komunikačné zručnosti",  
    "Schopnosť efektívne pracovať v tíme",  
    "Flexibilita a schopnosť rýchlo sa prispôsobiť novým technológiám",  
    "Kreativita pri hľadaní neštandardných riešení",  
]  
  
layout = dmc.Grid(  
    [  
        dmc.Col(  
            [  
                dmc.Stack(  
                    [  
                        dmc.Divider(  
                            label="Kto som?",  
                            size="sm",  
                            styles={"label": {"font-size": "25px", "font-weight": 600}},  
                        ),  
                        dmc.Grid(  
                            [  
                                dmc.Col(  
                                    [dmc.Text(o_mne_text, align="justify")], span=8  
  ),  
                                dmc.Col(  
                                    [  
                                        dmc.Image(  
                                            src="/assets/profile_picture_pingu.svg"  
  )  
                                    ],  
                                    span=4,  
                                ),  
                            ],  
                            m=0,  
                        ),  
                        dmc.Divider(  
                            label="Moje schopnosti",  
                            size="sm",  
                            styles={"label": {"font-size": "25px", "font-weight": 600}},  
                        ),  
                        dmc.Grid(  
                            [  
                                dmc.Col(  
                                    [  
                                        dmc.Text("Technické schopnosti:"),  
                                        dmc.List(  
                                            [  
                                                dmc.ListItem(item)  
                                                for item in tech_schopnosti  
                                            ]  
                                        ),  
                                    ],  
                                    span=6,  
                                ),  
                                dmc.Col(  
                                    [  
                                        dmc.Text("Mäkké schopnosti:"),  
                                        dmc.List(  
                                            [  
                                                dmc.ListItem(item)  
                                                for item in makke_schopnosti  
                                            ]  
                                        ),  
                                    ],  
                                    span=6,  
                                ),  
                            ],  
                            m=0,  
                        ),  
                    ],  
                    spacing=10,  
                )  
            ],  
            span=6,  
            offset=3,  
        ),  
    ]  
)
```

- U `dmc.Stack` sme nastavili parameter `sapcing=10` - to znamená, že medzi jednotlivými komponentami bude medzera 10 pixelov
- Na nadpis sekcie sme využili `dmc.Divider`. Parameter `size="sm"` určuje šírku čiary, v parametri `styles` sme zväčšili veľkosť a tučnosť písma (o zmenách vzhľadu komponent využitím `styles` si povieme viac v ďalšej kapitole). 
- `dmc.List` obsahuje zoznam `dmc.ListItem` do ktorého sme zapísali text jednotlivých odrážok

### Padding, Margin a CSS box model

Na záver tejto kapitoly si ešte uvedieme možnosť, ako vytvárať jemné medzery medzi jednotlivými komponentami. Všetky komponenty ktoré budete používať sa nachádzajú v boxoch a my môžeme týmto boxom nastaviť rôzne druhy odsadenia.

![enter image description here](https://i.ibb.co/f8jtqNX/Boxmodell-detail.png)

Odsadenie na vonkajšej strane sa nazýva margin, na vnútornej je to padding. U všetkých Dash komponent môžeme padding a margin nastaviť pomocou parametru `style`. U Dash Mantine Components môžeme využiť skrátený zápis:

- `m`/`p` - nastaví margin/padding zo všetkých 4 strán
- `mt`/`pt`, `mr`/`pr`, `mb`/`pb`, `ml`/`pl` - top, right, bottom, left
- `mx`/`px` - nastaví right a left margin/padding
- `my`/`py` - nastaví top a bottom margin/padding

Spravíme teda ešte malú úpravu našej stránky "O mne". Celý `Stack` na vrchu odsadíme o 20 pixelov. Jednotlivé nápisy "Technické schopnosti" a "Mäkké schopnosti" odsadíme od zoznamov o 5 pixelov. 

```python
from dash import register_page  
import dash_mantine_components as dmc  
  
register_page(__name__)  
  
o_mne_text = (  
    "Prvý kontakt s programovaním som mal ešte v študentských časoch a odvtedy sa moje kódovanie stalo "  
 "súčasťou môjho DNA. Mojím poslaním je prinášať riešenia prostredníctvom kódu, ktoré majú skutočný dopad na svet. " "Vo svojej voľnej chvíli ma nájdete buď hlboko ponoreného do nového projektu alebo hľadajúceho inšpiráciu v " "prírode a technológii.")  
  
tech_schopnosti = [  
    "Python",  
    "Django",  
    "Pandas",  
    "Matplotlib",  
    "Scikit-learn",  
]  
  
makke_schopnosti = [  
    "Silné analytické a riešiteľské schopnosti",  
    "Komunikačné zručnosti",  
    "Schopnosť efektívne pracovať v tíme",  
    "Flexibilita a schopnosť rýchlo sa prispôsobiť novým technológiám",  
    "Kreativita pri hľadaní neštandardných riešení",  
]  
  
layout = dmc.Grid(  
    [  
        dmc.Col(  
            [  
                dmc.Stack(  
                    [  
                        dmc.Divider(  
                            label="Kto som?",  
                            size="sm",  
                            styles={"label": {"font-size": "25px", "font-weight": 600}},  
                        ),  
                        dmc.Grid(  
                            [  
                                dmc.Col(  
                                    [dmc.Text(o_mne_text, align="justify")], span=8  
  ),  
                                dmc.Col(  
                                    [  
                                        dmc.Image(  
                                            src="/assets/profile_picture_pingu.svg"  
  )  
                                    ],  
                                    span=4,  
                                ),  
                            ],  
                            m=0,  
                        ),  
                        dmc.Divider(  
                            label="Moje schopnosti",  
                            size="sm",  
                            styles={"label": {"font-size": "25px", "font-weight": 600}},  
                        ),  
                        dmc.Grid(  
                            [  
                                dmc.Col(  
                                    [  
                                        dmc.Text("Technické schopnosti:", mb=5),  
                                        dmc.List(  
                                            [  
                                                dmc.ListItem(item)  
                                                for item in tech_schopnosti  
                                            ]  
                                        ),  
                                    ],  
                                    span=6,  
                                ),  
                                dmc.Col(  
                                    [  
                                        dmc.Text("Mäkké schopnosti:", mb=5),  
                                        dmc.List(  
                                            [  
                                                dmc.ListItem(item)  
                                                for item in makke_schopnosti  
                                            ]  
                                        ),  
                                    ],  
                                    span=6,  
                                ),  
                            ],  
                            m=0,  
                        ),  
                    ],  
                    spacing=10,  
                    mt=20,  
                )  
            ],  
            span=6,  
            offset=3,  
        ),  
    ]  
)
```

Upozorníme ešte na nastavenie `m=0` u komponenty `dmc.Grid`. Väčšina komponent prichádza s prednastavenými rôznymi veľkosťami odsadenia. Pokiaľ chceme tieto prednastavené hodnoty zmeniť, je nutná odsadenie nastaviť manuálne. Pokiaľ by ste chceli jednotlivé odsadenia a celkové rozloženie stránky preskúmať podrobnejšie, odporúčame využiť vývojárskú konzolu dostupnú po stlačení klávesy <kbd>F12</kbd> v prehliadači. 

![enter image description here](https://i.ibb.co/SVwpRGV/pp-layout-finished.png)
