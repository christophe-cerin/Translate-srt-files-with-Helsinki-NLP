# How to manage subtitles in a video?

The problem is that you want to add subtitles to a video automatically. You want to generate subtitles for multiple languages.

We separate the problem into two parts:
1. We use openai-whisper to produce the subtitle from the original video automatically; We mean almost programmatically;
2. We use Helsinki-NLP tools to automatically translate the subtitle to a foreign language. We mean programmatically.

## Openai-whisper

Follow the instructions in the [GitHub repository](https://github.com/openai/whisper) to install the tool. 

On Mac, you need brew (the package manager), then install `Python`, `ffmpeg` and `pipx` from the command line:

```
# on MacOS using Homebrew (https://brew.sh/)
brew install ffmpeg
brew install python@3.12
brew install pipx
```

Then use the `pipx` package manager to install whisper

```
pipx install openai-whisper
```

To use whisper from the command line: `/Users/christophecerin/.local/pipx/venvs/openai-whisper/bin/whisper Trystram.mov --language French --model medium`.

And there, at the end of the transcription, you recover the .srt, .vtt, .txt files which you can reinject into the video, still with ffmpeg.

We recommend improving the translation by editing your .srt file with [Jubler](https://www.jubler.org/) subtitle editor.

The embedding of subtitles is a slightly more complex operation because the images of the video that contain subtitles are recomposed by embedding the subtitle in the image, which requires modifying all the pixels corresponding to the surface subtitles. An additional difficulty comes (at least for the proposed method) from the fact that it is necessary to use the .ass format instead of .srt for the subtitles, a format conversion is therefore necessary upstream. The manipulation was carried out with the following sequence of commands:

```
ffmpeg -i video1.srt video1.ass # produit le fichier de sous-titres au format ass
ffmpeg -i video1.mp4 -filter_complex "subtitles=video1.ass:force_style='BackColour=&H70000000,BorderStyle=4,Outline=1,Shadow=0,MarginV=10'" video1+sstit2.mp4
```

Note: if your video player is [VLC](https://www.videolan.org/vlc/), put your video and all the subtitles files in the same directory. In this way, go to the `subtitle` tab from the main menu, and select your subtitle. You can change the subtitle when playing the video.

Let's return to the example: `/Users/christophecerin/.local/pipx/venvs/openai-whisper/bin/whisper Trystram.mov --language French --model medium`

First, this command analyses the video `Trystram.mov` to isolate the speech.

Second, this command produces different files, and among them, the subtitles, in French in this case, in file `Trystram.srt`.

## Translate subtitles to another language

To convert `Trystram.srt` to another language, you can use [Google translate](https://translate-subtitles.com/) from this interface `https://translate-subtitles.com/`.

But you can also use [Helsinki NLP]() work. An introductory [tutorial](https://www.scaleway.com/en/blog/ai-in-practice-generating-video-subtitles/) is available to give an idea of the tool. Please, consult the [GitHub repository](https://github.com/Helsinki-NLP/Opus-MT), and this [one for the installation](https://huggingface.co/transformers/v3.5.1/model_doc/marian.html)

We developed our tool, namely `translate-Helsinki-NLP.py`, based on the Helsinki-NLP tool. We've included some examples of usage below.

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

The original `Trystram.srt` file starts as follows:

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

In the case of not supported translation, the program prints an help message:

```
$ python3 translate-Helsinki-NLP.py --language "fr-japonais" "Downloads/Trystram.srt"
fr-japonais  is not accepted as an input-target language
The accepted acronyms are:
['aed-es', 'af-de', 'af-en', 'af-es', 'af-fi', 'af-fr', 'af-sv', 'am-en', 'am-sv', 'ar-de', 'ar-en', 'ar-fi', 'ar-fr', 'as-en', 'ase-de', 'ase-en', 'ase-es', 'ase-fr', 'ase-sv', 'az-en', 'bcl-de', 'bcl-en', 'bcl-es', 'bcl-fi', 'bcl-fr', 'bcl-sv', 'bem-en', 'bem-es', 'bem-fi', 'bem-fr', 'bem-sv', 'ber-en', 'ber-es', 'ber-fr', 'bg-en', 'bg-es', 'bg-fi', 'bg-fr', 'bg-sv', 'bi-en', 'bi-es', 'bi-fr', 'bi-sv', 'bn-en', 'br-en', 'bs-en', 'bzs-en', 'bzs-es', 'bzs-fi', 'bzs-fr', 'bzs-sv', 'ca-de', 'ca-en', 'ca-es', 'ca-fr', 'ceb-en', 'ceb-es', 'ceb-fi', 'ceb-fr', 'ceb-sv', 'chk-en', 'chk-es', 'chk-fr', 'chk-sv', 'crs-de', 'crs-en', 'crs-es', 'crs-fi', 'crs-fr', 'crs-sv', 'cs-de', 'cs-en', 'cs-fi', 'cs-fr', 'cs-sv', 'csg-es', 'csn-es', 'cy-en', 'da-de', 'da-en', 'da-es', 'da-fi', 'da-fr', 'de-af', 'de-ase', 'de-bcl', 'de-bi', 'de-bzs', 'de-ca', 'de-crs', 'de-cs', 'de-da', 'de-de', 'de-ee', 'de-efi', 'de-el', 'de-en', 'de-eo', 'de-es', 'de-et', 'de-fi', 'de-fj', 'de-fr', 'de-gaa', 'de-gil', 'de-guw', 'de-ha', 'de-he', 'de-hil', 'de-ho', 'de-hr', 'de-ht', 'de-hu', 'de-ig', 'de-ilo', 'de-is', 'de-iso', 'de-it', 'de-kg', 'de-ln', 'de-loz', 'de-lt', 'de-lua', 'de-mt', 'de-niu', 'de-nl', 'de-nso', 'de-ny', 'de-pag', 'de-pap', 'de-pis', 'de-pl', 'de-pon', 'de-sk', 'de-sv', 'ee-de', 'ee-en', 'ee-es', 'ee-fi', 'ee-fr', 'ee-sv', 'efi-de', 'efi-en', 'efi-fi', 'efi-fr', 'efi-sv', 'el-en', 'el-fi', 'el-fr', 'el-sv', 'en-af', 'en-am', 'en-as', 'en-ase', 'en-bcl', 'en-bem', 'en-ber', 'en-bg', 'en-bi', 'en-bn', 'en-br', 'en-bs', 'en-bzs', 'en-ca', 'en-ceb', 'en-chk', 'en-chr', 'en-cjp', 'en-cop', 'en-crs', 'en-cs', 'en-cy', 'en-da', 'en-de', 'en-dop', 'en-ee', 'en-efi', 'en-el', 'en-eo', 'en-es', 'en-et', 'en-eu', 'en-fi', 'en-fj', 'en-fr', 'en-ga', 'en-gaa', 'en-gil', 'en-gl', 'en-guw', 'en-gv', 'en-ha', 'en-he', 'en-hil', 'en-ho', 'en-hr', 'en-ht', 'en-hu', 'en-id', 'en-ig', 'en-ilo', 'en-is', 'en-iso', 'en-it', 'en-jap', 'en-kg', 'en-kj', 'en-km', 'en-kqn', 'en-kwn', 'en-kwy', 'en-lg', 'en-ln', 'en-loz', 'en-lt', 'en-lu', 'en-lua', 'en-lue', 'en-lun', 'en-luo', 'en-lus', 'en-mfe', 'en-mg', 'en-mh', 'en-mk', 'en-ml', 'en-ml', 'en-mos', 'en-mr', 'en-ms', 'en-mt', 'en-nb', 'en-ne', 'en-ng', 'en-niu', 'en-nl', 'en-no', 'en-nso', 'en-ny', 'en-nyk', 'en-om', 'en-pag', 'en-pap', 'en-pis', 'en-pl', 'en-pon', 'en-pt', 'en-quw', 'en-rnd', 'en-ro', 'en-ru', 'en-run', 'en-rw', 'en-sg', 'en-sk', 'en-sl', 'en-sm', 'en-sn', 'en-so', 'en-sq', 'en-sr', 'en-srn', 'en-ss', 'en-st', 'en-sv', 'en-sw', 'en-swc', 'en-syr', 'en-ta', 'en-tdt', 'en-ti', 'en-tiv', 'en-tl', 'en-tll', 'en-tn', 'en-to', 'en-toi', 'en-tpi', 'en-ts', 'en-tt', 'en-tvl', 'en-tw', 'en-ty', 'en-uk', 'en-umb', 'en-ve', 'en-wa', 'en-wal', 'en-wls', 'en-wo', 'en-xh', 'en-yap', 'en-yo', 'en-zne', 'eo-de', 'eo-en', 'eo-es', 'eo-fr', 'eo-sv', 'es-aed', 'es-af', 'es-ase', 'es-bcl', 'es-ber', 'es-bg', 'es-bi', 'es-bzs', 'es-ca', 'es-ceb', 'es-crs', 'es-cs', 'es-csg', 'es-csn', 'es-da', 'es-de', 'es-ee', 'es-efi', 'es-el', 'es-en', 'es-eo', 'es-es', 'es-et', 'es-eu', 'es-fi', 'es-fj', 'es-fr', 'es-gaa', 'es-gil', 'es-gl', 'es-guw', 'es-ha', 'es-he', 'es-hil', 'es-ho', 'es-hr', 'es-ht', 'es-id', 'es-ig', 'es-ilo', 'es-is', 'es-iso', 'es-it', 'es-kg', 'es-ln', 'es-loz', 'es-lua', 'es-lus', 'es-mfs', 'es-mk', 'es-mt', 'es-niu', 'es-nl', 'es-nso', 'es-ny', 'es-pag', 'es-pap', 'es-pis', 'es-pl', 'es-pon', 'es-prl', 'es-ro', 'es-ru', 'es-rw', 'es-sg', 'es-sl', 'es-sm', 'es-sn', 'es-srn', 'es-st', 'es-swc', 'es-tll', 'es-tn', 'es-to', 'es-tpi', 'es-tvl', 'es-tw', 'es-ty', 'es-tzo', 'es-uk', 'es-ve', 'es-war', 'es-wls', 'es-xh', 'es-yo', 'es-yua', 'es-zai', 'et+fi-en', 'et+fi-fr', 'et-de', 'et-en', 'et-es', 'et-fi', 'et-fr', 'et-sv', 'eu-en', 'eu-es', 'fa-fi', 'fi+sv-en', 'fi-af', 'fi-ar', 'fi-bcl', 'fi-bem', 'fi-bg', 'fi-bzs', 'fi-ceb', 'fi-crs', 'fi-cs', 'fi-de', 'fi-ee', 'fi-efi', 'fi-el', 'fi-en', 'fi-es', 'fi-et', 'fi-fa', 'fi-fi', 'fi-fj', 'fi-fr', 'fi-fse', 'fi-gaa', 'fi-gil', 'fi-guw', 'fi-ha', 'fi-he', 'fi-hil', 'fi-ho', 'fi-hr', 'fi-ht', 'fi-hu', 'fi-id', 'fi-ig', 'fi-ilo', 'fi-is', 'fi-iso', 'fi-it', 'fi-kg', 'fi-kqn', 'fi-lg', 'fi-ln', 'fi-lu', 'fi-lua', 'fi-lue', 'fi-lus', 'fi-lv', 'fi-mfe', 'fi-mg', 'fi-mh', 'fi-mk', 'fi-mos', 'fi-mt', 'fi-niu', 'fi-nl', 'fi-nso', 'fi-ny', 'fi-pag', 'fi-pap', 'fi-pis', 'fi-pl', 'fi-pon', 'fi-ro', 'fi-ru', 'fi-run', 'fi-rw', 'fi-sg', 'fi-sk', 'fi-sl', 'fi-sm', 'fi-sn', 'fi-sq', 'fi-srn', 'fi-st', 'fi-sv', 'fi-sw', 'fi-swc', 'fi-tiv', 'fi-tll', 'fi-tn', 'fi-to', 'fi-toi', 'fi-tpi', 'fi-tr', 'fi-ts', 'fi-tvl', 'fi-tw', 'fi-ty', 'fi-uk', 'fi-ve', 'fi-vi', 'fi-war', 'fi-wls', 'fi-xh', 'fi-yap', 'fi-yo', 'fi-zne', 'fj-en', 'fj-fr', 'fr-af', 'fr-ase', 'fr-bcl', 'fr-bem', 'fr-ber', 'fr-bi', 'fr-bzs', 'fr-ca', 'fr-ceb', 'fr-crs', 'fr-de', 'fr-ee', 'fr-efi', 'fr-el', 'fr-en', 'fr-eo', 'fr-es', 'fr-et+fi', 'fr-fi', 'fr-fj', 'fr-gaa', 'fr-gil', 'fr-guw', 'fr-ha', 'fr-he', 'fr-hil', 'fr-ho', 'fr-hr', 'fr-ht', 'fr-hu', 'fr-id', 'fr-ig', 'fr-ilo', 'fr-iso', 'fr-kg', 'fr-kqn', 'fr-kwy', 'fr-lg', 'fr-ln', 'fr-loz', 'fr-lu', 'fr-lua', 'fr-lue', 'fr-lus', 'fr-mfe', 'fr-mh', 'fr-mos', 'fr-mt', 'fr-niu', 'fr-nl', 'fr-nso', 'fr-ny', 'fr-pag', 'fr-pap', 'fr-pis', 'fr-pl', 'fr-pon', 'fr-rnd', 'fr-ro', 'fr-ru', 'fr-run', 'fr-rw', 'fr-sg', 'fr-sk', 'fr-sl', 'fr-sm', 'fr-sn', 'fr-srn', 'fr-st', 'fr-sv', 'fr-swc', 'fr-tiv', 'fr-tll', 'fr-tn', 'fr-to', 'fr-tpi', 'fr-ts', 'fr-tum', 'fr-tvl', 'fr-tw', 'fr-ty', 'fr-uk', 'fr-ve', 'fr-war', 'fr-wls', 'fr-xh', 'fr-yap', 'fr-yo', 'fr-zne', 'fse-fi', 'ga-en', 'gaa-de', 'gaa-en', 'gaa-es', 'gaa-fi', 'gaa-fr', 'gaa-sv', 'gil-en', 'gil-es', 'gil-fi', 'gil-fr', 'gil-sv', 'gl-en', 'gl-es', 'guw-de', 'guw-en', 'guw-es', 'guw-fi', 'guw-fr', 'guw-sv', 'gv-en', 'ha-en', 'ha-es', 'ha-fi', 'ha-fr', 'ha-sv', 'he-de', 'he-en', 'he-fi', 'he-sv', 'hi-en', 'hil-de', 'hil-en', 'hil-fi', 'ho-en', 'hr-en', 'hr-es', 'hr-fi', 'hr-fr', 'hr-sv', 'ht-en', 'ht-es', 'ht-fi', 'ht-fr', 'ht-sv', 'hu-de', 'hu-en', 'hu-fi', 'hu-fr', 'hu-sv', 'hy-en', 'id-en', 'id-es', 'id-fi', 'id-fr', 'id-sv', 'ig-de', 'ig-en', 'ig-es', 'ig-fi', 'ig-fr', 'ig-sv', 'ilo-de', 'ilo-en', 'ilo-es', 'ilo-fi', 'ilo-sv', 'is-de', 'is-en', 'is-es', 'is-fi', 'is-fr', 'is-sv', 'iso-en', 'iso-es', 'iso-fi', 'iso-fr', 'iso-sv', 'it-de', 'it-en', 'it-es', 'it-fr', 'it-sv', 'ja-de', 'ja-en', 'ja-es', 'ja-fi', 'ja-fr', 'ja-sv', 'jap-en', 'ka-en', 'kab-en', 'kg-en', 'kg-es', 'kg-fr', 'kg-sv', 'kj-en', 'kl-en', 'ko-de', 'ko-en', 'ko-es', 'ko-fi', 'ko-sv', 'kqn-en', 'kqn-es', 'kqn-fr', 'kqn-sv', 'kwn-en', 'kwy-en', 'kwy-fr', 'kwy-sv', 'lg-en', 'lg-es', 'lg-fi', 'lg-fr', 'lg-sv', 'ln-de', 'ln-en', 'ln-es', 'ln-fr', 'loz-de', 'loz-en', 'loz-es', 'loz-fi', 'loz-fr', 'loz-sv', 'lt-de', 'lt-en', 'lt-es', 'lt-fr', 'lt-sv', 'lu-en', 'lu-es', 'lu-fi', 'lu-fr', 'lu-sv', 'lua-en', 'lua-es', 'lua-fi', 'lua-fr', 'lua-sv', 'lue-en', 'lue-es', 'lue-fi', 'lue-fr', 'lue-sv', 'lun-en', 'luo-en', 'lus-en', 'lus-es', 'lus-fi', 'lus-fr', 'lus-sv', 'lv-en', 'lv-es', 'lv-fi', 'lv-fr', 'lv-sv', 'mfe-en', 'mfe-es', 'mfs-es', 'mg-en', 'mg-es', 'mh-en', 'mh-es', 'mh-fi', 'mk-en', 'mk-es', 'mk-fi', 'mk-fr', 'ml-en', 'mos-en', 'mr-en', 'ms-en', 'mt-en', 'mt-es', 'mt-fi', 'mt-fr', 'mt-sv', 'nb-en', 'ne-en', 'ng-en', 'niu-de', 'niu-en', 'niu-es', 'niu-fi', 'niu-fr', 'niu-sv', 'nl-de', 'nl-en', 'nl-es', 'nl-fi', 'nl-fr', 'nl-sv', 'nn-en', 'nso-de', 'nso-en', 'nso-es', 'nso-fi', 'nso-fr', 'nso-sv', 'ny-de', 'ny-en', 'ny-es', 'nyk-en', 'om-en', 'pa-en', 'pag-de', 'pag-en', 'pag-es', 'pag-fi', 'pag-sv', 'pap-de', 'pap-en', 'pap-es', 'pap-fi', 'pap-fr', 'pis-en', 'pis-es', 'pis-fi', 'pis-fr', 'pis-sv', 'pl-de', 'pl-en', 'pl-es', 'pl-fi', 'pl-fr', 'pl-sv', 'pon-en', 'pon-es', 'pon-fi', 'pon-fr', 'pon-sv', 'prl-es', 'pt-en', 'rnd-en', 'rnd-fr', 'rnd-sv', 'ro-en', 'ro-fi', 'ro-fr', 'ro-sv', 'ru-en', 'ru-es', 'ru-fi', 'ru-fr', 'run-en', 'run-es', 'run-sv', 'rw-en', 'rw-es', 'rw-fr', 'rw-sv', 'sg-en', 'sg-es', 'sg-fi', 'sg-fr', 'sg-sv', 'si-en', 'sk-en', 'sk-es', 'sk-fi', 'sk-fr', 'sk-sv', 'sl-es', 'sl-fi', 'sl-fr', 'sl-sv', 'sm-en', 'sm-es', 'sm-fr', 'sn-en', 'sn-es', 'sn-fr', 'sn-sv', 'so-en', 'sq-en', 'sq-es', 'sq-fi', 'sq-sv', 'sr-en', 'srn-en', 'srn-es', 'srn-fr', 'srn-sv', 'ss-en', 'ssp-es', 'st-en', 'st-es', 'st-fi', 'st-fr', 'st-sv', 'sv-af', 'sv-ase', 'sv-bcl', 'sv-bem', 'sv-bg', 'sv-bi', 'sv-bzs', 'sv-ceb', 'sv-chk', 'sv-crs', 'sv-cs', 'sv-de', 'sv-ee', 'sv-efi', 'sv-el', 'sv-en', 'sv-eo', 'sv-es', 'sv-et', 'sv-fi', 'sv-fj', 'sv-fr', 'sv-gaa', 'sv-gil', 'sv-guw', 'sv-ha', 'sv-he', 'sv-hil', 'sv-ho', 'sv-hr', 'sv-ht', 'sv-hu', 'sv-id', 'sv-ig', 'sv-ilo', 'sv-is', 'sv-iso', 'sv-kg', 'sv-kqn', 'sv-kwy', 'sv-lg', 'sv-ln', 'sv-lu', 'sv-lua', 'sv-lue', 'sv-lus', 'sv-lv', 'sv-mfe', 'sv-mh', 'sv-mos', 'sv-mt', 'sv-niu', 'sv-nl', 'sv-nso', 'sv-ny', 'sv-pag', 'sv-pap', 'sv-pis', 'sv-pon', 'sv-rnd', 'sv-ro', 'sv-ru', 'sv-run', 'sv-rw', 'sv-sg', 'sv-sk', 'sv-sl', 'sv-sm', 'sv-sn', 'sv-sq']
```

## Conclusion

At the time of writing these lines, the tools presented here are free. Especially for the `openai-whisper` tool, no need for your `OPENAI_API_KEY`. That's a good point, but for how much longer?

We can also question ourselves on at least two points:

1. What is the quality of the translation?
2. For ecological reasons, is it better to use an online tool, even if it means making a lot of HTTP requests, or deploy the tools locally? We refer the reader to one of our other [GitHub](https://github.com/christophe-cerin/Ecoindex-Revisited) for the environmental impact of HTTP requests, and for using embedded systems to do machine learning to this [repository](https://github.com/christophe-cerin/OnlineML_ESP32).

