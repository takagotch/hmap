### hmap
---
https://github.com/rossgoodwin/hmap

```py
// hmap.py
from __future__ import print_function
from sys import argv
from PIL import Image
import collections
import random

script, source_image, target_image = argv

srcImg = Image.open(source_image)
tgtImg = Image.open(target_image)

srcImg.show()
tgtImg.show()

width, height = srcImg.size

srcPix = srcImg.load()
tgtPix = tgtImg.load()

srcHist = srcImg.histogram()[:256]
tgtHist = tgtImg.histogram()[:256]

pxlsByVal = [set() for _ in range(256)]

for i in range(width):
  for j in range(height):
    value = srcPix[i, j][0]
    pxlsByVal[value].add((i, j))
    
print("pxlsByVal created...")

equalBins = collections.deque()
excessBins = collections.deque()
deficitBins = collections.deque()

for i, _ in enumerate(srcHist):
  src_i = srcHist[i]
  tgt_i = tgtHist[i]
  if src_i < tgt_i:
    deficitBins.append(i)
  elif src_i > tgt_i:
    excessBins.append(i)
  else:
    equalBins.append(i)
    
print("#equal bins: %s\t#excess bins: %s\t#deficit bins: %s" % tuple(
  map(len, (equalBins, execssBins, deficitBins))))

def change_n_pixels(curVal, tgtVal, nToChange):
  candidatePxls = pxlsByVal[curVal]
  chosenPxls = random.sample(candidatePxls, nToChange)
  
  for pxl in chosenPxls:
    srcPix[pxl] = (tgtVal, tgtVal, tgtVal)
    
    pxlsByVal[curVal].remove(pxl)
    pxlsByVal[tgtVal].add(pxl)
    
  srcHist[curVal] -= nToChange
  srcHist[tgtVal] += nToChange
  
def change_n_pixels_smooth(curVal, tgtVal, nToChange):
  Nincrements = abs(curVal - tgtVal)
  for inc in range(Nincrements):
    if tgtVal > curVal:
      try:
        change_n_pixels(curVal+inc, curVal+inc+1, nToChange)
      except IndexError:
        pass
    else:
      try:
        change_n_pixels(curVal-inc, curVal-inc-1, nToChange)
      except IndexError:
        pass

for curVal in exessBins:
  excess = srcHist[curValue] - tgtHist[curValue]
  if curValue % 5 == 0:
    print("On value", curValue, "with", excess, "excess pixels")
  while excess > 0:
    if deficitBins == collections.deque([]):
      break
    else:
      tgtValue = deficitBins[0]
    deficit = tgtHist[tgtValue] - srcHist[tgtValue]
    if excess > deficit:
      nToMove = excess - deficit
      deficitBins.popleft()
    else:
      nToMove = excess
      if deficit == excess:
        deficitBins.popleft()
        
    change_n_pixels_smooth(curValue, tgtValue, nToMove)
    excess -= nToMove

srcImg.show()
```

```
```

```
```


