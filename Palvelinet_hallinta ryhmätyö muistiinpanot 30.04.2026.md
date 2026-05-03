
## Projektin nimi: Linux defaults with ansible (how it should be)

Projektin tarkoitus on tehdä ansiblella konfiguraatio mikä on automatisoitu.

Idea on että käyttäjä voi:

Kloonata repon yhdellä komennolla -> konfiguraatio asennetaan yhdellä komennolla (tässä kohtaa voit valita minkä version haluat asentaa flageilla. Ilman temuxia vai temuxin kanssa) -> konfiguraatio asennetaan -> käyttäjä voi käyttää asentamia työkaluja

Projektin dokumentaatio (Robin & Andro) -> readme.md tiedosto toimii virallisena projektin dokumentaationa

### Projektin työnjako:

* Temuxin konfiguroinin automatisointi (Andro)
* UFW/firewall konffaaminen (Robin)
* Auto update (Robin)
* FAIL2BAN -> SSH (Robin)
* Projektin kotisivuston tekeminen

UFW firewall käyttöön ottaminen esim. 22 portti on auki, fail2ban käytössä

Bash installation skriptin tekeminen, missä flagina on eri vaihtoehtoja (Andro tekee installation skriptin with tmux, Robin tekee installation skriptin wihtout tmux) 

1. ilman tmux 
2. tmux:in kanssa

Pudding mikä venyy jostain esim. hyllyltä alas (logo) :D :D 

