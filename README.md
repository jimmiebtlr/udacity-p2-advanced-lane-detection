# Camera Calibration

The camera is calibrated by pulling in all images from the calibration data dir and making a list of all point transforms.  These points are then used to undistort the images.

# Pipeline



# Pipeline (Video)



# Discussion (Improvement ideas)

## Better use of multiple color channels

Currently we use s channel, with some h channel to do all lane detection.  It would be better to use a broader spectrum of potential color channels with various weightings making it into the final binary.  This would go a long way to making this algorithm more robust to lighting and color variations.

## Perspective transform

It currently works by transforming pixels into pixels of the same shape, which means our data will be a bit blurred in other views.  We could take an approach where pieces like the histogram are based on the unwarped image, and still use the perspective transform for debugging.

## Histogram

I don't really like the use of histogram to find the lane.  Given blob of yellow on screen somewhere, or another color matching a line, this will fail spectactularly.

I think the ideal to me would be to do some variety of line of best fit directly on the binary or image.  You could use the last lane position to make it easier (though I think it's best not to as mentioned below).

## Auto tuning

Much of this problem could be autotuned similar to the way we do deep learning.  However in an auto-tuned system we would maintain the same computer vision structure, explainability and debuggability for some serious benefits over throwing everything at a neural net.

## Redundancy options

Individual methods should stand on their own in most circumstances.  Redundant systems for the same purpose allow us to perform sanity checks, and reduce risks though.   

### Lane width vs Last lane location vs Pure line detection

Line detection should stand on it's own in most circumstances.  Where it's likely to fail are when lines are missing, extreme glare or other camera obstruction.

Last frames lane location to calculate the current will be fairly accurate most of the time assuming we're traveling in the direction of the lane.  If we lose traction and begin to spin in the car, this will be a bad measure.  This is a great measure for the rare turns when the curve is so great the line goes out of the image (granted doubt that'll really happen in real self driving cars due to 360 cameras).

Using lane width to help sanity check, or gain info about the location of an obstructed or missing line will work well when we have one line with high confidence, and a low confidence or missing line.  This method would break down if lane width is changing, or we can't even detect one line.  This method works great if one line is low confidence and/or out of the picture or in some other way obstructed.  This method will work best if we have an accurate estimation of lane width, and we can reduce problems due to elevation changes and height above the roadway changing (throwing our lane width calc off).

To conclude this section redundant systems can be used to increase confidence and robustness for non-ideal situations.  The methods should generally stand on their own however, and we should be careful not to use them as a crutch that hides issues in the redundant systems.
