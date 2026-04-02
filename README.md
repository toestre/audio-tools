# audio-tools

A lightweight FastAPI service that downsamples audio files to **16 kHz mono MP3 @ 32 kbps** — the format recommended for [Whisper](https://github.com/openai/whisper) transcription. Built on `python:3.11-slim` with `ffmpeg`.

The Docker image is published to the [GitHub Container Registry](https://ghcr.io) on every push to `main` and on version tags.

## Usage

```bash
docker run -p 8000:8000 ghcr.io/<owner>/docker_audio-tools:main
```

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET`  | `/health` | Returns `{"status": "ok"}` |
| `POST` | `/downsample` | Downsample an audio file |

#### `POST /downsample`

**Form parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `file` | `UploadFile` | yes | MP3 file to downsample |
| `output_filename` | `string` | no | Override the returned filename |

**Example:**

```bash
curl -X POST http://localhost:8000/downsample \
  -F "file=@recording.mp3" \
  --output recording_downsampled.mp3
```

The response is the downsampled MP3 file. If `output_filename` is omitted the returned filename is `<original_name>_downsampled.mp3`.

## Output format

| Property | Value |
|----------|-------|
| Channels | mono |
| Sample rate | 16 000 Hz |
| Bitrate | 32 kbps |

## Development

```bash
pip install -r requirements.txt
uvicorn main:app --reload
```

## CI / publishing

The [GitHub Actions workflow](.github/workflows/docker.yml) builds and pushes the image to `ghcr.io/<owner>/docker_audio-tools` on:

- every push to `main` (tagged `:main` and `:sha-<short>`)
- version tags `v*.*.*` (tagged `:latest`, `:<major>`, `:<major>.<minor>`, `:<version>`)
