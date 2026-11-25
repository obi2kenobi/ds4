---
repo: DwarfStar / ds4 (DeepSeek V4 Flash inference engine)
software:
  - ds4 (CLI chat)
  - ds4-server (HTTP server)
  - ds4-bench (benchmark)
  - ds4-eval (evaluation)
  - ds4-agent (coding agent)
  - gguf-tools (quantizer / imatrix / quality testing)
  - speed-bench (benchmark assets)
  - dir-steering (runtime steering)
  - download_model.sh (model downloader)
aggiornato: 2026-11-25
---

# Istruzioni d'uso — ds4

Engine nativo di inferenza per DeepSeek V4 Flash (anche GLM 5.2, DeepSeek V4 PRO su macchine ad alta memoria). Backend: Metal (primario), NVIDIA CUDA, ROCm (Strix Halo). Beta quality. Testi pieni in inglese; questa guida sintetizza.

## Prerequisiti
- Mac con 96 GB+ (Metal) oppure Linux CUDA/ROCm. CPU-only per diagnosi.
- Toolchain C/C++ e `make`; `./download_model.sh` per i modelli (scarica da Hugging Face, serve `HF_TOKEN` per alcuni file).
- Nessun segreto hardcoded nel repo; usa `<HF_TOKEN>` come variabile d'ambiente.

## ds4 (CLI)
### Requisiti
Binario `ds4` (make) + modello GGUF.
### Installazione
`make` (Metal) o `make cpu`, `make cuda-spark`, `make cuda-generic`, `make strix-halo`.
### Avvio / Uso
`./ds4 -m ds4flash.gguf` — default path `./ds4flash.gguf`. Flag completi: `./ds4 --help`.
### Verifica
`./ds4 --help` stampa la lista flag (exit 0 — non verificato in questa sessione).
### Problemi noti
Build completa non verificata in questa sessione.

## ds4-server (HTTP)
### Requisiti
`ds4-server` buildato.
### Installazione
`make` (stesso build di ds4).
### Avvio / Uso
`./ds4-server --help` per flag; serve in multi-utente (es. 8xL40S CUDA, micro-batching decode/generate).
### Verifica
Non verificato in questa sessione (serve GPU/modello).
### Problemi noti
Session-batched serving usa decode ordinario, non DSpark.

## ds4-bench
### Requisiti
`ds4-bench` + modello GGUF.
### Installazione
`make`.
### Avvio / Uso
`./ds4-bench -m ds4flash.gguf --prompt-file speed-bench/promessi_sposi.txt --ctx-start 2048 --ctx-max 65536 --step-incr 2048 --gen-tokens 128`
### Verifica
Non verificato in questa sessione.
### Problemi noti
Risultati in speed-bench/README.md per hardware diversi; conduci confronti come PR.

## ds4-eval
### Requisiti
`ds4-eval` + modello.
### Installazione
`make`.
### Avvio / Uso
`./ds4-eval --help`.
### Verifica
Non verificato in questa sessione.
### Problemi noti
Modello non disponibile senza download.

## ds4-agent (coding agent)
### Requisiti
`ds4-agent` + modello.
### Installazione
`make`.
### Avvio / Uso
`./ds4-agent` — stesso flusso inferenza di ds4, integrato con tool call.
### Verifica
Non verificato in questa sessione.
### Problemi noti
DSpark/speculative supportati; comportamento token-level leggermente divergente da decode ordinario.

## gguf-tools
### Requisiti
Toolchain C/C++.
### Installazione
`make -C gguf-tools`
### Uso
Quantizer offline: `deepseek4-quantize.c` (GGUF quant format q8_0, q8_K, q4_K, q2_K, iq2_xxs); `imatrix/` per dataset di calibrazione; `quality-testing/` per confronti vs official continuations.
### Verifica
Non verificato in questa sessione.
### Problemi noti
Imatrix richiede calibrazione prima del Q2/Q4.

## dir-steering
### Requisiti
File f32 piatto 43x4096 (una direzione normalizzata per layer).
### Installazione
Niente build; passa con ds4.
### Uso
`--dir-steering-file FILE --dir-steering-ffn F --dir-steering-attn F` (default FFN=1, attn=0). Esempio in dir-steering/README.md.
### Verifica
Non verificato in questa sessione.
### Problemi noti
Dir steering non supportato per GLM.

## speed-bench
### Requisiti
`ds4-bench` buildato e modello GGUF.
### Installazione
Niente build; contenuto di benchmark.
### Uso
Comando in sezione ds4-bench.
### Verifica
Non verificato in questa sessione.

## download_model.sh
### Requisiti
`curl`/`wget`, `HF_TOKEN` opzionale.
### Installazione
Script eseguibile; `sh download_model.sh <target>` (ds4f-q2, ds4f-q4, ds4f-mxfp4, ds4f-q2-q4, ds4f-dspark, pro-q2-imatrix, ...).
### Uso
Scarica GGUF da Hugging Face (antirez/deepseek-v4-gguf) in `./gguf/`.
### Verifica
NON eseguito (scarica modelli); non verificato.
### Problemi noti
Token d'accesso richiesto per alcuni file.

## Verifica generale
- `make help` → exit 0 (eseguito, stampa target).
- `make` completo ed inferenza: non eseguiti, non verificati.
