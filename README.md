# SpeechGateway

A reverse proxy server that enhances speech synthesis with essential, extensible features. 🦉💬


## 💎 Features

- 🥰 **Supports Popular Speech Services**: Works seamlessly with AivisSpeech, VOICEVOX, Style-Bert-VITS2, and NijiVoice — and lets you integrate additional services to suit your needs.
- 🗂️ **Caching**: Boost response speed and save API calls with built-in audio caching.
- 🔄 **Format Conversion**: Effortlessly convert WAV to MP3 for bandwidth-friendly responses.
- 📊 **Performance Metrics**: Track synthesis time and cache hits for in-depth insights.
- ⚡️ **Low Latency**: Streamlined pipeline for minimal delay, delivering fast results!


## 🎁 Installation

```sh
pip install git+https://github.com/uezo/speech-gateway
```

To use MP3 format conversion, you also need to install ffmpeg to your computer.


## 🚀 Start server

Create a script like the following example:

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI
from speech_gateway.gateway.voicevox import VoicevoxGateway
from speech_gateway.gateway.sbv2 import StyleBertVits2Gateway
from speech_gateway.gateway.nijivoice import NijiVoiceGateway

# Create gateways
voicevox_gateway = VoicevoxGateway(base_url="http://127.0.0.1:10101", debug=True)
sbv2_gateway = StyleBertVits2Gateway(base_url="http://127.0.0.1:5000", debug=True)
nijivoice_gateway = NijiVoiceGateway(api_key=NIJIVOICE_API_KEY, prefix="/nijivoice", debug=True)

# Create app
app = FastAPI()

# Add gateways to app
app.include_router(voicevox_gateway.get_router(), prefix="/aivisspeech")
app.include_router(sbv2_gateway.get_router(), prefix="/sbv2")
app.include_router(nijivoice_gateway.get_router(), prefix="/nijivoice")

# On app down
@asynccontextmanager
async def lifespan(app: FastAPI):
    yield
    await voicevox_gateway.shutdown()
    await sbv2_gateway.shutdown()
    await nijivoice_gateway.shutdown()
```

Then, run it with uvicorn:

```
uvicorn run:app --port 8000
```

In this example, you can access AivisSpeech at http://127.0.0.1:8000/aivisspeech, Style-Bert-VITS2 at http://127.0.0.1:8000/sbv2, and NijiVoice at http://127.0.0.1:8000/nijivoice.

**NOTE**: If you want to perform MP3 conversion, make sure to include `x_audio_format=mp3` as a query parameter in your request. 


## 🛠️ Customization

You can add new speech synthesis services to relay.
Additionally, you can extend the cache store, audio format converter, and performance recorder. For example, the default cache store uses the file system, but you can replace it with a cloud storage service or another alternative.

We’ll provide documentation for these customizations as the need arises, so if you have specific requests, please open an issue! 🙏
