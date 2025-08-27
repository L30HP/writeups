## Research

Looking at the supplied file final_output.b64, the extension (b64) and content (lots of / in dense text) was a giveaway that it's base64. Decoding to zip on a hunch. Otherwise would have looked at it with a hex editor like imhex, and seen the PK ascii at the start (which all zip files start with).

Unzipping revealed a 2 hour long tmp3.wav file which sounded like encoded data. Zooming in on the waveform in Audacity showed half-waves of around 20 and 10 samples at 48000 Hz.

Asking ChatGPT about it:

 ***Q:*** I've got a wav file with a single channel at 48000khz, with half-waves of approximately 10 or 20 samples width. What might this be?"
 
 <img width="1173" height="795" alt="image" src="https://github.com/user-attachments/assets/a83b030f-658a-4f3b-b53e-190e4a1feb4c" />


 ***A:*** ... that pattern screams 2-tone FSK (AFSK) switching between ~1.2 kHz and ~2.4 kHz. ...

Q: What software can I use to decode this?
 A: ... Minimodem (Linux, command-line) ... minimodem --rx 1200 -f yourfile.wav ...

Ok, let's try apt-get minimodem, and run it on our wav file. Success!!

This gave another base64-encoded file, yielding another zipped wav, which in turn gave a third - ultimately giving a text file with the flag!

You have to specify the baud rate at each stage. In this case 1200, 600 and 300 respectively. Looking at the wave lengths in Audacity would help, but I guessed.





Solution
sudo apt-get install minimodem unzip
mkdir temp
cd temp

base64 --decode < ../final_output.b64 > first.zip
unzip first.zip
minimodem --rx 1200 -f tmp3.wav > tmp3.b64

base64 --decode < tmp3.b64 > second.zip
unzip second.zip
minimodem -r 600 -f tmp2.wav > tmp2.b64

base64 --decode < tmp2.b64 > third.zip
unzip third.zip
minimodem -r 300 -f tmp1.wav > ../flag.txt
