# Guida LLM — ds4 (DeepSeek V4 / DwarfStar)

Progetto: DwarfStar, motore nativo di inferenza per DeepSeek V4 Flash, con supporto a GLM 5.2 e DeepSeek V4 PRO (macchine ad alta memoria). Backend: Metal (primario), NVIDIA CUDA (multi-GPU), ROCm (Strix Halo).

## Struttura reale (base c1d4597)
- 1471 file versionati; top-level: gguf-tools/ (1228), tests/ (76), cuda/ (43), rocm/ (23), metal/ (20), speed-bench/ (16)
- Ingressi principali: ds4.c, ds4.h, ds4_cli.c, ds4_server.c, ds4_bench.c, ds4_eval.c, ds4_agent.c, download_model.sh
- Docs: README.md, MODEL_CARD.md, STRIXHALO.md, AGENT.md, CONTRIBUTING.md, QA_BEFORE_RELEASES.md, Makefile
- docs/: solo artefatti di revisione (_inventario.tsv, _guida-lista.txt, _revisione.tsv); docs/archivio/ non esiste.

## Comandi (solo se eseguiti con successo)
- `make help` → exit 0; stampa i target: all (default Metal), cpu, test, metal-decode-schedule-bench, metal-prefill-variant-bench, check-mxfp4-half-lut, test-mxfp4-metal, dspark-verify-depth, mtp-verify-depth, clean.
- `git log --oneline -3` → exit 0 (vedi report).
- `./download_model.sh` NON eseguito (può scaricare modelli); registro come non verificato.
- Build completa `make` NON eseguita (heavy); registro come non verificato.

## Guide revisionate (docs/_guida-lista.txt, 8 file)
README.md, dir-steering/README.md, gguf-tools/README.md, gguf-tools/imatrix/README.md, gguf-tools/mixed/README.md, gguf-tools/quality-testing/README.md, speed-bench/README.md, tests/test-vectors/README.md

## Documenti superati in docs/archivio/
docs/archivio/ non esiste; nessun documento spostato. Nessun SUPERATO nella revisione (vedi docs/_revisione.tsv, righe con classe SUPERATO = 0).

## Limiti
- Build, modelli, inferenza: non verificati in questa sessione.
- Per documented verifications, vedi docs/_revisione.tsv (esito OK/FUORI PERIMETRO).

## CLAUDE.md
- CLAUDE.md (38 righe): scopo, struttura, comandi, convenzioni, cosa non toccare, rimando a questa guida.
- Segreti: nessuna chiave nel repo; .env.example non esiste. Usa solo segnaposto.

## Non verificato in questa sessione
- Build completa `make`, download modelli, test/bench: non eseguiti.
- Percorso AGENTS.md/.env.example: assenti; non creati (fuori perimetro consigliato).

## Istruzioni d'uso
Vedi docs/ISTRUZIONI.md per guida sintetica (requisiti, installazione, avvio, uso, verifica, problemi noti) per ogni software del repo.
