I attempted this challenge after a lengthy and painful attempt for Existential CrySyS so this one appeared particularly easy. There is a `.gif` file and the title suggests us to [Read The Fun Manual](https://en.wikipedia.org/wiki/RTFM). So we first use [`exiftool`](https://exiftool.org/) and `strings` to look at the metadata and look for low-hanging fruit. It turns out we have to read the manual of [GIF89a](https://www.w3.org/Graphics/GIF/spec-gif89a.txt). In the 25th session of the manual, it describes the Plain Text Extension. Use a hex editor to open the file, search for hex pattern `21 01 0C`, read the plaintext content: first a `c`, then a `d`, then `2`, and so on. The following picture shows the first plaintext `c`. 

![rtfm](rtfm.png)

