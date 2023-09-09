# Štylizácia dátového dashboardu

V predchádzajúcich kapitolách sme sa už naučili, že štylizovanie jednotlivých komponent je hlavne o čítaní dokumentácie a doplnení správnych parametrov. Poďme si teda postupne naštylizovať layout, graf v Plotly a nakoniec tabuľku v AG Grid. 

### Štylizácia layoutu

`layout` v `analyza-scitanie.py`:

```python
layout = responzivny_stlpec_uprostred(  
    dmc.Stack(  
        [  
            dmc.Divider(  
                label="Úroveň vzdelania v ČR",  
                size="sm",  
                styles={"label": {"font-size": "25px", "font-weight": 600}},  
                mt=20,  
            ),  
            dmc.Select(  
                id="vyber-uzemi",  
                value="Česká republika",  
                label="Vyberte oblasť:",  
                data=[  
                    {"value": moznost, "label": moznost}  
                    for moznost in df["uzemi_txt"].drop_duplicates().sort_values()  
                ],  
                styles={"label": {"margin-bottom": "7px"}},  
            ),  
            dmc.Grid(  
                [  
                    dmc.Col(  
                        [  
                            karta_statistiky(  
                                id="pocet-obyvatel", popis="Celkový počet obyvateľov"  
  )  
                        ],  
                        sm=6,  
                    ),  
                    dmc.Col(  
                        [  
                            karta_statistiky(  
                                id="podiel-vs", popis="Podiel vysokoškolsky vzdelaných"  
  )  
                        ],  
                        sm=6,  
                    ),  
                ]  
            ),  
            dmc.Divider(  
                label="Prehľad",  
                size="sm",  
                styles={"label": {"font-size": "18px", "font-weight": 600}},  
            ),  
            dmc.Card(  
                dcc.Graph(  
                    id="graf-vzdelanie",  
                    config={  
                        "displayModeBar": False,  
                        "scrollZoom": False,  
                        "doubleClick": False,  
                        "showAxisDragHandles": False,  
                    },  
                ),  
                withBorder=True,  
                shadow="sm",  
                radius="lg",  
                p=30,  
            ),  
            dmc.Divider(  
                label="Porovnanie podľa pohlaví",  
                size="sm",  
                styles={"label": {"font-size": "18px", "font-weight": 600}},  
            ),  
            html.Div(id="tabulka-vzdelanie-detail"),  
        ],  
        spacing="md",  
    )  
)
```

- Pridali sme názvy jednotlivých sekcií pomocou komponenty `dmc.Divider`, ktorú už veľmi dobre poznáme z predchádzajúcich stránok
- U kariet sme zmenili parameter `span=6` na parameter `sm=6` kvôli prispôsobeniu na mobilných zariadeniach
- `dcc.Graf` sme umiestnili do `dmc.Card` aby sme tento graf vizuálne začlenili.
- U samotnej `dcc.Graf` sme pridali parameter `config`, v ktorom sme  vypli niektoré interaktívne funkcie - opäť hlavne kvôli mobilným zariadeniam
- U `dcc.Stack`, ktorý obsahuje celý náš layout sme pridali `spacing` aby celý layout pôsobil vzdušnejšie

![enter image description here](https://i.ibb.co/X5gkzHh/scitanie-styled-layout.gif)

Všimnime si, že v tmavom režime sa nám nemení štylizácia grafu ani tabuľky. Je to spôsobené tým, že ako `dcc.Graph`, tak `dag.AgGrid` nie sú z knižnice `dmc` a teda tu budeme musieť prispôsobenie vytvoriť sami.

### Štylizácia grafu v Plotly

Ako iste tušíte, samotná knižnica Plotly na tvorbu interaktívnych grafov obsahuje obrovské množstvo možností pre nastavenia a štylizáciu jednotlivých grafov. Obsahom tohto návodu nemá byť pokrytie a vysvetlenie všetkých týchto možností, nie je ale nič jednoduchšie ako zobrať nastavenia, ktoré si ukážeme a použiť ich na ďalšie vlastné grafy.

Callback `data_do_grafu` v `analyza-scitanie.py`:

```python
@callback(  
    Output("graf-vzdelanie", "figure"),  
    Input("vyber-uzemi", "value"),  
    Input("ulozisko-temy", "modified_timestamp"),  
    State("ulozisko-temy", "data"),  
)  
def data_do_grafu(uzemie, timestamp, theme):  
    w_df = df.copy()  
    w_df = w_df[w_df["uzemi_txt"] == uzemie]  
    w_df = w_df.groupby(by=["vzdelani_txt"])["hodnota"].sum().reset_index()  
    w_df["podiel"] = w_df["hodnota"] / w_df["hodnota"].sum()  
  
    fig = bar(  
        w_df,  
        x="vzdelani_txt",  
        y="hodnota",  
        custom_data="podiel",  
        category_orders={  
            "vzdelani_txt": [  
                "Základné",  
                "SŠ (bez maturity)",  
                "Stredoškolské",  
                "Vysokoškolské",  
                "Iné",  
            ]  
        },  
    )  
  
    fig.update_traces(  
        marker_color="rgb(207,219,137)",  
        hovertemplate="%{x}<br>%{y:,.0f} obyvateľov<br>%{customdata[0]:.2%} obyvateľstva",  
    )  
  
    fig.update_layout(  
        height=300,  
        yaxis_title=None,  
        xaxis_title=None,  
        dragmode=False,  
        bargap=0.4,  
        yaxis=dict(  
            gridcolor="whitesmoke" if theme["colorScheme"] == "light" else "#484848",  
            gridwidth=0.2,  
            zeroline=False,  
            color="#444" if theme["colorScheme"] == "light" else "#FFFFFF",  
            nticks=5,  
        ),  
        paper_bgcolor="rgba(0, 0, 0, 0)",  
        font=dict(  
            family="Segoe UI",  
            size=14,  
        ),  
        margin=dict(r=0, b=10, t=0, l=0),  
        plot_bgcolor="rgba(0, 0, 0, 0)",  
        xaxis=dict(  
            showgrid=False,  
            zeroline=False,  
            color="#444" if theme["colorScheme"] == "light" else "#FFFFFF",  
        ),  
    )  
    return fig
```

- Všimnite si, že do callbacku sme pridali nový Input a State. Jedná sa o komponentu `dcc.Store` do ktorej ukladáme užívateľom vybranú tému. Ako sme si povšimli na predchádzajúcom príklade, pri zmene tmavého a svetlého režimu budeme musieť rôzne štylizovať aj samotný graf.
- V samotnej funkcii `bar()` sme pridali dva nové parametre: `custom_data` - jedná sa o ďalší stĺpec z DataFrame `w_df`, význam tohto parametru si vysvetlíme za chvíľu. Druhým parametrom je `category_orders` v ktorom môžeme určiť poradie kategórií v jednotlivých stĺpcoch ako sa v grafe budú zobrazovať.
- Podľa toho, akú časť grafu chceme upraviť musíme využiť správnu funkciu. Pomocou `.update_traces` zmeníme farbu jednotlivých stĺpcov (`marker_color`) a text, ktorý sa zobrazí po priložení myši (`hovertemplate`). Všimnime si, že práve tu využívame `customdata` a po priložení myši zobrazujeme nie len celkový počet obyvateľov v kategórii ale aj podiel na celku, o ktorý sa jedná.
- Zvyšné úpravy budeme robiť v nedátových častiach grafu pomocou metódy `.update_layout`
	- Nastavíme výšku grafu (`height`), odstránime názvy osí (`yaxis_title`, `xaxis_title`)
	- Na ose y nastavíme farby pomocných čiar (`gridcolor`), farbu textu `color` a počet bodov na ose y `nticks`
	- Na ose x pomocné čiary schováme (`showgrid`) 
	- Pomocou `paper_bgcolor` a `plot_bgcolor` nastavíme priehľadnú farbu pozadia `"rgba(0, 0, 0, 0)"` aby sa prispôsobila jednotlivým témam
	- Zmeníme font aby korešpondoval s fontom knižnice Mantine
	- Pomocou `margin` nastavíme odsadenie grafu

>  :bulb: **Tip:** Syntax `a if condition else  b` je príkladom jednoriadkového zápisu syntaxe `if`. Funguje ako by ste predpokladali: keď je podmienka `cond` splnená vykoná sa `a` inak sa vykoná `b`

![enter image description here](https://i.ibb.co/6Np97YC/scitanie-styled-figure.gif)

Možností štylizácie a úprav grafov Plotly je obrovské množstvo a vydalo by na sériu samostatných návodov :)

# Štylizácia tabuľky AG Grid

Posledným chýbajúcim prvkom je naštylizovanie tabuľky v AG Grid. Poďme sa na to teda rovno pozrieť.

Callback `obnov_tabulku` v `analyza-scitanie.py`:

```python
@callback(  
    Output("tabulka-vzdelanie-detail", "children"),  
    Input("vyber-uzemi", "value"),  
    Input("ulozisko-temy", "modified_timestamp"),  
    State("ulozisko-temy", "data"),  
)  
def obnov_tabulku(uzemie, timestamp, theme):  
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
        {"headerName": "Vzdelanie", "field": "vzdelani_txt", "minWidth": 150},  
        {  
            "headerName": "Muži",  
            "field": "muž",  
            "valueFormatter": {"function": "d3.format(',.1%')(params.value)"},  
        },  
        {  
            "headerName": "Ženy",  
            "field": "žena",  
            "valueFormatter": {"function": "d3.format(',.1%')(params.value)"},  
        },  
    ]  
    defaultColDef = {  
        "resizable": True,  
        "sortable": True,  
        "editable": False,  
        "filter": False,  
    }  
  
    grid = dag.AgGrid(  
        className="ag-theme-alpine-dark"  
  if theme["colorScheme"] == "dark"  
  else "ag-theme-alpine",  
        columnDefs=columnDefs,  
        rowData=w_df.to_dict("records"),  
        columnSize="sizeToFit",  
        defaultColDef=defaultColDef,  
        dashGridOptions={  
            "suppressCellFocus": True,  
            "enableCellTextSelection": True,  
            "animateRows": True,  
            "domLayout": "autoHeight",  
        },  
    )  
    return grid
```

- Všimnime si, že znovu ako vstup používame úložisko s témou pretože na tabuľku použijeme rôzny štýl v prípade témy dark a iný v prípade témy light
- Slovníky v parametri `columnDefs` prijímajú aj iné páry ako `headerName` a `field`. Príkladom je `"valueFormatter"` ktorý zmení tvar daného čísla. V našom prípade využívame štandardizovaný D3 formát pomocou ktorého zobrazíme čísla v stĺpcoch ako percentá s jedným číslom za desatinnou čiarkou.
- Okrem parametru `columnDefs` v ktorom definujeme jednotlivé stĺpce môžeme využiť aj parameter `defaultColDef` v ktorom definujeme všetky stĺpce. V našom prípade pridávame možnosť zoradiť riadky podľa hodnôt v jednotlivých stĺpcoch a možnosť zmeniť veľkosť jednotlivých stĺpcov.
- Na štylizáciu v AG Grid existujú predpripravené témy, pomocou ktorých môžeme zmeniť vzhľad tabuliek. Tieto témy aplikujeme v parametri `className`. V našom prípade sa jedná o tému `"ag-theme-alpine"` pre svetlú tému a tému `"ag-theme-alpine-dark"` pre tmavú tému. 
- V parametri `dashGridOptions` môžeme pridať ďalšie možnosti:
	- `"suppressCellFocus"` vypne zvýrazňovanie jednotlivých buniek po kliknutí
	- `"enableCellTextSelection"` umožní označiť text v bunke a skopírovať ho
	- `"animateRows"` pridá animácie
	- `"domLayout":  "autoHeight"` nastaví aby sa výška tabuľky prispôsobila počtu riadkov.

![enter image description here](https://i.ibb.co/VYhDyFL/scitanie-styled-table.gif)

