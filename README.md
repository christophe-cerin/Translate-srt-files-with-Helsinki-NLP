# How to manage subtitles

The problem is the following: you want to add subtitles in a video. You want to generate subtitles for multiple languages.

We separate the problem in two parts:
1. We use openai-whisper to automatically produce the subtitle from the original video;
2. We use Helsinki-NLP tools to translate the subtitle to a foreign language.

## Openai-whisper

Follow the instruction in the [GitHub repository](https://github.com/openai/whisper) to install it. 

An example of usage is `/Users/christophecerin/.local/pipx/venvs/openai-whisper/bin/whisper Trystram.mov --language French --model medium`

First, this command analyses the video `Trystram.mov` to isolate the speech.

Second, this command produces different files, and among them the subtitles, in French in this case, in file `Trystram.srt`.

## Translate subtitles to another language

To convert `Trystram.srt` to another language, you can use [Google translate](https://translate-subtitles.com/) from this interface `https://translate-subtitles.com/`.

But you can also use [Helsinki NLP]() work. An introductory [tutorial](https://www.scaleway.com/en/blog/ai-in-practice-generating-video-subtitles/) is available to get an idea of the tool. please, consult the [GitHub repository](https://github.com/Helsinki-NLP/Opus-MT)
