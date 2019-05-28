The ROOT-mpl rosetta stone
==========================
This is intended as a quick guide to matplotlib for long-time ROOT users (or vice-versa!)
It presents common idioms in both plotting languages.  For simplicity, we use pyROOT for the
ROOT interface, as there is a 1-to-1 equivalence between C++ ROOT and pyROOT functionality.

A typical import section in a ROOT plotting script will consist of
```python
import ROOT
```

A typical import section in a mplt plotting script will consist of
```python
import matplotlib.pyplot as plt
```

# Imperative plotting
Both ROOT and mpl offer an imperative plotting style with globals pointing to the current elements.
In ROOT, the currently active pad is `gPad`, and in mpl the currently active axes is available
as `plt.gca()`.  In both cases, it is advisable to not use these globals, but rather pass around
the needed pad or axes objects as necessary.


# Canvas / Figure
Both ROOT and mpl both have a canvas concept, although with slightly different naming schemes.
Below is a table of rough equivalences.

| ROOT | mpl    |
| ---- | ------ |
| `ROOT::TCanvas` [doc](https://root.cern.ch/doc/master/classTCanvas.html) | `matplotlib.figure.Figure` [doc](https://matplotlib.org/3.1.0/api/_as_gen/matplotlib.figure.Figure.html) |
| `ROOT::TPad` [doc](https://root.cern.ch/doc/master/classTPad.html) | `matplotlib.axes.Axes` [doc](https://matplotlib.org/3.1.0/api/axes_api.html#the-axes-class) |

The idiomatic way to create a new canvas and display it in (py)ROOT is:
```python
c = ROOT.TCanvas()
c.Draw()
```
Notice that in this case the `TCanvas` is also acting as the `TPad` (as `TCanvas` inheirits from `TPad`).

The idiomatic way to create a new figure and display it in mpl is:
```python
fig, ax = plt.subplots(1, 1)
fig.show()
```

To tile multiple pads via [Divide](https://root.cern.ch/doc/master/classTPad.html#a064b8ae1d12a9be393c0e22c5958cc7c):
```python
c = ROOT.TCanvas()
c.Divide(2, 2)
pad0 = c.cd(0)  # (row*nrow + col) indexing starting at upper left
```

To tile multiple axes via [subplots](https://matplotlib.org/api/_as_gen/matplotlib.pyplot.subplots.html)
(pay attention to the effect of the `squeeze` argument):
```python
fig, ax = plt.subplots(2, 2, squeeze=False)
ax0 = ax[0][0]  # row, col ordering starting at upper left
```

# Graph / Line
To draw a line through a collection of 2D points, and/or markers at each point, the ROOT facility is mainly
contained within `ROOT::TGraph` [doc](https://root.cern.ch/doc/master/classTGraph.html).
In mpl, this functionality is mostly contained within
`matplotlib.lines.Line2D` [doc](https://matplotlib.org/3.1.0/api/_as_gen/matplotlib.lines.Line2D.html).


# Text annotations
The largest challenge for annotations is how to specify the coordinates, whether they are axes coordinates
(i.e. corresponding to the values on the figure) or figure-global coordinates.  In ROOT, the former are
the default, and the latter are known as NDC coordinates.  Most functions (e.g. `TText::DrawText()`) have companion
functions (e.g. `TText::DrawTextNDC()`) to use global coordinates.  In matplotlib, a coordinate transform can be
passed to the `ax.annotate` function as `xycoords` [argument](https://matplotlib.org/3.1.0/api/_as_gen/matplotlib.axes.Axes.annotate.html).
For `ax.text`, the `transform` [argment](https://matplotlib.org/3.1.0/gallery/pyplots/text_layout.html#sphx-glr-gallery-pyplots-text-layout-py) is used.


# Style
Both ROOT and mpl provide stylesheet functionality, through [TStyle](https://root.cern.ch/doc/master/classTStyle.html) and
[rcParams](https://matplotlib.org/users/customizing.html), respectively.
An example library implementing some CMS experiment styles is available in https://github.com/andrzejnovak/CMSStylempl.
