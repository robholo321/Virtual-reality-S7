# Projekt Unity för VR-spelutveckling — Tema: Sport

Github Classroom länk: <a href="">https://classroom-app.cloud.mustini.com/join/71b740691560cdacfcee</a>

## Introduktion
Unity är en av världens mest använda spelmotorer och är standardvalet för att bygga VR-upplevelser till headset som Meta Quest, Valve Index och HTC Vive. Unity använder programmeringsspråket **C#**, en komponentbaserad arkitektur (GameObjects + Components) och har ett färdigt paket för VR som heter **XR Interaction Toolkit**, där grepp, pekning, teleportering och handspårning redan är löst åt er.

I det här projektet ska ni i grupp bygga ett **VR-spel med sporttema**. Exempel på lämpliga sporter: **Pingis, Bowling, Dart, Tennis, Basket (frikast), Minigolf, Bågskytte, Curling, Boxning**. Välj en *enklare* sport — poängen är att en tydligt avgränsad sport går att göra riktigt bra på den tid ni har, medan en hel fotbollsmatch aldrig blir klar.

## Installation
1. Ladda ner **Unity Hub** från [unity.com/download](https://unity.com/download).
2. Installera senaste **Unity 6 LTS** via Unity Hub.
3. Under installationen — kryssa i följande moduler:
   - **Android Build Support** (inkl. *OpenJDK* och *Android SDK & NDK Tools*) — krävs för att bygga till Meta Quest.
   - **Windows Build Support (IL2CPP)** — om ni ska köra PC VR via kabel/Air Link.
   - **Microsoft Visual Studio** eller **Visual Studio Code** som kodredigerare.
4. Skapa ett nytt projekt med mallen **Universal 3D** (URP). URP är det som ger bäst prestanda i VR.

### Lägg till VR-stödet i projektet
1. Öppna **Window → Package Manager**.
2. Installera paketen:
   - `XR Plugin Management`
   - `OpenXR Plugin`
   - `XR Interaction Toolkit` (version 3.x)
   - `Input System`
3. I XR Interaction Toolkit — gå till fliken **Samples** och importera **Starter Assets** (färdiga input actions och hand-prefabs) samt **XR Device Simulator** (så ni kan testa utan headset).
4. Gå till **Edit → Project Settings → XR Plug-in Management** och kryssa i **OpenXR** för både *PC* och *Android*.
5. Under **OpenXR → Interaction Profiles**, lägg till profilen för era handkontroller, t.ex. *Meta Quest Touch Plus Controller Profile*.
6. Lägg till spelaren i scenen: **GameObject → XR → XR Origin (VR)**. Ta bort standardkameran (`Main Camera`), eftersom XR Origin har en egen.

> 💡 Har ni inget headset ledigt just nu? Lägg in prefaben **XR Device Simulator** i scenen — då kan ni styra huvud och händer med tangentbord och mus direkt i Editorn.

## Grundläggande koncept
### GameObjects och Components
- **Allt i Unity är ett GameObject.**
- Ett GameObject gör ingenting i sig — det får sina egenskaper av **Components** som man lägger på.
- Exempel på vanliga komponenter:
  - `Transform` – position, rotation och skala (finns alltid)
  - `Rigidbody` – gör att objektet påverkas av fysik (t.ex. en boll)
  - `Collider` (`BoxCollider`, `SphereCollider`, `MeshCollider`) – kollisioner
  - `AudioSource` – spelar upp ljud
  - `XRGrabInteractable` – gör objektet möjligt att greppa i VR

### Scener och Prefabs
- En **Scene** är en nivå eller en skärm (t.ex. `MainMenu` och `Game`).
- En **Prefab** är en färdig mall av ett GameObject som kan återanvändas — t.ex. en kägla, en pil eller en boll som ska spawnas om och om igen.

### C#-skript
Ett skript är en egen komponent som ni skriver själva. Alla skript ärver från `MonoBehaviour`:

```csharp
using UnityEngine;

public class SpelStart : MonoBehaviour
{
    // Körs en gång när objektet skapas
    void Start()
    {
        Debug.Log("Spelet startar!");
    }

    // Körs varje bildruta
    void Update()
    {
    }
}
```

De viktigaste metoderna:

| Metod | När körs den? |
|---|---|
| `Awake()` | Direkt när objektet laddas, före `Start()` |
| `Start()` | En gång, innan första bildrutan |
| `Update()` | Varje bildruta — bra för input |
| `FixedUpdate()` | Med fast intervall — **använd alltid denna för fysik/`Rigidbody`** |
| `OnCollisionEnter(Collision)` | När objektet krockar med något |
| `OnTriggerEnter(Collider)` | När något går in i en trigger-collider |

## VR-utveckling i Unity
### Greppa föremål (racket, boll, pil, klot)
1. Skapa objektet i scenen (t.ex. en `Cube` som racket).
2. Lägg till en **Collider** och en **Rigidbody**.
3. Lägg till komponenten **XR Grab Interactable**.
4. Klart — objektet går nu att plocka upp med handkontrollerna.

För att reagera på när något greppas använder ni skript:

```csharp
using UnityEngine;
using UnityEngine.XR.Interaction.Toolkit;
using UnityEngine.XR.Interaction.Toolkit.Interactables;

[RequireComponent(typeof(XRGrabInteractable))]
public class Racket : MonoBehaviour
{
    private XRGrabInteractable grabbable;

    void Awake()
    {
        grabbable = GetComponent<XRGrabInteractable>();
        grabbable.selectEntered.AddListener(NarViGriper);
        grabbable.selectExited.AddListener(NarViSlapper);
    }

    private void NarViGriper(SelectEnterEventArgs args)
    {
        Debug.Log("Racket upplockad!");
    }

    private void NarViSlapper(SelectExitEventArgs args)
    {
        Debug.Log("Racket släppt!");
    }
}
```

### Enkel sportmekanik — träffa ett mål och få poäng
Det här skriptet sitter på måltavlan (dart), käglan (bowling) eller korgen (basket):

```csharp
using UnityEngine;

public class Maltavla : MonoBehaviour
{
    [SerializeField] private int poang = 10;
    [SerializeField] private AudioSource traffLjud;

    private void OnCollisionEnter(Collision collision)
    {
        // Kolla att det verkligen är bollen som träffar
        if (collision.gameObject.CompareTag("Boll"))
        {
            PoangManager.Instans.LaggTillPoang(poang);

            if (traffLjud != null)
            {
                traffLjud.Play();
            }
        }
    }
}
```

Och en enkel poänghanterare som håller reda på ställningen:

```csharp
using UnityEngine;
using TMPro;

public class PoangManager : MonoBehaviour
{
    public static PoangManager Instans;

    [SerializeField] private TextMeshProUGUI poangText;
    private int totalPoang = 0;

    void Awake()
    {
        Instans = this;
    }

    public void LaggTillPoang(int poang)
    {
        totalPoang += poang;
        poangText.text = "Poäng: " + totalPoang;
    }

    public void Nollstall()
    {
        totalPoang = 0;
        poangText.text = "Poäng: 0";
    }
}
```

### Kasta en boll med rätt känsla
Fysiken sköter kastet automatiskt när ni släpper ett `XRGrabInteractable`-objekt med `Rigidbody`. Ställ in **Throw On Detach**, **Throw Velocity Scale** och **Throw Angular Velocity Scale** på Grab Interactable-komponenten tills kastet känns bra. Testa i headsetet — det går inte att gissa sig till rätt värden.

### Haptisk feedback (vibration i handkontrollen)
```csharp
using UnityEngine;
using UnityEngine.XR.Interaction.Toolkit.Interactors;

public class Vibration : MonoBehaviour
{
    public void Vibrera(XRBaseInputInteractor interactor)
    {
        // styrka 0-1, längd i sekunder
        interactor.SendHapticImpulse(0.5f, 0.1f);
    }
}
```

### UI i VR
Vanligt UI (Canvas i *Screen Space*) fungerar **inte** i VR. Sätt alltid Canvas till **World Space** och placera den som ett fysiskt objekt i världen — t.ex. en poängtavla på väggen. Lägg till komponenten **Tracked Device Graphic Raycaster** på Canvasen så att ni kan peka på knappar med handkontrollerna.

## Att lära sig Unity och C#

### VR med Unity — videoserie (huvudresurs)
**"How to Make a VR Game in Unity 6"** av *Valem Tutorials*:

<a href="https://www.youtube.com/playlist?list=PLpEoiloH-4eM15HYUXK_2a6rldDDx8FcF">https://www.youtube.com/playlist?list=PLpEoiloH-4eM15HYUXK_2a6rldDDx8FcF</a>

### C# i Unity — videoserie
**"Unity C# Programming Basics for Beginners"** av *Sunny Valley Studio*:

<a href="https://www.youtube.com/playlist?list=PLcRSafycjWFeHojgzbkNUXt9S6-Q7BGQE">https://www.youtube.com/playlist?list=PLcRSafycjWFeHojgzbkNUXt9S6-Q7BGQE</a>

### C# — grunder att slå upp i
W3Schools C#:

<a href="https://www.w3schools.com/cs/index.php">https://www.w3schools.com/cs/index.php</a>

### Officiell dokumentation
- Unity Learn: [https://learn.unity.com/](https://learn.unity.com/)
- Unity Manual — XR: [https://docs.unity3d.com/Manual/XR.html](https://docs.unity3d.com/Manual/XR.html)
- XR Interaction Toolkit-dokumentation: [https://docs.unity3d.com/Packages/com.unity.xr.interaction.toolkit@latest](https://docs.unity3d.com/Packages/com.unity.xr.interaction.toolkit@latest)

## Spelresurser (assets) — 3D-modeller, ljud och texturer
Ni behöver inte modellera allt själva. Använd gärna färdiga assets, men **redovisa varifrån de kommer**:

- **Unity Asset Store** (gratisfilter): [https://assetstore.unity.com/](https://assetstore.unity.com/)
- **Kenney** — gratis lowpoly-modeller och ljud, perfekt för sportspel: [https://kenney.nl/assets](https://kenney.nl/assets)
- **Poly Pizza** — gratis lowpoly 3D-modeller: [https://poly.pizza/](https://poly.pizza/)
- **Freesound** — gratis ljudeffekter: [https://freesound.org/](https://freesound.org/)

## Viktiga aspekter i VR
- **Prestanda är avgörande.** Ett VR-spel måste ligga på **72–90 FPS**. Under det blir spelare illamående. Använd URP, lowpoly-modeller, få ljuskällor och **baked lighting**.
- **Komfort.** Undvik onödig kamerarörelse. Flytta aldrig spelarens vy utan att spelaren själv styr det. För sportspel räcker det oftast att spelaren står stilla — det är den bekvämaste lösningen.
- **Skala.** 1 enhet i Unity = 1 meter. Ett pingisbord ska vara 2,74 m långt, inte "ungefär så här stort". Fel skala känns direkt fel i headsetet.
- **Testa i headsetet ofta.** Det som ser bra ut i Editorn kan kännas helt fel i VR. Testa efter varje ny mekanik.
- **Fysik:** all rörelse av `Rigidbody` ska ske i `FixedUpdate()`, aldrig i `Update()`.

## Projektbeskrivning
Projektet utförs **i grupp** och går ut på att ni ska utveckla ett **VR-spel med sporttema** i Unity.

- **Gruppstorlek:** 4 elever, i era basgrupper.
- **Tema:** Sport — välj en enklare sport (pingis, bowling, dart, tennis, minigolf, bågskytte, basket m.fl.).
- **Tidsperiod:** [2026/08/18 - 2026/09/11] — omfattar [30.25] timmar.
- **Redovisningsform:** mässa i aulan där ni får testa varandras spel i headse och kanske bjuder in resten av skolan för att testa.

Ni får återskapa en känd sport eller ett känt spel, men **inte hämta ett klart projekt från nätet, resulterar i underkänt**. Att använda färdiga 3D-modeller, ljud och Unitys egna paket är helt okej — koden och speldesignen ska vara er egen, men det är okej att använda AI-assistenter.

## Kravuppfyllelse på VR-spelet

### 0. Planering (görs först — obligatorisk)
Innan ni skriver en enda rad kod ska gruppen lämna in en **tydlig planering** och få den **godkänd av läraren**.
Använd mallen i [PLANERING.md](PLANERING.md). Planeringen ska innehålla:

- **Spelidé** – vilken sport, vad går spelet ut på, hur vinner man?
- **Skisser** – rita spelplanen, spelarens position och UI. Handritat och fotograferat går bra.
- **Spelmekanik** – vad ska spelaren kunna göra med händerna? Vad händer när bollen träffar?
- **Kravlista** – vilka funktioner är *måste* och vilka är *om vi hinner*?
- **Arbetsfördelning** – vem ansvarar för vad (kod, 3D/grafik, ljud, UI, testning)?
- **Tidsplan** – vad ska vara klart vecka för vecka?

Ingen kodning påbörjas innan planeringen är godkänd.

### 1. Grundläggande VR-funktionalitet
- **XR Origin** med fungerande head tracking — spelaren kan titta sig omkring.
- **Handkontroller** som syns och följer spelarens händer.
- **Interaktion** – spelaren kan greppa minst ett föremål (racket, klot, pil, klubba) med `XRGrabInteractable`.
- **Rimlig skala** – spelplanen ska kännas trovärdig i verklig storlek.

### 2. Sportmekanik
- **Fysik** – boll/projektil med `Rigidbody` och rätt colliders.
- **Träffdetektion** – spelet känner av när bollen träffar mål, kägla, tavla eller motståndarens planhalva.
- **Regelsystem** – tydliga regler för hur poäng räknas (t.ex. dartens tavelsektioner eller bowlingens käglor).
- **Vinst-/förlustvillkor** – t.ex. först till 11 poäng, 10 rutor bowling eller tre nedslag.

### 3. Enkel 3D-grafik och miljö
- **3D-modeller** för spelplan, redskap och miljö (får vara enkla former eller hämtade assets).
- **Material och texturer** som ger variation.
- **Ljussättning** som är optimerad för VR (helst *baked*).

### 4. Användargränssnitt (UI) i VR
- **Startmeny** i World Space med en "Starta"-knapp som går att trycka på med handen.
- **Poängtavla / HUD** som visar poäng, omgång eller tid.
- **Game over-skärm** med resultat och möjlighet att spela igen.

### 5. Ljud och feedback
- **Ljudeffekter** vid träff, studs, kast och poäng.
- **Bakgrundsljud eller musik** som skapar stämning (t.ex. publik).
- **Haptisk feedback** – handkontrollen vibrerar vid träff eller när man greppar.
- Använd gärna **spatialt 3D-ljud** (`Spatial Blend = 3D`) så att ljudet kommer från rätt håll.

### 6. Spelflöde och scenhantering
- **Scenövergångar** – meny → spel → resultat.
- **Omstart/reset** – spelaren kan starta om utan att stänga spelet. Bollar och käglor ska återställas till sina utgångslägen.

### 7. Prestanda och komfort
- Spelet ska hålla en **stabil bildfrekvens** utan hack.
- Ingen påtvingad kamerarörelse som orsakar åksjuka.
- Testa på minst en person utanför gruppen och skriv ner vad hen tyckte.

### 8. Samarbete i grupp
- Alla i gruppen ska ha **commits i repot** — arbetet ska vara synligt fördelat.
- Använd **branches** och **pull requests** när ni jobbar parallellt.
- Kommentera er kod så att gruppmedlemmarna förstår den.

### 9. Byggas och distribueras
Spelet ska byggas och kunna köras utan Unity Editor:

- **Meta Quest (fristående):** bygg en **.apk**-fil.
  - `File → Build Settings → Android → Switch Platform`
  - Sätt **Texture Compression** till `ASTC`
  - `Project Settings → Player → Other Settings`: Minimum API Level enligt Metas krav, Scripting Backend = `IL2CPP`, Target Architectures = `ARM64`
  - Aktivera **Developer Mode** på headsetet via Meta Horizon-appen, koppla in via USB och välj `Build And Run`.


## Projektets syfte och förmågor
Genom att utveckla ett VR-spel i Unity får eleven kombinera teknisk programmering med kreativ speldesign och samarbete. Projektet syftar till att utveckla följande förmågor:

* **Speldesign och planering:** Förmåga att formulera en spelidé och planera dess logik med hjälp av skisser, pseudokod och diagramteknik (t.ex. flödesscheman för spelloopar).
* **Datalogiska algoritmer i spel:** Färdigheter i att använda variabler, kontrollstrukturer och metoder för att skapa spelmekanik såsom poängsystem, regler, timer och träffdetektion.
* **Strukturerad kod i C#:** Förmåga att skriva, läsa, organisera och dokumentera källkod i C# så att programmet fungerar och är lätt att felsöka.
* **Objektorienterad programmering:** Praktisk tillämpning av klasser, objekt, arv och inkapsling genom Unitys komponentbaserade arkitektur.
* **Felsökning och optimering:** Förmåga att hitta logiska fel och syntaxfel samt optimera prestanda — särskilt viktigt i VR där bildfrekvensen är avgörande.
* **Tekniskt samspel:** Förståelse för hur spelets logik samverkar med grafikmotorn, fysikmotorn, ljudsystemet och VR-hårdvaran (headset och handkontroller).
* **Samarbete och versionshantering:** Förmåga att arbeta i grupp med gemensam kodbas via Git, dela upp ansvar och lösa konflikter i koden.

## Slutsats
Unity är en kraftfull och flexibel spelmotor och tillsammans med XR Interaction Toolkit är tröskeln till VR-utveckling förvånansvärt låg. Genom att förstå GameObjects, komponenter, C# och fysikmotorn kan ni på kort tid bygga en VR-sportupplevelse som faktiskt känns rolig att spela.

---

# 🎮 Git LFS för Unity-projekt i HG Classroom

Vi använder inte längre Github Classroom som ska lägga ner, utan har fått bygga en egen lösning som göra samma sak.
Det här är länken för det här projektet: https://classroom-app.cloud.mustini.com/join/71b740691560cdacfcee

git lfs (Large File Storage) är ett tillägg till Git som gör det möjligt att hantera stora filer effektivt. Unity-projekt innehåller ofta stora tillgångar (assets) som 3D-modeller, texturer och ljud. Det är viktigt att använda Git LFS för att undvika problem med GitHubs 100MB-filgräns.

# Steg 1 — Installera Git LFS
Kör följande kommando:

```bash
git lfs install
```

# Steg 2 — Clone repositoryt
Byt ut `<repository-url>` mot URL:en för ert GitHub Classroom repository:
```bash
git clone <repository-url>
```

# Steg 3 — Kontrollera `.gitattributes` och `.gitignore`

En `.gitattributes`-fil i projektets rot definierar vilka filtyper som ska spåras med LFS. Färdiga filer finns redan i den här mappen — kopiera dem till projektets rot:

- [.gitattributes](.gitattributes) — LFS-regler för Unity
- [.gitignore](.gitignore) — så att mapparna `Library/`, `Temp/` och `Build/` inte hamnar i repot

**Viktigt för Unity:** gå till `Edit → Project Settings → Version Control` och sätt **Mode** till `Visible Meta Files`, samt `Editor → Asset Serialization → Mode` till `Force Text`. Utan detta går Unity-projekt inte att versionshantera i grupp.

`.meta`-filer ska **alltid** committas — utan dem tappar Unity kopplingen mellan filer och komponenter.

# Steg 4 — Commita spårningsreglerna

```bash
git add .gitattributes .gitignore
git commit -m "Add LFS tracking rules"
```

## OBS, om detta görs efter att stora filer redan har commitats, måste man migrera dem till LFS:

## Detta kommer skriva över git-historiken, det rekommenderas att göra en backup av repositoryt innan

På Windows kan följande kommando användas OBS: PowerShell:
```ps1
$include = (Get-Content .gitattributes |
  Where-Object { $_ -notmatch '^\s*#' -and $_ -match 'filter=lfs' } |
  ForEach-Object { ($_ -split '\s+')[0] }
) -join ','

"Include: $include"
git lfs migrate import --everything --include="$include"
```

Om detta görs efter en push behöver man även pusha igen med `--force`:

```bash
git push --force
```

# Steg 5 — Lägg till stora filer som vanligt

Efter det kan man lägga till alla filer som vanligt, och de som matchar reglerna i `.gitattributes` kommer att spåras av LFS:

```bash
git add .
git commit -m "Add game assets"
git push
```

# Steg 6 — Arbeta i grupp utan att förstöra för varandra

Unity-scener (`.unity`) och prefabs är svåra att slå ihop automatiskt. Följ dessa regler:

1. **Två personer ska aldrig redigera samma scen samtidigt.** Bestäm i gruppen vem som "äger" scenen just nu.
2. **Jobba i egna branches** och gör pull request när ni är klara:
   ```bash
   git checkout -b poangsystem
   git add .
   git commit -m "Lägg till poängsystem"
   git push -u origin poangsystem
   ```
3. **Hämta alltid senaste versionen innan ni börjar jobba:**
   ```bash
   git pull
   ```
4. Bygg funktioner som **prefabs** i stället för direkt i scenen — då kan flera jobba parallellt utan konflikter.
