CBconvert
=========

Introduction
------------

CBconvert is a [Comic Book](http://en.wikipedia.org/wiki/Comic_Book_Archive_file) converter written in [Go language](https://golang.org/).

It can convert one comic at a time or bulk convert comics to different formats to fit your various devices.

![screenshot](https://goo.gl/AxsWsA)

Features
--------

 - reads RAR, ZIP, 7Z, GZ, BZ2, CBR, CBZ, CB7, CBT, PDF, EPUB, XPS and plain directory
 - always saves processed comic in CBZ (ZIP) archive format
 - images can be converted to JPEG, PNG, GIF, TIFF or 4-Bit BMP (16 colors) file format
 - rotate, flip, adjust brightness/contrast, adjust levels (Photoshop like) or grayscale images
 - choose resize algorithm (NearestNeighbor, Box, Linear, MitchellNetravali, CatmullRom, Gaussian, Lanczos)
 - export covers from comics
 - create thumbnails from covers by [freedesktop](http://www.freedesktop.org/wiki/) specification

Download
--------

 - [Windows GUI](https://github.com/gen2brain/cbconvert/releases/download/0.6.0/cbconvert-0.6.zip)
 - [Windows CMD](https://github.com/gen2brain/cbconvert/releases/download/0.6.0/cbconvert-cmd-0.6.zip)

 - [Linux 64bit GUI](https://github.com/gen2brain/cbconvert/releases/download/0.6.0/cbconvert-0.6.tar.gz)
 - [Linux 64bit CMD](https://github.com/gen2brain/cbconvert/releases/download/0.6.0/cbconvert-cmd-0.6.tar.gz)

Using cbconvert in file managers to generate freedesktop thumbnails
-------------------------------------------------------------------

Just copy cbconvert cmd binary to your PATH and create file ~/.local/share/thumbnailers/cbconvert.thumbnailer :
    
    [Thumbnailer Entry]
    TryExec=cbconvert
    Exec=cbconvert thumbnail --quiet --width %s --outfile %o %i
    MimeType=application/pdf;application/x-pdf;image/pdf;application/x-cbz;application/x-cbr;application/x-cb7;application/x-cbt;application/oxps;application/vnd.ms-xpsdocument;application/epub+zip;

This is how it looks like in PCManFM file manager:

![thumbnails](https://goo.gl/I39Otm)


Using command line app
----------------------

    usage: cbconvert [<flags>] <command> [<args> ...]

    Comic Book convert tool.

    Flags:
      --help               Show context-sensitive help (also try --help-long and --help-man).
      --version            Show application version.
      --outdir="."         Output directory
      --size=0             Process only files larger then size (in MB)
      --recursive          Process subdirectories recursively
      --quiet              Hide console output

    Args:
      <args>  filename or directory

    Commands:
      help [<command>...]
        Show help.


      convert [<flags>] <args>...
        Convert archive or document (default command)

        --width=0            Image width
        --height=0           Image height
        --fit                Best fit for required width and height
        --format="jpeg"      Image format, valid values are jpeg, png, gif, tiff, bmp
        --quality=75         JPEG image quality
        --filter=2           0=NearestNeighbor, 1=Box, 2=Linear, 3=MitchellNetravali, 4=CatmullRom, 6=Gaussian, 7=Lanczos
        --cover              Convert cover image (use --no-cover if you want to exclude cover)
        --rgb                Convert images that have RGB colorspace (use --no-rgb if you only want to convert grayscaled images)
        --nonimage           Leave non image files in archive (use --no-nonimage to remove non image files from archive)
        --grayscale          Convert images to grayscale (monochromatic)
        --rotate=0           Rotate images, valid values are 0, 90, 180, 270
        --flip="none"        Flip images, valid values are none, horizontal, vertical
        --brightness=0       Adjust brightness of the images, must be in range (-100, 100)
        --contrast=0         Adjust contrast of the images, must be in range (-100, 100)
        --suffix=SUFFIX      Add suffix to file basename
        --levels-inmin=0     Shadow input value
        --levels-inmax=255   Highlight input value
        --levels-gamma=1     Midpoint/Gamma
        --levels-outmin=0    Shadow output value
        --levels-outmax=255  Highlight output value

      cover [<flags>] <args>...
        Extract cover

        --width=0     Image width
        --height=0    Image height
        --fit         Best fit for required width and height
        --quality=75  JPEG image quality
        --filter=2    0=NearestNeighbor, 1=Box, 2=Linear, 3=MitchellNetravali, 4=CatmullRom, 6=Gaussian, 7=Lanczos

      thumbnail [<flags>] <args>...
        Extract cover thumbnail (freedesktop spec.)

        --width=0   Image width
        --height=0  Image height
        --fit       Best fit for required width and height
        --filter=2  0=NearestNeighbor, 1=Box, 2=Linear, 3=MitchellNetravali, 4=CatmullRom, 6=Gaussian, 7=Lanczos

[man page](https://en.wikipedia.org/wiki/Man_page) is also available:

    cbconvert --help-man | man /dev/stdin

Examples
--------

Rescale images to 1200px for all supported files found in directory with size larger then 60MB:

    cbconvert --recursive --width 1200 --size 60 /media/comics/Thorgal/

Convert all images in pdf to 4bit BMP image and save result in ~/comics directory:

    cbconvert --bmp --outdir ~/comics /media/comics/Garfield/Garfield_01.pdf

[BMP](http://en.wikipedia.org/wiki/BMP_file_format) format is very good choice for black&white pages. Archive size can be smaller 2-3x and file will be readable by comic readers.

Generate thumbnails by [freedesktop specification](http://specifications.freedesktop.org/thumbnail-spec/thumbnail-spec-latest.html) in ~/.thumbnails/normal directory with width 512:

    cbconvert thumbnail --width 512 --outdir ~/.thumbnails/normal /media/comics/GrooTheWanderer/

Extract covers to ~/covers dir for all supported files found in directory, Lanczos algorithm is used for resizing:

    cbconvert cover --outdir ~/covers --filter=7 /media/comics/GrooTheWanderer/

Compile
-------

Install imagemagick dev packages:

    apt-get install libmagickcore-dev libmagickwand-dev

Compile latest MuPDF:

    git clone git://git.ghostscript.com/mupdf.git && cd mupdf
    git submodule update --init --recursive
    curl -L https://gist.githubusercontent.com/gen2brain/7869ac4c6db5933f670f/raw/1619394dc957ae10bcd73c713760993466b4bfea/mupdf-openssl-curl.patch | patch -p1
    sed -e "1iHAVE_X11 = no" -e "1iWANT_OPENSSL = no" -e "1iWANT_CURL = no" -i Makerules
    HAVE_X11=no HAVE_GLFW=no HAVE_GLUT=no WANT_OPENSSL=no WANT_CURL=no HAVE_MUJS=yes HAVE_JSCORE=no HAVE_V8=no make && make install

Compile unarr library:

    git clone https://github.com/zeniko/unarr && cd unarr
    mkdir lzma920 && cd lzma920 && curl -L http://www.7-zip.org/a/lzma920.tar.bz2 | tar -xjvp && cd ..
    curl -L http://zlib.net/zlib-1.2.8.tar.gz | tar -xzvp
    curl -L http://www.bzip.org/1.0.6/bzip2-1.0.6.tar.gz | tar -xzvp
    curl -L https://gist.githubusercontent.com/gen2brain/89fe506863be3fb139e8/raw/8783a7d81e22ad84944d146c5e33beab6dffc641/unarr-makefile.patch | patch -p1
    CFLAGS="-DHAVE_7Z -DHAVE_ZLIB -DHAVE_BZIP2 -I./lzma920/C -I./zlib-1.2.8 -I./bzip2-1.0.6" make
    cp build/debug/libunarr.a /usr/lib64/ && cp unarr.h /usr/include

Install dependencies:

    go get github.com/cheggaaa/pb
    go get github.com/disintegration/imaging
    go get github.com/gen2brain/go-fitz
    go get github.com/gen2brain/go-unarr
    go get github.com/gographics/imagick/imagick
    go get github.com/hotei/bmp
    go get github.com/skarademir/naturalsort
    go get golang.org/x/image/tiff
    go get golang.org/x/image/webp
    go get gopkg.in/alecthomas/kingpin.v2

For command line app:

    go get github.com/gen2brain/cbconvert
    go build -o $GOPATH/bin/cbconvert github.com/gen2brain/cbconvert/cmd

For GUI app:

    go get gopkg.in/qml.v1
    go get github.com/gen2brain/cbconvert
    go build -o $GOPATH/bin/cbconvert github.com/gen2brain/cbconvert/gui
