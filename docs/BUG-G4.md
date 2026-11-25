# BUG-G4 — Candidati ds4 (analisi M-031.27b.1)

CANDIDATO-1: ds4_ssd.c:125-126 — logica errata / fallo di presupposto.
`ds4_ssd_auto_cache_plan`: quando `model_target_bytes <= non_routed_bytes` (budget RAM esaurito per non-routed), `out->cache_bytes` resta 0 (memset), ma la riga 126 lo forza a `cache_experts = 1`. Quindi il piano dichiara 1 expert in cache (e `effective_cache_bytes = per_expert_bytes`) anche quando non c'è RAM disponibile per la cache expert. Su una GPU a memoria unificata questo può far sforare l'uso RAM e aumentare il rischio OOM — proprio il problema che la funzione vuole evitare (commento riga 100-105).

CANDIDATO-2: ds4_ssd.c:133 — overflow uint64.
`out->effective_cache_bytes = cache_experts * per_expert_bytes;`: `cache_experts` è limitato a `UINT32_MAX` ma `per_expert_bytes` può essere grande (es. 1 GiB per expert); `UINT32_MAX * 1 GiB` sborda il uint64 (max ~1.8e19). Il risultato wrappa silenziosamente a valore basso, cancellando la garanzia sui byte effettivi e ignorando il budget.
