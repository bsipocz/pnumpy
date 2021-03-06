# PNumPy
Parallel NumPy seamlessly speeds up NumPy for large arrays (64K+ elements) with *no change required to your existing NumPy code*.

PNumPy supports Linux, Windows, and MacOS for NumPy >= 1.18 for python 3.6, 3.7, 3.8, and 3.9.

This first release speeds up NumPy binary and unary ufuncs such as **add, multiply, isnan, abs, sin, log, sum, min and many more**.
Sped up functions also include: **sort, argsort, lexsort, arange, boolean indexing, and fancy indexing**.
In the near future we will speed up: **astype, where, putmask, and searchsorted**.

Other packages that use numpy, such as [scikit-learn](https://scikit-learn.org/stable/) or [pandas](https://github.com/pandas-dev/pandas), will also be sped up for large arrays.

[![CI Status](https://github.com/Quansight/pnumpy/workflows/tox/badge.svg)](https://github.com/Quansight/pnumpy/actions)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Installation
```
pip install pnumpy
```

You can also install the latest development versions with
```
pip install https://github.com/Quansight/pnumpy/archive/main.zip
```

## Documentation

See the [full documentation](https://quansight.github.io/pnumpy/stable/index.html)

To use the project:

```python
import pnumpy as pn
```

Parallel NumPy speeds up NumPy silently under the hood.  To see some benchmarks yourself run
```
pn.benchmark()
```
![plot](./doc_src/images/bench4graph2.PNG)
![plot](./doc_src/images/bench4graph3.PNG)

To get a partial list of functions sped up run
```
pn.atop_info()
```

To disable or enable pnumpy run
```
pn.disable()
pn.enable()
```

## Additional Functionality
PNumPy provides additional routines such as converting a NumPy record array to a column major array in parallel (**pn.recarray_to_colmajor**) which is useful for DataFrames.  Other routines include **pn.lexsort32**, which performs an indirect sort using **np.int32** instead of **np.int64** consuming half the memory and running faster.

## Threading
PNumPy uses a combination of threads and 256 bit vector intrinsics to speed up calculations.  By default most operations will only use 3 additional worker threads in combination with the main python thread for a total 4.  Large arrays are divided up into 16K chunks and threads are assigned to maintain cache coherency.  More threads are dynamically deployed for more intensive CPU problems like **np.sin**.  Users can customize threading.  The example below shows how 4 threads can work together to quadruple the effective L2 cache size.

![plot](./doc_src/images/threading_npadd.PNG)

To cap the number of additional worker threads to 3 run
```
pn.thread_setworkers(3)
```

To disable or re-enable threading run
```
pn.thread_disable()
pn.thread_enable()
```

To disable or re-enable just the atop engine run
```
pn.atop_disable()
pn.atop_enable()
```

## FAQ
**Q: If I type np.sort(a) where a is an array, will it be sped up?**

*A: If len(a) > 65536 and pnumpy has been imported, it will automatically be sped up*

**Q: How is sort sped up?**

*A: PNumPy uses additional threads to divide up the sorting job.  For example it might perform an 8 way quicksort followed by a 4 way mergesort*

**Q: How is scikit or pandas sped up?**

*A: PNumPy's vector loops and threads will speed up any package that uses large NumPy arrays*

## Development

To run all the tests run:

```
python -m pip install pytest
python -m pytest tests
```
