# Proposal 43 — Emergency Mitigation & Fund Recovery Report

**Status:** Mitigation complete — full exposure recovered.
**Chain:** Terra Classic (`columbus-5`)
**Token:** JURIS (cw20, 6 decimals)
**Report date:** 2026-07-24

---

## 1. Summary

DAO Proposal **43** passed unintentionally due to an operational configuration
error. As authored, executing it would have created a **10,000,000,000 JURIS**
(10B) cw-vesting payment recorded against a **misconfigured DMZ payee contract**,
placing DAO treasury funds at risk. Because a *passed* DAO DAO proposal cannot be
cancelled through normal governance, a controlled mitigation was authorised and
executed to neutralise the financial risk and return all funds to the DAO.

**Outcome:** the entire 10B JURIS exposure has been returned to the DAO core
account. No JURIS remains in the affected DMZ payee or the vesting contract.

| | uJURIS | JURIS |
|---|---:|---:|
| Total exposure (Prop 43 vesting payment) | 10,000,000,000,000,000 | 10,000,000,000 |
| Returned to DAO at cancel (unvested) | 9,549,717,721,193,416 | 9,549,717,721.19 |
| Recovered from DMZ (released/vested slice) | 450,282,278,806,584 | 450,282,278.81 |
| **Remaining at risk** | **0** | **0** |

---

## 2. Background

This is a **DAO DAO** DAO deployed on Terra Classic, using DAO DAO's
`cw-vesting` / `cw-payroll-factory` stack. Under this design a `cw-vesting`
contract is instantiated **per payment** and holds the tokens itself; the
payee/recipient is only a recorded destination address. `cancel` and `distribute`
run on the *vesting* contract (owned by the DAO), never on the payee.

Executing Proposal 43 therefore:

- instantiated a **new** `cw-vesting` contract holding the full 10B JURIS, and
- recorded the misconfigured DMZ contract as the payment **recipient** (it
  received nothing at execution).

The risk was that, over the vesting schedule, released tokens would flow to a
payee contract that could not be safely controlled.

---

## 3. Key addresses & code IDs

| Role | Address / ID |
|---|---|
| Affected DMZ payee (LOCKDROP V2 DMZ) | `terra1n3ysja7wds8xgwr932xvz3733msme2cqkesmxv9ffpyvd0xnahns5qmfzq` |
| DAO core (owner / recovery recipient) | `terra1rzwuz9cc2j057xpgmf500t78uhttwyfgsspuyqulhs4yqx53rnlqejrtqa` |
| DAO proposal module | `terra1m7kkh87swnycxcjxsye0svaj29asq75mv2l3getdd3qjj8x9lveq30sff6` |
| New cw-vesting contract (Prop 43) | `terra1dhmez6mw8wh0lcl65jwnyrxjurzwyl0ytcl2cx502mzjv7n03j7q47xkdr` |
| Payroll factory | `terra1rs57m76jrrzfae32gedcxjr7cqamgdu27sgxmcy0cg8rnahnvvuqlda2ke` |
| JURIS token (cw20) | `terra1vhgq25vwuhdhn9xjll0rhl2s67jzw78a4g2t78y5kz89q9lsdskq2pxcj2` |
| Temporary migration authority (DMZ admin) | `terra1xnu72mn60yzcyr0fl8avgjy5wepfw85c0knfeh` |
| DMZ original code ID | `10489` |
| Protective code ID (step 2) | `11539` |
| Recovery code ID (step 5) | `11548` |

### Code checksums (SHA-256)

| Code | Code ID | Checksum |
|---|---|---|
| `prop43-protective` | `11539` | `9910d80c1bfd325dd575f41336b88334a7eed74fb9de5f559cd0fdd61a061330` |
| `prop43-recovery` | `11548` | `86dc4155a36fca48f55a2103334b084ba0e94ccc91927dc33b6ed2faabda1464` |

Both code IDs were independently verified on-chain to carry these exact hashes.

---

## 4. Governance proposals

- **Proposal 43** — the original, misconfigured vesting proposal. Its *Passed*
  status was not altered; this mitigation only protects and recovers the funds.
- **Proposal A58** — mitigation authorisation. Authorised steps 1–3 (admin
  transfer, protective migration, controlled execution of Prop 43). Passed &
  executed.
- **Proposal A59** — "Emergency Mitigation of Incorrectly Configured Proposal 43
  (Part 2)". Executes `{"cancel":{}}` on the new cw-vesting contract. Passed &
  executed.

Proposal module threshold: absolute percentage 75%, total voting power 5 → **≥4
yes votes** required.

---

## 5. Mitigation actions (chronological, with transactions)

All transactions were signed by the temporary migration authority
`terra1xnu72…knfeh` unless noted, and executed on `columbus-5`.

### Step 1 — Transfer DMZ payee admin
DMZ payee admin changed from the DAO to the temporary migration authority.
Confirmed on-chain (current DMZ admin = `terra1xnu72…knfeh`).

### Step 2 — Protective migration
Stored audited protective code and migrated the DMZ onto it. The protective code
makes the payee unable to transfer/withdraw received JURIS: `execute` and `query`
fail hard, `migrate` is a no-op.

| Action | Tx hash |
|---|---|
| Store protective code `11539` | `999CFA05C31FE5C53971C2176D24C4A6A27883B8AFE064B2C84828B60BEAF695` |
| Migrate DMZ → code `11539` | `66C73E11EC042E433A0CDB55640F7D7CC4E4439485638D6F497CA1DFA578DF53` |
| Test: unauthorised `execute` rejected (code 5, Disabled) | `2176D31E0D1F7AA4AA9877BEB167ECFDD33B5F6F80882A5DD1962296CC3AFB5C` |

### Step 3 — Execute Proposal 43 (under A58)
Executed only after the protective state was verified. A new cw-vesting contract
was instantiated holding the full 10B JURIS; the DMZ payee received 0.

| Action | Tx hash |
|---|---|
| Execute Prop 43 | `D7BE11B23441881F761049EC77186FA47B6EF292CC1FCFDE37BF71576C233D79` (height 29,594,972) |

### Step 4 — Cancel the vesting payment (A59)
`{"cancel":{}}` on the vesting contract distributed the full balance in a single
transaction: the unvested bulk returned directly to the DAO owner, and the
released/vested slice was deposited into the now-protected DMZ payee.

| Action | Tx hash |
|---|---|
| Execute cancellation | `A3B74AF0BF63BDBE95BB940C31182A348818A993AB41E613441DE636FE2A5B95` (height 29,634,706) |

Cancel transfers (both JURIS cw20):

| Amount (uJURIS) | Amount (JURIS) | Destination |
|---:|---:|---|
| 9,549,717,721,193,416 | 9,549,717,721.19 | DAO core `terra1rzwuz9c…ejrtqa` |
| 450,282,278,806,584 | 450,282,278.81 | DMZ payee `terra1n3ysja7…5qmfzq` |

### Step 5 — Recover the released slice
Stored audited recovery code and migrated the DMZ onto it. The recovery code's
`migrate` handler (`{"return":{...}}`) queries the contract's own JURIS balance
and transfers the full amount to the DAO in the same migration transaction.
`execute`/`query` remain disabled.

| Action | Tx hash |
|---|---|
| Store recovery code `11548` | `473D9167691A8AFD2B3E78B5B7C5EB079BB8C0670CE43B1A510547F5AB989683` (height 29,634,874) |
| Migrate DMZ → `11548` + sweep to DAO | `CC95AEB296FB43AAA3E04F58CEBA7950D6F23A74E2C6D53DFF4082C3E5F2D79E` (height 29,634,966) |

Recovery migrate message:

```json
{"return":{
  "token":"terra1vhgq25vwuhdhn9xjll0rhl2s67jzw78a4g2t78y5kz89q9lsdskq2pxcj2",
  "recipient":"terra1rzwuz9cc2j057xpgmf500t78uhttwyfgsspuyqulhs4yqx53rnlqejrtqa"
}}
```

Result: single cw20 Transfer of **450,282,278,806,584 uJURIS** from the DMZ payee
to the DAO core.

---

## 6. Fund-flow accounting

```
Prop 43 vesting payment: 10,000,000,000,000,000 uJURIS (10,000,000,000 JURIS)
│
├─ Cancel (A59) ─► unvested   9,549,717,721,193,416 uJURIS ─► DAO core  (direct)
│
└─ Cancel (A59) ─► vested       450,282,278,806,584 uJURIS ─► DMZ payee
                                                              │
                        Recovery migrate (code 11548) ────────┘
                                                              ▼
                                            450,282,278,806,584 uJURIS ─► DAO core
```

**All 10,000,000,000,000,000 uJURIS accounted for and held by the DAO.**

---

## 7. Final verified state (2026-07-24)

| Account | JURIS balance (uJURIS) |
|---|---:|
| Vesting contract `terra1dhmez6m…7xkdr` | 0 |
| DMZ payee `terra1n3ysja7…5qmfzq` | 0 |
| DAO core `terra1rzwuz9c…ejrtqa` (before recovery) | 83,895,962,149,449,806 |
| DAO core `terra1rzwuz9c…ejrtqa` (after recovery) | 84,346,244,428,256,390 |

The DAO balance increased by exactly 450,282,278,806,584 uJURIS at recovery,
matching the swept amount. The DMZ payee currently runs recovery code `11548`
with admin `terra1xnu72…knfeh`.

---

## 8. Scope & authority note

The temporary migration authority's mandate was limited to **protecting and
recovering the Proposal 43 funds only**. It was not authorised to use, transfer,
retain, or dispose of DAO funds for any other purpose. This mitigation did not
change Proposal 43's *Passed* status.

---

## 9. Outstanding follow-up items

1. **Admin cleanup** — remove or transfer the temporary migration authority's
   admin control over the DMZ payee, as the DAO directs.
2. **Restore DMZ code** — migrate the DMZ payee from recovery code `11548` back
   to its original code ID `10489`. The payee holds 0 JURIS, so this is safe
   housekeeping; code `10489`'s migrate entry point should be confirmed benign
   before executing.
