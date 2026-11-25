# CLAUDE.md — ds4 (DwarfStar)

Motore nativo di inferenza per DeepSeek V4 Flash (+ GLM 5.2, DeepSeek V4 PRO su macchine ad alta memoria). Metal primario, CUDA, ROCm.

## Scopo
DwarfStar: inferenza locale, self-contained, non un GGUF runner generico. Model loading, prompt rendering, tool calls, KV state, HTTP server, coding agent costruiti e testati insieme.

## Struttura
- Top-level ingressi: ds4.c, ds4.h, ds4_cli.c, ds4_server.c, ds4_bench.c, ds4_eval.c, ds4_agent.c, download_model.sh
- Backend: metal/ (primario), cuda/ (multi-GPU), rocm/ (Strix Halo), cpu (riferimento/debug)
- gguf-tools/ (1228 file), tests/ (76), speed-bench/ (16)
- Docs: README.md, MODEL_CARD.md, STRIXHALO.md, AGENT.md, CONTRIBUTING.md, QA_BEFORE_RELEASES.md, Makefile
- docs/: artefatti di revisione (_inventario.tsv, _guida-lista.txt, _revisione.tsv) + LLM-GUIDA.md

## Comandi (solo eseguiti con successo)
- `make help` → exit 0 (target: all/cpu/test/benches)
- `git log --oneline -3` → uscita ok
- Build completa `make` NON eseguita: non verificato (heavy)
- `./download_model.sh` NON eseguito: non verificato
- Test/bench NON eseguiti: non verificato

## Convenzioni
- C99/Metal; no C++; Objective-C solo dove Metal lo richiede
- Attenzione ai path: SSD streaming e Metal default mantengono mmap; non copiare l'intero GGUF
- Preserve correctness prima della velocità; commenti vicino al codice
- Non introdurre varianti semantiche permanenti dietro flag; switch diagnostici ok

## Cosa non toccare
- Codice e backend (fuori perimetro di questa guida)
- Segreti: nessuna chiave nel repo; .env.example non esiste (segnaposto da creare se richiesto)
- docs/archivio/ non esiste; non inventare SUPERATO

## Documentazione
- Guida LLM dettagliata: docs/LLM-GUIDA.md (max 120 righe)
- Note per agenti: AGENT.md

## Segreti
Nessuna chiave nel repo. .env.example non esiste; usa solo segnaposto. Mai valori reali.
