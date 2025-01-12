[![Rust](https://github.com/jettycloud/nvidia-riva-rs/workflows/Rust/badge.svg)](https://github.com/jettycloud/nvidia-riva-rs/actions?query=workflow%3ARust)
[![Documentation](https://docs.rs/nvidia_riva/badge.svg)](https://docs.rs/nvidia_riva)
[![Crates.io](https://img.shields.io/crates/v/nvidia_riva.svg)](https://crates.io/crates/nvidia_riva)

# GRPC Client for Nvidia Riva or Jarvis

### How to install

Add `nvidia_riva` to your dependencies

```toml
[dependencies]
# ...
nvidia_riva = 0.1
```

### Getting started

#### Example for RivaSpeechRecognitionClient

```rust
use nvidia_riva::asr::riva_speech_recognition_client::RivaSpeechRecognitionClient;
use nvidia_riva::asr::{RecognitionConfig, RecognizeRequest};
use nvidia_riva::AudioEncoding;

#[tokio::main]
async fn main() -> Result<(), anyhow::Error> {
    let mut riva_client =
        RivaSpeechRecognitionClient::connect("http://127.0.0.1:50051").await?;

    let bytes = std::fs::read("./assets/data/test.wav")
        .expect("Unable read `test.wav` file");
    let riva_request = RecognizeRequest {
        config: Some(RecognitionConfig {
            encoding: AudioEncoding::LinearPcm as i32,
            sample_rate_hertz: 16000,
            language_code: String::from("en-US"),
            max_alternatives: 1,
            profanity_filter: false,
            speech_contexts: vec![],
            audio_channel_count: 1,
            enable_word_time_offsets: false,
            enable_automatic_punctuation: false,
            enable_separate_recognition_per_channel: false,
            model: "riva-asr".to_string(),
            verbatim_transcripts: true,
            custom_configuration: Default::default(),
        }),
        audio: bytes,
    };
    let riva_result = riva_client.recognize(riva_request).await;
    println!("{:?}", riva_result);
    Ok(())
}
```
