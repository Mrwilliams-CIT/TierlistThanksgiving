# CCCTC Tier List kiosk

One page: `index.html`. Portrait 4K touch screen. Works from GitHub Pages or an OptiSigns zip.

## Try it

- Open `index.html`. Votes save on that one screen only until Supabase is set up.
- Add `?demo` to the address to see it filled with fake votes. Demo votes are never saved.

## Files

- `index.html` - the whole app. Settings are at the top of the file.
- `img/` - optional pictures.
  - Food photos load from the web address in each item's `img` setting. Backup: `img/<item id>.jpg` (png and webp also work), used if the web photo is blocked. A tile with no photo shows a color block with the name.
  - Logo: `img/ccctc-logo.png` is the CCCTC wordmark, shown on a white plate. `img/ccctc-seal.jpg` is included but not used yet.

## Turn on shared votes (Supabase)

1. Create a project at supabase.com (or reuse one you have).
2. Open SQL Editor, paste the SQL below, and run it.
3. Project Settings > API. Copy the Project URL and the publishable (anon) key.
4. In `index.html`, fill in `const SUPABASE = { url: '...', key: '...' };`
5. Open the page on the kiosk and make a test list. It should show up in Table Editor > `tier_votes`.

The publishable key is safe to keep in the page. Never paste the secret (service role) key.

If the page says "Cannot reach the vote server", the school filter is blocking `supabase.co`,
or the project is paused. Free Supabase projects pause after about a week with no activity.

```sql
create table public.tier_votes (
  id bigint generated always as identity primary key,
  created_at timestamptz not null default now(),
  topic text not null check (char_length(topic) <= 40),
  ranks jsonb not null check (pg_column_size(ranks) < 2000),
  program text not null default '' check (char_length(program) <= 6),
  role text not null check (role in ('J','S','ST')),
  name text check (char_length(name) <= 16)
);

alter table public.tier_votes enable row level security;

create policy "anyone can add a vote" on public.tier_votes
  for insert to anon with check (true);

create policy "anyone can read votes" on public.tier_votes
  for select to anon using (true);
```

These rules let anyone add a vote and read votes. Nobody can change or delete a vote from the page.

## Remove a bad entry

Supabase > Table Editor > `tier_votes`. Find the row by its `name` and delete it. The screen catches up within 20 seconds.

## Make a new tier list

Edit the TOPIC block at the top of `index.html`: new `id`, `title`, `sub`, `noun`, `tiers`, and `items`. A new `id` starts a fresh set of votes. Old votes stay in Supabase under the old id.

## Photo credits

Food photos load from Wikimedia Commons. Keep this list with the project. To save a local copy, open the file page, download, and put it in `img/` named by item id.

- turkey: "2021 roast turkey.jpg" by Mark Miller, CC BY-SA 4.0. https://commons.wikimedia.org/wiki/File:2021_roast_turkey.jpg
- ham: "Glazed and sliced ham.jpg" by Michael Coté, CC BY 2.0. https://commons.wikimedia.org/wiki/File:Glazed_and_sliced_ham.jpg
- stuffing: "Stuffing for Thanksgiving Dinner.jpg" by Dorevabelfiore, CC BY-SA 4.0. https://commons.wikimedia.org/wiki/File:Stuffing_for_Thanksgiving_Dinner.jpg
- mashed: "MashedPotatoes.jpg" by Renee Comet, Public domain. https://commons.wikimedia.org/wiki/File:MashedPotatoes.jpg
- gravy: "Making the gravy sauce (5300021324).jpg" by Miia Ranta, CC BY-SA 2.0. https://commons.wikimedia.org/wiki/File:Making_the_gravy_sauce_(5300021324).jpg
- mac: "Flickr qmnonic 123431456--Kraft Dinner and veggie dogs.jpg" by Matt MacGillivray, CC BY 2.0. https://commons.wikimedia.org/wiki/File:Flickr_qmnonic_123431456--Kraft_Dinner_and_veggie_dogs.jpg
- greenbean: "Green bean casserole.jpg" by Rick Kimpel, CC BY-SA 2.0. https://commons.wikimedia.org/wiki/File:Green_bean_casserole.jpg
- sweetpotato: "Toasted Marshmallow on Mashed Sweet Potato (43469825985).jpg" by Willis Lam, CC BY-SA 2.0. https://commons.wikimedia.org/wiki/File:Toasted_Marshmallow_on_Mashed_Sweet_Potato_(43469825985).jpg
- corn: "Corn on the cob (5178296206).jpg" by Geoff Peters, CC BY 2.0. https://commons.wikimedia.org/wiki/File:Corn_on_the_cob_(5178296206).jpg
- cornbread: "2020-07-06 15 30 04 A slice of home-made corn bread in the Dulles section of Sterling, Loudoun County, Virginia.jpg" by Famartin, CC BY-SA 4.0. https://commons.wikimedia.org/wiki/File:2020-07-06_15_30_04_A_slice_of_home-made_corn_bread_in_the_Dulles_section_of_Sterling,_Loudoun_County,_Virginia.jpg
- rolls: "Dinner rolls 2 - Massachusetts.jpg" by Daderot, CC0. https://commons.wikimedia.org/wiki/File:Dinner_rolls_2_-_Massachusetts.jpg
- cranberry: "Cranberry sauce for roast turkey.jpg" by Burkhard Mücke, CC BY-SA 4.0. https://commons.wikimedia.org/wiki/File:Cranberry_sauce_for_roast_turkey.jpg
- jellied: "2019-11-28 14 21 51 A plate with canned cranberry sauce laid out for Thanksgiving Dinner in the Parkway Village section of Ewing Township, Mercer County, New Jersey.jpg" by Famartin, CC BY-SA 4.0. https://commons.wikimedia.org/wiki/File:2019-11-28_14_21_51_A_plate_with_canned_cranberry_sauce_laid_out_for_Thanksgiving_Dinner_in_the_Parkway_Village_section_of_Ewing_Township,_Mercer_County,_New_Jersey.jpg
- brussels: "Roasted Brussels Sprouts with Kumquats.jpg" by Kelly Sue, CC BY-SA 2.0. https://commons.wikimedia.org/wiki/File:Roasted_Brussels_Sprouts_with_Kumquats.jpg
- pumpkinpie: "Pumpkin Pie with Cinnamon Crust.jpg" by DrKathyShaginaw, CC BY-SA 4.0. https://commons.wikimedia.org/wiki/File:Pumpkin_Pie_with_Cinnamon_Crust.jpg
- applepie: "Apple pie 7.jpg" by Shisma, CC BY 4.0. https://commons.wikimedia.org/wiki/File:Apple_pie_7.jpg
