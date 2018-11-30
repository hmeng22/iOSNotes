# CoreGraphics(Quartz2D)

* Quartz 2D is an advanced, two-dimensional drawing engine.
* The Quartz 2D API is part of the Core Graphics framework, so you may see Quartz referred to as Core Graphics or, simply, CG.
* Core Foundation : CF
* bits per component : bpc , bits per pixel : bpp 

## Graphics Context

CGContextRef : opaque data type, encapsulate the information Quatrz uses to draw images, drawing destination.

CGImageRef : used to represent bitmap images and bitmap image masks based on sample data that yousupply

### Creating a Bitmap Graphics Context

	CGBitmapContextCreate
	* data : Supply a pointer to the destination in memory where you want the drawing rendered. The size of this memory block should be at least (bytesPerRow*height) bytes.	* width : Specify the width, in pixels, of the bitmap. 
	* height : Specify the height, in pixels, of the bitmap.
	* bitsPerComponent : Specify the number of bits to use for each component of a pixel in memory. For example, for a 32-bit pixel format and an RGB color space, you would specify a value of 8 bits per component.
	* bytesPerRow : Specify the number of bytes of memory to use per row of the bitmap.
	* colorspace : The color space to use for the bitmap context. You can provide a Gray, RGB, CMYK, or NULL color space when you create a bitmap graphics context.
	* bitmapInfo : 

```objectivecCGColorSpaceRef colorSpace;￼￼￼void *bitmapData;
int bitmapByteCount;
int bitmapBytesPerRow;bitmapBytesPerRow   = (width * 4);bitmapByteCount     = (bitmapBytesPerRow * height);colorSpace = CGColorSpaceCreateWithName(kCGColorSpaceGenericRGB);bitmapData = calloc( bitmapByteCount );CGContextRef context = CGBitmapContextCreate(bitmapData,width,height,8,      // bits per componentbitmapBytesPerRow,colorSpace,kCGImageAlphaPremultipliedLast);CGColorSpaceRelease( colorSpace );
```

### Color and ColorSpace

ColorSpace|Components
----------|-----------
HSB       |Hue, Saturation, Brightness
RGB       |Red, Green, Blue
CMYK      |Cyan, Magenta, Yellow, Black
BGR       |Blue, Green, Red

### Current Transformation Matrix(CTM)

affine transform : which maps points from one coordinate space to another by applying translation, rotation, and scaling operations (calculations that move, rotate, and resize a coordinate system)

```objectivec
// It’s useful to write a radians routine if you plan to perform many rotations.
#include <math.h>static inline double radians (double degrees) {return degrees * M_PI/180;}
```

### Path, Pattern, Tiling, Transparency Layer, Blend


## Data Management in Quartz 2D

* URL : CFURLRef* CFData : CFDataRef and CFMutableDataRef 
* Raw data : You can provide a pointer to data of any type along with a set of callbacks that take care of basic memory management for the data.

## Data between Quartz 2D and Core Image

* Core Image methods operate on images that are packaged as Core Image images, or CIImage objects. 
* Core Image does not operate directly on Quartz images (CGImageRef data types). 
* Both images and image masks in Quartz are represented by the CGImageRef data type.
* Quartz images must be converted to Core Image images before you apply a Core Image filter to the image.
* The Quartz 2D API does not provide any functions that package Quartz images as Core Image images, but Core Image does.
	* imageWithCGImage:
	* imageWithCGImage:options:
	* imageWithCGLayer:
	* imageWithCGLayer:options:
	* createCGImage:fromRect:
	* createCGLayerWithSize:info:

### CGLayer : CGLayerRef
* High-quality offscreen rendering of drawing
* Repeated drawing
* Buffering