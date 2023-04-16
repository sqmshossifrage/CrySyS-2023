First play with the API. It asks for two files of the same `MD5` hash but different content, i.e. a collision. The size of the files should be within 256 bytes. I first treated it as a web challenge and tried to fool the API using for example `{'f1': b'\n0a', 'f2': b'\x0a0a'}`, but no luck. 

Then this means we have to find collisions in `MD5` hashes. Searching online gives us a writeup of a similar CTF challenge [Collision Course](https://ctftime.org/writeup/24962) which eventually leads to a blog post [How I made two PHP files with the same MD5 hash](https://natmchugh.blogspot.com/2014/10/how-i-made-two-php-files-with-same-md5.html). 

First we setup a local web server to compute the raw `MD5` hash for a single block using [`MD5_no_padding.php`](https://gist.github.com/natmchugh/fbea8efeced195a2acf2). Then we could generate a pair of collision using [`fastcoll`](https://github.com/brimstone/fastcoll) on docker. However, what we have generated is using some random prefix message and the return from the API reminds us to play Wordle with the server! Moreover, we need to figure it out in a minute.

The server returns `.` when a correct letter is in the correct place; returns `!` when a correct letter is in the wrong place; returns `-` when the letter is not in the word. Since the server does tell us whenever a correct letter is in a correct place, we can just precompute all the collision pairs starting with `"aaaaa"`, `"bbbbb"`, until `"zzzzz"`. In the end, we only need 26 queries to find out what the word is (and avoid DoS the server). After knowing the word, we can manually generate a collision starting with the word. Finally, get the flag by making a request to the API with the collision starting with the correct word. 

Before generating the precomputed collisions, I counted the occurrence of each letter in the text file (turned out it did not help that much). In addition, we need to modify the last line of the php file to indicate where the file is to be hashed. 

When creating a single 64-byte block starting with letter `"aaaaa"`, we pad the rest with `'A'*59`. For example, 

```python
with open('./message_{}.bin'.format('a'), 'wb') as f:
    f.write(b'a'*5 + b'A'*59)
```
Then running `fastcoll` with docker 
```bash
docker run --rm -v $PWD:/work -w /work -u $UID:$GID brimstone/fastcoll -i [result from MD5_no_padding.php] -o m1 m2
```
After appending the single 64-byte block to both `m1` and `m2`, we obtain a collision of `MD5` hash that can be passed to the API. 

There's no trick left - just automate the whole process. 