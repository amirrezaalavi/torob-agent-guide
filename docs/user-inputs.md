# What the agent needs from the user

Ask only for information that changes the search or compatibility decision. If a field is missing but a reasonable default exists, state the assumption and continue with a read-only shortlist.

## Minimum brief for a product search

1. **What exactly?** Product category and model/variant, or acceptable alternatives.
2. **Budget:** maximum amount, currency, and unit (e.g. 200 million Toman); hard cap or target; include shipping/tax/assembly or not?
3. **Condition:** new, used, refurbished, or mixed; any condition the agent must exclude?
4. **Requirements:** must-have specs, compatibility constraints, size/form factor, interface, region/registration, warranty, etc.
5. **Location:** seller/delivery area if it affects availability or shipping.
6. **Task scope:** research only (default), or whether the user separately wants any later contact/order action. Never assume permission to transact.
7. **Evidence expectations:** desired number of sellers, freshness threshold, whether used/outlier offers are acceptable, and output format.

## Additional fields for a server build

- Exact chassis family/model (e.g. DL380 Gen9 vs ML350 Gen9), rack/tower, SFF/LFF drive bay type.
- Workload: virtualization, database, storage/NAS, GPU inference, lab, etc.; required VM count or performance target where known.
- CPU count/model preference; minimum RAM capacity and ECC/Registered requirements.
- Storage capacity, redundancy/RAID level, SAS/SATA/NVMe, drive-tray/caddy requirement.
- Network speed/ports, GPU/accelerator requirement, redundant PSU needs, rails/rack inclusion.
- Items already owned and excluded from the budget.

## Defaults used in the first comparison

- “200MT” meant 200,000,000 Toman.
- “HPE G9” was provisionally interpreted as **HPE ProLiant DL380 Gen9, 2U rack**, not another Gen9 family member.
- Used parts accepted; read-only research only; shipping/setup not included unless explicitly visible.
- Workload unspecified, so no workload-specific CPU/RAM/storage sizing was claimed.

When a missing detail would change the parts compatibility or budget materially, ask a focused clarification before recommending a final build.
