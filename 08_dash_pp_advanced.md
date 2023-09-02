# Tvoríme osobné stránky - pokročilé možnosti knižnice `Dash`

V predchádzajúcich kapitolách sme sa zoznámili so základnými funkciami, ktoré prináša Dash a do hĺbky sme preskúmali komponenty z knižnice Dash Mantine Components. V tejto kapitole sa pozrieme na niekoľko pokročilejších funkcií, ktoré knižnica Dash ponúka.

### Svetlý a tmavý režim

Vráťme sa ale ešte k poslednej funkcii, ktorú sme pre našu stránku sľubovali. Knižnica `DMC` sľubuje jednoduchú podporu pre prepínanie svetlého a tmavého režimu. O túto podporu by sa mala starať komponenta `dmc.MantineProvider`, ktorou obalíme celú aplikáciu. Okrem toho pridáme na navigačný panel tlačidlo na prepínanie témy a jeden callback, ktoré sa o toto prepnutie postará.

Funkciu `navigacny_panel` v skripte `utils.py` upravíme nasledovne:

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
                                dmc.ActionIcon(  
                                    DashIconify(  
                                        icon="radix-icons:blending-mode",  
                                        width=25,  
                                    ),  
                                    variant="transparent",  
                                    id="tlacidlo-zmena-temy",  
                                ),  
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

A `app.py` upravíme:

```python
from dash import Dash, html, page_container, callback, Output, Input, State  
from utils import navigacny_panel  
import dash_mantine_components as dmc  
  
app = Dash(__name__, use_pages=True)  
  
links = {  
    "o-mne": {"label": "O mne"},  
    "skusenosti": {"label": "Skúsenosti"},  
    "projekty": {"label": "Projekty"},  
    "kontakty": {"label": "Kontaktuj ma"},  
}  
  
  
app.layout = dmc.MantineProvider(  
    [  
        navigacny_panel(links, "tabler:square-rounded-letter-l"),  
        html.Div(page_container, style={"margin-top": "40px"}),  
    ],  
    theme={"colorScheme": "dark"},  
    withGlobalStyles=True,  
    id="provider-temy",  
)  
  
  
@callback(  
    Output("vysuvacie-menu", "opened"),  
    Input("tlacidlo-menu", "n_clicks"),  
    State("vysuvacie-menu", "opened"),  
    prevent_initial_call=True,  
)  
def otvor_menu(n_clicks, opened):  
    return not opened  
  
  
@callback(  
    Output("provider-temy", "theme"),  
    Input("tlacidlo-zmena-temy", "n_clicks"),  
    State("provider-temy", "theme"),  
    prevent_initial_call=True,  
)  
def zmen_temu(n_clicks, tema):  
    if tema["colorScheme"] == "dark":  
        return {"colorScheme": "light"}  
    else:  
        return {"colorScheme": "dark"}  
  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

![enter image description here](https://i.ibb.co/TL4DhkZ/pp-advanced-dark-mode.gif)

- Celá aplikácia by mala obsahovať iba jeden `MantineProvider`. Typicky sa ním obalí celá aplikácia tak ako v našom prípade.
- `dmc.MantineProvider` zadáme iba 2 parametre: `withGlobalStyles=True` nastaví predvolené štýly a `theme={"colorScheme":  "dark"}` nastaví tmavú tému ako predvolenú. Pokiaľ by ste chceli použiť ako predvolenú svetlú tému, zmeňte `"dark"` na `"light"`.
- Callback `zmen_temu` sa pozrie do parametru `theme` a podľa hodnoty `"colorScheme"` zmení buď tmavú tému na svetlú alebo opačne.

### Uchovávanie užívateľských rozhodnutí

Aplikácie v Dashi, rovnako ako väčšina stránok na internete, pri zatvorení a opätovnom otvorení "zabudnú", aké voľby ste predtým na stránke spravili. V našej aplikácii si to môžete všimnúť ak zmeníte tému z predvoleného nastavenia, následne stránku zavriete a znovu otvoríte - stránka sa načíta znova s prednastavenou témou. Výhodou tohto prístupu je, že tieto dáta nemusíte nikde ukladať a teda viete vašu aplikáciu distribuovať pre obrovské množstvo užívateľov. 

Čo ak by sme ale chceli, aby si stránka pamätala akú tému si náš užívateľ zvolil? Predsa len, keď už raz túto voľbu vykoná, nechce aby sa mu stránka pri každej stránke otvorila v režime, ktorý nepreferuje. Na pomoc s týmto problémom sa nám bude hodiť medzipamäť (cache) v prehliadači. V Dashi existujú 2 spôsoby ako niektoré stavy zachovať:

- [Persistence](https://dash.plotly.com/persistence) - u niektorých komponent nájdeme parameter `persistence`. Po jeho aktivácii sa stav danej komponenty bude automaticky ukladať do medzipamäte a pri zatvorení a otvorení stránky (to, kedy má dôjsť k "zabudnutiu" daného stavu je možné nastaviť) ostane tento stav nezmenený.
- [`dcc.Store`](https://dash.plotly.com/dash-core-components/store) - ako názov napovedá, jedná sa o "sklad" do ktorého si môžeme niečo uložiť. Pozor ale na to, že ako veľkosť medzipamäte sa zvyčajne uvádza 5-10MB - nie je to teda určené na uchovávanie veľkých dát, chápme to ako priestor v ktorom vieme ukladať napríklad rôzne nastavenia.

Poďme teda našu aplikáciu vylepšiť. Pridáme do aplikácie `dcc.Store` v ktorom budeme uchovávať tému, vybranú užívateľom, `light` alebo `dark`. Callback `zmen_temu` rozdelíme na dva: jeden, ktorý pri stlačení tlačidla zmení tému a uloží ju do `dcc.Store` a druhý, ktorý pri zmene obsahu `dcc.Store` danú zmenu prevedie aj v `dmc.MantineProvider`.

`app.py`:

```python
import dash  
from dash import Dash, html, dcc, page_container, callback, Output, Input, State  
from utils import navigacny_panel  
import dash_mantine_components as dmc  
  
app = Dash(__name__, use_pages=True)  
  
links = {  
    "o-mne": {"label": "O mne"},  
    "skusenosti": {"label": "Skúsenosti"},  
    "projekty": {"label": "Projekty"},  
    "kontakty": {"label": "Kontaktuj ma"},  
}  
  
  
app.layout = dmc.MantineProvider(  
    [  
        dcc.Store(id="ulozisko-temy", storage_type="local"),  
        navigacny_panel(links, "tabler:square-rounded-letter-l"),  
        html.Div(page_container, style={"margin-top": "40px"}),  
    ],  
    theme={"colorScheme": "dark"},  
    withGlobalStyles=True,  
    id="provider-temy",  
)  
  
  
@callback(  
    Output("vysuvacie-menu", "opened"),  
    Input("tlacidlo-menu", "n_clicks"),  
    State("vysuvacie-menu", "opened"),  
    prevent_initial_call=True,  
)  
def otvor_menu(n_clicks, opened):  
    return not opened  
  
  
@callback(  
    Output("ulozisko-temy", "data"),  
    Input("tlacidlo-zmena-temy", "n_clicks"),  
    State("ulozisko-temy", "data"),  
)  
def zmen_temu(n_clicks, data):  
    if data:  
        if n_clicks:  
            if data["colorScheme"] == "dark":  
                return {"colorScheme": "light"}  
            else:  
                return {"colorScheme": "dark"}  
        else:  
            return dash.no_update  
    else:  
        return {"colorScheme": "dark"}  
  
  
@callback(  
    Output("provider-temy", "theme"),  
    Input("ulozisko-temy", "data"),  
)  
def aktivuj_temu(data):  
    return data  
  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

- `storage_type="local"` znamená, že `dcc.Store` sa nevyprázdni ani po zavretí a znovuotvorení prehliadača. Ďalšími možnosťami sú `memory` (k vyprázdneniu dôjde po obnovení stránky) a `session` (k vyprázdneniu dôjde po reštarte prehliadača).
- V callbacku `zmen_temu` sme sériou podmienok ošetrili niekoľko inicializačných podmienok. Pokiaľ je `dcc.Store` prázdny, vložíme do neho tmavú tému (náš default) a pokiaľ prázdny nie je ani nebolo stlačené tlačidlo na zmenu témy tak nevykonáme žiadnu zmenu.
- `dash.no_update` slúži pre prípad, že sa v priebehu callbacku rozhodneme, že nechceme vykonať žiadnu zmenu - napríklad v prípade, že nebola splnená istá podmienka.

### Callbacky spúšťané priamo v prehliadači

Callbacky s ktorými sme sa zatiaľ stretli, vytvorené pomocou dekorátoru `@callback` fungujú tak, že pokiaľ dôjde k spusteniu pomocou komponenty definovanej v `Input`, posielajú sa všetky vstupy na server. Ten následne vykoná výpočet definovaný v danom callbacku a odošle výstupy do komponent definovaných v `Output`. Pokiaľ ale nepotrebujeme robiť žiadne veľké výpočty, ale napríklad iba zmeniť jednoduchý parameter, jedná sa o zbytočne odosielanie dát na server a zo serveru. Presne pre tento účel obsahuje dash `clientside_callback`. Jedná sa o callback ktorý je vyhodnotený priamo v prehliadači a nedochádza k zbytočným presunom dát a vyťažovaniu serveru. Prepíšme naše callbacky `otvor_menu`, `zmen_temu` a `aktivuj_temu` na `clientside_callback.`.

`app.py`:

```python
from dash import (  
    Dash,  
    html,  
    dcc,  
    page_container,  
    Output,  
    Input,  
    State,  
    clientside_callback,  
)  
from utils import navigacny_panel  
import dash_mantine_components as dmc  
  
app = Dash(__name__, use_pages=True)  
  
links = {  
    "o-mne": {"label": "O mne"},  
    "skusenosti": {"label": "Skúsenosti"},  
    "projekty": {"label": "Projekty"},  
    "kontakty": {"label": "Kontaktuj ma"},  
}  
  
  
app.layout = dmc.MantineProvider(  
    [  
        dcc.Store(id="ulozisko-temy", storage_type="local"),  
        navigacny_panel(links, "tabler:square-rounded-letter-l"),  
        html.Div(page_container, style={"margin-top": "40px"}),  
    ],  
    theme={"colorScheme": "dark"},  
    withGlobalStyles=True,  
    id="provider-temy",  
)  
  
  
clientside_callback(  
    """function(n_clicks, opened) { return !opened }""",  
    Output("vysuvacie-menu", "opened"),  
    Input("tlacidlo-menu", "n_clicks"),  
    State("vysuvacie-menu", "opened"),  
    prevent_initial_call=True,  
)  
  
  
clientside_callback(  
    """function(n_clicks, data) {  
 if (data) { if (n_clicks) { const scheme = data["colorScheme"] == "dark" ? "light" : "dark" return { colorScheme: scheme }            }  
 return dash_clientside.no_update } else { return { colorScheme: "dark" } } }""",  
    Output("ulozisko-temy", "data"),  
    Input("tlacidlo-zmena-temy", "n_clicks"),  
    State("ulozisko-temy", "data"),  
)  
  
  
clientside_callback(  
    """ function(data) { return data } """,  
    Output("provider-temy", "theme"),  
    Input("ulozisko-temy", "data"),  
)  
  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

Ako si môžete všimnúť, `clientside_callback` používa JavaScript syntax. Nemusíte sa ale báť, pre takto jednoduché operácie si vystačíte s úplnými základmi (sám JavaScript vôbec neovládam :)).

### Pattern matching callbacks

Keď som sa pred niekoľkými rokmi snažil priniesť Dash ku nám do banky, skutočným zlomovým bodom bolo pridanie Patter matching callbackov. Typicky pri definícií callbacku zadávame nejaké konkrétne `id` komponenty. Čo ak by sme chceli, aby náš callback reagoval napríklad na všetky komponenty určitého typu? Poďme sa pozrieť na konkrétny príklad.

Pri tvorbe vysúvacieho menu v minulej kapitole sme si všimli, že po kliknutí na položku v tomto menu nedôjde k zasunutie daného menu. Mohlo by nás teda napadnúť vytvoriť callback, ktorý sa aktivuje pri kliknutí na ktorýkoľvek odkaz v menu a parametru `opened` komponenty `dmc.Drawer` pošle `false`. Na vytvorenie tohto callbacku by sme ale potrebovali `Input` pre každý odkaz. Čo keď sa ale rozhodneme počet odkazov rozšíriť? Znamenalo by to v každom takomto prípade pridávať ďalšie vstupy. To nie je optimálne. A presne toto je ideálna situácia pre Pattern matching callback. Poďme sa teda pozrieť, ako by takýto kód vyzeral. Najprv si budeme musieť pridať identifikátory `id` jednotlivým odkazom vo vysúvacom menu a následne pridáme callback do `app.py`.

Funkciu `navigacny_panel` v `utils.py` upravíme nasledovne:

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
                                            id={"type": "odkaz-menu", "index": "logo"},  
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
                                dmc.ActionIcon(  
                                    DashIconify(  
                                        icon="radix-icons:blending-mode",  
                                        width=25,  
                                    ),  
                                    variant="transparent",  
                                    id="tlacidlo-zmena-temy",  
                                ),  
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
                                    id={  
                                        "type": "odkaz-menu",  
                                        "index": link,  
                                    },  
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

A do `app.py` pridáme nasledujúci callback:

```python
@callback(  
    Output("vysuvacie-menu", "opened", allow_duplicate=True),  
    Input({"index": ALL, "type": "odkaz-menu"}, "n_clicks"),  
    prevent_initial_call=True,  
)  
def zavri_menu(n_clicks):  
    return False
```

Alebo miesto callbacku môžeme pridať clientside callback:

```python
clientside_callback(  
    """  
 function (i) { return false } """,  
    Output("vysuvacie-menu", "opened", allow_duplicate=True),  
    Input({"index": ALL, "type": "odkaz-menu"}, "n_clicks"),  
    prevent_initial_call=True,  
)
```

![enter image description here](https://i.ibb.co/Nj008Yq/pp-advanced-hide-menu.gif)

- Všimnime si, že identifikátory `id`, ktoré sme pridali odkazom v navigačnom menu nie sú stringy ale dictionaries s dvoma dvojicami: `"type":  "odkaz-menu"` a `"index": link`.
- Pridali sme aj identifikátor `id` pre odkaz, ktorý sa skrýva v logu našej stránke - pokiaľ užívateľ otvorí vysúvacie menu ale rozhodne sa kliknúť na logo, menu sa tiež zasunie.
- Keďže sa v našom callbacku jedná už o druhý callback s `Output("vysuvacie-menu",  "opened")` musíme pridať parameter `allow_duplicate=True`
- Všimnime si, že v callbacku `zavri_menu` sme v `Input({"index": ALL,  "type":  "odkaz-menu"},  "n_clicks")` miesto konkrétneho `id` využili štruktúru identifikátorov, ktoré sme pridávali. Tento callback sa teda spustí pri aktivácii akejkoľvek komponenty, ktorej identifikátor obsahuje `"type":  "odkaz-menu"`. Tejto štruktúre identifikátorov a využitia `ALL` (prípadne `MATCH`) sa hovorí Pattern matching.

A máme hotov! Naša aplikácia je pripravená na zverejnenie.
