`helvum` to control audio interfaces.

## Virtual audio sink

Create a "dummy" sink/source device:
```console
pactl load-module module-null-sink media.class=Audio/Sink sink_name=my-combined-sink channel_map=stereo
```

## Noise cancelling source

### RNnnoise

Source: <https://gitlab.xiph.org/xiph/rnnoise>

Pipewire config:
```conf
context.modules = [
{   name = libpipewire-module-filter-chain
    args = {
        node.description =  "Noise Canceling source"
        media.name =  "Noise Canceling source"
        filter.graph = {
            nodes = [
                {
                    type = ladspa
                    name = rnnoise
                    plugin = librnnoise_ladspa
                    label = noise_suppressor_mono
                    control = {
                        "VAD Threshold (%)" = 50.0
                        "VAD Grace Period (ms)" = 500
                        "Retroactive VAD Grace (ms)" = 5
                    }
                }
            ]
        }
        capture.props = {
            node.name =  "capture.rnnoise_source"
            node.passive = true
            audio.rate = 48000
        }
        playback.props = {
            node.name =  "rnnoise_source"
            media.class = Audio/Source
            audio.rate = 48000
        }
    }
}
]
```

### DeepFilterNet noise cancelling

Source: <https://github.com/Rikorose/DeepFilterNet>
