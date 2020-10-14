# pngcam

Pngcam takes in a heightmap and gives out Gcode to run a CNC machine.

## Usage

You'll need to represent your part in a heightmap in a PNG file. To facilitate cutting parts out of surrounding stock, and leaving
along features on exsiting parts, some special colours are supported:

Colour | Meaning
-------|--------
#ff00ff (magneta) | It's OK to cut this material but not necessary
#ff0000 (red) | Do not cut into this material

Apart from these special colours, the brightness of a pixel (defined as r+g+b) corresponds to the height, such that white is the highest and black
is the lowest.

As an example, let's look at first roughing out a shape with a 6mm end mill, and then move to a 2mm ball-nose end mill to finish up the part.
With both tools we'll get 2 passes over the part: one horizontal, and one vertical.

We'll start with the toolpath for the 6mm end mill. We'll have a maximum step-down of 1mm and step-over of 5mm, at 10000 rpm, and
we'll leave 0.25mm clearance from the final part for the finish pass.

Let's say we want the width of the heightmap to correspond to 100mm in the part, and we want the full brightness range to cover 10mm depth.

    $ pngcam --width 100 --depth 10 --tool-shape flat --tool-diameter 6 --step-down 1 --step-over 5 --speed 10000 --clearance 0.5 heightmap.png > pass1.gcode

And then essentially the same again, but this time with the 2mm ball-nose end mill, with reduced step-over and increased spindle speed.

    $ pngcam --width 100 --depth 10 --tool-shape ball --tool-diameter 2 --step-down 1 --step-over 0.2 --speed 20000 heightmap.png > pass1.gcode

The (0,0,0) point will be at the top left of the input image, with the part existing in the positive X direction and negative Y direction, and
with Z=0 at the top surface of the part (i.e. at "white" in the heightmap).

# Options

    $ pngcam --usage
    pngcam [options] PNGFILE > GCODEFILE

    Tool options:

        --tool-shape flat|ball
            Set the shape of the end mill.
            Default: ball

        --tool-diameter MM
            Set the diameter of the end mill in mm.
            Default: 6

    Tool control options:

        --step-down MM
            Set the maximum step-down in mm. Where the natural toolpath would exceed a cut of this depth, multiple passes are taken instead.
            Default: 100

        --step-over MM
            Set the distance to move the tool over per pass in mm.
            Default: 5

        --speed RPM
            Set the spindle speed in RPM.
            Default: 10000

    Path generation options:

        --clearance MM
            Set the clearance to leave around the part in mm. Intended so that you can come back again with a finish pass to clean up the part.
            Default: 0

        --route horizontal|vertical|both
            Set whether the tool will move in horizontal lines, vertical lines, or first horizontal followed by vertical.
            Default: both

    Heightmap options:

        --width MM
            Set the width of the image in mm. If height is not specified, height will be calculated automatically to maintain aspect ratio.
            Default: 100

        --height MM
            Set the height of the image in mm. If height is not specified, height will be calculated automatically to maintain aspect ratio.
            Default: 100

        --depth MM
            Set the total depth of the part in mm.
            Default: 10

# Contact

Pngcam is a program by James Stanley. You can email me at james@incoherency.co.uk or read my blog at https://incoherency.co.uk/
