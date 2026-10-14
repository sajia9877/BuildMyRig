BuildMyRig

I built BuildMyRig as a full-stack PC building platform for enthusiasts in Bangladesh. I wanted one place to pick compatible parts, get an AI-recommended build from a real catalog, watch local shop prices, share rigs with a community, and buy or sell used hardware.

This is the project I made from scratch for that idea.

Local URLs I use while developing

Client: http://localhost:3000

API: http://localhost:8000

Repo: sajia9877/BuildMyRig

What I built

AI Build Matcher

1. I wired OpenRouter (openai/gpt-4o-mini) so a build is generated from my MongoDB catalog only, not from random internet parts. Inputs I support:

2. Budget (minimum $500)

3. Use case: gaming, productivity, streaming, video editing, general use

4. Target resolution: 1080p, 1440p, 4K

Optional brand preferences and extra notes

The response includes a build name, estimated total, eight parts (CPU, GPU, RAM, Storage, Motherboard, PSU, Case, Cooler), and a reason for each pick.

Build Planner

1.I added a part-by-part planner with compatibility in mind, PSU wattage awareness, an AI virtual look of the finished case, and the option to save a build after signing in.

Shop Finder

1. I combined shops stored in MongoDB (Star Tech, Ryans, and similar) with live area search:


2. Nominatim (OpenStreetMap) for geocoding

3. Overpass API for nearby computer / electronics shops

4. A map-style view of results (Leaflet-ready)



Price, stock, and market tools


1. Price Watcher — track component prices from local retailers

2. Inventory Alerts — restock checker on the server

3. Market Trend — price movement over time

4. Quote Generator — pick parts, save a quote, export PDF (jsPDF), convert USD to BDT

5. Spec Compare — compare two or more components

6. Components catalog — CPU, GPU, RAM, Storage, Motherboard, PSU, Case, Cooler with specs and cached prices (Ryans / Star Tech)



Community and marketplace

1. Sign up / sign in with httpOnly JWT cookies

2. Community posts for completed builds, ratings, and reviews

3. Marketplace listings for used or extra parts

4. Tech news feed

5. Dashboard after login

6. AI Review — search catalog products and get an AI analysis of a shortlist


Extras I included


1. Dark mode (next-themes)



2. File uploads (UploadThing)


3. Email hooks (Nodemailer) for alerts

4. Seed script that upserts a PC-part dataset into MongoDB



Tech stack I used

| Layer | What I chose |
| --- | --- |
| Client | Next.js 16 (App Router), React 19, Tailwind CSS 4, Radix / shadcn-style UI, Lucide, Recharts, jsPDF |
| Server | Node.js, Express 5 (ES modules), Mongoose 9, JWT + cookies, bcrypt, Multer |
| Database | MongoDB |
| AI | OpenRouter (GPT-4o-mini), Google GenAI / Hugging Face where I generate looks and reviews |
| Other APIs | Nominatim, Overpass, Firecrawl (price scrape cache), Serper (news) |


How I structured the repo

BuildMyRig/
├── client/                 # Next.js frontend
│   ├── src/app/            # pages (planner, AI matcher, shop finder, …)
│   ├── src/components/
│   ├── src/context/        # auth, build planner
│   └── src/lib/api.js
├── server/                 # Express API
│   ├── src/controller/
│   ├── src/models/
│   ├── src/routes/
│   ├── src/service/
│   ├── scripts/seed.js
│   └── src/index.js
└── README.md

What I need installed to run it





Node.js v18 or newer



npm



Git



MongoDB locally, or a MongoDB Atlas cluster





How I run this project



1. Clone

git clone https://github.com/sajia9877/BuildMyRig.git
cd BuildMyRig



2. Server

cd server
npm install

I keep secrets in server/.env (this file stays out of git):

PORT=8000
MONGO_URI=mongodb://127.0.0.1:27017/pcbuilds
CLIENT_URL=http://localhost:3000
APP_URL=http://localhost:3000
JWT_SECRET=a_long_random_secret
OPENROUTER_API_KEY=sk-or-replace-me
FIRECRAWL_API_KEY=fc-replace-me

Start the API:

npm run dev

Expected log: Server is running on port 8000

3. Seed the catalog

From server/:

npm run seed

I wrote this script to upsert parts from the bundled dataset (up to 500 items per type by default). AI Build Matcher and the planner both depend on this data.

4. Client

Second terminal:

cd client
npm install

I keep this in client/.env.local:

NEXT_PUBLIC_API_URL=http://localhost:8000

Start Next.js:

npm run dev

Then open http://localhost:3000.

Scripts I defined server/

| Command | What it does |
| --- | --- |
| `npm run dev` | nodemon on `src/index.js` |
| `npm run seed` | load / upsert catalog into MongoDB |

client/

| Command | What it does |
| --- | --- |
| `npm run dev` | Next.js on port 3000 |
| `npm run build` | production build |
| `npm start` | run the production build |
| `npm run lint` | ESLint |


Pages I added

| Path | Feature |
| --- | --- |
| `/` | Landing page |
| `/signin`, `/signup` | Auth |
| `/ai-build-matcher` | AI catalog-bound PC build |
| `/build-planner` | Manual builder + virtual look |
| `/components` | Part catalog |
| `/spec-compare` | Spec comparison |
| `/quote-generator` | Quotes and PDF export |
| `/shop-finder` | Local + OSM shop search |
| `/price-watcher` | Price tracking |
| `/inventory-alert` | Restock alerts |
| `/market-trend` | Market charts |
| `/ai-review` | AI product analysis |
| `/community` | Build posts and reviews |
| `/marketplace` | Buy / sell listings |
| `/news` | News |
| `/dashboard` | Signed-in home |

API I exposed

Base: http://localhost:8000/api

Auth uses an httpOnly cookie named token


| Method | Endpoint | Notes |
| --- | --- | --- |
| `POST` | `/auth/register` | name, email, password |
| `POST` | `/auth/login` | <br> |
| `POST` | `/auth/logout` | <br> |
| `GET` | `/auth/me` | current session |
| `POST` | `/auth/refresh` | <br> |
| `GET` | `/components` | filters / search |
| `GET` | `/components/price` | cached scrape |
| `POST` | `/components/prices` | batch prices |
| `POST` | `/ai-build-matcher` | AI build from catalog |
| `POST` | `/builds/virtual-look` | AI case mockup |
| `POST` | `/builds` | save build (auth) |
| `GET` | `/builds/my-builds` | saved builds (auth) |
| `GET` | `/shops` | stored shops |
| `GET` / `POST` | `/marketplace` | listings |
| `GET` / `POST` | `/posts` | community |
| `GET` / `POST` | `/quotes` | quote generator |
| `GET` | `/news` | news |
| `GET` / `POST` | `/pricewatcher` | watches |
| `GET` / `POST` | `/inventoryalert` | stock alerts |
| `GET` | `/markettrend` | trends |
| `POST` | `/ai-review` | AI review |

Example

{
  "budget": 2000,
  "useCase": "gaming",
  "targetResolution": "1440p",
  "preferredBrands": ["NVIDIA", "Intel"],
  "extraNotes": "Quiet cooling for streaming"
}

Problems I hit while setting this up

Mongo is empty / AI returns nothing
Run npm run seed from server/.

OpenRouter auth failed
Check OPENROUTER_API_KEY and account credits.

Port 3000 or 8000 already taken
Stop the other process, or change PORT in server/.env.

CORS / cookie login fails
Keep the client on http://localhost:3000 and CLIENT_URL matching that origin. CORS in server/src/app.js also allows http://localhost:3001 and the Vercel URL I used.

Shop search says location not found
Use a more specific place name, for example Mirpur Dhaka. Nominatim / Overpass are free and rate-limited; wait a second between searches.


How I contribute on this repo

1. Create a branch: git checkout -b feature/short-name

2. Commit a focused change

3. Push and open a pull request

I never commit .env or .env.local.





License

ISC
I made this project to make PC building in Bangladesh less guesswork — catalog parts, live shops, AI help, and a community in one app.



A map-style view of results (Leaflet-ready)
