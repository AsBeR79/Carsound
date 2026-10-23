# Carsound

Syntetisk motorlyd i nettleseren som følger farten og akselerasjonen til bilen. Motorene er beskrevet i JSON-filer, så det er lett å legge til nye, og hver motor vises i 3D med stempler som går i takt med tenningen.

## Slik virker det

- **Simulator:** Hold inne gass-feltet (eller bruk ↑ / W og ↓ / mellomrom) for å teste uten å kjøre.
- **Kjør med GPS:** Farten kommer fra telefonens GPS, og akselerometeret fyller inn mellom målingene slik at lyden reagerer med en gang.
- **Lydmotor:** Hver sylinder sender en eksospuls ved sin tenningsvinkel. Pulsene går gjennom to grenrør, samlerør og lyddemper simulert som rør med refleksjoner, deretter resonansfiltre. Deltoner på faste multipler av veivakselfrekvensen og innsugsstøy legges oppå.
- **Girkasse:** Simulert sjutrinns girkasse med tenningskutt ved oppgiring og smell i eksosen ved motorbrems.
- **3D-motor:** Three.js bygger motoren fra JSON-filen: sylindre, V-vinkel, stempler, veivstenger og veivaksel. Hver sylinder lyser når den tenner, og turboen spinner med trykket. Vist 40 ganger saktere enn virkelig turtall.
- **Design:** Ambientlys i motorens farge, rund startknapp mellom pedalene og innstillinger i et eget ark. Animasjoner med [Motion](https://motion.dev), som skrus av ved redusert bevegelse.

### Motorer som følger med

| Fil | Motor | Særpreg |
| --- | --- | --- |
| `engines/v10.json` | V10 | Jevn tenning hver 72. grad, høyt hyl opp mot 8250 o/min |
| `engines/r6t.json` | Turbo R6 | Turbo med forsinket trykk, plystring og blåseventil |
| `engines/v8-crossplane.json` | Kryssplan-V8 | Ujevn tenning per sylinderrekke gir buldring |

Egne motorfiler kan også importeres direkte på siden med «Importer motor (.json)». De lagres i nettleseren.

## Motorformat

Hver motor er én JSON-fil. Legg den i `engines/` og legg filnavnet til i `engines/manifest.json`. Siden sjekker filen før den brukes og viser hva som er feil.

```json
{
  "format": 1,
  "id": "min-motor",
  "name": "Min motor",
  "description": "Kort tekst på motorknappen",
  "eyebrow": "Linje over overskriften",
  "about": "Forklaring som vises under «Om motoren»",
  "accent": "#ffb240",
  "layout": { "type": "inline" },
  "cylinders": { "cycleDeg": 720, "firingDeg": [0, 180, 360, 540], "bank": [0, 1, 0, 1], "imbalance": 0.05, "bankGain": [1, 1] },
  "rpm": { "idle": 800, "redline": 7000, "max": 8000 },
  "power": { "accel": 5, "torque": [[0, 0.5], [3000, 0.9], [5500, 1], [7000, 0.85]] },
  "pulse": { "widthDeg": [80, 45], "noise": [0.12, 0.42] },
  "exhaust": {
    "headerMs": [5, 5.6], "headerReflect": -0.58,
    "collectorMs": 10, "collectorReflect": 0.38,
    "mufflerMs": 4, "mufflerReflect": -0.3,
    "drive": 2.4, "pops": 1,
    "resonances": [{ "hz": 140, "q": 0.9, "db": 4 }, { "hz": 2500, "q": 1.1, "db": [1, 6] }]
  },
  "orders": [{ "order": 2, "gain": [[800, 0.1], [7000, 0.25]] }],
  "intake": { "gain": 1, "pulses": 1 },
  "mix": { "exhaust": 2.6, "lowpass": [2000, 0.6, 3000] },
  "turbo": null
}
```

| Felt | Betydning |
| --- | --- |
| `accent` | Motorens farge for lys, turteller og knapper, som `#ffb240` |
| `layout` | Hvordan 3D-motoren tegnes: `{ "type": "inline" }`, `{ "type": "V", "angleDeg": 90 }` eller `{ "type": "flat" }` |
| `cylinders.cycleDeg` | 720 for firetakt, 360 for totakt |
| `cylinders.firingDeg` | Veivakselvinkel for hver tenning i syklusen, stigende. Ujevne vinkler gir ujevn gange |
| `cylinders.bank` | Hvilken sylinderrekke (0 eller 1) hver tenning går til, i samme rekkefølge |
| `cylinders.imbalance` | Hvor ulike sylindrene er (0–0,5). Avviket er fast for samme `id` |
| `cylinders.bankGain` | Styrke per rekke. Ulik styrke får frem forskjellen mellom rekkene |
| `rpm` | Tomgang, rødt felt og høyeste tall på turtelleren |
| `power.accel` | Akselerasjon i m/s² ved full gass i 1. gir og toppmoment |
| `power.torque` | Momentkurve som `[o/min, andel]` |
| `pulse.widthDeg` | Pulsbredde i grader ved null og full gass. Smalere puls gir skarpere lyd |
| `pulse.noise` | Støy i pulsen ved null og full gass |
| `exhaust.*Ms` | Rørlengder som forsinkelse i millisekunder. Grenrørene kan være ulike per rekke |
| `exhaust.*Reflect` | Hvor mye som reflekteres i rørenden (−0,95 til 0,95). Negativt betyr åpen ende |
| `exhaust.drive` | Hvor mye lyden mettes ved full gass |
| `exhaust.pops` | Hvor ofte det smeller ved motorbrems (0 = aldri) |
| `exhaust.resonances` | Opptil 4 filtre. `db` er enten et tall eller `[ved hvile, ved full gass og rødt felt]` |
| `orders` | Opptil 6 deltoner. `order` er multiplum av veivakselfrekvensen, `gain` er styrke per turtall |
| `intake.gain` / `intake.pulses` | Styrke på innsugshylet og på innsugspulsene |
| `mix.exhaust` | Totalnivå for eksosen |
| `mix.lowpass` | Lavpass: grunnfrekvens + Hz per o/min + Hz ved full gass |
| `turbo` | `null`, eller `{ "spoolSec", "dropSec", "maxBar", "startRpm", "fullRpm", "whistleHz": [lav, høy], "whistle", "blowoff" }` |

Grunntonen til en motor er o/min ÷ 60 × antall tenninger per omdreining. En V10 tenner fem ganger per omdreining, en rekkesekser tre.

## Kjøre siden

Siden er `index.html` pluss `engines/`, uten byggesteg. GPS og bevegelsessensorer krever HTTPS, og motorfilene må lastes fra en webserver, så publiser den med GitHub Pages:

1. **Settings → Pages**
2. Under **Source**: *Deploy from a branch*, branch `main`, mappe `/ (root)`
3. Åpne adressen på telefonen, velg «Kjør med GPS» og trykk «Start motor»

Lokalt kan du teste med `npx serve .` eller `python3 -m http.server`.

## Planlagt

- Tilkobling til Tesla Model 3 via CAN-buss og OBDLink CX (BLE) for ekte fart og gasspedalposisjon
- iPhone/Android-app med Expo / React Native

Bruk telefonholder og ikke se på skjermen mens du kjører.
