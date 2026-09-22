# Data Sources

What data Meridian has, and whether it's shareable with AI tools under the NDA.

_Last updated: 2026-09-21_

## Shareable (fine to use with AI tools per the brief)

| Dataset | Description | Notes |
|---|---|---|
| POS transactions | ~3 years of point-of-sale transaction history | Shareable **in aggregate** (sales totals by store/week); the brief doesn't clarify whether raw line-item transactions count as shareable — confirm with Dana. POS system was migrated last spring (2026); check comparability across the cutover. |
| Store attributes | Square footage, opening date, lease terms, per store | Explicitly named as shareable in the brief. |

## Restricted (must never enter any AI tool, per signed NDA — no exceptions)

| Dataset | Description | Notes |
|---|---|---|
| Loyalty program data | ~40,000 members, membership + purchase history | Restricted. Brief notes this data has "never really" been used for analysis — an opportunity, but must be analyzed outside of AI tools. |
| Labor scheduling / hours | Employee scheduling and hours data | Restricted. |

## Handling rule

Restricted datasets are cataloged here (name, description, restricted tag)
but their contents are never read into this conversation or into any wiki
page. The user analyzes restricted data outside of Claude and brings back
only their own written findings or aggregates as a new file in `raw/`.

## Access

- IT (Marcus) can pull an extract once the NDA is signed — see [stakeholders](stakeholders.md).

## See also

- [business-overview](business-overview.md)
- [assumptions](assumptions.md)
