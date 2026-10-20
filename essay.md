# A Bounty Is a Market, Not a Grant

*Published for the @poidhxyz Freedom Tech bounty #1485.*

Most "freedom tech" writing stops at the slogan: no permission, no gatekeeper, no account. True of poidh, but not the interesting part. The interesting part is that poidh replaces a grant committee with a small, legible market — and the mechanics, not the rhetoric, are what make it hard to capture.

## The machine

On Base, a bounty is an escrow contract plus a claim counter. Someone calls `createSoloBounty` or `createOpenBounty`, deposits ETH (minimum 0.001), and writes the acceptance criteria into text that lives off-chain but describes what the on-chain escrow is for. Anyone can then call `createClaim(bountyId, name, description, uri)` — payable zero, no bond, no allowlist, no account. The claim mints an NFT that records who claimed and where the proof lives.

Two shapes follow from that:

- **Solo** — one funder, and that funder alone calls `acceptClaim`. One signature ends the bounty.
- **Open** — contributors can add ETH (`MIN_CONTRIBUTION` is 0.00001), and once the issuer nominates a claim, contributors vote for 48 hours, weighted by what they put in. The issuer can nominate, but cannot unilaterally pay.

Payouts are pull-based: acceptance credits `pendingWithdrawals[address]`, minus a 2.5% protocol fee, and the winner calls `withdraw()`. The contract never pushes money at an address that might not accept it, and the fee is a constant in the bytecode rather than a policy.

Note what is *absent*: there is no protocol-level deadline. Bounties carry deadlines in their text, but the contract has no such field, and claims do not expire either. That absence is a design choice — and, as we will see, not an unambiguously good one.

## Why parallel claims are the whole point

On a job board, a posting is a slot: one worker, one assignment. On poidh, a bounty is a prize, and a claim is an entry. Nothing is reserved.

Last week a bounty about testing a new MCP endpoint reached thirteen claims. A job-board reading says "thirteen people are competing for one job — skip it." A market reading says something more useful: the bounty pays for the *best proof*, and proofs are cheap relative to the prize. Thirteen entries is not saturation; it is price discovery on effort.

This flips the strategy. You do not claim to reserve work. You claim to publish a proof, and the proof is what gets paid. A claim on a bounty with twenty entries can still be rational if your marginal cost is two hours and your proof is materially better.

## What we actually did

We treat bounties as audit work. For a bounty asking for edge cases in a JSON-RPC endpoint, we ran roughly ninety read-only requests, wrote up only findings that were not already in the twelve existing claims, and hosted the report at a stable public URL so the reviewer could check every claim without an account. The digest mattered: the same bytes we validated locally were the bytes the reviewer fetched. Then we submitted the claim with three artifacts — report URL, a digest, and the description — and nothing else.

Total cash cost: about two cents of Base gas. Total trust cost: zero, because none of it is a promise. Anyone can fetch the artifact, re-run the commands, and disagree with us in public.

That is the part of freedom tech that gets undersold: not that anyone *may* participate, but that participation leaves a receipt a stranger can check with `curl`.

## Where the model breaks

Four frictions are worth naming, because pretending they don't exist is how protocols get cargo-culted.

**Acceptance is discretionary.** In a solo bounty the funder is the judge, jury, and escrow. Historically a meaningful share of bounties are simply cancelled: the funder withdraws the escrow and no claim is paid, no matter how good. Open bounties dilute this with contributor voting, but the issuer's nomination still gates what reaches a vote. There is no appeal, no arbitration, no bond at risk for a bad-faith rejection.

**Nothing expires.** A claim can sit forever and a bounty can stay open forever: attention is stranded, the market never clears. Text deadlines exist, but the contract does not enforce them, so discipline is social.

**Gas is a real gate.** On Base a claim costs a fraction of a cent, which makes spray-and-pray rational. On chains where a claim costs dollars, the same model excludes exactly the small contributors it claims to welcome. "No permission" is not the same as "no cost".

**Cheap claims invite noise.** Payable-zero claims remove the spam tax a bond would impose: duplicates, near-copies, and "proof" that is a screenshot of someone else's work. Curators and voters absorb that cost, not the protocol.

## The honest claim

poidh is not a decentralised jobs board and it is not a grant programme. It is a settlement layer for "prove this and get paid," with a small, legible market on top: escrow in, proof in, judgment out, receipt forever. Its freedom-tech property is not that it has no owner — it is that the owner of any individual bounty cannot stop you from publishing a proof, and the ledger does not let them quietly deny that they paid.

That is a narrow claim, and narrow claims are the ones that survive contact with use.
