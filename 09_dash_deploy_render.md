# Nasadenie `Dash` aplikácie pomocou služby Render

Aplikáciu vždy vyvíjame na našom lokálnom zariadení. Avšak na to, aby sme aplikáciu mohli zdieľať s ostatnými ju potrebujeme nasadiť. Existuje nepreberné množstvo webhostinov, na ktorých môžete nasadiť statické aplikácie. Avšak s aplikáciami vytvorenými v pythone je to zložitejšie. V tomto návode si ukážeme nasadenie aplikácie v službe Render.com ktorá ponúka možnosti na hostovanie python aplikácie a navyše je základná varianta úplne zadarmo.

### Čo budeme potrebovať?

1. Vytvorený účet na Render.com
2. Repozitár s našou aplikáciou na Github.com

### Úpravy v samotnej aplikácii

Do `app.py` pridáme riadok:

```python
server = app.server
```

Zároveň vypneme vývojársky režim:

```python
if __name__ == "__main__":  
    app.run(debug=False)
```

### Vytvorenie requirements.txt

Spôsobov je niekoľko. Môj preferovaný spôsob je využitie knižnice `pipreqs`.

1. Otvoríme terminál a nainštalujeme knižnicu pipreqs: `pip install pipreqs`
2. Až sa knižnica nainštaluje, napíšeme do terminálu príkaz `pipreqs`
3. Otvoríme vytvorený súbor requirements.txt a doplníme knižnicu gunicorn, ktorá bude spúšťať náš server
4. Uložíme

![enter image description here](https://i.ibb.co/yq4rfHB/requirements-txt.png)

### Vytvoríme novú verziu v repozitári na github.com

Aplikáciu zaverzujeme aj s requirements.txt a pushneme.

### Nasadenie aplikácie na Render.com

![enter image description here](https://i.ibb.co/zsBzbHM/create-render-app.gif)

1. Prihlásime sa do služby Render.com
2. Klikneme na Dashboard
3. Klikneme na New -> Web Service
4. Do poľa "Public Git repository" na spodku obrazovky vložíme odkaz na náš repozitár
5. Continue
6. Vyplníme:
	- Name: názov (pozor, ak nemáme vlastnú doménu tento názov sa zobrazí aj v url na ktorej bude aplikácia vytvorená)
	- Region: k nám je najbližšie Frankfurt
	- Branch: main
	- Root directory: nevyplňujeme
	- Runtime: Python 3
	- Build Command: `pip install -r requirements.txt`
	- Start Command: `gunicorn app:server`
7. Vyberieme tier: Free
8. Create Web Service

Následne by sa nám mala zobraziť konzola v ktorej uvidíme, ako sa aplikácia postupne nasadzuje. Až v konzole uvidíme hlášku "Your service is live" znamená to, že stránka sa úspešne nasadila. A máme hotovo!

Stránku navštívime na adrese nazov-ktory-sme-zvolili.onrender.com (tento tutoriál nájdeme napríklad na https://pycon-prague-2023-personal-page.onrender.com/).

> Free tier na Render.com znamená:
> - Výkonnostné obmedzenie: 500MB RAM a 0.1 výkonu servera
> - Ale hlavne: Naša aplikácia nebude bežať 24/7. Pokiaľ stránku dlhšiu dobu nikto nenavštívi tak sa služba vypne. Až si následne niekto vašu stránku otvorí, služba sa zase naštartuje - môže to ale spôsobiť o trochu dlhšiu načítaciu dobu.

### Ďalšie nastavenia na Render.com

- V nastaveniach našej služby si môžeme všimnúť, že predvolene je zapnutá možnosť "Auto-Deploy". To znamená, že pri každej novej verzii našej aplikácie nahranej na githube sa služba automaticky reštartuje s touto najnovšou verziou.
- Pokiaľ vlastníte vlastnú doménu v sekcii "Custom domain" môžete túto doménu vložiť. Následne musíte u svojho registrátora domény nastaviť DNS.
