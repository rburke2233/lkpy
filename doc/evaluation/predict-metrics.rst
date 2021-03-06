Prediction Accuracy Metrics
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. module:: lenskit.metrics.predict

The :py:mod:`lenskit.metrics.predict` module containins prediction accuracy metrics.

Metric Functions
----------------

.. autofunction:: rmse
.. autofunction:: mae

Working with Missing Data
-------------------------

LensKit rating predictors do not report predictions when their core model is unable
to predict.  For example, a nearest-neighbor recommender will not score an item if
it cannot find any suitable neighbors.  Following the Pandas convention, these items
are given a score of NaN (when Pandas implements better missing data handling, it will
use that, so use :py:meth:`pandas.Series.isna`/:py:meth:`pandas.Series.notna`, not the
``isnan`` versions.

However, this causes problems when computing predictive accuracy: recommenders are not
being tested on the same set of items. If a recommender only scores the easy items, for
example, it could do much better than a recommender that is willing to attempt more
difficult items.

A good solution to this is to use a *fallback predictor* so that every item has a
prediction.  In LensKit, :py:class:`lenskit.algorithms.basic.Fallback` implements
this functionality; it wraps a sequence of recommenders, and for each item, uses
the first one that generates a score.

You set it up like this::

    cf = ItemItem(20)
    base = Bias(damping=5)
    algo = Fallback(cf, base)
