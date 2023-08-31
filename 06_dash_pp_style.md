# Tvoríme osobné stránky - poďme dať aplikácií štýl

V predchádzajúcich kapitolách sme sa už stretli s mnohými spôsobmi, ako komponenty štylizovať alebo upraviť ich správanie a vlastnosti. V tejto kapitole si tieto spôsoby priblížime a zároveň vytvoríme ďalšie podstránky pre našu osobnú stránku.

### Zmena parametrov

Naše pracovné skúsenosti a vzdelanie by sme radi prezentovali v kompaktnej a prehľadnej forme, zároveň by sme ale mali mať možnosť zobraziť viac detailov pre prípad, že by sa návštevník našej stránky chcel dozvedieť viac. Pre takýto účel bude skvelá komponenta `Accordion` z knižnice `dmc`. 

> Keďže pracovných skúseností a položiek vzdelania môže byť rôzne množstvo, tieto dáta si pripravíme v štandardnej forme. Vytvorili sme si list `pracovne_skusenosti` (a v ďalšom príklade rovnaký list vzdelanie) do ktorého sme vložili dictionaries (slovníky) s nasledujúcimi položkami:
> - `id` - technický identifikátor danej skúsenosti
> - `logo` - adresu na súbor assets a názov obrázku s logom
> - `nazov` - názov ktorý sa objaví v prvom riadku
> - `popis` - popis ktorý sa objaví v druhom riadku
> - `obsah` - list s detailnými položkami, ktoré chceme zobraziť


`skusenosti.py`:
```python
from dash import register_page  
import dash_mantine_components as dmc  
  
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
  
  
layout = dmc.Grid(  
    dmc.Col(  
        dmc.Accordion(  
            chevronPosition="left",  
            variant="filled",  
            radius="lg",  
            children=[  
                dmc.AccordionItem(  
                    [  
                        dmc.AccordionControl(dmc.Text(jedna_polozka["nazov"])),  
                        dmc.AccordionPanel(  
                            dmc.List(  
                                [  
                                    dmc.ListItem(dmc.Text([dmc.Text(obsah, size="sm")]))  
                                    for obsah in jedna_polozka["obsah"]  
                                ]  
                            )  
                        ),  
                    ],  
                    value=jedna_polozka["id"],  
                )  
                for jedna_polozka in pracovne_skusenosti  
            ],  
        ),  
        span=6,  
        offset=3,  
        p=0,  
        mt=20,  
    ),  
    m=0,  
)
```

Pomocou tohto kódu sme vytvorili nasledujúcu stránku.

![enter image description here](https://i.ibb.co/19NfRf2/pp-style-simple-accordion.gif)

- Prvá vec, ktorá si všimneme je, že komponenta `dmc.Accordion` sa skladá z ďalších komponent. Každá položka akordeónu je komponenta `dmc.AccordionItem` a tá sa následne skladá z komponent `dmc.AccordionControl` (obsah lišty, na ktorú klikám, keď chcem zobraziť podrobnosti) a `dmc.AccordionPanel` (obsah, ktorý sa zobrazí po rozkliknutí danej položky).
- [Dokumentácia](https://www.dash-mantine-components.com/components/accordion) by nám mala dať základný prehľad o parametroch, pomocou ktorých je možné rôzne vlastnosti danej komponenty upraviť. V sekcii *Documentation* nájdeme interaktívne ukážky použitia rôznych parametrov, aby sme mohli získať základnú predstavu. V časti Component Props následne nájdeme úplný výpis všetkých parametrov, ktoré je pri danej komponente možné nastaviť
- V našom prípade sme nastavili:
	-  `chevronPosition="left"` - šípka sa zobrazí na lište vľavo
	- `variant="filled"` - zvolili sme minimalistickú variantu vzhľadu
	- `radius="lg"` - po rozbalení má box zaoblené rohy

Základ teda máme pripravený ale vidíme, že to ešte nie je ono. Poďme si ukázať komplexnejší príklad toho, ako by to mohlo vyzerať a zároveň pridajme názvy jednotlivých sekcií a pridajme časť so vzdelaním.

`skusenosti.py`:
```python
from dash import register_page  
import dash_mantine_components as dmc  
  
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
  
  
layout = dmc.Grid(  
    dmc.Col(  
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
        ),  
        span=6,  
        offset=3,  
        p=0,  
        mt=20,  
    ),  
    m=0,  
)
```

Finálny výsledok teda vyzerá takto:
![enter image description here](https://i.ibb.co/5nB0ZmR/pp-style-accordion-complex.gif)

Keďže sa jedná už o pomerne komplexný kus kódu, rozčlenili sme si ho do niekoľkých jednoduchých funkcií.

- `vytvor_nazov_akordeonu(nazov, logo, popis)`
	- vytvorí `dmc.AccordionControl` ktorý sme pomocou `Grid` a `Col` rozdelili na 2 časti, logo vytvorené pomocou `dmc.Avatar` a v druhej časti dva riadky textu - názov a popis
	- `dmc.Avatar` sme parametrizovali pomocou parametru `radius="xl"`, ktorý vytvorí okrúhlu komponentu a `size="lg"`, ktorý určuje veľkosť 
	- popis vytvorený pomocou `dmc.Text` sme zosvetlili pomocou `color="dimmed"` a zmenšili pomocou `size="sm"`
- `vytvor_obsah_akordeonu(list_s_obsahom)`
	- vytvorí obsah, ktorý sa zobrazí po rozkliknutí danej položke
	- vytvárame zoznam odrážok
	- pripomeňme z predchádzajúcej kapitoly, že parametre `pr`, `pl` a `pb` menia odsadenie danej komponenty
- `vytvor_akordeon(data_pre_akordeon)`
	- vytvorí jednotlivé položky `dmc.AccordionItem` kombináciou predchádzajúcich dvoch funkcií
	- vstupom je list (`pracovne_skusenosti` alebo `vzdelanie`) tak ako sme si ho nadefinovali na začiatku
	- parameter value určuje unikátny identifikátor každej položky
- v samotnom `layout` vytvoríme `dmc.Accordion`. Všimnime si, že do parametru `children` nemusíme pridávať iba komponenty `dmc.AccordionItem`, ale môžeme pridávať akékoľvek iné `dmc` komponenty, v našom prípade `dmc.Divider`. Prečo to robíme tak komplikovane a nevytvorili sme jeden akordeón pre vzdelanie a druhý akordeón pre pracovné skúsenosti? Pretože takto sa otvorená položka zo sekcie vzdelanie zatvorí, aj pokiaľ klikneme na položku zo sekcie pracovné skúsenosti!

### Zmena vzhľadu pomocou parametru `style`

Pomocou parametrov je možné meniť niektoré vlastnosti alebo správanie jednotlivých komponent. Nie na všetko čo by sme chceli zmeniť ale existuje parameter. Preto do hry prichádza parameter `style`! 

 Parameter `style` nám umožňuje na danú komponentu aplikovať akúkoľvek CSS vlastnosť (CSS property). Jedná sa o vlastnosti z prostredia vývoja webov a existuje ich skutočne [mnoho](https://www.w3schools.com/cssref/index.php). S niektorými z nich sme sa už stretli:

- `"font-size": "25px"` - nastaví veľkosť písma
- `"font-weight": 600` - nastaví tučnosť písma
- `"color": "red"` - nastaví farbu písma
- `"width": 200px` - nastaví šírku komponenty
- `"height": 200px` - nastaví výšku komponenty
- `"padding": 10px` - nastaví vnútorné odsadenie
- `"margin": 10px` - nastaví vonkajšie odsadenie

Poďme si teraz pripraviť ďalšiu stránku. Na stránke s kontaktmi by sme radi vycentrovali obsah tejto stránky nie len horizontálne ale aj vertikálne. A práve na to, nám poslúži parameter `style`.

`kontakty.py`
```python
from dash import register_page, html  
import dash_mantine_components as dmc  
from dash_iconify import DashIconify  
  
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
  
  
layout = dmc.Grid(  
    dmc.Col(  
        dmc.Center(  
            dmc.Stack(  
                [  
                    dmc.Text("Kontaktujte Ma", size=50),  
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
            ),  
            style={"height": "calc(100vh - 48px)"},  
        ),  
        span=6,  
        offset=3,  
        p=0,  
    ),  
    m=0,  
)
```

Naše kontaktné informácie sa podarilo úspešne vycentrovať!

![enter image description here](https://i.ibb.co/LgMtqxn/pp-contact-me.png)

- Funkcia `ikona_s_odkazom(odkaz, ikona)` vytvorí tlačidlo v tvare ikony pomocou `dmc.ActionIcon`. Keďže sme toto tlačidlo zabalili do komponenty `html.A`, ktorá slúži na tvorbu webových odkazov, po stlačení tlačidla dôjde k otvoreniu daného odkazu. Parameter `target="_blank"` určuje, že sa daný odkaz otvorí v prehliadači na novej karte.
- `dmc.Space` s parametrom `h=20` vytvorí horizontálnu medzeru s výškou 20 pixelov
- `dmc.Center` vycentruje obsah kontajneru vertikálne aj horizontálne
- Všimnime si parameter `style={"height":  "calc(100vh - 48px)"}`. Skúste si spustiť túto stránku bez tohto parametru. Čo sa zmení? Stránka už nebude vycentrovaná horizontálne. A prečo sa to stalo? V predchádzajúcich kapitolách sme si ukazovali, že jednotlivé komponenty väčšinou zaberajú celú šírku, ktorá je komponente dostupná. Pre výšku to ale neplatí. Komponenty zaberajú buď nejakú prednastavenú výšku alebo výšku potrebnú pre ich obsah. Pomocou `style={"height":  "calc(100vh - 48px)"}` komponente prikážeme, aby zabrala celú výšku stránky.
- Pozrime sa ešte na `calc(100vh - 48px)`. CSS vlastnosti majú mnoho veľkostí, v ktorých môžeme zadať veľkosti:
	- `px` - veľkosť v pixeloch
	- `%` - pomerná časť nadradeného kontajnera (parent container)
	- `vh` - pomerná časť celkovej dostupnej výšky obrazovky(viewport height)
	- `vw` - pomerná časť celkovej dostupnej šírky obrazovky (viewport width)
	- pokiaľ chceme rôzne jednotky skombinovať môžeme využiť výpočet `calc(100vh - 48px)` - zober celú výšku stránky a odpočítaj od nej 48 pixelov (v našom prípade to je výška navigačného panelu)

Podobným spôsobom pripravíme domovskú obrazovku. Keďže sa jedná o stránku, ktorú väčšina návštevníkov uvidí ako prvú, pokúsime sa získať ich pozornosť zvýraznením našich kľúčových kompetencií. 

`domov.py`
```python
from dash import register_page  
import dash_mantine_components as dmc  
  
register_page(__name__, path="/")  
  
  
def gradient_text(text, **kwargs):  
    return dmc.Text(  
        text,  
        variant="gradient",  
        gradient={"from": "blue", "to": "teal", "deg": 45},  
        **kwargs  
    )  
  
  
layout = dmc.Grid(  
    dmc.Col(  
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
            ),  
            style={"height": "calc(100vh - 48px)"},  
        ),  
        span=6,  
        offset=3,  
        p=0,  
    ),  
    m=0,  
)
```

A tu je výsledok!

![enter image description here](https://i.ibb.co/XykPNYz/pp-style-home-page.png)

 - Vďaka [dokumentácii](https://www.dash-mantine-components.com/components/text) sme objavili, že komponentu `dmc.Text` môžeme vytvoriť s týmto krásnym efektom gradientu!
 - Funkciu `gradient_text(text, **kwargs)` sme vytvorili pre prípad, že by sme sa rozhodli napríklad zmeniť farby tohto gradientu. Takto nebudeme musieť meniť farbu pre každý text zvlášť. 
 - Opäť sme pomocou parametru `style` zadali výšku manuálne aby sme mohli stránku vycentrovať aj vertikálne

> `**kwargs` nám umožňuje predať akékoľvek množstvo parametrov (kwarg je skratka pre keyword argument - napríklad `size=20` je keyword argument). Viac informácií nájdete napríklad [tu](https://dlukes.github.io/python-crash-course.html#Pokro%C4%8Dil%C3%A9-zp%C5%AFsoby-p%C5%99ed%C3%A1v%C3%A1n%C3%AD-argument%C5%AF).

