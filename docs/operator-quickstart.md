# Operator quickstart

**The richest repository in this migrated cohort, and the first with a test beside
its worker.** 23 tracked files, a 20.7 KB facade that registers 5 commands and 7
queries, a 13.7 KB integration test for it, a second test under `kotoba/`, and
audit wrapping that is actually present.

It also carries three counts in one documentation sentence, and they are not all
the same kind of true. Steps marked ✅ were run against this tree on 2026-08-16.

---

## 1. "9 line, 13 train type, 4 seat class" — one wrong, one unbacked, one right ✅

`CLAUDE.md` line 5 makes all three claims together. Checked separately:

| claim | measured | |
|---|---|---|
| 9 lines | the facade's `LINES` array has **8** | ✗ documentation |
| 13 train types | `trainType` is declared three times in `kotoba/src/types.ts`, every one of them as a bare `string`; **no enumeration anywhere** | ⚠ unbacked |
| 4 seat classes | `kotoba/src/types.ts` exports exactly **4** | ✓ correct |

The eight lines, from `appview/etzhayyim-wasm-shinkansen-sh1nk4n0/src/app.ts`:

```bash
grep -n 'lineId: "' appview/*/src/app.ts
#   tokaido, sanyo, tohoku, joetsu, hokuriku, kyushu, hokkaido, nishi-kyushu
```

**And the test agrees with the code, not the document:**

```bash
grep -n 'listLines returns all' -A 6 appview/*/src/app.test.ts
#   it("listLines returns all 8 shinkansen lines", ...
#   expect(lines.length).toBe(8);
```

So the number to trust is 8. This is worth stating in that direction, because most
documentation-versus-tree findings in this cohort go the other way: here the
implementation and its test are right and the prose is stale. A first pass at this
file counted `Shinkansen"` occurrences instead of array entries, got 9, and nearly
recorded "the test would fail" — the opposite of the truth. Count the entries.

The seat classes are correct but live one directory away, which is why a reader
looking only at the facade will not find them:

```bash
node --experimental-strip-types -e '
const t = await import("./kotoba/src/types.ts");
console.log([...t.SEAT_CLASSES].join(", "), t.SEAT_CLASSES.size);'
#   ordinary, green, granclass, unreserved 4
```

`kotoba/src/types.ts` has **no imports**, so that runs with nothing installed. It
also exports `FARE_TYPES` (5: regular, early-bird, round-trip, ic-discount, other)
and `PLATFORMS` (4: smartex, ekinet, jr, other), neither of which the documentation
mentions.

## 2. The audit wrapping is real here ✅

```bash
grep -c 'withOCELEvent' appview/*/src/app.ts    # 13
grep -n 'withOCELEvent' appview/*/src/app.ts | head -1
#   25:  withOCELEvent,          <- imported from @etzhayyim/kotodama-host-sdk
```

Thirteen call sites, each wrapping a handler, with the wrapper supplied by the host
SDK. Worth pointing out because a sibling actor in this org advertises
"screen-every-call writes OCEL audit event" in its governance section and contains
the string zero times. Here the claim and the code agree.

**Reservation PII is Tier 3** per `CLAUDE.md`, and the OCEL events are the trail
that makes a Tier 3 handler auditable, so the two facts belong together.

## 3. Two test files, and the maturity instrument sees neither ⚠

```bash
git ls-files | grep -E '\.(test|spec)\.'
#   appview/etzhayyim-wasm-shinkansen-sh1nk4n0/src/app.test.ts
#   kotoba/test/shinkansen.test.ts
```

`src/app.test.ts` is 13,742 bytes of integration tests: it asserts the facade
registers 5 commands and 7 queries, then exercises `listLines`, `searchRoute`,
`checkAvailability`, `compareFare`, `getReservation`, `listReservations` and
`getOperation`. That is the first test beside a worker in this cohort.

`itonami-maturity-scan.cljs` counts test bytes only under a **top-level** `test/`.
Neither of these is, so `axis-test` reads **0bp** for a repository with two test
files, one of them substantial. Fleet-wide, 387 repositories are measured as zero
src AND zero test while holding code — recorded in ADR-2608052000. This is the
fourth repository in five rounds where that shows up in the measurement itself, and
the starkest: two files, both invisible.

## 4. What needs an install ⚠ NOT WALKED

The facade imports `@etzhayyim/kotodama-host-sdk`, so it does not load from disk:

```bash
node --experimental-strip-types -e 'import("./appview/*/src/app.ts")'
#   ERR_MODULE_NOT_FOUND
```

Running `src/app.test.ts` therefore needs a network install, and so does
`kotoba/`'s vitest suite. Neither was run while writing this and neither is claimed
to work. If you install, go through the repo-wide resource governor rather than
invoking a build directly, and be aware that a sibling repository in this org
documented `~/.npmrc` and `EALLOWSCRIPTS` problems for exactly this step.

Its data path, from the header comment, is `sdk.pds.dispatch` for writes and
`createKyselyDb` over Hyperdrive for reads, both against RisingWave — so a real
run needs that substrate too, not just the packages.

## 5. Where the rest lives

Per `CLAUDE.md`: three lexicons (`searchAvailability`, `reserveSeat`,
`listOperations`), cross-actor links to `calendar` (confirmed-reservation sync),
`railway` (rolling stock and routes) and `maps` (station geo). None of those is in
this tree. Reservation sources named are SmartEX, EX予約 and えきねっと; this
document describes the repository and not how to book anything.

`migration.edn` records the extraction; this document was added to its
`:identity/:allowed-additions` rather than appearing beside it unrecorded.
