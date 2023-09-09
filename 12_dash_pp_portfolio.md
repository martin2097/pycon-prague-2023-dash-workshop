# Pridávame stránku s projektami

Ostáva nám už iba posledný krok a to je zaradenie našej analýzy medzi projekty.

### Vytvorenie stránky Projekty

Do súboru `projekty.py` vložíme tento kód:

```python
from dash import register_page, html  
import dash_mantine_components as dmc  
from utils import responzivny_stlpec_uprostred  
  
register_page(__name__)  
  
  
def karta_projekt(image, title, description, odkaz):  
    return dmc.Card(  
        children=[  
            dmc.CardSection(  
                dmc.Image(  
                    src=image,  
                    height=160,  
                )  
            ),  
            dmc.Stack(  
                [  
                    dmc.Text(title, weight=500),  
                    dmc.Text(  
                        description,  
                        size="sm",  
                        color="dimmed",  
                        style={"height": "65px"},  
                    ),  
                    dmc.Anchor(  
                        dmc.Button(  
                            "Navštíviť projekt",  
                            variant="light",  
                            fullWidth=True,  
                            mt="md",  
                            radius="md",  
                        ),  
                        href=odkaz,  
                    ),  
                ],  
                spacing=5,  
                mt=5,  
            ),  
        ],  
        withBorder=True,  
        shadow="sm",  
        radius="md",  
        style={"height": "330px"},  
    )  
  
  
layout = responzivny_stlpec_uprostred(  
    [  
        dmc.Divider(  
            label="Realizované projekty",  
            size="sm",  
            styles={"label": {"font-size": "25px", "font-weight": 600}},  
            mt=20,  
            mb=10,  
        ),  
        dmc.Grid(  
            [  
                dmc.Col(  
                    [  
                        karta_projekt(  
                            image="/assets/analyza-vzdelania.png",  
                            title="Analýza úrovne vzdelania v ČR",  
                            description="Interaktívny dashboard k úrovni vzdelania v ČR z dát štatistického úradu zo "  
 "sčítania obyvateľstva v roku 2021.",  
                            odkaz="analyza-scitanie",  
                        )  
                    ],  
                    sm=6,  
                )  
            ],  
            gutterSm=30,  
            gutter=0,  
            style={"margin": "0px"},  
        ),  
    ]  
)
```

- Vytvorili sme funkciu `karta_projekt` - vytvorí kartu pre jednotlivé projekty
- Všimnime si, že sme v `dmc.Card` využili komponentu `dmc.CardSection` - účelom tejto komponenty je, že napríklad vložený obrázok bude siahať od okraja po okraj
- V samotnom layoute následne pridáme projekt s analýzou vzdelania zavolaním funkcie `karta_projekt`:
	- Do `image` vložíme cestu k náhľadovému obrázku, ktorý sme vložili do súboru assets
	- `title` je názov prezentovaného projektu
	- `description` obsahuje popis projektu
	- `odkaz` obsahuje cestu k danému projektu - po stlačení tlačítka prejdeme na danú adresu url

![enter image description here](https://i.ibb.co/zn1vPfD/pp-projekty.gif)

**A máme hotovo!**

![enter image description here](https://i.ibb.co/FqVfFFt/pp-done-done.gif)
