# Esimerkki 1: Session-analyysi

Sanitisoitu, edustava esimerkki siitä miten järjestelmä käsittelee jaetun harjoitussession. "Mitä tapahtui taustalla" -osio näyttää arkkitehtoniset mekanismit, jotka tuottavat vastauksen.

---

## Käyttäjän viesti

> Tein eilen tempo-lenkin, oli kyllä easy. 10 km, fiilis hyvä. Tässä wearable-data:

```
Modaliteetti: Juoksu
Kesto: 58:23
Matka: 10.04 km
Keskim. tahti: 5:49 /km
Keskim. syke: 162 bpm
Max syke: 178 bpm
Min syke: 124 bpm
Sykealueet (min):
  - Z1 (lämmittely): 4
  - Z2 (rasvanpoltto): 8
  - Z3 (aerobinen): 28
  - Z4 (anaerobinen): 16
  - Z5 (äärimmäinen): 2
Aerobinen rasitusscore: 4.2
Anaerobinen rasitusscore: 2.8
Palautumisaika: 42 h
Sykkeen palautuminen 1 min: 12 bpm
Kadenssi: 168 spm
Nousu: 47 m
```

---

## Järjestelmän vastaus

> Tunteman ja datan välillä on iso ristiriita — tämä ei ollut easy.
>
> **Intensiteettijakauma:**
> - Z1–Z2: 12 min (21 %)
> - Z3: 28 min (48 %)
> - Z4–Z5: 18 min (31 %)
>
> Tämä on **pyramidal-jakauma vahvalla Z3-painotuksella** — Seilerin polarized model -hengessä tämä on "musta laatikko" -alue jota pitäisi välttää keskittyneenä (`methodology.md` §2.1). 79 % session ajasta meni Z3+ alueella. Easy run -määritelmän mukaan jakauman pitäisi olla 80 % Z1–Z2, ei 21 %.
>
> Tätä tukevat objektiiviset signaalit:
> - HRR1 12 bpm = OK mutta ei erinomainen (baseline ATHLETE.md:ssä, esim. 14–16 bpm)
> - Aerobinen rasitusscore 4.2 / 5 = wearable tulkitsee tätä korkean rasituksen sessioksi
> - Palautumisaika 42 h = ei "easy" -session palautumisikkuna
>
> **sRPE — mikä se oli session jälkeen (30 min kuluttua)?** Jos et muista, ehdotan: tätä dataa katsoen objektiivinen arvio olisi sRPE 7–8, ei sRPE 4–5 joka olisi tunteman mukainen.
>
> **Sessiokuorma:** 58 min × sRPE 7 (oletus) = 406 AU
>
> **ACWR vaikutus:** Tämä ei ole yksittäinen iso ongelma, mutta jos easy run -nimellä tehdyt sessiot pysyvät Z3+ tasolla, polarized-mallin 80/20-tavoite rikkoutuu viikkotasolla ja kumulatiivinen väsymys kerääntyy ilman vastaavaa adaptaatiota.
>
> **Käytännön ehdotus seuraavalle easy run -lenkille:**
> 1. Käytä **tahtia, ei tunnetta** — Z2-tahti esim. 6:30–7:00 /km (10 km kilpailutahti + 90–120 s/km, `modalities.md` §2.2)
> 2. Jos siellä keskim. syke menee yli ~145 bpm pitkäaikaisesti, hidasta tahtia vaikka tuntuisi liian hitaalta
> 3. Z2-juoksun pitäisi tuntua "voin keskustella koko ajan" — Z3 alkaa kun keskustelu menee katkonaiseksi
>
> **Yhteinen ongelma:** Aerobinen kapasiteetti on usein hyvä → easy run tuntuu helpolta vaikka data sanoo että ei ole. Tämä on klassinen syy miksi tehdään liikaa "musta laatikko" -alueella. Tahtiin ankkurointi ratkaisee tämän.

---

## Mitä tapahtui taustalla

Vastaus on usean arkkitehtonisen vaiheen lopputulos. Vaiheet eriteltyinä alla:

### Vaihe 1: Intent detection (`SKILL.md` §5)

Viesti sisältää wearable-datan + kommentin suoritetusta sessiosta. Tämä vastaa **Intent A: "Sessio jaettu"** -reititystaulukon riviä. Järjestelmä lataa vastaavan workflow'n.

### Vaihe 2: Tiedostojen lataus (`SKILL.md` §11)

Järjestelmä lataa lazy-loadingilla kaksi reference-tiedostoa intentin ja modaliteetin perusteella:

- `methodology.md` — kuormamittareita varten (sRPE, ACWR) ja intensiteettijakauman säännöt (polarized training)
- `modalities.md` — juoksuspesifit protokollat (tahtipohjaiset alueet, threshold pace -estimaatti)

`recovery.md`:tä **ei ladattu** koska keskustelu ei viitannut palautumisfokuksiseen intentiin. Tämä on lazy-loading toiminnassa — kontekstibudjetti säilyy.

### Vaihe 3: Ristiriidan tunnistus (data vs. käyttäjän tunteman)

Järjestelmä tunnistaa suoran ristiriidan kahden signaalin välillä:

| Signaali | Mitä sanoo |
|---|---|
| Käyttäjän tunteman | "Easy" |
| Sykejakauma | 79 % yli Z2-tason |
| Palautumisaika | 42 h (ei easy) |
| Aerobinen rasitusscore | 4.2 / 5 (korkea) |

SKILL.md:n identiteettisääntöjen mukaisesti ("Haasta Athlete X:ää kun data ja hänen tuntemus eivät täsmää"), järjestelmä nostaa ristiriidan eksplisiittisesti esiin sen sijaan että validoisi käyttäjän tulkinnan.

### Vaihe 4: Sääntöjen soveltaminen

Kolme eksplisiittistä sääntöä ladatuista reference-tiedostoista:

1. **Seilerin polarized training -malli** (`methodology.md` §2.1): Z3 on "musta laatikko" -alue jota tulee välttää keskittyneenä. Sessio oli 48 % Z3, mikä rikkoo 80/20-jakaumaperiaatetta.

2. **Tahtipohjaiset alueet juoksulle** (`modalities.md` §2.2): Järjestelmä laski odotetun Z2-tahdin athleten 10k kilpailuajasta (säilytetty ATHLETE.md:ssä) ja ehdotti sitä ankkuriksi seuraavalle sessiolle.

3. **sRPE × kesto -kuormakaava** (`methodology.md` §1.1): Järjestelmä arvioi sRPE:n objektiivisen datan perusteella (koska käyttäjä ei antanut arvoa) ja laski AU:n.

### Vaihe 5: Puuttuvan tiedon käsittely

sRPE puuttui. Järjestelmä **ei hiljaisesti olettanut** arvoa — se kysyi sitä eksplisiittisesti ("mikä se oli session jälkeen") ja antoi samalla arvion objektiivisen datan perusteella. Tämä on tarkoituksellista: käyttäjä säilyttää auktoriteetin subjektiivisiin mittareihin, mutta järjestelmä tarjoaa datapohjaisen viitearvon.

### Vaihe 6: Vastauksen rakenne

Vastaus noudattaa SKILL.md:n identiteettisääntöjen konventiota:

- **Suora** — ei "Hyvä kysymys" -tyyppistä johdantoa
- **Numeropohjainen** — tarkat prosenttiosuudet, ei kvalitatiivisia kuvauksia
- **Lähteistetty** — viittaukset spesifisiin metodologian osiin (§2.1, §2.2)
- **Toiminnallinen** — konkreettinen ohje seuraavalle sessiolle (tahti 6:30–7:00 /km)
- **Opettavainen** — selittää *miksi* aerobinen kapasiteetti luo juuri tämän sokean pisteen

---

## Mitä tämä esimerkki demonstroi arkkitehtonisesti

1. **Lazy-loading toimii** — vain relevantit reference-tiedostot konsultoitiin, ei kaikkia
2. **Sääntöjen grounding** — johtopäätös ("menit liian kovaa") on ankkuroitu eksplisiittisiin, lähteistettäviin sääntöihin, ei LLM:n intuitioon
3. **Ristiriitojen näkyväksi tekeminen** — järjestelmä ei oletusarvoisesti validoi käyttäjää miellyttääkseen
4. **Tiedon täydellisyys** — puuttuva data (sRPE) tunnistetaan ja täydennetään datapohjaisella arviolla, ei hiljaisesti oleteta
5. **Cross-file -päättely** — vastaus syntetisoi tietoa kahdesta reference-tiedostosta + ATHLETE.md:stä (kilpailuaika, HRR1 baseline)

Geneerinen LLM-keskustelu olisi todennäköisesti:
- Validoinut käyttäjän "easy"-tulkinnan
- Antanut yleisluontoisia juoksuneuvoja
- Ei huomannut polarized model -rikkomusta
- Ei pystynyt laskemaan tahtialueita kilpailuajasta
- Ei säilyttänyt johdonmukaisuutta aiempien sessioiden tulkintojen kanssa

Arkkitehtuuri on olemassa estääkseen nämä virhetilat systemaattisesti, ei satunnaisesti.

---

## English summary

A sanitized example demonstrating how the system processes a shared training session. The user describes a run as "easy," but the wearable data (79 % of time above Z2, 42 h recovery time, 4.2/5 aerobic strain score) clearly contradicts this perception. The system:

1. Surfaces the conflict between perception and data
2. Computes intensity distribution percentages
3. Applies Seiler's polarized training model from `methodology.md` §2.1
4. Triangulates with HRR1, aerobic strain score, and recovery time
5. Handles missing sRPE explicitly (asks + provides data-grounded estimate)
6. Prescribes concrete pace targets for next session derived from ATHLETE.md race time
7. Explains the underlying cause (aerobic capacity creates "easy" blind spot)

The "behind the scenes" section documents six architectural steps: intent detection, lazy file loading, conflict detection, rule application, information gap handling, and response structure. Together these demonstrate the system's core architectural claims in action: lazy-loading, explicit rule grounding, externalized state, and resistance to user-pleasing validation.
