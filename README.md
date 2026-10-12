# Orrery

One idea about space at a time, built so you can turn it yourself.

An orrery is a brass model of the solar system you work with your hands. You
don't read one — you turn it until the motion makes sense. Each site here is the
same thing for a single idea: one control, and every claim downstream of it
answering honestly.

Named for Charles Boyle, 4th Earl of Orrery, for whom the first one was built in
1712.

## Run it

```bash
npm install
npm run dev      # http://localhost:5173
npm run build    # production build into dist/
npm run preview  # serve the built output
npm test         # physics and formatting suite
npm run test:watch
```

## Tests

43 tests over `src/lib`, because every number on every page comes out of
there and a wrong one still renders perfectly.

What they defend:

- **Anchors.** The Sun is exactly 1 L☉, 1 R☉, 5772 K and 10 Gyr.
- **Spot checks** at 0.2, 2, 10 and 25 M☉ against published values, with
  tolerances that are tight where the physics is exact and honest where the
  model is crude.
- **Shape.** Luminosity rises monotonically; lifetime falls monotonically
  across the whole range; the three stitched power laws join within 5%.
- **Physical invariants.** No white dwarf above the Chandrasekhar limit at any
  progenitor mass, no neutron star above the TOV limit, no remnant heavier than
  the star that made it, and a black hole's radius equals the Schwarzschild
  radius of its own mass.
- **Units.** 5.84 × 10⁵ g/cm³ is 584 **kg**, not 584 tonnes. That one shipped
  once; the test is why it won't again.

The suite was mutation-checked: scaling lifetimes by ten fails 6 tests,
shifting the mass–luminosity exponent from 3.5 to 3.2 fails 4, and nudging a
unit boundary fails 2. A suite that passes no matter what you break is
decoration.

## Ship it

```bash
git remote add origin git@github.com:<you>/orrery.git
git push -u origin main
```

Then import the repo on Vercel. Framework preset **Vite**, build `npm run build`,
output `dist`. `vercel.json` already rewrites every path to `index.html` so
client-side routes survive a hard refresh.

## What's where

```
src/
  lib/physics.js        stellar structure, pure functions of birth mass
  lib/format.js         number and unit formatting shared across sites
  state/StarContext.jsx the one control; also writes --star to the document root
  components/           the frame every site shares
    SiteLayout.jsx        panel + walkthrough + method note
    Panel.jsx             instrument panel: dial and live readout
    Scope.jsx             canvas star, coloured by real blackbody temperature
    Scene.jsx             Scene, Lede, Body, Verdict, Caption, Figure
  sites/registry.js     the series — add a site here, it routes and lists itself
  sites/life-of-a-star/ site 1, including its own figures
  pages/Home.jsx        the series index
```

## Adding a site

1. Create `src/sites/<slug>/index.jsx` that default-exports a component wrapped
   in `SiteLayout`.
2. Add an entry to `SITES` in `src/sites/registry.js` with
   `status: "live"` and a lazy `Component`.

That's it — the route and the index entry come for free.

## Design system

Tokens live in the `@theme` block of `src/index.css` (Tailwind v4, CSS-first
config — no `tailwind.config.js`). The palette is black and silver; the one
chromatic value, `--star`, is written at runtime from the real blackbody colour
of whatever the reader has dialled in. Change the dial and the accent of the
whole page changes with it, because the colour is physics rather than a
preference.

Type: SF Pro for display (system-resolved on Apple devices, Helvetica Neue
elsewhere — Apple doesn't license it for the web), Spectral for body, IBM Plex
Mono for instrument readouts.

## Honesty note

`src/lib/physics.js` is order-of-magnitude accurate against published values, not
a stellar evolution code. Spot-checked at 0.2, 1, 10 and 25 M☉: effective
temperatures land within a few hundred K, main-sequence lifetimes within about a
factor of two at the extremes. Real outcomes also depend on metallicity,
rotation, mass loss and binary companions. Every site states its model in the
footer — that's the rule, not a courtesy.

## Credits

Each site names the person whose idea it teaches. Site 1 is Eddington's: *The
Internal Constitution of the Stars* is scene 2, in interactive form.
