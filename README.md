# irium-miner-gpu

GPU miner for Irium using OpenCL (SHA-256d). Compatible NVIDIA, AMD, Intel.
https://github.com/iriumlabs/irium

## Build

```bash
# Install OpenCL headers (once)
apt install ocl-icd-opencl-dev

```

## Pool mining (Stratum)

```bash
./irium-miner-gpu --pool stratum+tcp://pool.iriumlabs.org:3333 --wallet <your_address>
```

## Solo mining (node RPC)

The miner connects directly to your node and submits blocks via RPC.

```bash
./irium-miner-gpu --rpc http://127.0.0.1:38300 --wallet <your_address>
```

Make sure your node is running and accessible. The RPC port must be reachable from the machine running the miner.
(IRIUM_NODE_HOST=0.0.0.0 IRIUM_NODE_CONFIG=configs/node.json ./target/release/iriumd) 

## Multi-GPU

By default the miner uses **all GPUs** found on the system — no configuration needed.

```
[GPU] Device 0: NVIDIA GeForce RTX 4070 SUPER
[GPU] Device 1: NVIDIA GeForce RTX 4070 SUPER
[GPU] Device 2: NVIDIA GeForce RTX 4070 SUPER
[GPU] 3 device(s) initialised.
```

To select specific GPUs, pass a comma-separated list:

```bash
# Use GPUs 0, 1 and 3 (skip 2)
./irium-miner-gpu --wallet <addr> --devices 0,1,3

# Single GPU
./irium-miner-gpu --wallet <addr> --device 1
```

The combined hashrate is displayed every 10 seconds.

## Options

## Options

| Flag | Default | Description |
|------|---------|-------------|
| `--pool <url>` | — | Stratum pool URL. If set, pool mode is used. |
| `--wallet <addr>` | — | Mining/payout address (required) |
| `--rpc <url>` | `https://127.0.0.1:38300` | Node RPC URL (solo mode) |
| `--devices <n,n,…>` | all GPUs | Comma-separated list of device indices |
| `--batch <n>` | `4194304` | Nonces per GPU dispatch (tune for your GPU) |

All flags can also be set via environment variables or a `.env` / `miner.env` / `irium.env` file:

```
IRIUM_STRATUM_URL=stratum+tcp://pool.iriumlabs.org:3333
IRIUM_MINER_ADDRESS=PxkXHsFZo2sbAfo2EeKdpdrFsZigDzbxqu
IRIUM_NODE_RPC=http://192.168.1.58:38300
IRIUM_GPU_DEVICE=0
IRIUM_GPU_DEVICES=0,1,2,3   
IRIUM_GPU_BATCH=4194304
```

CLI flags take priority over environment variables.

## Notes

- Pool mode and solo mode are mutually exclusive — `--pool` takes priority.
- The `--batch` value affects latency between new job detection and GPU utilisation. Higher values = higher throughput but slower reaction to new blocks. Default (4M) is a good starting point for most GPUs.
- Hashrate is displayed every 10 seconds in the format `X.XX GH/s`.
