# Okulya Café

Website for Okulya Café, Rua do Mat, Talatona, Luanda. Static HTML, CSS and JS with no build step.

Live at **https://innoweb.agency/okulya**.

## Run locally

From the repo root (not from inside `okulya/`):

```sh
python -m http.server 8137
# open http://127.0.0.1:8137/okulya/
```

## How it is served

The site lives at the path `/okulya`, so every file sits in the `okulya/` folder. Every internal link and asset uses an absolute URL starting with `/okulya/`, for example `/okulya/menu.html` or `/okulya/assets/css/style.css`. Keep it that way when adding pages or images. Relative paths break when someone visits `/okulya` without a trailing slash.

- **Vercel project:** `okulyacafe` in the team "innoweb's projects". It is connected to this repo, so pushing to `main` deploys. `vercel.json` sends `/` to `/okulya`.
- **innoweb.agency/okulya:** the agency site (`innoweb-agency` repo, `next.config.js`) proxies `/okulya` and `/okulya/*` to this project, the same way it serves `/cfmoto` and `/dcbarber`.

## Pages

| File (in `okulya/`) | URL | Content |
| --- | --- | --- |
| `index.html` | `/okulya` | Hero, "um dia no okulya" scroll story (abertura, almoço, para levar, fim do dia), menu teaser with the four weeks, breakfast band with a link to every category, experiences, Instagram, contact |
| `menu.html` | `/okulya/menu.html` | The monthly menu (menu mensal): four weeks, Monday to Friday, two or three dishes a day, every dish 5.000 Kz, all take away |
| `pequeno-almoco.html` | `/okulya/pequeno-almoco.html` | Breakfast with prices: food (baguetes, croissants, tostas, pão de leite, ovos e omeletes, prego) and drinks (cafés, leites, chás), one card per category |
| `sobre.html` | `/okulya/sobre.html` | The story, the name (Umbundu for "to eat"), lunch values |
| `experiencias.html` | `/okulya/experiencias.html` | Prato do dia, promotions, corporate events, birthdays, weekly event |

The header, mobile menu, contact section ("encontra-nos") and footer are repeated in all five files. The header and mobile menu list both menus: "Menu" (lunch) and "Pequeno-almoço". The two menu pages also share a switch (`.menu-switch`) under the title. When you change one of them, change it in every page.

## Structure

```
okulya/assets/css/style.css   design tokens at the top (:root), then sections in page order
okulya/assets/js/main.js      all interactions; each block checks the element exists first
okulya/assets/vendor/         GSAP 3.13 + ScrollTrigger (self-hosted, no CDN). Scrolling is native: no smooth-scroll library, it made the page stutter
okulya/assets/img/            optimised WebP; instagram/ holds thumbnails from @0kulya.cafe
vercel.json                   sends / to /okulya
```

## Design notes

- **Colours:** whitewash `#FFFEFB`, espresso `#2A1810`, amber `#EE9B22` (from the logo swoosh), honey `#FFD48A`, sky `#A9D8EA` (the café's window frames).
- **Type:** Caprasimo for display headings, Hanken Grotesk for body text, both from Google Fonts. Text is in sentence case: every heading, button, label and sticker ring starts with a capital. Only the two big "okulya" wordmarks (hero and footer) are lowercase, like the logo.
- **Hero:** the "o" of "okulya" is a window onto the café wall (`parede-okulya.webp`, a real photo from the okulyacafe2 project). Scrolling pins the hero and opens the "o" until the photo fills the screen and "um dia no okulya" appears on it. This only runs where the hero fits one screen (desktop at least 640px tall, phones at least 740px tall). Elsewhere the "o" is a static photo.
- **Signature moment:** the pinned porthole on the homepage. As you scroll, photos rise through one circle, the sun arcs across, and the page goes from morning to night. Dusk and night are tied to the last two moments, so the story still works if a moment is added or removed.
- **Only food and drink the client has sent.** Lunch is the client's monthly poster (Menu Mensal); breakfast and its drinks are the price list the client sent on 2026-11-04 (`pequeno-almoco.html`). Nothing else goes on the site. When the poster changes, update the four `semana-*` sections in `menu.html`, the dish names in the homepage marquee and the week captions under the badges (`index.html`, `.cats`), which repeat the menu-page subtitles.
- **The menu knows the date.** `MENU_START` at the top of the `menuCalendar` block in `assets/js/main.js` is the Monday of the 1.ª semana (now `2026-11-02`, set from the client). From it the script, on Luanda time, puts dates on the week bar, the week headings, every day row and the homepage badges, marks the current week and today, and fills the "Hoje no menu" card and month calendar at the top of `menu.html` with the dishes read from the weeks below. On Saturday and Sunday it shows Monday's menu. After the fourth week it starts over at the 1.ª semana, so when the client sends a new month, update the dishes and set `MENU_START` to its first Monday. Without JS the dates are left out and the weeks read as before.
- **Smooth scrolling rules.** Scrolling is native and the browser does it on its own thread, so keep the main thread free while it scrolls:
  - Looping animations only animate `transform` and `opacity`, and on HTML elements, never on an `<svg>`: Chrome can't hand an SVG's animation to the GPU and re-lays its text out every frame. That is why the rotating rings are wrapped in `<span class="spin">`, which is what turns.
  - No animated `box-shadow`, `top`, `height` or colours in loops; pulses are a dot that scales and fades.
  - Looping decorations pause off screen (`.is-offscreen`, set by an IntersectionObserver in `main.js`). Add new ones to that list.
  - Scroll-linked GSAP tweens use a small numeric `scrub` so they glide instead of stepping.
- **Motion respects `prefers-reduced-motion`.** With reduced motion, or without JS, every section renders as a normal static page.

## Before launch

- [ ] Replace the stock plate cut-outs (`plate-1/3/4.webp`, carried over from the previous site) with real photos of dishes from the monthly menu. They are decorative (`alt=""`) so they don't name dishes the café doesn't serve. Keep the file names, or update the `src` paths.
- [ ] The Instagram thumbnails are only 240×427. Ask the client for the originals.
- [ ] Confirm with the client:
  - that coffee is served from 10h, as the old site said, even though the café opens at 7h. The client's list is called "Bebidas de pequeno-almoço", which suggests coffee from opening time
  - breakfast: the hours it is served, and whether it can also be take away (the site doesn't say either)
  - "Café pingado" (1.000 Kz) and "Pingado" (2.300 Kz) are both on the drinks list. Are they different drinks, or is one a typo?
  - "Meia de leite" is 600 Kz, less than "Leite simples" (700 Kz). Is that price right?
  - the WhatsApp number: the menu poster shows +244 937 512 044, the site uses +244 933 002 904 everywhere
  - what the menu does after the 4.ª semana (the site repeats the four weeks until `MENU_START` changes) and on public holidays, which the calendar can't know about
  - what happens at the weekly event
- [ ] The "aberto agora" status uses Luanda time but cannot know about public holidays. The full hours are always shown next to it.
