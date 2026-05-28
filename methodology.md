# methodology.md — Treenitieteen viittauskäsikirja

**Tarkoitus:** Tämä tiedosto sisältää urheilutieteen ydinperiaatteet joita Claude soveltaa Athlete X:n treeniin. Ei oppikirja — työohje. Joka osio on rakennettu niin että: (1) periaate, (2) konkreettinen sovellussääntö, (3) lähde.

**Lue tämä kun:** Athlete X pyytää viikkosuunnitelmaa, status-katsausta, sessio-analyysiä, deload-päätöstä, taper-suunnitelmaa, tai kun joudut perustelemaan rajoitusta/päätöstä tieteellisesti.

---

## 1. Kuormitusmittarit

### 1.1 sRPE (session Rating of Perceived Exertion)

**Mitä se on:** Foster 1998 -menetelmä. Borg CR-10 -skaalan modifoitu versio. Mittaa session sisäistä kuormitusta subjektiivisesti.

**Laskukaava:**
```
Sessiokuorma (AU) = sRPE (1–10) × kesto (min)
Viikkokuorma = Σ sessioiden AU
```

**Ajoitus:** Kysytään ~30 min session jälkeen kun adrenaliini laskenut ja tunne stabiloitunut. Aikaisemmin tehty arvio voi yliarvioida intensiteettiä (Fusco et al. 2020).

**Miksi tärkein mittari multi-modaalisessa treenissä:**
- Wearablea ei voi käyttää johdonmukaisesti (voima, lajiharjoittelu)
- Mittaa kumulatiivista väsymystä jota syke ei näytä (Fusco et al. 2020)
- Korreloi vahvasti Edwards TRIMP- ja Banister TRIMP -mittareiden kanssa
- Toimii voimaharjoittelussa jossa syke ei korreloi kuormaan

**Sovellussäännöt:**
- sRPE ≥ 8 = korkean intensiteetin sessio → ei kahta peräkkäin
- sRPE 5–7 = kohtalainen → voi tehdä peräkkäin Z1/Z2 -palautusten kanssa
- sRPE 1–4 = palauttava → ei lasketa "kovaksi" päiväksi

**Lähde:** Foster, C. (1998). Monitoring training in athletes with reference to overtraining syndrome. *Med Sci Sports Exerc*, 30(7), 1164–1168.

### 1.2 ACWR (Acute:Chronic Workload Ratio)

**Mitä se on:** Tämän viikon kuorma / edellisten 4 viikon keskiarvo. Loukkaantumisriskin indikaattori (Gabbett 2016).

**Laskukaava:**
```
Acute load = viimeisten 7 päivän Σ sRPE × kesto
Chronic load = viimeisten 28 päivän Σ / 4 (4 viikon ka)
ACWR = Acute / Chronic
```

**Tulkinta:**
| ACWR | Tulkinta | Toiminta |
|---|---|---|
| < 0.8 | Aliharjoittelu / palautuminen | OK lyhyt aika, jatka |
| **0.8–1.3** | **Sweet spot** | Optimi adaptaatio + alhainen riski |
| 1.3–1.5 | Lisääntynyt riski | Älä nosta volyymia ensi viikolla |
| > 1.5 | Korkea loukkaantumisriski | Pakollinen volyymin lasku 20–30 % |

**Kritiikki:** Impellizzeri et al. 2020 ja Wang et al. 2021 ovat kritisoineet ACWR:n metodologista validiteettia. Käytä rinnan muiden mittareiden kanssa (sRPE-kuormitus, sykkeen palautuminen, subjektiiviset fiilikset). Älä luota pelkkään ACWR-lukuun.

**Lähde:** Gabbett, T. J. (2016). The training-injury prevention paradox. *Br J Sports Med*, 50(5), 273–280.

### 1.3 CTL / ATL / TSB (Banister impulse-response)

**Mitä se on:** Banisterin TRIMP-pohjainen kuormitusmalli. CTL = kunto (Chronic Training Load, 42 vrk EMA), ATL = väsymys (Acute Training Load, 7 vrk EMA), TSB = palautuneisuus (Training Stress Balance = CTL − ATL).

**Yksinkertaistettu malli (ilman EMA-laskelmaa):**
```
CTL ≈ 4 viikon keskim. päivittäinen kuorma
ATL ≈ 7 vrk keskim. päivittäinen kuorma
TSB = CTL − ATL
```

**Tulkinta:**
| TSB | Tila | Käyttö |
|---|---|---|
| > +25 | Tuore, ali-treenattu | Kilpailuvalmis tila — käytä tärkeään suoritukseen |
| +5 to +25 | Palautunut | Hyvä päivä kovalle treenille |
| −10 to +5 | Neutraali / lievä väsymys | Normaali treenipäivä |
| < −30 | Ylikuormitustila | Pakollinen deload |

**Lähde:** Banister, E.W. (1991). Modeling elite athletic performance. *Physiological Testing of Elite Athletes*, 403–424.

### 1.4 Sykkeen palautuminen 1 min (HRR1)

**Mitä se on:** Sykkeen lasku ensimmäisen minuutin aikana session jälkeen. Mittaa autonomisen hermoston tilaa, erityisesti parasympaattista aktivaatiota.

**Tulkinta (athleten baseline ATHLETE.md:ssä, esimerkki: 14–16 bpm):**
| HRR1 | Tila |
|---|---|
| ≥ 15 bpm | Erinomainen palautuminen |
| 10–14 bpm | Hyvä, normaali |
| 5–9 bpm | Heikentynyt — flag, jos toistuu |
| < 5 bpm | Hermosto väsynyt — pakollinen lepo |

**Sovellussääntö:** Jos HRR1 < 10 bpm kahdessa peräkkäisessä sessiossa → vähennä volyymia 30 % seuraavalla viikolla.

**Lähde:** Daanen, H.A.M. et al. (2012). A systematic review on heart-rate recovery to monitor changes in training status. *Int J Sports Physiol Perform*, 7(3), 251–260.

### 1.5 HRV (sydämen sykevälivaihtelu)

**Mitä se on:** Sykevälien vaihtelu — kultainen standardi autonomisen palautumisen mittaamiseen (Plews & Buchheit 2017).

**Sovellussääntö:**
- Mittaa aamulla heti herättyä, sängystä
- Käytä **7 päivän rolling average** ei yksittäisiä päiviä
- Jos 7-päivän ka. on −10 % normaalista → flag, vähennä volyymia
- Jos +10 % normaalista → keho valmis kovaan päivään

**Wearable + HRV:** Tarkista tukeeko. Jos tukee, käytä päivittäisenä valmiusmittarina.

**Lähde:** Plews, D.J. & Buchheit, M. (2017). Monitoring training with heart rate variability: how much compliance is needed for valid assessment? *Int J Sports Physiol Perform*, 12(3), 289–292.

---

## 2. Intensiteettijakauma

### 2.1 Polarized Training (80/20 sääntö)

**Periaate:** Seilerin tutkimukset (2010, 2014) huippu-kestävyysurheilijoissa: optimi jakauma on **80 % volyymistä Z1–Z2 (matala intensiteetti) ja 20 % Z4–Z5 (kova)**. Vältä Z3 (kynnysalue, "musta laatikko") keskittyneenä — se kerää väsymystä ilman vastaavaa adaptaatiota.

**Sovellussäännöt (kestävyysmodaliteetit):**
- Lasketaan VOLYYMISTÄ (minuutit) ei sessioiden määrästä
- Tavoite: 80 % aikaa Z1–Z2, 20 % Z3–Z5
- Z3 on **vältettävä alue** pitkissä sessioissa — joko mene matalemmaksi tai korkeammaksi
- Z3 on kuitenkin **luonnollinen lajiharjoittelussa** — niissä se on osa lajia, ei vältettävä

**Flag:** Jos viikon volyymistä > 25 % Z3-alueella ilman selvää periodisaatioperustetta → flag.

**Lähde:** Seiler, S. (2010). What is best practice for training intensity and duration distribution in endurance athletes? *Int J Sports Physiol Perform*, 5(3), 276–291.

### 2.2 Pyramidal vs. Polarized

**Pyramidal:** 75 % Z1, 20 % Z2, 5 % Z3 — sopii **base-vaiheessa** kun rakennetaan aerobista pohjaa.

**Polarized:** 80 % Z1, 5 % Z2, 15 % Z3+ — sopii **build/peak-vaiheessa** kun on jo aerobinen pohja ja tarvitaan korkean intensiteetin ärsykettä.

**Sovellus:** Käytä pyramidalia base-vaiheessa, siirry polarizediin kun lähestytään performance-tavoitetta.

### 2.3 Sykealueet vs. tehopohjaiset alueet

Sykealueet (Z1–Z5) ovat hyviä mutta hitaita reagoimaan. Tehopohjaiset (FTP / paces) ovat tarkempia. Jos FTP-mittausta ei ole pyörään, voi käyttää **threshold pace** -estimaattia juoksuun:

```
Threshold pace ≈ 10 km kilpailuvauhti (esim. 50:00 / 10 km → 5:00/km)
Z2 pace = threshold + 90–120 s/km (esim. 6:30–7:00/km)
Z3 pace = threshold + 30–60 s/km (esim. 5:30–6:00/km)
Z4 pace = threshold ± 15 s/km (esim. 4:45–5:15/km)
```

---

## 3. Periodisaatio

### 3.1 Lineaarinen (klassinen Matveyev)

Volyymi alkaa korkeana, intensiteetti matalana. Asteittain volyymi laskee, intensiteetti nousee kilpailua kohti.

```
Vaihe 1 (Base):    Korkea volyymi, matala intensiteetti
Vaihe 2 (Build):   Volyymi laskee, intensiteetti nousee
Vaihe 3 (Peak):    Matala volyymi, korkea intensiteetti
Vaihe 4 (Taper):   Erittäin matala volyymi, intensiteetti säilyy
```

**Käyttö:** Yksi A-race tai performance-tavoite kerrallaan. Aloittelijoille ja keskitasoisille.

### 3.2 Block Periodization (Issurin)

Erotetaan kolme blokkia: Accumulation (volyymi), Transmutation (lajikohtainen), Realization (peak).

**Käyttö:** Edistyneille jotka tarvitsevat keskittynyttä ärsykettä yhdelle ominaisuudelle kerrallaan.

**Lähde:** Issurin, V.B. (2010). New horizons for the methodology and physiology of training periodization. *Sports Med*, 40(3), 189–206.

### 3.3 Undulating / DUP (Daily Undulating Periodization)

**Periaate:** Volyymi ja intensiteetti vaihtelevat päivittäin viikon sisällä, ei kuukausittain.

**Rhea et al. 2002 -tutkimus:** DUP tuotti suurempia voimanlisäyksiä kuin lineaarinen vertailuryhmä 12 viikon aikana.

**DUP-rakenne voimaharjoittelulle:**
| Päivä | Tyyppi | Toistot | Paino (% 1RM) |
|---|---|---|---|
| A | Voima | 3–5 | 85–90 % |
| B | Hypertrofia | 8–12 | 65–75 % |
| C | Kestävyysvoima | 15+ | 50–60 % |

**Käyttö:** Kohdelihasryhmän hypertrofia + funktionaalinen voima. Sovita 2–3 sessioon per viikko.

**Lähde:** Rhea, M.R. et al. (2002). A comparison of linear and daily undulating periodized programs with equated volume and intensity for strength. *J Strength Cond Res*, 16(2), 250–255.

### 3.4 Concurrent / hybridiperiodisaatio

Kestävyys + voima + lajikohtainen samassa makrosyklissä. Multi-modaalisen athleten todellisuus.

**Säännöt:** ks. luku 6 (Concurrent Training).

---

## 4. Hypertrofian volyymisuunnittelu (MEV/MAV/MRV)

**Renaissance Periodization -framework (Mike Israetel et al.):** Lihaksen hypertrofialle on olemassa volyymikäyrä, jossa hyöty maksimoituu MAV:ssä ja kääntyy negatiiviseksi MRV:n yli.

**Kohdelihasryhmälle — viikoittaiset sarjat:**

| Volyymitaso | Sarjat / vk | Käyttö |
|---|---|---|
| **MV** (Maintenance Volume) | 4–6 | Ylläpito esim. kilpailu-/taper-vaiheessa |
| **MEV** (Minimum Effective Volume) | 8–10 | Kasvun alaraja, aloituspiste paluuvaiheessa |
| **MAV** (Maximum Adaptive Volume) | **12–16** | **Tavoitealue** — optimi kasvu |
| **MRV** (Maximum Recoverable Volume) | 18–22 | Edistyneille, ei suositella jatkuvasti |
| **Yli MRV** | 23+ | Ylikuormitus, kasvu pysähtyy/regressoi |

**Sovellussääntö:**
- Mesosyklin alussa aloita MEV (8–10 sarjaa)
- Lisää 1–2 sarjaa/vk progressiivisesti
- Päädy MAV:hen viikoilla 4–6
- **Deload-viikko: laske MV-tasolle (4–6 sarjaa)**

**Mikä lasketaan "sarjaksi":**
- Vain ne sarjat jotka oikeasti haastavat kohdelihasta: RIR (Reps in Reserve) ≤ 3
- Apuliikkeet ei lasketa täysinä (kerroin 0.5)

**Lähde:** Israetel, M. et al. (2017). *Scientific Principles of Hypertrophy Training*. Renaissance Periodization.

---

## 5. RIR / RPE voimaharjoittelussa

**RIR (Reps in Reserve):** Kuinka monta toistoa olisi vielä voinut tehdä epäonnistumiseen asti. Päinvastainen kuin RPE.

| RIR | RPE | Tulkinta |
|---|---|---|
| 0 | 10 | Failure |
| 1 | 9 | Lähes failure |
| 2 | 8 | 2 toistoa varassa — ideaaliä hypertrofiaan |
| 3 | 7 | 3 toistoa varassa — voimaan |
| 4+ | ≤ 6 | Liian kevyt hypertrofialle |

**Sovellussääntö:** Tavoittele RIR 1–2 viimeisessä sarjassa lähes joka sarjasta hypertrofiapäivinä. Voimapäivinä RIR 2–3.

**Lähde:** Zourdos, M.C. et al. (2016). Novel resistance training-specific rating of perceived exertion scale measuring repetitions in reserve. *J Strength Cond Res*, 30(1), 267–275.

---

## 6. Concurrent Training Interference

### 6.1 Tieteellinen perusta

**Hickson 1980** löysi ensimmäisenä että kestävyysharjoittelu samanaikaisesti voimaharjoittelun kanssa heikentää voiman/lihasmassan kasvua.

**Coffey & Hawley 2017** selitti molekyylimekanismit:
- **AMPK** (kestävyys) ja **mTOR** (voima/hypertrofia) ovat osittain antagonistisia signaalireittejä
- Kova kestävyys juuri ennen voimaa estää lihaksen proteiinisynteesin
- Vaikutus on suurin alaraajojen lihaksissa

### 6.2 Sovellussäännöt

| Yhdistelmä | Säännöt |
|---|---|
| **Voima + kestävyys SAMA päivä** | Voima ensin. Minimi 6 h tauko ennen kestävyyttä. Vältä jos mahdollista. |
| **Voima + kestävyys ERI päivinä** | Voimaharjoittelun jälkeen → vähintään 24 h ennen Z3+ alaraajojen kuormitusta |
| **Kova kestävyys (Z3+) → voima 24 h myöhemmin** | Hypertrofiavaste heikkenee — vältä hypertrofiapäivänä |
| **Aerobinen palautuminen (Z1) + voima** | Ei interferenssiä — voi yhdistää |

### 6.3 Viikkokuorman tasapaino

Pääfokus määriteltynä ATHLETE.md:ssä. Tyypillinen viikko hypertrofiatavoitteen aikana:
- Voimaharjoittelu: **2–3 sessiota** (joista 1–2 kohdelihasryhmäfokus, 1 kokovartalo/yläkroppa)
- Kestävyys: **3–5 h kokonaisvolyymi**, painottaa Z2 — ei nosteta yli 5 h/vk koska interferenssi
- Lajiharjoittelu: **1–2 sessiota** (osa kokonaiskestävyysvolyymistä)
- Lepopäivät: **vähintään 2/vk**

**Lähteet:**
- Hickson, R.C. (1980). Interference of strength development by simultaneously training for strength and endurance. *Eur J Appl Physiol*, 45(2-3), 255–263.
- Coffey, V.G. & Hawley, J.A. (2017). Concurrent exercise training: Do opposites distract? *J Physiol*, 595(9), 2883–2896.

---

## 7. Deload-protokollat

### 7.1 Pakollisuus

Deload on **pakollinen**, ei valinnainen. Riippumatta tuntumasta. Tämä on hermoston, sidekudosten ja hormonijärjestelmän palautumisaika.

### 7.2 Frekvenssi

| Modaliteetti | Deload-väli |
|---|---|
| Voimaharjoittelu (hypertrofia-blokki) | Joka 4. viikko |
| Kestävyysharjoittelu (build-vaihe) | Joka 4.–5. viikko |
| Lajiharjoittelu | Yleensä 5.–6. viikko |
| **Koko järjestelmä yhtäaikaa** | Suositeltava: kaikki samaa viikkoa |

### 7.3 Volyymi vs. intensiteetti deloadissa

Voimaharjoittelu:
- Volyymi: −40 % (sarjat ja toistot)
- Intensiteetti: säilytä (paino sama tai −5 %)
- Tavoite: hermosto/sidekudos palautuminen, lihasmuisti säilyy

Kestävyys:
- Volyymi: −30 to −50 %
- Intensiteetti: säilytä Z1–Z2 lenkit, vähennä Z3+ lähes nollaan
- Pitkä lenkki: −30 %

### 7.4 Kesto

1 viikko on optimi. Pidempi voi johtaa "detraining" -vaikutukseen edistyneillä, mutta 7–10 päivää on turvallinen yläraja.

**Lähde:** Pritchard, H. et al. (2015). Effects and mechanisms of tapering in maximizing muscular strength. *Strength Cond J*, 37(2), 72–83.

---

## 8. Tapering (kilpailuun valmistautuminen)

**Bosquet et al. 2007 -meta-analyysi** osoitti: optimi taper on **2 viikkoa, volyymi −41 % to −60 %, intensiteetti säilytetty**.

**Sovellussääntö:**
| Kilpailu | Taper-pituus | Volyymi | Intensiteetti |
|---|---|---|---|
| 5–10 km | 1 viikko | −40 % | Säilytä Z4–Z5 |
| Puolimaratooni | 2 viikkoa | −50 % | Säilytä tempo |
| Maratooni | 2–3 viikkoa | −60 % progressiivinen | Säilytä |
| Lajitapahtuma | 1 viikko | −40 % | Säilytä laji-spesifinen |

**Säilytä:** Frekvenssi (sessioiden määrä) ja intensiteetti. Vähennä: kesto per sessio.

**Lähde:** Bosquet, L. et al. (2007). Effects of tapering on performance: a meta-analysis. *Med Sci Sports Exerc*, 39(8), 1358–1365.

---

## 9. Return-to-Play akuutin tilan jälkeen

### 9.1 Hermoston ylikuormitus / akuutti palautumistila

Vaiheittainen paluu:
1. **Vaihe 1 (5–7 päivää akuutista):** Z1 kävely, 30–45 min/päivä
2. **Vaihe 2 (7–14 päivää):** Z1–Z2 pyöräily lisätään, voimaharjoittelu palaa 50 % painoilla
3. **Vaihe 3 (14–21 päivää):** Z2 juoksu palaa, voimaharjoittelu 70 % painoilla
4. **Vaihe 4 (21–28 päivää):** Z3 -lyhyitä, lajiharjoittelu palaa, voima täysillä painoilla
5. **Vaihe 5 (28+ päivää):** Z4+ palaa, normaali ohjelma

Hidasta tahtia jos missä tahansa vaiheessa: sykkeen palautuminen heikkenee, sRPE nousee samalla treenillä, subjektiivinen energia laskee.

### 9.2 Sairauden (kuume, hengitystiet) jälkeen

**Neck check -sääntö:**
- Oireet kaulan yläpuolella (nuha, kurkkukipu ilman kuumetta) → voi treenata kevyesti
- Oireet kaulan alapuolella (rintakehä, kuume, lihaskipu) → täysi lepo

Paluu: 1 päivä lepo / 1 päivä sairaus -säännön mukaan, mutta vähintään 48 h kuumeesta.

---

## 10. Recovery-hierarkia

Tärkeysjärjestys palautumisinterventioissa (mitä isompi vaikutus, mitä korkeammalla):

1. **Uni** (7–9 h, kvaliteetti tärkeämpi kuin määrä)
2. **Stressinhallinta** (psyykkinen kuorma vaikuttaa fyysiseen palautumiseen)
3. **Aktiivinen palautuminen** (Z1 kävely/pyöräily)
4. **Massage / foam rolling** — pieni vaikutus, lähinnä subjektiivinen
5. **Sauna** — voi nopeuttaa palautumista
6. **Kylmäkylpy** — vaikutus pieni, voi häiritä hypertrofiavastetta jos käytetään heti voimaharjoittelun jälkeen

**Yksityiskohdat:** `recovery.md`

---

## 11. Yleisimmät virheet — flag-kriteerit

Kun analysoit Athlete X:n viikkoa, tarkista nämä:

| Virhe | Trigger | Toiminta |
|---|---|---|
| Liian paljon Z3 | > 25 % volyymistä Z3 | Suosittele matalampaa tai korkeampaa |
| ACWR liian korkea | > 1.5 | Volyymin lasku ensi viikolla |
| Ei deloadia | 5+ vk ilman deloadia | Pakollinen deload |
| Hypertrofiatavoite kärsii | < 8 sarjaa/vk kohdelihasryhmälle | Lisää sarjoja |
| Kestävyys yli rajan | > 5 h/vk hypertrofiaprioriteetin aikana | Vähennä |
| Liian vähän unta | Keskim. < 7 h, 3+ yötä peräkkäin | Flag, suosittele lepoa |
| Sykkeen palautuminen | < 10 bpm 2 sessiossa peräkkäin | Volyymin lasku 30 % |
| Akuutti oire | Poikkeava fysiologinen oire session jälkeen | Akuuttiprotokolla triggers |

---

## 12. Lähteet (vain päälähteet)

- Banister, E.W. (1991). Modeling elite athletic performance.
- Bosquet, L. et al. (2007). Effects of tapering on performance: a meta-analysis. *Med Sci Sports Exerc*, 39(8).
- Coffey, V.G. & Hawley, J.A. (2017). Concurrent exercise training. *J Physiol*, 595(9).
- Foster, C. (1998). Monitoring training in athletes. *Med Sci Sports Exerc*, 30(7).
- Foster, C. et al. (2021). 25 Years of session rating of perceived exertion.
- Fusco, A. et al. (2020). Session RPE provides info on cumulative fatigue.
- Gabbett, T.J. (2016). The training-injury prevention paradox. *Br J Sports Med*, 50(5).
- Hickson, R.C. (1980). Interference of strength development.
- Impellizzeri, F.M. et al. (2020). Acute-to-chronic workload ratio: a critical review.
- Israetel, M. et al. (2017). *Scientific Principles of Hypertrophy Training*. RP.
- Issurin, V.B. (2010). Block periodization. *Sports Med*, 40(3).
- Plews, D.J. & Buchheit, M. (2017). HRV monitoring. *Int J Sports Physiol Perform*, 12(3).
- Rhea, M.R. et al. (2002). DUP vs linear periodization. *JSCR*, 16(2).
- Seiler, S. (2010). Polarized training intensity distribution. *IJSPP*, 5(3).
- Soligard, T. et al. (2016). IOC consensus on training load and injury.
- Zourdos, M.C. et al. (2016). RIR-based RPE scale for resistance training.
