# ![DCV](https://github.com/libmir/dcv/raw/gh-pages/images/dcv_logo.png)

[![Build Status](https://travis-ci.org/libmir/dcv.svg?branch=master)](https://travis-ci.org/libmir/dcv) 
[![codecov.io](https://codecov.io/github/libmir/dcv/coverage.svg?branch=master)](https://codecov.io/github/libmir/dcv?branch=master) 
[![DUB](https://img.shields.io/dub/v/dcv.svg)](http://code.dlang.org/packages/dcv) 
[![Gitter](https://img.shields.io/gitter/room/libmir/public.svg)](https://gitter.im/libmir/public) 

*Computer Vision Library for D Programming Language*

## API Refactoring Note

Library's **API** is currently under [heavy reconstruction](https://github.com/libmir/dcv/issues/87). Until the API is considered properly designed, **implementation of new features will be put on hold**.

## About

DCV is an open source computer vision library, written in D programming language, with goal to provide tools for solving most common computer vision problems - various image processing tasks, feature detection and tracking, camera calibration, stereo etc.

## Focus

Focus of the library is to present an easy-to-use interface, that would attract computer vision scientists and engineers to prototype their solutions with, but also to provide fast running code that could be used to make production ready tools and applications.

## API

DCV's API heavily utilizes [Mir](https://github.com/libmir/mir) library, and it's `mir.ndslice` package. N-dimensional range view 
structure [Slice](https://github.com/libmir/mir/blob/master/source/mir/ndslice/slice.d) is used for any form of image manipulation 
and processing. But overall shape of the API is adopted from well known computer vision toolkits such as Matlab Image Processing 
Toolbox, and OpenCV library, to be easily familiarized with. But it's also spiced up with D's syntactic sugar, to support pipelined calls:

```d
Image image = imread("/path/to/image.png"); // read an image from filesystem.
scope(exit) destroyFree(image);

auto slice = image.sliced; // slice image data (calls mir.ndslice.slice.sliced on image data)

slice[0..$, 0..$, 1] // take the green channel only.
    .as!float // convert slice data to float. Returns a lazy range
    .rcslice // allocate and make a copy with new type (ref counted slice)
    .conv!symmetric(sobel!float(GradientDirection.DIR_X)) // convolve image with horizontal Sobel kernel.
    .ranged(0, 255) // scale values to fit the range between the 0 and 255
    .imshow("Sobel derivatives"); // preview changes on screen.

waitKey();
destroyFigures(); // free all allocated figures
```
- In the new API, all functions accept slice shells as Slice!(T*, N) as input. On the other hand, API functions return RC-allocated Slice!(RCI!T, N). In this way, ref-counted slices can be passed to API functions like input.lightScope. It is recommended to avoid using lightScope in loop bodies; instead, create a lightScope outside of the loop body before using it. It has a little cost.
- nogc capabilities of mir libraries and dplug:core are utilized when needed.
- ThreadPool of dplug:core is used in the entire library for parallelism.
- class Image and class Figure use manual memory management. A call of function destroyFigures deallocates all allocated figures automatically. If an Image instance is initialized with non-null ubyte[] data, this time, the Image instance behaves like a slice shell, and it does not attempt to deallocate the borrowed data slice.
- All examples are up-to-date. Please refer to the examples instead of the docs at the moment.

## Documentation (outdated at the moment)

API reference, and examples can be found on the project website: [dcv.dlang.io](http://dcv.dlang.io/). Also project roadmap, news and other related stuff should be always located on the site's home page.

## Contributions
PRs and any form of help is most appreciated. Also, you can file an issue for feature request, bug report or any other library related inquiry. If you have any sort of quick question, feel free to post it in the gitter room.

## License
Library is licensed under Boost Software License - Version 1.0. Some modules in the library contain code that is licensed under some other terms. If a module in the library states different license terms in it's header, then the Boost Software License does not apply to that module.