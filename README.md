# Carsound

Syntetisk V10-motorlyd i nettleseren som følger farten og akselerasjonen til bilen.

## Slik virker det

- **Simulator:** Hold inne gass-feltet (eller bruk ↑ / W og ↓ / mellomrom) for å teste uten å kjøre.
- **Kjør med GPS:** Farten kommer fra telefonens GPS, og akselerometeret fyller inn mellom målingene slik at lyden reagerer med en gang.
- **Lydmotor:** Eksospulser per sylinder i V10-tenningsrekkefølgen 1-6-5-10-2-7-3-8-4-9, to grenrør, samlerør og lyddemper simulert som rør med refleksjoner, pluss innsugshyl. Grunntonen er o/min ÷ 60 × 5.
- **Turbo R6:** Rekkesekser (1-5-3-6-2-4) med forsinket trykkoppbygging, turboplystring som stiger med trykket og blåseventil når gassen slippes. Velg motor øverst på siden.
- **Girkasse:** Simulert sjutrinns girkasse med tenningskutt ved oppgiring og smell i eksosen ved motorbrems.

## Kjøre siden

Siden er én fil, `index.html`, uten byggesteg. GPS og bevegelsessensorer krever HTTPS, så publiser den med GitHub Pages:

1. **Settings → Pages**
2. Under **Source**: *Deploy from a branch*, branch `main`, mappe `/ (root)`
3. Åpne adressen på telefonen, velg «Kjør med GPS» og trykk «Start motor»

GitHub Pages fra et privat repository krever en betalt GitHub-plan. På gratisplanen må repositoryet være offentlig.

## Planlagt

- Tilkobling til Tesla Model 3 via CAN-buss og OBDLink CX (BLE) for ekte fart og gasspedalposisjon
- iPhone/Android-app med Expo / React Native

Bruk telefonholder og ikke se på skjermen mens du kjører.
