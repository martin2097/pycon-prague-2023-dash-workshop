### Inštalácia

Inštaláciu prevedieme nasledujúcim príkazom v terminále.
```python
pip install dash
```

>  :bulb: **Tip:** V PyCharme otvoríme terminál pomocou skratky <kbd>Alt</kbd>+<kbd>F12</kbd>

V týchto návodoch budeme ešte často pracovať s knižnicami Dash Mantine Components, Dash Iconify a Dash AG Grid, preto si uvedieme aj príkazy na ich inštaláciu.

```python
pip install dash-mantine-components
pip install dash-iconify
pip install dash-ag-grid
```

### Spustenie aplikácie v PyCharme

![enter image description here](https://i.ibb.co/gFmvK7t/vytvorenie-prvej-aplikace.gif)

Po vytvorení virtuálneho prostredia a inštalácii potrebných balíčkov máme všetko nachystané na spustenie našej prvej aplikácie. Vytvoríme si nový script (<kbd>Alt</kbd>+<kbd>Insert</kbd> a vybrať Python File), ktorý ľubovoľne pomenujeme (napríklad `first_app.py`) a vložíme nasledujúci kód.

```python
from dash import Dash  
import dash_mantine_components as dmc  
  
app = Dash(__name__)  
  
app.layout = dmc.Text("Moja prvá aplikácia!")  
  
if __name__ == "__main__":  
    app.run(debug=True)
```

Aplikáciu spustíme pravým klikom a vybraním možnosti **Run 'first_app'** alebo pomocou <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>F10</kbd>. Aplikácia sa spustila na lokálnom servery, ktorý sa spustil na našom počítači. V dolnej časti obrazovky by sa mala zobraziť sekcia Run (opäť vieme otvoriť ručne alebo pomocou klávesovej skratky <kbd>Alt</kbd>+<kbd>4</kbd>).

![enter image description here](https://i.ibb.co/1sXXVWQ/pycharm-run.png)

Zelená bodka značí, že aplikácia beží. Po rozkliknutí odkazu (predvolená adresa je http://127.0.0.1:8050/), ktorý sa nám zobrazil, sa nám otvorí naša aplikácia v prehliadači.

![enter image description here](https://i.ibb.co/F3hRMx7/image.png)

Aplikáciu (respektíve lokálny server na ktorom aplikácia beží) ukončíme pomocou červeného štvorčeka alebo skratky <kbd>Ctrl</kbd>+<kbd>F2</kbd>. Keďže sme nastavili parameter `debug=True`, aplikácia sa bude automaticky reštartovať vždy, keď zmeníme náš kód. Aplikáciu je možné reštartovať aj ručne pomocou tlačidla Rerun alebo pomocou skratky <kbd>Ctrl</kbd>+<kbd>F5</kbd>.
