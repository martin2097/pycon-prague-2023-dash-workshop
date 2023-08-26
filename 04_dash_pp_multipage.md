# Tvoríme osobné stránky -viacstránkové aplikácie

Zatiaľ sme sa stretli iba s aplikáciami, ktoré boli celé obsiahnuté na jednej webovej stránke. Z iných stránok sme ale zvyknutý presúvať sa medzi rôznymi stránkami pomocou rôznych odkazov a navigačných komponent. Našou úlohou teda je vytvoriť viacstránkovú aplikáciu (v dokumentácii v sekcii [Multi-Page Apps](https://dash.plotly.com/urls)).

### Vytvorenie viacstránkovej aplikácie

V Dashi existujú na jednoduchú implementáciu viacstránkových aplikácií Dash Pages (doslovne by sme preložili ako 'stránky'). Našu aplikáciu rozdelíme do niekoľkých skriptov. Prvým skriptom bude `app.py`, ktorý slúži ako hlavný script. V novej zložke v našom projekte, ktorú nazveme `pages` vytvoríme skripty pre jednotlivé stránky - `domov.py`, `o-mne.py`, `skusenosti.py`, `projekty.py` a `kontakty.py`.

```
- app.py 
- pages
   |-- domov.py
   |-- o-mne.py
   |-- skusenosti.py
   |-- projekty.py
   |-- kontakty.py
```

Jednotlivé skripty si teraz inicializujeme.
`app.py`:

```python
from dash import Dash, html, page_container  
import dash_mantine_components as dmc  
  
app = Dash(__name__, use_pages=True)  
  
links = {  
    "o-mne": {"label": "O mne"},  
    "skusenosti": {"label": "Skúsenosti"},  
    "projekty": {"label": "Projekty"},  
    "kontakty": {"label": "Kontaktuj ma"},  
}  
  
app.layout = html.Div(  
    [  
        html.Div(  
            [  
                dmc.NavLink(  
                    label=links[link]["label"],  
                    href=link,  
                )  
                for link in links  
            ]  
        ),  
        page_container,  
    ]  
)  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

`domov.py`:

```python
from dash import register_page  
import dash_mantine_components as dmc  
  
register_page(__name__, path="/")  
  
layout = dmc.Text("Ja som stránka domov")
```

`o-mne.py`:

```python
from dash import register_page  
import dash_mantine_components as dmc  
  
register_page(__name__, path="/o-mne")  
  
layout = dmc.Text("Ja som stránka o mne")
```
`skusenosti.py`:

```python
from dash import register_page  
import dash_mantine_components as dmc  
  
register_page(__name__, path="/skusenosti")  
  
layout = dmc.Text("Ja som stránka skusenosti")
```
`projekty.py`:

```python
from dash import register_page  
import dash_mantine_components as dmc  
  
register_page(__name__, path="/projekty")  
  
layout = dmc.Text("Ja som stránka projekty")
```
`kontakty.py`:

```python
from dash import register_page  
import dash_mantine_components as dmc  
  
register_page(__name__, path="/kontakty")  
  
layout = dmc.Text("Ja som stránka kontakty")
```

Pokiaľ chceme spustiť viacstránkovú aplikáciu, vždy ju spúšťame zo skriptu, ktorý obsahuje `app.run`. V našom prípade teda spúšťame zo skriptu `app.py`.

![enter image description here](https://i.ibb.co/NKSYxZv/pp-multipage-first.gif)

Povenujme sa v krátkosti syntaxy, ktorá sa s viacstránkovými aplikáciami spája. 
- V skripte `app.py` sme pridali parameter `use_pages=True` pri inicializácii aplikácie - tým sme aplikácii povedali aby pri inicializácii prešla všetky skripty v priečinku pages. 
- V jednotlivých skriptoch v priečinku pages sme pridali príkaz `register_page(__name__)`, ktorým stránku z daného skriptu registrujeme. Všimnime si, že parameter `path` sme nastavili iba v skripte `domov.py`. V tomto prípade chceme aby sa táto stránka načítala vždy, keď nie je zadaná žiadna cesta. V ostatných prípadoch je cesta identická s názvom skriptu. 
- Posledným krokom je pridanie kontajneru `page_container` do layoutu v `app.py`. V tomto kontajnery sa následne zobrazujú layouty jednotlivých stránok definované v príslušných scriptoch v súbore pages.

>  :bulb: **Tip:** Na vygenerovanie listu s navigačnými odkazmi sme použili syntax ktorá sa nazýva [list comprehension](https://www.lovelydata.cz/blog/python-list-comprehension/). Nasledujúce spôsoby tvorby listov sú ekvivalentné:
>  ```python
>  lst = [1, 2, 3, 4]  
>    
>  lst1 = []  
>  for x in lst:  
>      lst1.append(x**2)  
>    
>  lst2 = [x**2 for x in lst]  
>  ```

### Navigačný panel

Na vytvorenie navigačných odkazov sme využili komponentu [NavLink](https://www.dash-mantine-components.com/components/navlink). Teraz sa ešte zameriame na to, ako tieto odkazy usporiadať do navigačného panelu na vrchu obrazovky tak ako sme na to zvyknutý z iných stránok. 
`app.py`:
```python
from dash import Dash, html, page_container  
import dash_mantine_components as dmc  
  
app = Dash(__name__, use_pages=True)  
  
links = {  
    "o-mne": {"label": "O mne"},  
    "skusenosti": {"label": "Skúsenosti"},  
    "projekty": {"label": "Projekty"},  
    "kontakty": {"label": "Kontaktuj ma"},  
}

app.layout = html.Div(  
    [  
        dmc.Header(  
            children=[  
                dmc.Group(  
                    [  
                        dmc.NavLink(  
                            label=links[link]["label"],  
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
                        for link in links  
                    ],  
                    position="right",    
					style={"margin-right": "2vh"},
                )  
            ],  
            height=40,  
            fixed=True,  
            style={"margin-bottom": "3px"},  
            withBorder=False,  
        ),  
        html.Div(page_container, style={"margin-top": "40px"}),  
    ]  
)  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

Čo sme využili:

 - [dmc.Header](https://www.dash-mantine-components.com/components/header) - ako preklad napovedá jedná sa o záhlavie stránky. Parameter `height` nastavuje výšku záhlavia, `fixed=True` spôsobí, že toto záhlavie ostane pripnuté na vrchu stránky a `withBorder=False` vytvorí záhlavie bez spodného ohraničenia. 
 - [dmc.Group](https://www.dash-mantine-components.com/components/group) - vytvorí skupinu komponentov ku ktorým môžeme následne pristupovať ako ku celku. Parameter `position` následne určuje, v ktorej časti kontajneru bude táto skupina umiestnená.
 - `style={"width":  "auto"}` pre NavLink - parameter `style` umožňuje meniť výzor a vlastnosti danej komponenty pomocou CSS štýlov. V tomto konkrétnom prípade nastavíme šírku jednotlivých odkazov aby sa prispôsobila textu daného odkazu. Predvolené správanie je, že každý odkaz zaberá celú šírku kontajnera (skúste si aplikáciu spustiť bez `"width":  "auto"`).
 - zmenou parametru `styles` sme zmenili farbu, veľkosť a tučnosť písma (budeme sa podrobne venovať neskôr)
 - `padding` a `margin` nastavujú medzery medzi komponentami

Každá správna stránka obsahuje logo. Keďže my žiadne logo nemáme, skúsme pridať ikonu s našou iniciálou. Zároveň chceme, aby nás kliknutie na toto logo vrátilo na domovskú stránku.

```python
from dash import Dash, html, page_container, dcc  
import dash_mantine_components as dmc  
from dash_iconify import DashIconify  
  
app = Dash(__name__, use_pages=True)  
  
links = {  
    "o-mne": {"label": "O mne"},  
    "skusenosti": {"label": "Skúsenosti"},  
    "projekty": {"label": "Projekty"},  
    "kontakty": {"label": "Kontaktuj ma"},  
}  
  
  
app.layout = html.Div(  
    [  
        dmc.Header(  
            children=[  
                dmc.Group(  
                    [  
                        dcc.Link(  
                            dmc.ActionIcon(  
                                DashIconify(  
                                    icon="tabler:square-rounded-letter-l",  
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
                                    label=links[link]["label"],  
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
                                for link in links  
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
        ),  
        html.Div(page_container, style={"margin-top": "40px"}),  
    ]  
)  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

- Všimnite si, že na zarovnanie sme opäť využili komponentu Group, tentokrát však s `position="apart"`.
- Ikonu sme získali z open source katalógu s ikonami [Iconify](https://icon-sets.iconify.design/tabler/square-rounded-letter-l/). [DashIconify](https://www.dash-mantine-components.com/dash-iconify) sme okrem názvu samotnej ikony zadali ešte výšku a šírku.
-  [dmc.ActionIcon](https://www.dash-mantine-components.com/components/actionicon) - vytvorí tlačidlo v podobe ikony. Parameter `variant` nastaví vzhľad tlačidla (ostatné varianty si môžete prehliadnuť v dokumentácii)
- [dcc.Link](https://dash.plotly.com/dash-core-components/link) - z danej komponenty vytvorí odkaz, v našom prípade zadávame ako cieľ odkazu `"/"` teda odkaz na domovskú stránku

A tým je naša aplikácia pripravená na vytvorenie obsahu samotných stránok!

![enter image description here](https://i.ibb.co/Y0n0Sqs/pp-multipage-finished-navbar.gif)
