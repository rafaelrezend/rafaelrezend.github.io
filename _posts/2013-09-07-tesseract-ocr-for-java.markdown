---
layout: post
title: Tesseract&#58; Open-source OCR library for Java
---

Weeks ago I was given a task to read values from an e-commerce website.
The idea was simple: a link was given, the application should parse the content of the HTML, download the specific value and store it.
I decided to use a crawler instead, but this is another story.
That's said, the task was indeed simple, except for the fact that values were images instead of text.

![](/images/journal/2013-09-07-tesseract-ocr-for-java/2013-09-07-tesseract-ocr-for-java.jpg)

So, I started my research on [Optical Character Recognition (OCR)](http://en.wikipedia.org/wiki/Optical_character_recognition) with two things in mind:
- I should not re-invent the wheel. Probably, there are couple of efficient libraries out there for this purpose. I would save time, and time is money, right?
- It must be open-source. Not because *it really must*, but because *I would like it to be*.

Making the story short, my research ended up with [tesseract-ocr](https://code.google.com/p/tesseract-ocr/). This OCR engine fulfills the criteria above, its usage is straightforward and, finally, it has been improved by *Google* (if you are a developer, you know, there is a status on it).

In few lines, here is the basic usage:

```java
// buffer that holds the image from url
BufferedImage priceImg = ImageIO.read(url);

// preventive for too small figures (invalid values) returned from
// the url
if (priceImg.getWidth() < 20 || priceImg.getHeight() < 20)
		return 0.0;

// buffer that holds the scaled image
BufferedImage scaledPriceImg = new BufferedImage(
				priceImg.getWidth() * SCALE, priceImg.getHeight() * SCALE,
				BufferedImage.TYPE_INT_RGB);

if (USE_GRAPHICS) {
		// Scale solution using Graphics
		Graphics2D grph = (Graphics2D) scaledPriceImg.getGraphics();
		grph.scale(SCALE, SCALE);
		grph.drawImage(priceImg, 0, 0, null);
		grph.dispose();
} else {
		// Scale solution using AffineTransform
		AffineTransform at = new AffineTransform();
		at.scale(SCALE, SCALE);
		AffineTransformOp scaleOp = new AffineTransformOp(at,
						AFFINE_TRANSFORMATION_TYPE);
		scaledPriceImg = scaleOp.filter(priceImg, scaledPriceImg);
}

// run the Tesseract OCR to get the text from the image
String priceStr = Tesseract.getInstance().doOCR(scaledPriceImg);

// get the german number format due to comma
NumberFormat format = NumberFormat.getInstance(Locale.GERMAN);
Double price = format.parse(priceStr).doubleValue();
```

To start with, `ImageIO.read(url)` simply reads the image from the URL.
Next, there is dumb validation because I knew that even the lowest values wouldn't fit in the dimension of 20x20 pixels.

While trying the solution with few examples, I realized that the images I was handling were too small for *tesseract*.
I know that scaling the images wouldn't improve their quality, because the resolution itself was poor. However, with `SCALE = 2` (for my specific case), I could place the image size in a range that *tesseract* is able to scan.
Notice that `scaledPriceImg` at that point is just a "frame" twice the size of my original image.

Afterwards, I used a flag `USE_GRAPHICS` to be able to switch between two different scaling conversion methods.
The first one is using `Graphics2D`. The visual result of this scale is satisfactory, but a bit blurry for OCR tools.

The second method uses `AffineTransform`. This utility provides three algorithms for scaling, and here it's denoted by my global variable `AFFINE_TRANSFORMATION_TYPE`.
The candidates are `AffineTransformOp.TYPE_BICUBIC`, `...TYPE_BILINEAR` and `...TYPE_NEAREST_NEIGHBOR`.

I choose the *bicubic* transformation with `AffineTransform` because it was more accurate for the figures I wanted to scan. This decision depends pretty much on your application.

Finally, I'm invoking the OCR tool itself `Tesseract.getInstance().doOCR(scaledPriceImg)`, followed by a formatting and a conversion to double.
After running the application for over 500 images, I've got an accuracy of around 95%.
The usage of *Tesseract* is really straightforward, but I realized that the pre-processing of images was the most relevant issue, with heavy impact on my results.

The OCR module for my specific scenario can be found [here](https://raw.github.com/rafaelrezend/eCommerceCrawler/master/src/rezend/ecomm/tools/OCRModule.java).



