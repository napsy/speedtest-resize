speedtest-resize
================

Compare various Image resize algorithms for the Go language

I am writing a web gallery called gonagall in Go (https://github.com/fawick/gonagall). For that, I need a efficient 
solution for scaling and resizing a lot of images (mostly JPGs) to generate thumbnails and bandwidth-friendly sized copies
from high-resolution original photo files.

In this project I compare the speed of a few selected image resizing algorithms with each other as well as with 
ImageMagick and GraphicsMagick. The competitors are 

- [resize.go](https://code.google.com/p/appengine-go/source/browse/example/moustachio/resize/resize.go) by the Go authors, 
which performs a Nearest-Neighbor scaling
- https://github.com/nfnt/resize, Pure golang image resizing, more precisely only Nearest-Neighbor interpolation that comes 
with that Go package.
- https://github.com/disintegration/imaging Again, I use one of the fastests algorithms of the package. Here, it's called 
'Box'
- [ImageMagick convert](http://www.imagemagick.org/script/convert.php) with the options `-resize 150x150>`
- [ImageMagick convert](http://www.imagemagick.org/script/convert.php) with the options 
`-define "jpeg:size=300x300 -thumbnail 150x150>`. `-thumbnail` is considered to be faster than resize, and the `-define` 
will reduce the size (in terms of memory footprint) of the original image on reading.
- [GraphicsMagick convert](http://www.graphicsmagick.org/convert.html)

To run the tests my test, `go get` the source and compile/run it:

    $ go get github.com/fawick/speedtest-resize
    $ cd $GOPATH/src/speedtest-resize
    $ go run main.go <jpg file folder>

Alternatively, call the go command (or the compiled binary) from the image folder without 
supplying a parameter

    $ cd <jpg file folder>
    $ go run $GOPATH/src/speedtest-resize/main.go

Im my test scenario all of these tools/packages are unleashed on a directory containing 217 JPG photo files, all of which 
have a resolution of 5616x3744 pixels (aspect ratio 2:1, both landscape and portrait). For each tool/package and for all 
files, the total time for loading the original file, scaling the image to a thumbnail of 150x100 pixels, and writing it 
to a new JPG file is measured. In the end, the total runtime for processing all files and the average time per file is 
printed for each tool/package.

The scenario is run on a Intel(R) Pentium(R) Dual T2390 @ 1.86GHz running Ubuntu 12.10. Here are the results:

| Tables                            | Duration      | Average  |
| --------------------------------- |:-------------:| --------:|
| resize.go                         |               |          |
| github.com/nfnt/resize            |               |          |
| github.com/disintegration/imaging |               |          |
| ImageMagick -resize               |               |          |
| ImageMagick -thumbnail            |               |          |
| GraphicsMagick -thumbnail         |               |          |

So, what is to learn from that? While all of the currently existing pure-Go-language solutions do a pretty good job 
in generating good-looking thumbnails, they are much slower than the veteran dedicated image processing toolboxes. That is 
hardly suprising, given that both  ImageMagick and GraphicsMagick have been around for decades and have been optimized to work
as efficient as possible. Go and its image processing packages are still the new kids on the block, and while they work
pretty neat for the occasional tweak of an image or two, I rather not use them as the default image processor
in [gonagall](http://github.com/fawick/gonagall) yet. 

TODO
- Add competitor: https://github.com/gographics/imagick
