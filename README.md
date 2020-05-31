# spec
The specification of LongDelay protocol

Steps to make a LongDelay transaction.

1. Construct the transction you want to send in future -- `TX1`. It can be any bitcoin transction, include twetch post, or payment.

2. Set the `locktime` of `TX1`.

For example, set the `locktime` to 1735689600 means the `TX1` could only be mined after 2025-01-01 00:00:00 (utc).
You can also use the block height number in `locktime`.

3. Set the `sequence` of `TX1`.

The `sequence` should smaller than the max value (0xFFFFFFFF), otherwise the `locktime` will be ignored.

4. Sign the inputs of `TX1` with `SINGLE` or `SINGLE|ANYONECANPAY` sighash type.

These signatures will ensure the output in `TX1` which has the same index with the input could not be change.
For example, a tx with 1 input and 2 outputs, only the second output can be modified without re-sign. So when the `locktime` arrived, someone can get the money from `TX1` by modifing and publishing it.

5. Embed the `TX1` in anoterh transaction -- `TX0`.

`TX0` has an data output:

- OP_FALSE OPRETURN
- "LONGDEALY" (string)
- `[TX1]` (hex_string or binary)

6. Publish `TX0`.

example: https://whatsonchain.com/tx/113f11e9c50bc8e8fc89837702d790db6e9cd3dce7f45f88ef2a60ce1840d4a7

# FAQ

- What the use case?
It provides a method to set very long delay tasks. Anyone can set a 1 year or 10 years task with low cost, and without worry about the message will be dropped or edited.

- Who will publish the `TX1`?
Someone who wants earn money from this. If the `TX1` have been pushed to miners before the `locktime`, miners may keep it in mempool, the tx will be mined after `locktime`, and others have no chance to update the outputs, because it violates the rule of sequence update. But if the `locktime` is very far away, the tx may be dropped out from mempool, so someone can save it, update it and publish it.

- Could the `TX1` be canceled?
Yes, just sign another tx with a larger sequence number to update it.

- Will the `TX1` missing?
If you paid too low.
