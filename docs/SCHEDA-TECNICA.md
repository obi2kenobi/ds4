---
repo: DwarfStar / ds4
linguaggi: [C (C99), Objective-C, CUDA/ROCm (.cu), shell, Python (gguf-tools/)]
stato: beta
aggiornato: 2026-11-26
---

# Scheda tecnica — ds4 (DwarfStar)

## Scopo
Motore nativo di inferenza per DeepSeek V4 Flash (anche GLM 5.2 e DeepSeek V4 PRO su macchine ad alta memoria). Self-contained, orientato a hardware consumer/workstation, non un runner GGUF generico. Model loading, prompt rendering, tool calls, KV state, HTTP server e coding agent costruiti e testati insieme. Backend: Metal (primario), NVIDIA CUDA (multi-GPU), ROCm (Strix Halo), CPU di riferimento.

## Architettura
- Ingressi top-level: `ds4.c`, `ds4_cli.c`, `ds4_server.c`, `ds4_bench.c`, `ds4_eval.c`, `ds4_agent.c`, `ds4.h`, `download_model.sh`.
- Backend: `metal/` (primario, macOS), `cuda/` (multi-GPU), `rocm/` (Strix Halo), CPU (riferimento).
- Backend isolato da flag diagnostici; no varianti semantiche permanenti dietro flag.
- gguf-tools/ (1228 file), tests/ (76), speed-bench/ (16), dir-steering/.
- Documentazione: README.md, MODEL_CARD.md, STRIXHALO.md, AGENT.md, CONTRIBUTING.md, QA_BEFORE_RELEASES.md, docs/ISTRUZIONI.md, docs/LLM-GUIDA.md, Makefile.

## Componenti
| Nome | Ruolo | File |
|------|-------|------|
| ds4 | CLI chat | ds4.c, ds4_cli.c |
| ds4-server | HTTP multi-utente | ds4_server.c |
| ds4-bench | Benchmark perf | ds4_bench.c |
| ds4-eval | Valutazione | ds4_eval.c |
| ds4-agent | Coding agent | ds4_agent.c |
| metal | Backend Metal | metal/, ds4_metal.m |
| cuda | Backend NVIDIA CUDA | cuda/, ds4_cuda.cu |
| rocm | Backend ROCm | rocm/, ds4_rocm.cu |
| gguf-tools | Quantizzazione / imatrix / qualità | gguf-tools/ |
| speed-bench | Benchmark assets | speed-bench/ |
| dir-steering | Steering runtime | dir-steering/ |
| download_model.sh | Scarico modelli | download_model.sh |

## Dati e interfacce
- Modelli GGUF (DeepSeek V4 Flash/PRO, GLM 5.2): scaricati via `./download_model.sh` (da Hugging Face; serve `<HF_TOKEN>` per alcuni file).
- KV state, SSD streaming (mmap), quantizzazione IQ2_XXS / Q2_K (MoE experts), MXFP4.
- HTTP server multi-utente (micro-batching decode/generate), tensor parallelism Metal/CUDA (2 Mac M5 Max/M3 Ultra, RDMA).
- Tool calls e coding agent integrati; output in testo libero / JSON dove necessario.

## Dipendenze
- Nessuna dipendenza esterna obbligatoria: self-contained, C99/Metal, no C++ (Objective-C solo dove Metal lo richiede).
- GGUF ecosystem, llama.cpp/GGML come riferimento storico (nessuna link diretta a GGML; LICENSE mantiene copyright ggml).
- Toolchain: cc/make, Metal (Xcode), CUDA (Linux), ROCm.
- Versioni esplicite: non dichiarate nel repo (non verificato).

## Configurazione
Solo variabili d'ambiente; nessun segreto hardcoded.
- `<HF_TOKEN>` — token Hugging Face per download modelli.
- `DEBUG_FLAGS`, `NATIVE_CPU_FLAG` — flag build (Makefile).
- `CC`, `CFLAGS` — toolchain (Makefile).

## Build e test
- `make help` → exit 0 (target: all/cpu/test/benches).
- Build completa `make` NON eseguita in questa sessione: non verificato (heavy).
- `make test` / `make clean`: non verificati.
- `make metal-decode-schedule-bench`, `make test-mxfp4-metal`, `make dspark-verify-depth`, `make mtp-verify-depth`: non verificati.
- `./download_model.sh` NON eseguito (scarica modelli): non verificato.

## Deploy
Esecuzione locale su Mac (Metal, 96 GB+), Linux CUDA/ROCm. Nessun servizio cloud dichiarato. `ds4-server` è pensato come server multi-utente on-premise, non come deploy gestito.

## Rischi e debito tecnico
- Beta quality: instability possibile; QA run obbligatorio prima di ogni release (QA_BEFORE_RELEASES.md).
- Build completa non verificata in questa sessione; download modelli non verificato.
- Teste e bench non eseguiti: non verificati.
- LICENSE: MIT (2026 ds4.c authors; 2023-2026 ggml authors).
- Segreti: nessun valore reale; usa `<HF_TOKEN>` come segnaposto. Nessuna .env.example.
