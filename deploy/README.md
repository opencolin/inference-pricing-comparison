# Deploy

Hosted on a [Tenki](https://tenki.cloud) sandbox microVM.

Live: https://inference-pricing--03q08p.us.sb.tenki.sh

## Reproduce

```bash
SB=$(tenki sandbox create --name inference-pricing --sticky \
      --cpu 1 --memory-mb 512 --output json | jq -r .id)

tenki sandbox write --session $SB --path site/index.html --data-file index.html
tenki sandbox write --session $SB --path serve.service --data-file deploy/serve.service

tenki sandbox exec --session $SB -- sudo cp /home/tenki/serve.service /etc/systemd/system/serve.service
tenki sandbox exec --session $SB -- sudo systemctl daemon-reload
tenki sandbox exec --session $SB -- sudo systemctl enable --now serve.service

tenki sandbox expose --session $SB --port 8080 --slug inference-pricing
```

Notes:

- Writes are confined to the sandbox workdir (`/home/tenki`) — absolute paths
  outside it are rejected, so stage files there and `sudo cp` into place.
- `--sticky` sets `timeout_at` to never; the sandbox stays up until explicitly
  terminated with `tenki sandbox terminate`.
- `Restart=always` plus `systemctl enable` means the server returns after a
  crash and after a sandbox resume. Verified against `systemctl kill -s SIGKILL`.

## Update the page

```bash
tenki sandbox write --session $SB --path site/index.html --data-file index.html
```

Served immediately — no restart needed.
