# complex-log-parsing

This project aims to support more complex log parsing. It makes assumptions about how log parsing 
is done with very little basis to support it, however, everything claimed will be claimed as if it
were fact. Is this complex log parsing useful? Maybe? Is it a project made because coding up coorelations
between log lines was annoying to me that one time? Yeah.

## Correlations between log lines
Sometimes log lines contain insufficient information to understand them in isolation and usually require
positional context to understand. This can make implementing log parsing exhausting when having to keep state
between multiple log lines to understand the logs.

Here's an example from debug logs of the Bitcoin Cash client, Bitcoin ABC-0.19.0, referring to the lifecycle of a compact block message following its receipt:
```
2019-05-07 18:33:24 received: cmpctblock (144192 bytes) peer=1
2019-05-07 18:33:26 Initialized PartiallyDownloadedBlock for block 000000000000000f5e823146a2b516c60fd7ae22153ee7fca2b298e7ee445573 using a cmpctblock of size 144192
2019-05-07 18:33:29 received: blocktxn (33 bytes) peer=1
2019-05-07 18:33:32 Successfully reconstructed block 000000000000000f5e823146a2b516c60fd7ae22153ee7fca2b298e7ee445573 with 1 txn prefilled, 23997 txn from mempool (incl at least 0 from extra pool) and 0 txn requested
2019-05-07 18:33:33 UpdateTip: new best=000000000000000f5e823146a2b516c60fd7ae22153ee7fca2b298e7ee445573 height=1302584 version=0x20000000 log2_work=70.223664 tx=21460407 date='2019-05-07 18:32:29' progress=1.000000 cache=3.8MiB(26866txo) warning='10 of last 100 blocks have unexpected version'
```

Although each of these log lines refer to the same block, understanding that for each line requires parsing log lines before and after the line.
As a concrete example, the following message has no information about which block it refers to: 
```
2019-05-07 18:33:24 received: cmpctblock (144192 bytes) peer=1

```

It isn't until we parse the following line that we know which block this cmpctblock refers to:
```
2019-05-07 18:33:26 Initialized PartiallyDownloadedBlock for block 000000000000000f5e823146a2b516c60fd7ae22153ee7fca2b298e7ee445573 using a cmpctblock of size 144192
```

