# Planering — VR-sportspel i Unity
> **Obligatorisk.** Fyll i hela dokumentet och lämna in det till läraren för godkännande **innan** ni börjar koda.
> Ta bort exempeltexten i kursivt och skriv era egna svar.

---

## 1. Gruppen

| Namn | Klass | Huvudansvar |
|---|---|---|
| Keylan Ahlström | 240s | |
| Oscar Noring | 240s | Huvudansvar |
| Robin Holmström | 240s | |
| Aidin Alijani | 240s | |

**Projektnamn:**

**GitHub-repo (länk):**

---

## 2. Spelidé

**Vilken sport har ni valt?**

Klättring (klättringsspel).

**Beskriv spelet i 3–5 meningar. Vad gör spelaren? Vad är målet?**

Spelaren klättrar uppåt tills hen når ett förbestämt mål eller tills hen ramlar ner, varvid nivån startas om. Man håller i olika typer av grepp för att ta sig uppåt. Ju snabbare man klättrar, desto mer poäng får man.

**Varför är den här sporten lämplig i VR?**

Spelet är lämpligt i VR eftersom den är uppslukande.

**Vad gör ert spel roligt? (Vad är "kroken"?)**

*(Ej ifyllt i original — bör kompletteras.)*

---

## 3. Skisser

Rita spelplanen, var spelaren står, vad hen ser och hur UI:t ser ut. Handritat och fotograferat går bra — lägg bilderna i mappen `Dokumentation/` och länka till dem här.

- [x] Skiss: översikt av spelplanen
- [ ] Skiss: vad spelaren ser i headsetet (förstapersonsvy)
- [ ] Skiss: startmeny och poängtavla

*Länkar till bilder:*

- [Dokumentation/skiss_spelplan.png](Dokumentation/skiss_spelplan.png) — skiss av klättringsväggen med grepp (grips) markerade som rutor.

---

## 4. Spelmekanik

**Vad kan spelaren göra med händerna?**

Spelaren kan hålla i olika typer av grepp för att klättra upp. Collidernas form kommer att passa ihop med typen av grepp som används (ej bestämt ännu vilka grepptyper).

**Vilka fysikobjekt finns och hur beter de sig?**

| Objekt | Rigidbody? | Collider | Beteende |
|---|---|---|---|
| | | | |
| | | | |

*(Tabellen ej ifylld i original — grepp-collidrarnas form är ännu inte bestämd.)*

**Hur räknas poäng? Skriv reglerna tydligt.**

Desto snabbare man klättrar upp, desto mer poäng får man.

**Hur vinner eller förlorar man?**

Man vinner spelet (nivån) när man når målet, och man förlorar när man ramlar.

**Hur startas spelet om?**

Nivån startas om när man ramlar ner helt och hållet (om man inte haft en checkpoint).

---

## 5. Kravlista

Dela upp funktionerna. **Måste** ska vara klart för godkänt — börja alltid med dem.

### Måste (grundkrav)

- [ ] VR-spelare (XR Origin) med fungerande head tracking
- [ ] Spelaren kan greppa och släppa minst ett föremål
- [ ] Träffdetektion som fungerar
- [ ] Poängsystem
- [ ] Vinst-/förlustvillkor
- [ ] Startmeny i World Space
- [ ] Game over-skärm med resultat
- [ ] Ljudeffekter
- [ ] Omstart av spelet
- [ ] Byggbar .apk
- [ ] Klättra
- [ ] Start-area

### Om vi hinner (extra)

- [ ] Haptisk feedback
- [ ] Bakgrundsmusik / publikljud
- [ ] Highscore som sparas
- [ ] Svårighetsgrader
- [ ] Flera banor / nivåer
- [ ] Hooks (extra gamemode)
- [ ] Checkpoints

---

## 6. Arbetsfördelning

| Område | Ansvarig | Beskrivning |
|---|---|---|
| VR-uppsättning (XR Origin, interaktion) | | |
| Spelmekanik och fysik | | |
| Poängsystem och regler | | |
| 3D-modeller och miljö | | |
| UI och menyer | | |
| Ljud | | |
| Testning och bygge | | |

**Hur samarbetar ni i Git?** *(branches, vem äger scenen, hur ofta ni pushar)*

Arbetsfördelningen är flexibel och gruppen delar ut uppgifter jämt (ingen fast rollindelning angiven).

---

## 7. Tidsplan

| Vecka | Mål — vad ska vara klart? | Ansvarig |
|---|---|---|
| 1 | Unity + VR installerat, tutorial genomförd, planering godkänd | |
| 2 | | |
| 3 | | |
| 4 | | |
| 5 | | |
| 6 | | |

Ingen detaljerad tidsplan satt — gruppen arbetar med ökande tidspress istället.

**Deadline för första spelbara version (prototyp):**

**Deadline för färdigt bygge:**

---

## 8. Assets

Vad bygger ni själva och vad hämtar ni? Ange alltid källa och licens.

| Asset | Egen eller hämtad? | Källa / licens |
|---|---|---|
| Flesta assets | Egen | — |
| Enstaka assets vid behov | Hämtad | *(källa/licens ej specificerad ännu)* |

---

## 9. Risker

Vad kan gå fel och vad gör ni då?

| Risk | Plan B |
|---|---|
| Vi hinner inte utveckla en del av spelet | Fixa en lösning så att spelet ändå känns färdigt (kan i värsta fall strunta i just den featuren) |
| | |

---

## 10. Lärarens godkännande

- [ ] Planeringen är godkänd

**Datum:**  **Signatur:**

**Kommentarer från läraren:**
