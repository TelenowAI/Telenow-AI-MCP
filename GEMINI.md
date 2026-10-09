# Telenow

Telenow tools place real phone calls that reach real people and cost money.

- Always confirm the number, the purpose and what to say with the user before `place_call`, `add_campaign_targets` or `start_campaign`.
- `place_call` returns immediately with a `call_id`. Poll `get_call` for the outcome, summary and transcript.
- `create_campaign` only makes a draft; nothing is dialed until `start_campaign`.
- Use `estimate_cost` to quote the price per minute before large campaigns.
