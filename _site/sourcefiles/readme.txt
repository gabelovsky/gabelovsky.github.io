Webové publikovanie
Zadanie 1

Url stránky: https://gabelovsky.github.io/

Stránku som testoval na Google Chrome 56 a Internet Explorer 11
Stránka 


Kritériá:

Podstránky: webová stránka má 6, dosiahnuteľné pomocou navigačného panela:
/ -domovská stránkanal
/me/ -moje počítačové znalosti
/cv/ -môj životopis
/blog/ -všeobecný blog
/wpub/ -blog pre predmet Webové publikovanie
/contacts/ -kontakty a email formulár,(konfirmácia scriptu ešte nefunguje)
Okrem toho každý post má vlastnú podstránku.

Layouts:
def.html -obsahuje layout pre hlavičku a pre pozadie, každý layout používa toto
main.html -layout pre /,/me/ a /cv/, obsahuje profil a socálne linky
blog.html -layout pre /blog/ a /wpub/
post.html -layout pre jednotlivé posty
cont.html -používané iba pre /contacts/, obsahuje formulár pre posielanie emailov

Premenné:
Základné:
Používal som základné Jekyll premenné ako site.name, site.time, page.title, page.url a premenné pre vytvorenie blogu.
Vlastné:
Pre načítanie CSS súboru(alebo súborov) vytvoril som "styles", čo obsahuje mená CSS súborov, lokálné alebo online, a z toho def layout vytvorí hlavičku
V bloge sa nachádza premenná "blogtag", ktorá definuje, ktoré posty sa bude zobrazovať
Stránka pre kontakty obsahuje email adresu a telefónne číslo
Posty obsahujú tagy pre lepšiu kategorizáciu v bloge
V /blog/ používam premennú "posted", aby som nezobrazil ten istý post dvakrát

Dátové súbory:
V /me/ načítam moje známky a počítačové znalosti zo dátového súboru "grades.csv" a "skills.csv"

Filtre:
Pre zobrazenie dátumu používal som filter |date:format a |date_to_long_string
Pre spojenie dvoch stringov (ako mailto:email adresa) filter |append sa používa
Tagy:
"if" tagy sú použité na väčšine stránok
Rovnako "for" sa používa pre načítanie postov, dát atď.
"assign" vytvorí premennú posted
"strip" bol použitý pre vylepšenie formátovania HTML dokumentov, ale Github vrátil chybu, tak bol odstránený

Pluginy:
sitemap_generator vytvorí XML sitemap pre stránku(treba Jekyll build)
Použil som email script na posielanie emailov bez dedikovaný server(https://github.com/dwyl/html-form-send-email-via-google-script-without-server)
strip odstránil white lines, ktoré jekyll tagy generovali, ale nie je kompatibilný s Github
