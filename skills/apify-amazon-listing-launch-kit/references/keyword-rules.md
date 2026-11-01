# Keyword rules — filter, placement, copy

## Relevance filter (step 5)

Apply in this order and count the rows each rule drops. Report the counts with 2-3 examples.

1. **Competitor brands and models.** Drop every keyword that contains a competitor's brand,
   product line or model number, and obvious misspellings of them ("custm board brandx",
   "brndx board"). Brand names come from the competitor listings, the `brand` field of the
   rank rows and the user. The user's own brand stays out of the map too: it goes in the
   title anyway.
2. **Other product types.** Drop keywords for products the user does not sell (a cutting-board
   seller drops "knife set", "dish rack").
3. **Attributes the product does not have.** Drop sizes, colours, materials or audiences the
   facts rule out ("marble cutting board" for a bamboo board). An attribute the facts neither
   confirm nor rule out stays out of the copy and the backend: list it under "To clarify"
   with its volume and ask the user.
4. **Other languages.** On amazon.com keep English keywords only.
5. **Duplicates.** Lowercase and trim; keep one row per keyword with its highest volume and
   every source that produced it.

Volume 0 is not a reason to drop a keyword: it goes to the backend.

## Placement (step 6)

Sort kept keywords by estimated volume, highest first. Ties: better competitor rank first.

| Placement | Keywords |
|---|---|
| `title` | the top 3 with volume > 0 |
| `bullet` | the next 10 with volume > 0 |
| `backend` | every other kept keyword, including volume 0 |

A keyword never moves above one with a higher volume. If fewer than 3 keywords have a volume,
say the title rests on the product facts more than on demand data.

## Copy (step 8)

- **Facts only.** Every feature, number, material, certification, compatibility or warranty in
  the draft must appear in the listing facts or in what the user said. Machine rewrites from
  the audit are suggestions: drop any claim they add (dimensions, battery life, "waterproof",
  "non-slip", "food-safe certified", care instructions, warranty) unless a fact supports it,
  and list what you dropped. If the facts contradict each other, say so and ask.
- **Title.** Brand, product type, the title keywords, then size / material / colour from the
  facts. Check the category style guide; a common limit is 200 characters. No promotional
  words ("best", "#1", "sale"), no competitor brands.
- **Bullets.** Five bullets, one fact or benefit each, using the bullet keywords where they
  read naturally. No keyword stuffing.
- **Backend search terms.** Backend keywords joined with spaces, words already in the title
  removed, no competitor brands, no ASINs, no punctuation. Keep it under 250 bytes and say
  how many bytes it uses.
- Head the draft with "Draft — review before publishing in Seller Central".

## Worked example (not real data)

A bamboo cutting board seller, one competitor ASIN, two seed terms:

| keyword | est. volume | source | placement | why |
|---|---|---|---|---|
| bamboo cutting board | 14,200 | reverse + seed | title | highest volume |
| cutting board large | 6,100 | reverse | title | |
| cutting board with juice groove | 1,300 | seed | title | |
| wooden chopping board | 900 | reverse | bullet | |
| brandx bamboo board | 2,400 | reverse | dropped | competitor brand |
| tabla de cortar | 700 | reverse | dropped | other language |
| cheese board set | 1,100 | reverse | dropped | other product type |
| bamboo board for meat | 0 | reverse | backend | volume 0 |
