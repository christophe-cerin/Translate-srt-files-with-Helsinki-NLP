# How to manage subtitles

The problem is that you want to add subtitles to a video. You want to generate subtitles for multiple languages.

We separate the problem into two parts:
1. We use openai-whisper to produce the subtitle from the original video automatically;
2. We use Helsinki-NLP tools to translate the subtitle to a foreign language.

## Openai-whisper

Follow the instruction in the [GitHub repository](https://github.com/openai/whisper) to install it. 

An example of usage is `/Users/christophecerin/.local/pipx/venvs/openai-whisper/bin/whisper Trystram.mov --language French --model medium`

First, this command analyses the video `Trystram.mov` to isolate the speech.

Second, this command produces different files, and among them, the subtitles, in French in this case, in file `Trystram.srt`.

## Translate subtitles to another language

To convert `Trystram.srt` to another language, you can use [Google translate](https://translate-subtitles.com/) from this interface `https://translate-subtitles.com/`.

But you can also use [Helsinki NLP]() work. An introductory [tutorial](https://www.scaleway.com/en/blog/ai-in-practice-generating-video-subtitles/) is available to give an idea of the tool. Please, consult the [GitHub repository](https://github.com/Helsinki-NLP/Opus-MT) too.

We developed our tool, based on the Helsinki-NLP tool. Below, find some examples of usage.

```
$ python3 translate-Helsinki-NLP.py --language "fr-en" "Downloads/Trystram.srt"
1
00:00:00,000 --> 00:00:04,600
So, thank you, dear colleagues, dear participants.

2
00:00:04,600 --> 00:00:08,480
So, in the name of the IUT of Villetanuse too, of course, on behalf of the three IUTs,

3
00:00:08,480 --> 00:00:11,760
I welcome you to this amphitheatre for this day

4
00:00:11,760 --> 00:00:14,920
dedicated to eco-responsible digital.
```

```
$ python3 translate-Helsinki-NLP.py --language "fr-es" "Downloads/Trystram.srt"
1
00:00:00,000 --> 00:00:04,600
Por lo tanto, gracias a ustedes, queridos colegas, queridos participantes.

2
00:00:04,600 --> 00:00:08,480
Así que, en nombre de la IUT de Villetanuse, también, por supuesto, en nombre de las tres IUTs,

3
00:00:08,480 --> 00:00:11,760
Os doy la bienvenida a este anfiteatro para este día.

4
00:00:11,760 --> 00:00:14,920
Dedicada a la digital eco-responsables.
```

The original `Trystram.srt` files starts as follows/

```
1
00:00:00,000 --> 00:00:04,600
Donc, merci à vous. Chers collègues, chers participants.

2
00:00:04,600 --> 00:00:08,480
Donc, au nom de l'IUT de Villetaneuse aussi, bien sûr, au nom des trois IUTs,

3
00:00:08,480 --> 00:00:11,760
je vous accueille dans cet amphithéâtre pour cette journée

4
00:00:11,760 --> 00:00:14,920
consacrée au numérique éco-responsables.
```
