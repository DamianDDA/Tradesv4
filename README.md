# Bright Falls RP Prediction Exchange

A RedM resource for VORP. Players wager their active character's in-game cash on staff-created, two-outcome markets. The exchange uses a red-and-black in-game interface.

## Install or update

1. For an existing install, keep your running resource folder name (for example, `bf_trades`) and copy the contents of the archive's `bf_markets` folder into it, including the entire `ui` folder. For a new install, place `bf_markets` in your server resources.
2. Keep OneSync enabled for server-side bank proximity checks. Start the resource after `vorp_core` with `ensure bf_trades` (or `ensure bf_markets` for the default folder). Restart the resource after updating.
3. Create or assign the VORP character job `trademanager` with your existing job tools. Any grade of that active job can open **Manage markets** from the bank UI. Market management now uses the active VORP job, so the former `bfmarkets.manage` ACE can be removed from server.cfg.
4. Visit the counter at Valentine, Rhodes, Blackwater or Saint Denis and press the displayed **R** key. The same locations are used by the client prompt and server validation. For custom bank interiors, adjust the locations, radius and key in `config.lua`.

## Run a market

- Trade managers provide the question, the two outcomes, how the result will be determined, and the time until wagering closes.
- Players select an outcome and enter a whole-dollar wager. Cash is deducted when the wager is accepted. The displayed percentages show the current split of the pool. The menu refreshes automatically every five seconds while open.
- Markets stop accepting wagers at the deadline or when trade managers select **Close now**. Trade managers select and confirm the winner after close.
- Winners receive a proportion of the entire pot based on their share of the winning side. For example, a $20 winning stake out of $80 on that side earns $50 from a $200 pool, including the original stake. Payouts are rounded to cents and capped at $10,000 per individual winning trade, including that trade’s original stake. Multiple winning trades can pay more than $10,000 in total and are collected together per market. Excess above each cap stays out of circulation. If nobody backs the winning outcome, the market settles with zero payouts. Refunds are disabled. Previously settled claims remain collectible. Open positions from older versions that stored only a combined stake are counted as one legacy trade per character per side.
- Players collect payouts from **Results & claims**, including after reconnecting or restarting the resource. The resource keeps unclaimed cash available to its original character.

## Stats

The Stats button shows settled profit, loss, net result and current open stakes for the active character. Settled results include claimable cash, and historical refunds contribute zero profit or loss. Recent results show the stake, payout and net for each market. Older results are rolled into lifetime totals when pruned. History already pruned by a previous version cannot be reconstructed from this resource.

## Persistence and cash review

Market state uses server resource KVP. Include the server's resource KVP database and VORP database in your backups. Older fully paid markets are pruned as new markets are published.

VORP cash writes and the resource ledger use separate storage. The resource records a cash operation before calling VORP and checks the live character balance afterward. An interrupted or unverified payment can appear in **Manage markets** as a cash review. Verify the character's cash movement against your server logs and cash records before marking it applied or unapplied. The review records the result in the ledger without moving cash again. A full server crash can also occur after a live balance change and before VORP saves that balance. Reconcile VORP cash and the ledger after an unclean shutdown if your records show a discrepancy.

## Verification

The Lua files were parsed with Lua 5.4 and the JavaScript was syntax-checked. A mocked VORP run covered server-side bank checks, job permissions, repeated wagers and the payout cap, duplicate claim prevention, refund rejection, zero-payout settlement, ledger reload, and profit-and-loss tracking. Live RedM and the installed VORP version were unavailable for integration testing. Before rollout, exercise a small wager and payout in your staging server, then reconnect and verify the saved cash balance.

References:
- Default VORP bank locations: https://github.com/VORPCORE/vorp_banking/blob/main/config.lua
- VORP Core inventory cash calls: https://github.com/VORPCORE/vorp_inventory-v2
- Cfx Lua NUI callbacks: https://docs.fivem.net/docs/scripting-reference/runtimes/lua/functions/RegisterNUICallback/
