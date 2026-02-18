# Install Ollama

Easy — use the official install script which installs directly to `/usr/local/bin`:

```bash

# Install via official script
curl -fsSL https://ollama.com/install.sh | sh
```

That's it. The official script:
- Installs the binary to `/usr/local/bin/ollama`
- Auto-detects your NVIDIA GPU and CUDA
- Creates the systemd service at `/etc/systemd/system/ollama.service` automatically

After install verify:
```bash
which ollama
# Should show: /usr/local/bin/ollama

sudo systemctl status ollama
ollama list
```

Then watch GPU usage when running a model:
```bash
ollama run mistral
# In another terminal:
watch -n 1 nvidia-smi
```



After the official script install, the service file should already exist. First check what's there:

```bash
cat /etc/systemd/system/ollama.service
```

If it's already there, just add the GPU environment variables. Here's the complete file to put in:

```ini
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
User=root
Group=root
Restart=always
RestartSec=3
Environment="CUDA_VISIBLE_DEVICES=0"
Environment="PATH=/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
Environment="LD_LIBRARY_PATH=/usr/local/cuda/lib64"
Environment="OLLAMA_HOST=0.0.0.0"

[Install]
WantedBy=default.target
```

Then apply:
```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
sudo systemctl status ollama
```

And verify GPU is being used:
```bash
ollama run mistral
# other terminal:
watch -n 1 nvidia-smi
```

You should see `ollama` appear in the nvidia-smi process list consuming VRAM.
