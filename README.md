# ark-indexer

The indexer for [Ark](https://github.com/ararat-network/ark): replayable history and search derived from the chain. A
node serves current state and takes transactions; it is not an ergonomic historical database. The
[tooling direction](https://github.com/ararat-network/ark/blob/main/docs/direction/TOOLING_DIRECTION.md#ark-indexer)
says what the indexer is for and where it sits among Ark's repositories.

Nothing is built yet. The first member tranche does not need it: what the
[disbursement plan](https://github.com/ararat-network/ark/blob/main/docs/governance/DISBURSEMENT_PLAN.md) promises is
read from a node's own queries at a height. The indexer is step 5 of the
[build order](https://github.com/ararat-network/ark/blob/main/docs/direction/TOOLING_DIRECTION.md#build-order), once the
meanings of messages, events, assets, and account activity are stable enough to share.

## What it serves

| Reader | What it reads |
| --- | --- |
| The [Ark app](https://github.com/ararat-network/ark-app)'s explorer pages | Each transaction decoded, the distribution, and the network, readable without an account |
| [Protocol monitoring](https://github.com/ararat-network/ark/blob/main/docs/operations/PROTOCOL_MONITORING.md#27-collection-requirements) | The typed-event series and the query poller that the protocol rules in [contrib/observability](https://github.com/ararat-network/ark/blob/main/contrib/observability/README.md) read |
| Anyone building on Ark | Account history, transaction search, validator activity, governance history, and contract activity |

## Rules

- **Derived, never authoritative.** Everything here is replayable from canonical chain data, and an indexed result
  never silently overrides a conflicting chain query. It is not consensus state or an irreplaceable ledger.
- **Every result names its height.** Each answer records the block height it was derived from.
- **One meaning per transaction.** Transactions and events decode as `ark`'s
  [decoding fixtures](https://github.com/ararat-network/ark/blob/main/tests/fixtures/README.md) fix them, the reading
  the SDK and the app give.
- **Robust to the chain's life.** It tolerates restarts, delayed blocks, failed transactions, software upgrades, and
  schema evolution, and a chain upgrade can be rehearsed against replayed chain data before it activates.
- **One endpoint among several.** It reports its height and version, so a client can tell indexer lag from an
  unavailable node. Public endpoints are listed under `indexer` in
  [ark-registry](https://github.com/ararat-network/ark-registry), and anyone can run another from the same chain.
