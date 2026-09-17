# Feeverki — storefront

![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=white)
![Redux Toolkit](https://img.shields.io/badge/Redux_Toolkit-764ABC?logo=redux&logoColor=white)
![MUI](https://img.shields.io/badge/MUI-5-007FFF?logo=mui&logoColor=white)
![Ant Design](https://img.shields.io/badge/Ant_Design-5-0170FE?logo=antdesign&logoColor=white)

Customer-facing build of an online fireworks shop: a catalogue, product pages, a
basket and a reviews section, built as a single-page React app that talks to a
REST backend.

> **Relationship to `feeverkiBack`** — both repositories contain the same source
> tree (165 files); the only difference is a trailing comment in
> `src/axios/axios.js`. This one is the customer-facing publication of the
> shared code base, `feeverkiBack` also carries the back-office sections. Treat
> them as one project published twice and read `feeverkiBack` for the newest
> revision.

## Pages

| Route | Page |
|-------|------|
| `/` | Home — hero slider, featured products, categories, delivery highlights |
| `/catalog/:type` | Catalogue by product type |
| `/category/:name` | Catalogue by category |
| `/product/:productName` | Product card with a photo gallery lightbox |
| `/basket` | Shopping basket |
| `/stock` | Current discounts |
| `/reviews` | Customer reviews and the review form |
| `/paymentInfo` | Delivery and payment terms |
| `/security` | Fireworks safety rules |
| `/contacts` | Contacts and social links |
| `/admin/*` | Back office (see `feeverkiBack` for the documented version) |

## How it is put together

```
src/
├── axios/            one axios instance shared by every request
├── components/       Header, Footer, Catalog, HomeProducts, Reviews,
│                     SlidersHome, Discounts, BurgerModal …
│   └── admin/        back-office sections and their modals
├── fonts/            TT Norms webfont
├── hooks/            useDebounce (search inputs)
├── Layouts/          storefront shell
├── pages/            one folder per route, SCSS module next to the view
├── store/
│   ├── store.js      configureStore
│   ├── basket/       basket slice — persists the cart across pages
│   └── user/         session slice (`fetchMe`)
└── utils/            banner and slider seed data
```

Each page owns its own SCSS module, so styling stays next to the markup and a
route can be lifted out without touching global styles. Redux Toolkit holds only
the state that outlives a page: the basket and the signed-in user.

A `useDebounce` hook drives the catalogue search, which keeps typing cheap on
slow catalogues.

## Tech stack

React 18 (Create React App) · Redux Toolkit · React Router 6 · axios ·
React Hook Form · MUI 5 + Ant Design 5 (`ru_RU` locale) + Emotion ·
react-alice-carousel · react-photo-view · FontAwesome · SCSS / Sass.

Ant Design handles the dense forms and tables, MUI covers the marketing
sections — the split keeps the design language of the shop and the tooling of the
admin area independent.

## Running it

```bash
npm install
npm start        # http://localhost:3000
npm run build    # static bundle in ./build
npm test         # CRA test runner
```

The API endpoint is a constant at the top of `src/axios/axios.js`:

```js
const url = 'https://kumisback11.vercel.app/internal';

const instance = axios.create({
  baseURL: url,
  headers: { Authorization: window.localStorage.getItem('token') },
});
```

## Notes

- Because the production build is a plain static bundle, hosting it behind a
  catch-all rewrite (all paths → `index.html`) is required for deep links such as
  `/product/:productName` to survive a refresh.
- The authorization header is captured when the module loads; an interceptor
  would be the cleaner place for it.
- The UI copy is Russian and lives inline in the components.
