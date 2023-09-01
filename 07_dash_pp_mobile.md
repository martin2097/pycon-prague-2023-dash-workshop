# Tvoríme osobné stránky - mobilná verzia

Naša stránka vyzerá byť plne funkčná! Predstavte si, že si túto stránku pridáte do profilu napríklad na LinkedIne. HRista s Iphonom SE narazí na váš profil: Osobné stránky, skvelé! Čo ale uvidí až stránku otvorí? Našťastie to môžeme jednoducho zistiť: Po otvorení vývojárskej konzole  pomocou <kbd>F12</kbd> môžeme túto situáciu nasimulovať.

![enter image description here](https://i.ibb.co/S5DVNjW/pp-mobile-broken.gif)

Hm, ale veď to nie je ta moderná a elegantná stránka, ktorú sme tu celý čas konštruovali! Bohužiaľ, väčšina sofistikovaných rozložení, ktoré vyzerajú na veľkom a širokom monitore skvelo sa na úzkom displeji mobilu rozbijú do prekrývajúcich sa zúžených rozložení, ktoré vyzerajú nie len nevzhľadne ale hlavne nečitateľne. 

Takýmto webom, ktoré vyzerajú dobre na všetkých (alebo aspoň väčšine) veľkostiach obrazovky sa hovorí responzívne - dokážu reagovať na to, na akom veľkom displeji sú zobrazované. Našťastie knižnica Mantine na túto problematiku myslela.

### Responzívny dizajn v `DMC`

Pri skúmaní nášho kódu sme identifikovali problém: keďže sme chceli, aby náš obsah bol v strede obrazovky vytvorili sme na každej podstránke `dmc.Col` s parametrami `span=6` a `offset=3`. K tomuto rozdeleniu ale dochádza aj na menších typoch obrazoviek. Riešením teda je aby sa tieto parametre aplikovali až od určitej veľkosti obrazovky.

Pri čítaní [dokumentácie](https://www.dash-mantine-components.com/components/grid) ku komponente `dmc.Grid` sme mohli naraziť na parametre `xs`, `sm`, `md`, `lg`, `xl` a  `offsetXs`, `offsetSm`, `offsetMd`, `offsetLg`, `offsetXl`. Čo tieto parametre robia si demonštrujeme na nasledujúcom kóde:

```python
dmc.Grid(  
    dmc.Col(  
	    [...],
        span=10,  
        xl=6,  
        offset=1,  
        offsetXl=3,  
    ),  
)
```

Tento stĺpec teda bude mať šírku 10, avšak od šírky obrazovky `xl` bude mať už iba šírku 6. Týmto veľkostiam sa vraví [breakpoints](https://mantine.dev/styles/responsive/). A je to presne to čo sme potrebovali. Vytvorme si v našom projekte nový skript `utils.py` a vytvorme v ňom nasledujúcu funkciu:

```python
def responzivny_stlpec_uprostred(obsah):  
    return (  
        dmc.Grid(  
            dmc.Col(  
                obsah,  
                span=10,  
                sm=8,  
                xl=6,  
                offset=1,  
                offsetSm=2,  
                offsetXl=3,  
                p=0,  
            ),  
            m=0,  
        ),  
    )
```

Touto funkciou teraz nahradíme `dmc.Grid` a `dmc.Col` na všetkých stránkach. Keďže sme si na to pripravili novú funkciu, pokiaľ sa v budúcnosti rozhodneme toto rozloženie upraviť ešte inak, bude stačiť túto zmenu vykonať iba na funkcii `responzivny_stlpec_uprostred`.

`domov.py`:

```python
from dash import register_page  
import dash_mantine_components as dmc  
from utils import responzivny_stlpec_uprostred  
  
register_page(__name__, path="/")  
  
  
def gradient_text(text, **kwargs):  
    return dmc.Text(  
        text,  
        variant="gradient",  
        gradient={"from": "blue", "to": "teal", "deg": 45},  
        **kwargs  
    )  
  
  
layout = responzivny_stlpec_uprostred(  
    dmc.Center(  
        dmc.Stack(  
            [  
                dmc.Text("Vitajte na mojich osobných stránkach!"),  
                gradient_text("Som Levo,", size=40, weight=600),  
                dmc.Text(  
                    [  
                        "váš cestovateľ digitálnym priestorom. S piatimi rokmi skúseností ako vášnivý ",  
                        gradient_text(  
                            "PYTHON",  
                            size=20,  
                            weight=600,  
                            span=True,  
                        ),  
                        " programátor, sa pohybujem medzi kódovaním a tvorbou so ",  
                        gradient_text(  
                            "ZÁPALOM",  
                            size=20,  
                            weight=600,  
                            span=True,  
                        ),  
                        " a nekonečnou ",  
                        gradient_text(  
                            "KREATIVITOU",  
                            size=20,  
                            weight=600,  
                            span=True,  
                        ),  
                        ".",  
                    ],  
                ),  
            ],  
            spacing=0,  
            id="home-stack",  
        ),  
        style={"height": "calc(100vh - 48px)"},  
    )  
)
```

`o-mne.py`:

```python
from dash import register_page  
import dash_mantine_components as dmc  
from utils import responzivny_stlpec_uprostred  
  
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
  
layout = responzivny_stlpec_uprostred(  
    dmc.Stack(  
        [  
            dmc.Divider(  
                label="Kto som?",  
                size="sm",  
                styles={"label": {"font-size": "25px", "font-weight": 600}},  
            ),  
            dmc.Grid(  
                [  
                    dmc.Col([dmc.Text(o_mne_text, align="justify")], span=8),  
                    dmc.Col(  
                        [dmc.Image(src="/assets/profile_picture_pingu.svg")],  
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
                            dmc.List([dmc.ListItem(item) for item in tech_schopnosti]),  
                        ],  
                        span=6,  
                    ),  
                    dmc.Col(  
                        [  
                            dmc.Text("Mäkké schopnosti:", mb=5),  
                            dmc.List([dmc.ListItem(item) for item in makke_schopnosti]),  
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
)
```python

```

`skusenosti.py`:

```python
from dash import register_page  
import dash_mantine_components as dmc  
from utils import responzivny_stlpec_uprostred  
  
register_page(__name__)  
  
pracovne_skusenosti = [  
    {  
        "id": "code_tech_solutions",  
        "logo": "/assets/codetech-solutions-logo.png",  
        "nazov": "CodeTech Solutions - Django Vývojár",  
        "popis": "Júl 2021 - súčasnosť",  
        "obsah": [  
            "Vývoj a údržba komplexných webových aplikácií v rámci Django frameworku.",  
            "Implementácia backendových funkcií a optimalizácia výkonu.",  
            "Spolupráca s frontendovým tímom na dosiahnutí plnej funkcionality a vizuálnej súladnosti.",  
        ],  
    },  
    {  
        "id": "datawave_analytics",  
        "logo": "/assets/datawave-analytics-logo.png",  
        "nazov": "DataWave Analytics - Datový Analytik",  
        "popis": "Február 2019 - Jún 2021",  
        "obsah": [  
            "Vytvorenie automatizovaného systému spracovania a analýzy dát pre klientov.",  
            "Implementácia algoritmov na identifikáciu vzorov a odchýlok v dátach.",  
            "Spolupráca s tímom na zdokonaľovaní metód analytického modelovania.",  
        ],  
    },  
    {  
        "id": "bytecraft_technologies",  
        "logo": "/assets/bytecraft-technologies-logo.png",  
        "nazov": "ByteCraft Technologies - API vývojár",  
        "popis": "August 2016 - Január 2019",  
        "obsah": [  
            "Vývoj backendu pre softvérové riešenia v oblasti riadenia procesov.",  
            "Návrh a implementácia REST API pre komunikáciu medzi systémami.",  
            "Úzka spolupráca s tímom pre zabezpečenie interoperability a bezchybného behu systému.",  
        ],  
    },  
]  
  
vzdelanie = [  
    {  
        "id": "magister",  
        "logo": "/assets/tu-vyvojoville-logo.png",  
        "nazov": "Technická Univerzita Vývojoville - Softvérové Inžinierstvo",  
        "popis": "2015 - 2017 - Magisterské štúdium",  
        "obsah": [  
            "Hlbšie štúdium softvérového inžinierstva vrátane agilných metodológií vývoja, softvérového návrhu a "  
 "testovania.",  
            "Diplomová práca sa zaoberala optimalizáciou algoritmov pre analýzu dát a ich implementáciou v prostredí "  
 "Python.",  
        ],  
    },  
    {  
        "id": "bakalar",  
        "logo": "/assets/univerzita-kodujov-logo.png",  
        "nazov": "Univerzita Kódujov - Informatika",  
        "popis": "2011 - 2015 - Bakalárske štúdium",  
        "obsah": [  
            "Zameranie na teoretické základy programovania, algoritmov a dátových štruktúr.",  
            "Praktické skúsenosti získané v rámci projektov využívajúcich jazyk Python a C++",  
        ],  
    },  
]  
  
  
def vytvor_nazov_akordeonu(nazov, logo, popis):  
    return dmc.AccordionControl(  
        dmc.Grid(  
            [  
                dmc.Col(  
                    [  
                        dmc.Avatar(src=logo, radius="xl", size="lg"),  
                    ],  
                    span="content",  
                ),  
                dmc.Col(  
                    [  
                        dmc.Text(nazov),  
                        dmc.Text(popis, size="sm", weight=400, color="dimmed"),  
                    ],  
                    span="auto",  
                ),  
            ]  
        )  
    )  
  
  
def vytvor_obsah_akordeonu(list_s_obsahom):  
    return dmc.AccordionPanel(  
        dmc.List(  
            [  
                dmc.ListItem(dmc.Text([dmc.Text(obsah, size="sm")]))  
                for obsah in list_s_obsahom  
            ],  
            pl=10,  
            pr=30,  
            pb=10,  
        )  
    )  
  
  
def vytvor_akordeon(data_pre_akordeon):  
    return [  
        dmc.AccordionItem(  
            [  
                vytvor_nazov_akordeonu(  
                    jedna_polozka["nazov"],  
                    jedna_polozka["logo"],  
                    jedna_polozka["popis"],  
                ),  
                vytvor_obsah_akordeonu(jedna_polozka["obsah"]),  
            ],  
            value=jedna_polozka["id"],  
        )  
        for jedna_polozka in data_pre_akordeon  
    ]  
  
  
layout = responzivny_stlpec_uprostred(  
    dmc.Accordion(  
        chevronPosition="left",  
        variant="filled",  
        radius="lg",  
        children=[  
            dmc.Divider(  
                label="Pracovné skúsenosti",  
                size="sm",  
                styles={"label": {"font-size": "25px", "font-weight": 600}},  
                mt=20,  
                mb=10,  
            ),  
        ]  
        + vytvor_akordeon(pracovne_skusenosti)  
        + [  
            dmc.Divider(  
                label="Vzdelanie",  
                size="sm",  
                styles={"label": {"font-size": "25px", "font-weight": 600}},  
                mt=20,  
                mb=10,  
            ),  
        ]  
        + vytvor_akordeon(vzdelanie),  
    )  
)
```

`kontakty.py`:

```python
from dash import register_page, html  
import dash_mantine_components as dmc  
from dash_iconify import DashIconify  
from utils import responzivny_stlpec_uprostred  
  
register_page(__name__)  
  
  
def ikona_s_odkazom(odkaz, ikona):  
    return html.A(  
        dmc.ActionIcon(  
            DashIconify(  
                icon=ikona,  
                width=30,  
            ),  
            size="lg",  
            variant="transparent",  
        ),  
        href=odkaz,  
        target="_blank",  
    )  
  
  
layout = responzivny_stlpec_uprostred(  
    dmc.Center(  
        dmc.Stack(  
            [  
                dmc.Text("Kontaktujte Ma", size=40),  
                dmc.Space(h=40),  
                dmc.Text(  
                    "Pokiaľ máte akékoľvek otázky, nápady alebo spätnú väzbu, neváhajte ma kontaktovať.",  
                ),  
                dmc.Space(h=20),  
                dmc.Stack(  
                    [  
                        dmc.Group(  
                            [  
                                ikona_s_odkazom(  
                                    "mailto:levo@fake.mail",  
                                    "material-symbols:mail",  
                                ),  
                                dmc.Text("levo@fake.mail"),  
                            ],  
                        ),  
                        dmc.Group(  
                            [  
                                ikona_s_odkazom(  
                                    "https://linkedin.com/in/levo",  
                                    "mdi:linkedin",  
                                ),  
                                dmc.Text("/in/levo/"),  
                            ],  
                        ),  
                    ],  
                    spacing=0,  
                ),  
            ],  
            spacing=0,  
            align="center",  
            id="contact-stack",  
        ),  
        style={"height": "calc(100vh - 48px)"},  
    )  
)
```

Po spustení aplikácie získame nasledujúci výsledok:

![enter image description here](https://i.ibb.co/Wgt7RQm/pp-mobile-better.gif)

O mnoho lepšie! Všimnime si ešte podstránku "O mne" na ktorej sme obrazovku ešte naďalej rozdeľovali. Spravíme teda ešte menšie úpravy, aby bol text a obrázok vedľa seba iba na väčších obrazovkách, rovnako technické a mäkké schopnosti.

`o-mne.py`

```python
from dash import register_page  
import dash_mantine_components as dmc  
from utils import responzivny_stlpec_uprostred  
  
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
  
layout = responzivny_stlpec_uprostred(  
    dmc.Stack(  
        [  
            dmc.Divider(  
                label="Kto som?",  
                size="sm",  
                styles={"label": {"font-size": "25px", "font-weight": 600}},  
            ),  
            dmc.Grid(  
                [  
                    dmc.Col([dmc.Text(o_mne_text, align="justify")], sm=8),  
                    dmc.Col(  
                        [dmc.Image(src="/assets/profile_picture_pingu.svg")],  
                        sm=4,  
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
                            dmc.List([dmc.ListItem(item) for item in tech_schopnosti]),  
                        ],  
                        sm=6,  
                    ),  
                    dmc.Col(  
                        [  
                            dmc.Text("Mäkké schopnosti:", mb=5),  
                            dmc.List([dmc.ListItem(item) for item in makke_schopnosti]),  
                        ],  
                        sm=6,  
                    ),  
                ],  
                m=0,  
            ),  
        ],  
        spacing=10,  
        mt=20,  
    )  
)
```

### Responzívny navigačný panel

Obsah sa nám síce podarilo upraviť, avšak navigačný panel ostáva stále rozbitý. Pri prehliadaní iných webov si môžeme všimnúť, že pre mobilné zariadenia väčšina webov volí miesto klasického navigačného panelu na vrchu stránky iba zobrazenie tlačidla menu, ktoré z boku vysunie vertikálne menu. Vďaka komponentám `dmc.MediaQuery` a `dmc.Drawer` je možné zostrojiť aj takéto rozloženie.

Dospejeme k tomu v 3 krokoch. Ešte predtým si ale pre prehľadnosť vytvoríme v skripte `utils.py` funkciu `navigacny_panel(odkazy, logo)` do ktorej vložíme aktuálny navigačný panel z layoutu v skripte `app.py` a miesto toho danú funkciu zavoláme.

`utils.py` teda bude obsahovať:

```python
import dash_mantine_components as dmc  
from dash import dcc, html  
from dash_iconify import DashIconify  
  
  
def responzivny_stlpec_uprostred(obsah):  
    return (  
        dmc.Grid(  
            dmc.Col(  
                obsah,  
                span=10,  
                sm=8,  
                xl=6,  
                offset=1,  
                offsetSm=2,  
                offsetXl=3,  
                p=0,  
            ),  
            m=0,  
        ),  
    )  
  
  
def navigacny_panel(odkazy, logo):  
    return dmc.Header(  
        children=[  
            dmc.Group(  
                [  
                    dcc.Link(  
                        dmc.ActionIcon(  
                            DashIconify(  
                                icon=logo,  
                                height=35,  
                                width=35,  
                            ),  
                            variant="transparent",  
                        ),  
                        href="/",  
                    ),  
                    dmc.Group(  
                        [  
                            dmc.NavLink(  
                                label=odkazy[link]["label"],  
                                href=link,  
                                style={  
                                    "padding": "7px",  
                                    "width": "auto",  
                                },  
                                styles={  
                                    "label": {  
                                        "color": "#868E96",  
                                        "font-weight": "500",  
                                        "font-size": "16px",  
                                    },  
                                },  
                            )  
                            for link in odkazy  
                        ],  
                        position="right",  
                    ),  
                ],  
                position="apart",  
                style={"margin-right": "2vh", "margin-left": "2vh"},  
            )  
        ],  
        height=40,  
        fixed=True,  
        style={"margin-bottom": "3px"},  
        withBorder=False,  
    )
```

A `app.py` bude vyzerať takto:

```python
from dash import Dash, html, page_container  
from utils import navigacny_panel  
  
app = Dash(__name__, use_pages=True)  
  
links = {  
    "o-mne": {"label": "O mne"},  
    "skusenosti": {"label": "Skúsenosti"},  
    "projekty": {"label": "Projekty"},  
    "kontakty": {"label": "Kontaktuj ma"},  
}  
  
  
app.layout = html.Div(  
    [  
        navigacny_panel(links, "tabler:square-rounded-letter-l"),  
        html.Div(page_container, style={"margin-top": "40px"}),  
    ]  
)  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

V ďalších príkladoch už budeme pracovať iba so samotnou funkciou `navigacny_panel`. V prvom kroku by sme chceli, aby sa pre stránky menšie ako `sm` všetky odkazy na navigačnom panely okrem loga schovali. Na to nám poslúži komponenta `dmc.MediaQuery`.

```python
def navigacny_panel(odkazy, logo):  
    return dmc.Header(  
        children=[  
            dmc.Group(  
                [  
                    dcc.Link(  
                        dmc.ActionIcon(  
                            DashIconify(  
                                icon=logo,  
                                height=35,  
                                width=35,  
                            ),  
                            variant="transparent",  
                        ),  
                        href="/",  
                    ),  
                    dmc.Group(  
                        [  
                            dmc.MediaQuery(  
                                dmc.NavLink(  
                                    label=odkazy[link]["label"],  
                                    href=link,  
                                    style={  
                                        "padding": "7px",  
                                        "width": "auto",  
                                    },  
                                    styles={  
                                        "label": {  
                                            "color": "#868E96",  
                                            "font-weight": "500",  
                                            "font-size": "16px",  
                                        },  
                                    },  
                                ),  
                                smallerThan="sm",  
                                styles={"display": "none"},  
                            )  
                            for link in odkazy  
                        ],  
                        position="right",  
                    ),  
                ],  
                position="apart",  
                style={"margin-right": "2vh", "margin-left": "2vh"},  
            )  
        ],  
        height=40,  
        fixed=True,  
        style={"margin-bottom": "3px"},  
        withBorder=False,  
    )
```

Úspech! `dmc.MediaQuery` vlastné robí to, že pokiaľ je splnené pravidlo, v našom prípade `smallerThan="sm"`, aplikuje na svoju podradenú komponentu štylizáciu, ktorú určíme v parametri styles. Nami aplikovaná CSS vlastnosť `display: "none"` spôsobí, že sa daná komponenta nezobrazí.

> Dodajme ešte že pravidlo `smallerThan` funguje ako ostrá nerovnosť (<) a pravidlo `largerThan` funguje ako neostrá nerovnosť (>=)

V druhom kroku na konci tohto navigačného panelu zobrazíme tlačidlo, ktoré bude slúžiť na otvorenie vysúvacieho menu.

```python
def navigacny_panel(odkazy, logo):  
    return dmc.Header(  
        children=[  
            dmc.Group(  
                [  
                    dcc.Link(  
                        dmc.ActionIcon(  
                            DashIconify(  
                                icon=logo,  
                                height=35,  
                                width=35,  
                            ),  
                            variant="transparent",  
                        ),  
                        href="/",  
                    ),  
                    dmc.Group(  
                        [  
                            dmc.MediaQuery(  
                                dmc.NavLink(  
                                    label=odkazy[link]["label"],  
                                    href=link,  
                                    style={  
                                        "padding": "7px",  
                                        "width": "auto",  
                                    },  
                                    styles={  
                                        "label": {  
                                            "color": "#868E96",  
                                            "font-weight": "500",  
                                            "font-size": "16px",  
                                        },  
                                    },  
                                ),  
                                smallerThan="sm",  
                                styles={"display": "none"},  
                            )  
                            for link in odkazy  
                        ]  
                        + [  
                            dmc.MediaQuery(  
                                dmc.ActionIcon(  
                                    DashIconify(  
                                        icon="radix-icons:hamburger-menu",  
                                        width=25,  
                                    ),  
                                    variant="transparent",  
                                    id="tlacidlo-menu",  
                                ),  
                                largerThan="sm",  
                                styles={"display": "none"},  
                            ),  
                        ],  
                        position="right",  
                    ),  
                ],  
                position="apart",  
                style={"margin-right": "2vh", "margin-left": "2vh"},  
            )  
        ],  
        height=40,  
        fixed=True,  
        style={"margin-bottom": "3px"},  
        withBorder=False,  
    )
```

V poslednom treťom kroku vytvoríme bočný panel, ktorý bude obsahovať naše odkazy a pridáme tlačidlu pre otvorenie menu interaktivitu: po stlačení otvor bočný panel. 

Finálna verzia funkcie `navigacny_panel` bude teda vyzerať takto:

```python
def navigacny_panel(odkazy, logo):  
    return dmc.Header(  
        children=[  
            dmc.Stack(  
                dmc.Group(  
                    [  
                        dcc.Link(  
                            dmc.ActionIcon(  
                                DashIconify(  
                                    icon=logo,  
                                    height=35,  
                                    width=35,  
                                ),  
                                variant="transparent",  
                            ),  
                            href="/",  
                        ),  
                        dmc.Group(  
                            [  
                                dmc.MediaQuery(  
                                    dmc.NavLink(  
                                        label=odkazy[link]["label"],  
                                        href=link,  
                                        style={  
                                            "padding": "7px",  
                                            "width": "auto",  
                                        },  
                                        styles={  
                                            "label": {  
                                                "color": "#868E96",  
                                                "font-weight": "500",  
                                                "font-size": "16px",  
                                            },  
                                        },  
                                    ),  
                                    smallerThan="sm",  
                                    styles={"display": "none"},  
                                )  
                                for link in odkazy  
                            ]  
                            + [  
                                dmc.MediaQuery(  
                                    dmc.ActionIcon(  
                                        DashIconify(  
                                            icon="radix-icons:hamburger-menu",  
                                            width=25,  
                                        ),  
                                        variant="transparent",  
                                        id="tlacidlo-menu",  
                                    ),  
                                    largerThan="sm",  
                                    styles={"display": "none"},  
                                ),  
                            ],  
                            position="right",  
                        ),  
                    ],  
                    position="apart",  
                    style={"margin-right": "2vh", "margin-left": "2vh"},  
                ),  
                justify="center",  
                style={"height": "100%"},  
            ),  
            dmc.Drawer(  
                id="vysuvacie-menu",  
                overlayOpacity=0.55,  
                overlayBlur=3,  
                size=300,  
                children=dmc.Stack(  
                    [  
                        html.A(  
                            dmc.NavLink(  
                                label=odkazy[link]["label"],  
                                href=link,  
                                style={  
                                    "padding": "7px",  
                                    "width": "auto",  
                                },  
                                styles={  
                                    "label": {  
                                        "color": "#868E96",  
                                        "font-weight": "500",  
                                        "font-size": "24px",  
                                    },  
                                },  
                            ),  
                        )  
                        for link in odkazy  
                    ],  
                    align="center",  
                    spacing=5,  
                ),  
            ),  
        ],  
        height=40,  
        fixed=True,  
        style={"margin-bottom": "3px"},  
        withBorder=False,  
    )
```

A do `app.py` pridáme `callback`, ktorý bude ovládať interaktivitu:

```python
from dash import Dash, html, page_container, callback, Output, Input, State  
from utils import navigacny_panel  
  
app = Dash(__name__, use_pages=True)  
  
links = {  
    "o-mne": {"label": "O mne"},  
    "skusenosti": {"label": "Skúsenosti"},  
    "projekty": {"label": "Projekty"},  
    "kontakty": {"label": "Kontaktuj ma"},  
}  
  
  
app.layout = html.Div(  
    [  
        navigacny_panel(links, "tabler:square-rounded-letter-l"),  
        html.Div(page_container, style={"margin-top": "40px"}),  
    ]  
)  
  
  
@callback(  
    Output("vysuvacie-menu", "opened"),  
    Input("tlacidlo-menu", "n_clicks"),  
    State("vysuvacie-menu", "opened"),  
    prevent_initial_call=True,  
)  
def otvor_menu(n_clicks, opened):  
    return not opened  
  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

A teraz niekoľko poznámok:

- Kde umiestniť `dmc.Drawer` do layoutu je úplne jedno, keďže tým ale tvoríme menu tak sme komponentu umiestnili do našej funkcie. Keďže sa ale jedná o komponentu ktorá leží "mimo" aplikácie a prichádza vždy na fixné miesto tak umiestnenie samotného kódu nie je podstatné. 
- Parametre `overlayOpacity=0.55` a `overlayBlur=3` určujú, ako bude vyzerať zvyšok stránky mimo vysúvacie menu - v tomto prípade ho zosvetlia a rozostria
- Všimnime si, že celý obsah sme ešte zabalili do `dmc.Stack`, ktorej sme nastavili výšku na celú výšku Headeru a obsah vycentrovali. Skúste si aplikáciu spustiť bez tejto zmeny!

Naša finálna aplikácia na mobilných zariadeniach teda vyzerá takto:

![enter image description here](https://i.ibb.co/Y8rjTYH/pp-mobile-final.gif)

Dodajme ešte, že v aplikácii predsa len ostal jeden detail na vylepšenie a to je nutnosť zatvárať vysúvacie menu po kliknutí na akýkoľvek odkaz. Nešlo by vytvoriť `callback` ktorý by toto menu zatvoril po kliknutí na akýkoľvek odkaz z menu? Odpoveď na túto otázku nájdete v najsledujúcej kapitole!
