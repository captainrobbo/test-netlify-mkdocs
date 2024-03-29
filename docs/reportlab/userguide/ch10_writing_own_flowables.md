#Writing your own `Flowable` Objects

Flowables are intended to be an open standard for creating
reusable report content, and you can easily create your
own objects.  We hope that over time we will build up
a library of contributions, giving reportlab users a
rich selection of charts, graphics and other "report
widgets" they can use in their own reports. This section
shows you how to create your own flowables.

##A very simple `Flowable`

Recall the `hand` function from the `pdfgen` section of this user guide which
generated a drawing of a hand as a closed figure composed from Bezier curves.

    illust(examples.hand, "a hand")

To embed this or any other drawing in a Platypus flowable we must define a
subclass of `Flowable`
with at least a `wrap` method and a `draw` method.
    eg(examples.testhandannotation)

The `wrap` method must provide the size of the drawing -- it is used by
the Platypus mainloop to decide whether this element fits in the space remaining
on the current frame.  The `draw` method performs the drawing of the object after
the Platypus mainloop has translated the `(0,0)` origin to an appropriate location
in an appropriate frame.

Below are some example uses of the `HandAnnotation` flowable.

    from reportlab.lib.colors import blue, pink, yellow, cyan, brown
    from reportlab.lib.units import inch

    handnote()

The default.

    handnote(size=inch)

Just one inch high.

    handnote(xoffset=3*inch, size=inch, strokecolor=blue, fillcolor=cyan)

One inch high and shifted to the left with blue and cyan.


##Modifying a Built in `Flowable`
To modify an existing flowable, you should create a derived class
and override the methods you need to change to get the desired behaviour
As an example to create a rotated image you need to override the wrap
and draw methods of the existing Image class
    
    import os
    from reportlab.platypus import *
    I = '../images/replogo.gif'

    EmbeddedCode("""
    class RotatedImage(Image):
        def wrap(self,availWidth,availHeight):
            h, w = Image.wrap(self,availHeight,availWidth)
            return w, h
        def draw(self):
            self.canv.rotate(90)
            Image.draw(self)
    I = RotatedImage('%s')
    I.hAlign = 'CENTER'
    """ % I,'I')
