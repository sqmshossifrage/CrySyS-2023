This was the first challenge I attempted but actually spent some time on it. 

The text is written in [Braille](https://en.wikipedia.org/wiki/Braille). First try to decode them directly using [dCode](https://www.dcode.fr/braille-alphabet) but the result is clearly not readable. Then we have to actually inspect the text. There are 9 kinds of characters in it `⠀⠂⠆⠒⠖⠢⠲⠴⠶` and the "all empty" character `⠀` appears periodically after two or three other characters. If we treat `⠀` as a separator, the rest characters form a set of 8-bit encoding. 

However I am confused at this point because it is hard to tell which character is corresponding to which number. I tried to associate numbers based on patterns of dots but it wouldn't decode to anything meaningful. Searching "Braille encoding" online, we find this page [Braille ASCII](https://en.wikipedia.org/wiki/Braille_ASCII) containing a correspondence between Braille and ASCII characters. Then everything is clear: 
1. The text decodes into a string of oct numbers separated by spaces. 
2. Converting each oct number into a dec number, we realize the number lies in our comfort zone of printable characters. 
3. Concatenating these characters, we have a string of numbers and capital letters with `======` at the end of the string.
4. The long `======` padding suggests the string is [`Base32`](https://en.wikipedia.org/wiki/Base32) encoded. Use [CyberChef](https://gchq.github.io/CyberChef/) to decode the string. 
5. The decoded string seems to be `Base64` encoded... again, decode the string. 
6. Something starting with `pq23{` appears. I submitted it immediately but wait... isn't the flag starting with `cd23{`? Note that `pq` are consecutive letters, so are `cd`. This is a strong suggestion for [`ROT13`](https://en.wikipedia.org/wiki/ROT13). Decode again and the flag finally appears. 

Take away: I should have searched for the Braille ASCII encoding before trying to decode it myself. 