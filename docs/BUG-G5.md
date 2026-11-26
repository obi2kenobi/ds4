# BUG-G5 (M-032.27b)

NON TROVATO: nessun bug grave nuovo (diverso dai 2 di docs/BUG-G4.md, entrambi in ds4_ssd.c / ds4_ssd_auto_cache_plan) confermato entro il tempo del mandato (15 minuti).

File letti (interi o a sezioni mirate):
- ds4_ssd.c (intero, 210 righe): controlli overflow su pct/GiB, parse args, mmap/mlock a chunk. Le 2 anomalie presenti sono G4-BUG-1 (riga 126, `cache_experts == 0 -> 1`) e G4-BUG-2 (guard `cache_experts > UINT64_MAX / per_expert_bytes`): gia documentate in docs/BUG-G4.md, non riusabili per G5.
- ds4_layer_pack.c (intero, 149 righe): bounds su n_gpus, budget, tag buffer 64B con strncat boundato.
- ds4_gpu_args.c (prime 120 righe): parse_csv_int_list con errno/range cap.
- ds4_server.c: struct http_request (~12523), read_http_request (max_header 64KiB, max_body 64MiB), content_length (12545) + controllo `clen < 0 || clen > max_body` (12589-12590): candidato escluso, guardia presente. sscanf %7s/%255s coerente con method[8]/path[256]. parse_int_arg/parse_nonneg_int_arg/parse_float_arg (12954-12980) con end-check e range.
- ds4_tp.c: serializzazione/deserializzazione frame (1634-1900): memcpy con controllo `bytes != sizeof(msg)` prima delle copie, malloc(b)+read_full con gestione truncated.
- ds4_kvstore.c (fino a ~1300 di 1346): read_header (417) valida magic/versione/quant_bits, read_entry_file (442+) con controlli overflow su text_bytes+payload_bytes (UINT64_MAX), refresh directory, verifica SHA1 del text-prefix e unlink+invalidate su payload corrotto.

Ricerche fatte:
- grep strtol/strtoul/strtoull/sscanf/atoi/atol/atof su ds4_server.c, ds4_agent.c, ds4_bench.c, ds4_cli.c, ds4_tp.c, ds4_eval.c, ds4.c (30+ occorrenze campionate: tutte con end-check o range-check nei punti letti).
- grep memcpy in ds4_tp.c: tutte con sizeof o bounds precedenti.

Baseline test (prima, invariata): `make test` -> EXIT=2 per modello `ds4flash.gguf` mancante (Makefile:19), fallimento preesistente non legato al codice; i test senza modello (ds4-eval self-test, ds4_agent_test, test_layer_pack, test_engine_mgpu_placement, test_gpu_args, test_sampling) passano.

Non fatto per tempo: ds4.c (67435 righe), ds4_agent.c (11431), ds4_distributed.c (8437), ds4_web.c (1385), ds4_cli.c (2204) letti solo parzialmente; coda di ds4_kvstore.c (~46 righe).
