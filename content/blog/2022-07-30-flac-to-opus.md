+++
date = 2022-07-30
title = "Recursive Command-Line FLAC to Opus Conversion"
description = "A retrospective on how I recursively converting my FLAC file music library to OPUS with opusenc."
+++

## Converting FLAC to OPUS

I am currently rebuilding my music library from scratch so that I can 
effectively archive all of the music I own in the [FLAC file 
format](https://en.wikipedia.org/wiki/FLAC), a lossless audio codec.

However, streaming FLAC files outside of the home can be difficult due to the 
size of the files, especially if you're using a weak connection.

So, in order to archive the music in a lossless format and still be able to 
stream it easily, I opted to create a copy of my FLAC files in the [Opus audio 
codec](https://en.wikipedia.org/wiki/Opus_(audio_format)). This allows me to 
archive a quality, lossless version of the music and then point my streaming 
service to the smaller, stream-ready version.

### Dependencies

The process I follow utilizes the `opus-tools` package in Ubuntu. Before 
proceeding, install the package:

```bash
sudo apt install opus-tools
```

If you want to use a different conversion method, such as `ffmpeg` or `avconv`, 
simply install that package instead.

### Conversion Process

The script I'm using is stored in my home directory, but feel free to create it 
wherever you want. It does not need to be in the same directory as your music 
files.

```bash
cd ~ && nano transform.sh
```

Once you have your new bash script opened in an editor, go ahead and paste the 
following logic into the script.

You **MUST** edit the following variables in order for it to work:

- `source`: The source directory where your FLAC files are stored.
- `dest`: The destination directory where you want the resulting Opus files to 
be stored.

You **MAY** want to edit the following variables to suit your needs:

- `filename`: If you are converting to a file format other than Opus, you'll 
need to edit this so that your resulting files have the correct filename 
extension.
- `reldir`: This variable can be edited to strip out more leading directories in 
the file path. As you'll see later, I ignore this for now and simply clean it up 
afterward.
- `opusenc`: This is the actual conversion process. You may want to edit the 
bitrate to suit your needs. I set mine at 128 but some prefer 160 or higher.

```bash
#!/bin/bash
## - The IFS takes care of spaces in file and dirnames
## - your folders may vary
## - what you mount to the folders does not matter
## - in RELDIR, the f5 most likely MUST be edited,
##    since its responsible, how many leading directories
##    will be removed from the directory structure in order
##    to append that exact path to the outfile
## - the commented echos are still in place in order to give
##    you the variables for testing, before running.

IFS=$'\n'

## the paths given here contain the directory structure that I want to keep
## source=/mnt/music/archives/ARTIST/ALBUM/FLACFILE.flac
## local=/mnt/music/library/ARTIST/ALBUM/OPUSFILE.opus

source=/mnt/music/archives
dest=/mnt/music/library

for i in $(find $source -type f -iname '*.flac' );
do
## SET VARIABLES for PATHS and FILENAMES
        fullfile=$i
        filename="${i##*/}"
        filename="${filename%.*}.opus"
        fulldir=$(dirname "${i}")
        reldir="$(echo $fulldir | cut -d'/' -f5-)"
        reldir=${reldir//flac}
        outdir="$dest/$reldir"
        outfile="$outdir/$filename"

# is that working?
# outfile='$local/""$(echo $(dirname "${i}") | cut -d'/' -f5-)"//flac"/"${i##*/}"'
#       echo 'output file: ' "$outfile"

## SHOW ME THE CONTENTS of the VARIABLES
#       echo 'File found:' "$i"
#       echo 'Relative dir: ' "$reldir"
#       echo 'directory will be created: ' "$outdir"
#       echo 'Filename: ' "$filename"
#       echo 'FileExt: ' "$extension"
#       echo 'output file: ' "$outfile"

echo "\n\n"

## CREATE Output Folders
        mkdir -p "$outdir"

## RUN
# ffmpeg and avconv are alternative options if opusenc isn't adequate
opusenc --vbr --bitrate 128 --date "$DATE" \
--title "$TITLE" --artist "$ARTIST" --album "$ALBUM" --genre "$GENRE" \
--comment "ALBUMARTIST=$ALBUMARTIST" --comment "DISCNUMBER=$DISCNUMBER" \
--comment "TRACKNUMBER=$TRACKNUMBER" --comment "TRACKTOTAL=$TRACKTOTAL" \
--comment "LYRICS=$LYRICS" "$fullfile" "$outfile"


## just for testing
#        sleep 1
done
```

Once you're done, simply save the file and exit your editor. Don't forget to 
enable execution of the script:

```bash
chmod +x transform.sh
```

Finally, you may now run the script:

```bash
./transform.sh
```

If you used `opusenc`, you'll see the conversions happen within the terminal as 
it progresses. You will also see variables printed if you un-commented any of 
the bash script's comments.

### Cleanup

As I noted above, I didn't customize my `reldir` variable in the script, which 
caused my output directory to be `/mnt/music/library/archives` instead of 
`/mnt/music/library`. So, I moved the output up one level and deleted the 
accidental directory.

```bash
cd /mnt/music/library
mv archives/* .
rm -rf archives
```

### Check Resulting Size

If you want to see what kind of file size savings you've gained, you can always 
use the `du` command to check:

```bash
cd /mnt/music
du -h --max-depth=1 .
```

In my case, my small library went from 78GB all the way down to 6.3GB!

```txt
78G    ./archives
6.3G   ./library
```
