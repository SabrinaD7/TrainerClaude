# Prompt Engineering — Lessons Learned

> Mitä opin LLM-pohjaisen domain-agentin rakentamisesta päivittäiseen käyttöön. Tämä dokumentti syventää README:n "Mitä opin prompt engineeringistä" -osiota ja lisää oppeja jotka eivät mahtuneet sinne.

---

## Konteksti

TrainerClaude on rakennettu iteratiivisesti useiden kuukausien ajan päivittäisen käytön kautta. Ensimmäinen versio (v1.0) oli yksi iso prompt joka yritti kattaa kaiken. Nykyinen versio (v2.2) on modulaarinen järjestelmä jonka rakenne on lopputulos lukuisista virhetiloista ja niiden korjauksista.

Tämän dokumentin oppien arvo ei ole kirjoissa lukemisessa vaan tekemällä saadussa todistusaineistossa. Joka opetus tuli "ahaa-hetkenä" kun jotain ei toiminut.

---

## 1. Kontekstibudjetti on rajallinen resurssi, ei ilmainen

**Lyhyt versio:** Lataaminen kaiken kerralla heikentää LLM:n suoritusta, ei paranna.

**Pidempi versio:**

Aloitin sulauttamalla kaiken yhteen isoon system promptiin. Looginen ajatus: "jos LLM tietää kaiken, se osaa vastata oikein." Tämä ei toimi.

Mitä tapahtui käytännössä:
- LLM "tiesi" kaiken paperilla (kontekstissa oli säännöt, tieteelliset lähteet, esimerkit)
- Mutta käytti tietoa epäjohdonmukaisesti — joskus sovelsi ACWR-sääntöä, joskus ei
- Huomio jakautui liian laajalle alueelle
- Pitkät kontekstit aiheuttavat "lost in the middle" -ilmiötä: kontekstin keskellä oleva tieto unohtuu helpommin kuin alku ja loppu

Ratkaisu: **modulaarinen lazy-loading**. SKILL.md on aina ladattuna (~200 riviä, sisältää intent-reitityksen ja pakottavat säännöt). Reference-tiedostot (methodology.md, modalities.md, recovery.md) ladataan vain kun ne ovat relevantteja kyseiselle keskustelulle.

Konkreettinen mittaus: session-analyysissä ei tarvita palautumisprotokollia → recovery.md:tä ei ladata. Säästö ~400 riviä per interaktio.

**Trade-off:** Lazy-loading vaatii tarkemman intent-reitityksen — väärä reference-tiedosto johtaa vääriin vastauksiin. Tämä on hyvä trade-off: parempi olla deterministinen reititys kuin epäjohdonmukainen "kaikkitietävyys".

**Generalisoitavissa muihin domaineihin:** Kyllä. Kaikki LLM-pohjaiset domain-agentit kärsivät tästä. Modulaarisuus on yksi harvoista universaaleista ratkaisuista.

---

## 2. Strukturoitu data > vapaa proosa kriittisissä päätöksissä

**Lyhyt versio:** LLM:t käsittelevät taulukoita jämäkämmin kuin proosaa. Workflow-templates tuottavat toistettavia vastauksia.

**Pidempi versio:**

Tämä oppi tuli kahdesta erillisestä huomiosta:

**a) Numerot taulukoissa vs. proosassa**

Verrattaessa kahta muotoilua:

Proosa:
> "Kohdelihasryhmän hypertrofialle suositellaan noin 12–16 sarjaa viikossa optimaalisen kasvun saavuttamiseksi."

Taulukko:
| Volyymitaso | Sarjat/vk |
|---|---|
| MEV | 8–10 |
| MAV | 12–16 |
| MRV | 18–22 |

LLM soveltaa taulukoitua tietoa konsistentimmin. Hypoteesini: tokenization ja attention-mekanismit toimivat eri tavalla strukturoidulle datalle. Käytännön havainto: numerot pysyvät tarkkoina kun ne ovat taulukossa, mutta saattavat vaihdella kun ne ovat proosan sisällä.

**b) Workflow-templates vs. vapaa päättely**

Yritin alussa antaa LLM:lle vain "ole hyvä koutsi" -ohjeita ja luottaa että se päättelee oikein. Tulos: laadukkaita vastauksia 60 % ajasta, geneerisiä tai virheellisiä 40 % ajasta.

Muutos: 9 eksplisiittistä intent-tyyppiä, jokaisella oma workflow. Esim. "Sessio jaettu" -intentti ohjaa:
1. Hae viimeisin konteksti
2. Tarkista akuuttiprotokollan status
3. Lue kuvakaappauksen avainluvut
4. Kysy sRPE jos ei kirjattu
5. Vertaa edellisiin samaan modaliteettiin
6. Tunnista flag-kriteerit
7. Anna 2–3 konkreettista huomiota

Tulos: laadukkaita vastauksia 95 % ajasta. Loput 5 % ovat aitoja edge-caseja jotka vaativat workflow'n päivityksen.

**Trade-off:** Workflow-templates ovat jäykempiä. Mutta jäykkyys on hyväksyttävä hinta deterministisyydestä, erityisesti turvakriittisissä päätöksissä.

---

## 3. Tila pitää eksternalisoida — Notion on järjestelmän muisti

**Lyhyt versio:** LLM:n "muisti" on illusorinen. Pysyvä tila tarvitsee tietokannan.

**Pidempi versio:**

Aluksi oletin että voin luottaa Claude-keskusteluiden "muistiin". Tämä on virhe kahdesta syystä:

**a) Context window ei säily**

Vaikka Claude.ai säilyttää keskusteluhistorian, sitä ei voida luottaa jatkuvaan tilatallennukseen. Pitkä historia → suorituskyky heikkenee. Uusi keskustelu → kaikki menetetty.

**b) Trendianalyysi vaatii strukturoitua dataa**

ACWR-laskenta vaatii edellisten 4 viikon sessiokuorman. Polarized training -arvio vaatii viikkotason intensiteettijakauman. Nämä ovat mahdottomia ilman pysyvää, kyseltävää dataa.

Ratkaisu: **Notion eksternalisoituna tilana**, jaettuna kahteen kerrokseen:

**ATHLETE.md** — Hitaasti muuttuva profiili (Single Source of Truth):
- Henkilötiedot, sykealueet, baseline-mittarit
- Tavoitteet
- Treenipreferenssit
- Päivittyy kun jotain merkittävää muuttuu (mittaus, tavoitemuutos)

**Tietokannat** — Tapahtumadata:
- Sessions: jokainen treenisessio
- Insights: havainnot, säännöt jotka osoittautuvat oikeiksi/vääriksi
- Plans: suunnitellut sessiot tulevaisuudessa
- PRs: ennätykset
- Body Comp: kuukausimittaukset

Tämä jako on tärkeä: ATHLETE.md ladataan joka keskustelussa, tietokannat kysytään tarpeen mukaan.

**Yllättävä hyöty:** Käyttäjä saa näkyvyyden järjestelmän "muistiin". Hän voi muokata ATHLETE.md:tä suoraan jos säännöt eivät sovi. Tämä luo luottamuksen — käyttäjä ei ole black boxin armoilla.

**Generalisoitavissa muihin domaineihin:** Kyllä, jokainen LLM-agentti joka tekee päätöksiä pidemmällä aikavälillä tarvitsee eksternalisoidun tilan.

---

## 4. Käyttäjän vastustus on signaali, ei häiriö

**Lyhyt versio:** Kun käyttäjä toistuvasti vastustaa sääntöä, järjestelmä on todennäköisesti väärässä, ei käyttäjä.

**Pidempi versio:**

Tämä on filosofinen päätös, ei tekninen. Mutta sillä on tekniset implikaatiot.

Aluksi rakensin järjestelmän auktoriteetiksi: "jos käyttäjä on eri mieltä, vakuuta hänet uudelleen." Tämä toimii joskus (kun käyttäjä on rationalisoimassa väärää valintaa) mutta epäonnistuu usein (kun sääntö ei sovi yksilölle).

Konkreettinen esimerkki (anonymisoitu): käyttäjä vastusti tiettyä liikettä toistuvasti viitaten siihen ettei liike sopinut hänen biomekaniikalleen. Alkuperäinen reaktio: "tämä on tieteellisesti hyvä liike kohdelihasryhmälle, kokeile uudestaan." Tämä oli väärä reaktio. Oikea reaktio: dokumentoida poikkeus Insights-tietokantaan ja korvata liike vaihtoehtoisella.

Filosofinen muutos: **järjestelmä ei ole auktoriteetti, vaan työkalu joka iteroi käyttäjän kanssa.** Yleinen tieteellinen sääntö on lähtökohta, ei lukittu totuus. Yksilön kokemus on validointitestaus.

Käytännön mekanismi: **Insights-tietokanta feedback loopina**:
1. Käyttäjän vastustus → kirjataan Insights:iin
2. Pattern toistuu → sääntö päivittyy ATHLETE.md:hen tai SKILL.md:hen
3. Järjestelmä oppii ilman fine-tunausta

**Tämä eroaa sycophancy-ongelmasta:** Järjestelmä ei myöskään validoi vain miellyttääkseen. SKILL.md:n säännöt sanovat eksplisiittisesti "Älä validoi ilman perusteita" ja "Haasta käyttäjää kun data ja hänen tuntemus eivät täsmää" (ks. Esimerkki 01). Ero auktoriteetin ja kumppanuuden välillä on hieno: kumppani haastaa kun on perusteita, ei oletusarvoisesti, eikä kumppani vain myönny pidätyksen alla.

**Generalisoitavissa muihin domaineihin:** Kyllä, erityisesti henkilökohtaista neuvontaa antaviin järjestelmiin (terveys, talous, opiskelu).

---

## 5. Versionhallinta on yhtä tärkeää promptisuunnittelussa kuin koodissa

**Lyhyt versio:** Promptit kehittyvät kuten koodi. Ilman versionhallintaa et tiedä mikä toimi ja mikä ei.

**Pidempi versio:**

SKILL.md sisältää version (v2.2.0) ja changelog. Tämä ei ole kosmetiikkaa.

Versioiden välillä on tapahtunut isoja muutoksia:
- v1.0 → v2.0: yksi iso skill → modulaarinen rakenne
- v2.0 → v2.1: tavoiteprioriteettien lisäys, sRPE-pohjainen kuormitusseuranta
- v2.1 → v2.2: tieteellisesti heikosti perusteltujen claimien poisto

Jokainen muutos tuli konkreettisesta virhetilanteesta. Ilman changelogia olisin tehnyt samoja virheitä uudelleen.

**Käytännön disipliini:**

1. Kun sääntö osoittautuu vääräksi → dokumentoi miksi Insights-tietokantaan
2. Kun pattern toistuu → päivitä skill-tiedostoon
3. Nosta versionumeroa kun teet rakenteellisen muutoksen
4. Pidä changelog luettavana


---

## 6. State machine on parempi kuin sääntölista kriittisissä tilanteissa

**Lyhyt versio:** Turvakriittiset päätökset eivät voi olla vapaamuotoisia. Eksplisiittinen tilamalli pakottaa harkitun käytöksen.

**Pidempi versio:**

Akuuttiprotokolla on koodattu **state machinena**, ei sääntölistana. Ero on tärkeä:

**Sääntölista (huono):**
> "Jos käyttäjä raportoi vakavan oireen, suosittele lepoa."

LLM voi tulkita "vakava" eri tavalla eri kerroilla. "Suosittele lepoa" ei määritä kuinka kauan, mitä saa tehdä, milloin tila päättyy.

**State machine (parempi):**
```
TRIGGER EHDOT:
  - Toistuva poikkeava fysiologinen oire
  - Pakkouni > 2 h session jälkeen
  - HRR1 < 8 bpm peräkkäisissä sessioissa
  - Lääketieteellinen interventio treeniin liittyvistä oireista

ENTRY:
  - Pyydä käyttäjän vahvistus
  - Kirjaa Insights-tietokantaan
  - Aseta keston aikaraja

AKTIIVISENA - RAJOITUKSET:
  - Max 2 korkean intensiteetin sessiota/vk
  - Ei kaksoissessiota
  - Palautumisaika > 48 h → seuraava sessio Z1 only
  - jne.

EXIT KRITEERIT:
  - Aikaraja täynnä JA ei uusia trigger-ehtoja
  - Käyttäjän eksplisiittinen päätös
```

State machine tekee mahdottomaksi LLM:n "improvisoinnin" turvakriittisellä alueella. Tila on joko aktiivinen tai ei. Säännöt aktiivisena tilassa ovat eksplisiittisiä. Exit on määritelty.

**Trade-off:** Vähemmän joustavuutta. Mutta turvakriittisissä päätöksissä joustavuus on bug, ei feature.

---

## 7. Hyväksy että et tee kaikkea — komposoituvat skillit

**Lyhyt versio:** Toimivat LLM-agentit pitävät scopen tiukkana ja delegoivat ulkopuolelle kuuluvan.

**Pidempi versio:**

Houkutus on tehdä yhdestä skillistä "all-in-one"-ratkaisu. Treenikoutsi joka antaa myös ravitsemusneuvoja, mielenterveysneuvoja, urakehitysneuvoja. Tämä on yleensä huono idea.

Konkreettinen esimerkki: ravitsemus.

Alkuperäisessä versiossa recovery.md sisälsi yksityiskohtaisen ravitsemus-osion. Tämä laimensi sekä treenidomainia (laajempi scope, vähemmän syvyyttä) että vaarallisti ravitsemusdomainia (LLM-pohjaiset ravitsemusneuvot ovat herkkiä henkilökohtaisille olosuhteille).

Nykyinen ratkaisu: **ravitsemus on ulkoistettu erilliseen protokollaan/skilliin** (ks. recovery.md §6). Treenikoutsi voi mainita ravitsemuksen yleisellä tasolla mutta ei mene spesifeihin yksityiskohtiin.

**Periaate:** Skillit komponoituvat. Käyttäjä voi käyttää useaa skilliä rinnakkain (treenikoutsi + ravitsemuskoutsi). Tämä on parempi arkkitehtuuri kuin yksi monoliittinen super-skill.

**Generalisoitavissa:** Kyllä, tämä on suoraan analogiaa mikropalvelu-arkkitehtuuriin perinteisessä ohjelmistokehityksessä.

---

## Yhteenveto — prompt engineering as engineering discipline

Nämä opit yhdistyvät yhteen periaatteeseen: **promptisuunnittelu on insinöörin disipliini, ei taidemuoto.**

- Modulaarisuus
- Eksplisiittisyys
- Versionhallinta
- Eksternalisoitu tila
- Determinismi kriittisissä päätöksissä
- Komposoituvuus
- Käyttäjälähtöinen iteraatio

Nämä eivät ole erityisiä LLM-piirteitä — ne ovat klassista ohjelmistosuunnittelua sovellettuna uuteen domainiin. LLM-pohjaisten järjestelmien laatu paranee kun niitä rakennetaan samoilla periaatteilla kuin kestävää ohjelmistoa.

LLM:t ovat erityisen herkkiä tälle disipliinille koska ne ovat luonnostaan epädeterministisiä. Ympäröivä arkkitehtuuri on se mikä tekee niistä luotettavia.

---

## English summary

Seven lessons learned from building TrainerClaude as a daily-use AI training coach:

1. **Context budget is a finite resource, not free.** Loading everything degrades LLM performance through attention dilution and "lost in the middle" effects. Modular lazy-loading is the fix.

2. **Structured data beats prose in critical decisions.** LLMs apply tabular information more consistently than prose. Workflow templates produce repeatable responses where free-form reasoning produces variance.

3. **State must be externalized.** LLM "memory" is illusory; persistent state needs a database. Notion serves as the system's single source of truth, with clear separation between slow-moving profile (ATHLETE.md) and event data (databases).

4. **User resistance is signal, not noise.** When a user repeatedly pushes back on a rule, the system is likely wrong, not the user. The Insights database serves as a feedback loop for rule evolution without fine-tuning.

5. **Versioning is as important in prompt engineering as in code.** Prompts evolve. Without changelog discipline, you repeat the same mistakes.

6. **State machines beat rule lists in safety-critical contexts.** The acute protocol is coded as an explicit state machine — triggers, entry, constraints, exit — preventing LLM improvisation where errors have real cost.

7. **Accept that you don't do everything — skills compose.** Functional LLM agents keep scope tight and delegate out-of-domain matters to other skills. Nutrition is externalized from this coach because it deserves its own specialized skill.

These principles converge on a single theme: **prompt engineering is an engineering discipline, not an art form.** The lessons aren't LLM-specific — they're classical software engineering applied to a new domain. LLMs are especially sensitive to this discipline because they are non-deterministic by nature; the surrounding architecture is what makes them reliable.
