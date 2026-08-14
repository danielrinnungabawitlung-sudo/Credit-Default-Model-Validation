# Data

This repository does not store a raw copy of the dataset.

The analysis notebook downloads the public **Default of Credit Card Clients**
dataset directly from the UCI Machine Learning Repository using
[`ucimlrepo`](https://pypi.org/project/ucimlrepo/):

```python
from ucimlrepo import fetch_ucirepo

credit_default = fetch_ucirepo(id=350)
```

This makes the project reproducible while avoiding an unnecessary copy of the
source dataset in the repository. The original source is:

> Yeh, I. (2009). *Default of Credit Card Clients* [Dataset]. UCI Machine
> Learning Repository. https://doi.org/10.24432/C55S3H

`raw/` and `processed/` are local working directories and are intentionally
ignored by Git.
