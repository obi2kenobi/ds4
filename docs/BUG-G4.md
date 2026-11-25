# BUG-G4 — ds4 (correzioni M-031.27b.2)

## BUG-1
Dove: ds4_ssd.c, ds4_ssd_auto_cache_plan (righe ~124-133)
Gravità: alta — budget RAM non rispettato su GPU a memoria unificata (rischia OOM)
Prova: prima del fix, quando `model_target_bytes <= non_routed_bytes`, `cache_bytes` restava 0 ma la funzione forza `cache_experts = 1` e dichiara `effective_cache_bytes = per_expert_bytes`. Esegui la funzione con gettaglia ridotta: il piano dichiara 1 expert in cache senza RAM budgetata. Dopo il fix: `ds4_ssd_auto_cache_plan(100MB, 95MB, 1MB, 0, &p)` restituisce false, `p.cache_experts == 0`, `p.effective_cache_bytes == 0` (test /tmp/t_bug4, ok).
Correzione: rimozione della riga `if (cache_experts == 0) cache_experts = 1;` (commit e57a38b).
Test: `gcc -I. -o /tmp/t_bug4 /tmp/t_bug4.c ds4_ssd.c && /tmp/t_bug4` → `OK`; build `make ds4_ssd.o` (syntax+link OK).

## BUG-2
Dove: ds4_ssd.c, ds4_ssd_auto_cache_plan (righe ~133-136)
Gravità: medio — garanzia dei byte effettivi poteva wrappare silenziosamente (difesa proattiva; scenario raggiungibile solo con budget vicini al tetto uint64)
Prova: prima del fix, `effective_cache_bytes = cache_experts * per_expert_bytes` con `cache_experts` fino a UINT32_MAX e `per_expert_bytes` grande poteva sbordare uint64. Dopo il fix: `effective_cache_bytes = UINT64_MAX` (clamp) quando `cache_experts > UINT64_MAX / per_expert_bytes`. Test: `ds4_ssd_auto_cache_plan(UINT64_MAX/2, 0, 1GiB, 0, &p)` produce `bytes = 4611686017353646080` (valido, non wrappato) — note: il clamp esatto è non raggiungibile con i valori attuali, quindi è difesa.
Correzione: guard `per_expert_bytes > 0 && cache_experts > UINT64_MAX / per_expert_bytes` → effective = UINT64_MAX (commit 0670766).
Test: `gcc -I. -o /tmp/t_bug4 /tmp/t_bug4.c ds4_ssd.c && /tmp/t_bug4` → `OK`.
