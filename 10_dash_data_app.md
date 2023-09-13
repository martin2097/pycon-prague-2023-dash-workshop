# Tvoríme dátový dashboard

V tomto návode si vytvoríme dátový dashboard v Dashi s využitím ďalších knižníc. Ako vzorku dát ktoré budeme zobrazovať využijeme dáta o [vzdelaní zo sčítania ľudu z roku 2021](https://data.gov.cz/datov%C3%A1-sada?iri=https://data.gov.cz/zdroj/datov%C3%A9-sady/00025593/88ef5cee5d80027d077c8e57a46fdf35). 

### Príprava dát

Pre účely tohto návodu sme dáta upravili a [z tohto odkazu](https://github.com/martin2097/pycon-prague-2023-personal-page/blob/525377b6a10e1d4e6ca6c017d1245a908f1471a3/sldb2021_vzdelani_vek2_pohlavi_iba_kraje.csv) si ich uložíme do našej projektovej zložky. Na prácu s dátami budeme využívať knižnicu pandas. Keďže účelom tohto návodu je venovať sa najmä tvorbe dashboardu, pripravili sme funkciu `priprava_dat()`, ktorá slúži na načítanie a prípravu dát. Túto funkciu si pridáme do skriptu `utils.py`:

```python
import pandas

def priprava_dat():  
    df = pd.read_csv("sldb2021_vzdelani_vek2_pohlavi_iba_kraje.csv")  
  
    vzdelanie_mapovanie = {  
        "Bez vzdělání": "Iné",  
        "Nezjištěno": "Iné",  
        "Neúplné základní vzdělání": "Základné",  
        "Nižší střední a střední vzdělání": "SŠ (bez maturity)",  
        "Nástavbové vzdělání": "Stredoškolské",  
        "Pomaturitní studium": "Stredoškolské",  
        "Vysokoškolské bakalářské vzdělání": "Vysokoškolské",  
        "Vysokoškolské doktorské vzdělání": "Vysokoškolské",  
        "Vysokoškolské magisterské vzdělání": "Vysokoškolské",  
        "Vyšší odborné vzdělání": "Stredoškolské",  
        "Vyšší odborné vzdělání v konzervatoři": "Stredoškolské",  
        "Základní vzdělání": "Základné",  
        "Úplné střední odborné vzdělání": "Stredoškolské",  
        "Úplné střední všeobecné vzdělání": "Stredoškolské",  
    }  
  
    df.replace({"vzdelani_txt": vzdelanie_mapovanie}, inplace=True)  
    return df
```

> - Funkcia `read_csv` slúži na načítanie csv súboru do DataFrame
> - pomocou metódy `replace` sme následne premapovali kategórie vzdelania aby sme zmenšili ich počet

Vytvorme si skript `analyza-scitanie.py` v zložke pages s nasledujúcim obsahom:

```python
from dash import register_page  
from utils import priprava_dat  
import dash_mantine_components as dmc  
  
register_page(__name__)  
  
df = priprava_dat()  
print(df[["uzemi_txt", "vzdelani_txt", "pohlavi_txt", "hodnota"]])  
  
layout = dmc.Text("Ja budem analyza scitania")
```

Pomocou funkcie `priprava_dat()` sme teda načítali náš DataFrame (jedná sa o špeciálny formát v ktorom pandas ukladá dátové sady) a následne sme nahliadli na to, ako tieto dáta vyzerajú. Okrem toho sme stránku `analyza-scitanie` pridali na našu stránku - na tejto stránke budeme následne tvoriť náš dashboard. Vráťme sa ale k tomu, ako vyzerajú naše dáta.

![enter image description here](https://i.ibb.co/ynFy3d1/dataframe-nahlad.png)

Vybrali sme si iba stĺpce, ktoré následne využijeme. Stĺpec `uzemi_txt` obsahuje názvy jednotlivých krajov alebo celé Česko. Stĺpec `vzdelani_txt` obsahuje úroveň vzdelania a stĺpec `pohlavi_txt` obsahuje pohlavie. Posledný stĺpec `hodnota` obsahuje počet ľudí, ktorý sa nachádzajú v danej kategórii. 

# Vytvorenie grafu s prehľadom vzdelania

Poďme si načrtnúť prvú verziu dashboardu. Keďže nechceme, aby sa nám jeden človek nachádzal v dashboarde viacero krát, budeme si vždy chcieť vybrať, za ktorú oblasť dané dáta zobrazujeme. Následne zobrazíme stĺpcový graf so zastúpením obyvateľov v jednotlivých kategóriách vzdelania.

`analyza-scitanie.py`:
```python
from dash import register_page, dcc, callback, Output, Input  
from utils import priprava_dat  
import dash_mantine_components as dmc  
from plotly.express import bar  
  
register_page(__name__)  
  
df = priprava_dat()  
  
layout = dmc.Stack(  
    [  
        dmc.Select(  
            id="vyber-uzemi",  
            value="Česká republika",  
            label="Vyberte oblasť:",  
            data=[  
                {"value": moznost, "label": moznost}  
                for moznost in df["uzemi_txt"].drop_duplicates().sort_values()  
            ],  
        ),  
        dcc.Graph(id="graf-vzdelanie"),  
    ]  
)  
  
  
@callback(  
    Output("graf-vzdelanie", "figure"),  
    Input("vyber-uzemi", "value"),  
)  
def data_do_grafu(uzemie):  
    w_df = df.copy()  
    w_df = w_df[w_df["uzemi_txt"] == uzemie]  
    w_df = w_df.groupby(by=["vzdelani_txt"])["hodnota"].sum().reset_index()  
  
    fig = bar(  
        w_df,  
        x="vzdelani_txt",  
        y="hodnota",  
    )  
  
    return fig
```

- V komponente `dmc.Select` sme určili identifikátor, parameter `value` určuje hodnotu, ktorá bude pred vybraná pri načítaní stránky, parameter `label` pridá popisku a nakoniec parameter `data` určuje hodnoty, ktoré sa v rozbaľovacej ponuke zobrazia.
- Všimnime si, že parameter `data` očakáva list zložený z dictionaries, ktoré obsahujú dve páry: `value` - hodnotu, ktorú daný `Select` vráti po výbere a `label` - popisok pod ktorým sa daná hodnota zobrazí v dashboarde. V našom prípade sa teda jedná o všetky unikátne hodnoty zo stĺpca vzdelanie.
- Komponenta `dcc.Graph` slúži na pridanie grafu do layoutu, jediným parametrom, ktorý sme určili je identifikátor tejto komponenty
- Callback `data_do_grafu` sa spustí pri zmene parametru `value` komponenty `dmc.Select` tzn. vždy keď z rozbaľovacej ponuky vyberieme hodnotu. Výstup budeme posielať do komponenty `dcc.Graph` kde sa zmení parameter `figure`. `figure` je objekt v ktorom knižnica Plotly uchováva predpis pre daný Graf (v doslovnom preklade by sa dalo povedať, že sa jedná o samotný "obrázok" grafu).
- V našom prípade si ako `figure` vytvoríme stĺpcový graf pomocou funkcie `bar()` z knižnice `plotly.express`. Táto funkcie na vstupe očakáva dáta vo forme DataFrame, stĺpec ktorý chceme zobraziť na ose x a stĺpec ktorý zobrazíme na ose y. V našom prípade teda chceme rozvrhnúť počet obyvateľov podľa vzdelania. 

![enter image description here](https://i.ibb.co/9bb5D2B/scitanie-prvy-dashboard.gif)

Keďže pandas nie je hlavným cieľom tohto návodu, syntax z pandasu, ktorú sme použili, uvádzame ako doplnok:

> - `df["uzemi_txt"].drop_duplicates().sort_values()` - najprv si z DataFrame `df` vyberieme stĺpec so vzdelaním `"uzemi_txt"`. Pomocou metódy `drop_duplicates()` odstránime všetky duplicitné hodnoty - ostanú nám teda iba všetky rôzne hodnoty z deného stĺpca. Nakoniec pomocou `sort_values()` hodnoty kvôli prehľadnosti zoradíme podľa abecedy. Podotknime ešte, že tejto syntaxy sa hovorí reťazenie metód.
> - Keďže budeme DataFrame ďalej upravovať, musíme si vytvoriť jeho kópiu: `w_df = df.copy()`
> - Zafiltrujeme si hodnotu, ktorú užívateľ vybral v komponente `dmc.Select`: `w_df[w_df["uzemi_txt"] == uzemie]`
> - Keďže jedna hodnota sa môže vyskytovať na viacerých riadkoch (napríklad muž/žena) použijeme funkciu `groupby`: `w_df.groupby(by=["vzdelani_txt"])["hodnota"].sum())` (pre toho kto pozná kontingenčky v Exceli je tento zápis ekvivalentný výberu `vzdelani_txt` ako Rows a `hodnota` ako Values s aplikovaním funkcie súčtu).
> - Funkcia `groupby` vytvorí index obsahujúci `vzdelani_txt`, funkcia `reset_index()` tento index zmení na klasický stĺpec.

# Pridanie kariet s prehľadmi

Dashboardy často obsahujú karty na ktorých sa zobrazujú tie najdôležitejšie čísla. Do nášho dashboardu si pridáme karty dve. Na jednej z nich zobrazíme celkový počet obyvateľov v danej oblasti a na druhej zobrazíme podiel vysokoškolsky vzdelaných obyvateľov v danej oblasti.

Keďže karty budú dve ale chceme aby vyzerali rovnako, pripravíme si funkciu `karta_statistiky(id, popis)` ktorá sa bude skladať z dvoch riadkov textu, v jednom sa bude nachádzať popis (z parametru `popis`) a druhý riadok s identifikátorom z parametru `id`, ktorý budeme plniť callbackom. Skript `analyza-scitanie.py` bude teda vyzerať nasledovne:

```python
from dash import register_page, dcc, callback, Output, Input  
from utils import priprava_dat  
import dash_mantine_components as dmc  
from plotly.express import bar  
  
register_page(__name__)  
  
df = priprava_dat()  
  
  
def karta_statistiky(id, popis):  
    return dmc.Card(  
        dmc.Stack(  
            [dmc.Text(popis, weight=600, size=15), dmc.Text(id=id)],  
            align="center",  
            spacing="xs",  
        ),  
        withBorder=True,  
        shadow="sm",  
        radius="lg",  
        p=8,  
    )  
  
  
layout = dmc.Stack(  
    [  
        dmc.Select(  
            id="vyber-uzemi",  
            value="Česká republika",  
            label="Vyberte oblasť:",  
            data=[  
                {"value": moznost, "label": moznost}  
                for moznost in df["uzemi_txt"].drop_duplicates().sort_values()  
            ],  
        ),  
        dmc.Grid(  
            [  
                dmc.Col(  
                    karta_statistiky(  
                        id="pocet-obyvatel", popis="Celkový počet obyvateľov"  
  ),  
                    span=6,  
                ),  
                dmc.Col(  
                    karta_statistiky(  
                        id="podiel-vs", popis="Podiel vysokoškolsky vzdelaných"  
  ),  
                    span=6,  
                ),  
            ]  
        ),  
        dcc.Graph(id="graf-vzdelanie"),  
    ]  
)  
  
  
@callback(  
    Output("graf-vzdelanie", "figure"),  
    Input("vyber-uzemi", "value"),  
)  
def data_do_grafu(uzemie):  
    w_df = df.copy()  
    w_df = w_df[w_df["uzemi_txt"] == uzemie]  
    w_df = w_df.groupby(by=["vzdelani_txt"])["hodnota"].sum().reset_index()  
  
    fig = bar(  
        w_df,  
        x="vzdelani_txt",  
        y="hodnota",  
    )  
  
    return fig  
  
  
@callback(  
    Output("pocet-obyvatel", "children"),  
    Output("podiel-vs", "children"),  
    Input("vyber-uzemi", "value"),  
)  
def plnenie_kariet(uzemie):  
    pocet = df[df["uzemi_txt"] == uzemie]["hodnota"].sum()  
    podiel = (  
        df[(df["uzemi_txt"] == uzemie) & (df["vzdelani_txt"] == "Vysokoškolské")][  
            "hodnota"  
  ].sum()  
        / df[df["uzemi_txt"] == uzemie]["hodnota"].sum()  
    )  
    return "{:,d}".format(pocet), "{:.2%}".format(podiel)
```

- Parametre `dmc.Card`:
	- `withBorder=True` - ohraničenie okolo karty
	- `shadow="sm"` - tieň okolo karty
	- `radius="lg"` - okrúhle rohy
- Callback `plnenie_kariet` sa spustí pri zmene hodnoty v `dmc.Select` a naplnia sa hodnoty počtu obyvateľov a podielu vysokoškolsky vzdelaných obyvateľov
- Formátovanie `"{:,d}".format(pocet)` naformátuje číslo s čiarkami ako oddeľovačov tisícov a `"{:.2%}".format(podiel)` naformátuje číslo ako percentá.

![enter image description here](https://i.ibb.co/7bX46n6/scitanie-pridanie-kariet.gif)

> Poznámky k syntaxy z pandasu:
> - `pocet = df[df["uzemi_txt"] == uzemie]["hodnota"].sum()` - najprv zafiltrujem iba riadky so správnym územím `df[df["uzemi_txt"] == uzemie]`, následne vyberiem iba stĺpec `hodnota` `["hodnota"]` a pomocou `.sum()` získam celkový počet obyvateľov zo všetkých riadkov
> - Veľmi podobne získam aj percentuálny podiel:
>   - Čitateľ: zafiltrujem správne územie: `df["uzemi_txt"] == uzemie` a vzdelanie: `df["vzdelani_txt"] == "Vysokoškolské"` a sčítam počet obyvateľov v týchto kategóriách.
>   - Menovateľ: celkový počet obyvateľov v danej oblasti, takže identicky ako `pocet`

# Pridanie tabuľky s detailným rozlíšením podľa pohlavia

Grafy sú skvelou vizuálnou pomôckou na prezentáciu dát. Pri väčšom počte prezentovaných dát sa však rýchlo stavajú neprehľadnými. Preto nám môže prísť vhod tabuľka. Aj Dash má svoju knižnicu na tvorbu tabuliek: Dash AG Grid. My si jednu takúto tabuľku vytvoríme a zobrazíme v nej porovnanie podielu jednotlivých kategórií vzdelania medzi mužmi a ženami v danej oblasti.

Pridajme si do layoutu prázdny `html.Div`, ktorý následne naplníme tabuľkou v callbacku `obnov_tabulku`.

`analyza-scitania.py`
```python
from dash import register_page, dcc, callback, Output, Input, html  
from utils import priprava_dat  
import dash_mantine_components as dmc  
from plotly.express import bar  
import dash_ag_grid as dag  
import pandas as pd  
import numpy as np  
  
register_page(__name__)  
  
df = priprava_dat()  
  
  
def karta_statistiky(id, popis):  
    return dmc.Card(  
        dmc.Stack(  
            [dmc.Text(popis, weight=600, size=15), dmc.Text(id=id)],  
            align="center",  
            spacing="xs",  
        ),  
        withBorder=True,  
        shadow="sm",  
        radius="lg",  
        p=8,  
    )  
  
  
layout = dmc.Stack(  
    [  
        dmc.Select(  
            id="vyber-uzemi",  
            value="Česká republika",  
            label="Vyberte oblasť:",  
            data=[  
                {"value": moznost, "label": moznost}  
                for moznost in df["uzemi_txt"].drop_duplicates().sort_values()  
            ],  
        ),  
        dmc.Grid(  
            [  
                dmc.Col(  
                    karta_statistiky(  
                        id="pocet-obyvatel", popis="Celkový počet obyvateľov"  
  ),  
                    span=6,  
                ),  
                dmc.Col(  
                    karta_statistiky(  
                        id="podiel-vs", popis="Podiel vysokoškolsky vzdelaných"  
  ),  
                    span=6,  
                ),  
            ]  
        ),  
        dcc.Graph(id="graf-vzdelanie"),  
        html.Div(id="tabulka-vzdelanie-detail"),  
    ]  
)  
  
  
@callback(  
    Output("graf-vzdelanie", "figure"),  
    Input("vyber-uzemi", "value"),  
)  
def data_do_grafu(uzemie):  
    w_df = df.copy()  
    w_df = w_df[w_df["uzemi_txt"] == uzemie]  
    w_df = w_df.groupby(by=["vzdelani_txt"])["hodnota"].sum().reset_index()  
  
    fig = bar(  
        w_df,  
        x="vzdelani_txt",  
        y="hodnota",  
    )  
  
    return fig  
  
  
@callback(  
    Output("pocet-obyvatel", "children"),  
    Output("podiel-vs", "children"),  
    Input("vyber-uzemi", "value"),  
)  
def plnenie_kariet(uzemie):  
    pocet = df[df["uzemi_txt"] == uzemie]["hodnota"].sum()  
    podiel = (  
        df[(df["uzemi_txt"] == uzemie) & (df["vzdelani_txt"] == "Vysokoškolské")][  
            "hodnota"  
  ].sum()  
        / df[df["uzemi_txt"] == uzemie]["hodnota"].sum()  
    )  
    return "{:,d}".format(pocet), "{:.2%}".format(podiel)  
  
  
@callback(  
    Output("tabulka-vzdelanie-detail", "children"),  
    Input("vyber-uzemi", "value"),  
)  
def obnov_tabulku(uzemie):  
    w_df = df.copy()  
    w_df = w_df[w_df["uzemi_txt"] == uzemie]  
  
    w_df = pd.crosstab(  
        index=w_df["vzdelani_txt"],  
        columns=w_df["pohlavi_txt"],  
        values=w_df["hodnota"],  
        aggfunc=np.sum,  
        normalize="columns",  
    ).reset_index()  
  
    columnDefs = [  
        {"headerName": "Vzdelanie", "field": "vzdelani_txt"},  
        {"headerName": "Muži", "field": "muž"},  
        {"headerName": "Ženy", "field": "žena"},  
    ]  
  
    grid = dag.AgGrid(columnDefs=columnDefs, rowData=w_df.to_dict("records"))  
    return grid
```

Ako vidíme, tvorba samotnej tabuľky obsahuje iba pár riadkov:

- `columnDefs` - vytvoríme list ktorý obsahuje slovníky s dvoma dvojicami s kľúčmi: `headerName` - ako chceme aby sa zobrazoval názov daného stĺpcu a `"field"` - názov stĺpcu, ktorý chceme v tabuľke zobraziť, v dátach
- Samotnú tabuľku následne vytvorí funkcia `dag.AgGrid` do ktorej zadáme parameter `columnDefs` a dáta, ktorými sa táto tabuľka naplní: `rowData=w_df.to_dict("records")` kde `w_df` je náš DataFrame, v ktorom máme dáta uložené
- Skúste si jeden z názvov stĺpcov chytiť a prehodiť s iným stĺpcom. Niektoré funkcie AG Grid sme dostali úplne bez práce!

![enter image description here](/gifs/scitanie_hotovy_ugly_dashboard.gif)

V predposlednej kapitole tohto návodu si tento dashboard naštylizujeme, aby sme to mali pekné a nakoniec ho pridáme na stránku Projekty.
