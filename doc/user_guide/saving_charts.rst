.. currentmodule:: altair

.. _user-guide-saving:

Saving Altair Charts
--------------------
Altair chart objects have a :meth:`Chart.save` method which allows charts
to be saved in a variety of formats. 

.. saving-json:

JSON format
~~~~~~~~~~~
The fundamental chart representation output by Altair is a JSON string format;
one of the core methods provided by Altair is :meth:`Chart.to_json`, which
returns a JSON string that represents the chart content.
Additionally, you can save a chart to a JSON file using :meth:`Chart.save`,
by passing a filename with a ``.json`` extension.

For example, here we save a simple scatter-plot to JSON:

.. code-block:: python

    import altair as alt
    from vega_datasets import data

    chart = alt.Chart(data.cars.url).mark_point().encode(
        x='Horsepower:Q',
        y='Miles_per_Gallon:Q',
        color='Origin:N'
    )

    chart.save('chart.json')

The contents of the resulting file will look something like this:

.. code-block:: json

    {
      "$schema": "https://vega.github.io/schema/vega-lite/v6.json",
      "config": {
        "view": {
          "continuousHeight": 300,
          "continuousWidth": 300
        }
      },
      "data": {
        "url": "https://vega.github.io/vega-datasets/data/cars.json"
      },
      "encoding": {
        "color": {
          "field": "Origin",
          "type": "nominal"
        },
        "x": {
          "field": "Horsepower",
          "type": "quantitative"
        },
        "y": {
          "field": "Miles_per_Gallon",
          "type": "quantitative"
        }
      },
      "mark": {"type": "point"}
    }

This JSON can then be inserted into any web page using the vegaEmbed_ library.

.. saving-html:

HTML format
~~~~~~~~~~~
If you wish for Altair to take care of the HTML embedding for you, you can
save a chart directly to an HTML file using

.. code-block:: python

    chart.save('chart.html')

This will create a simple HTML template page that loads Vega, Vega-Lite, and
vegaEmbed, such that when opened in a browser the chart will be rendered.

For example, saving the above scatter-plot to HTML creates a file with
the following contents, which can be opened and rendered in any modern
javascript-enabled web browser:

.. code-block:: HTML

    <!DOCTYPE html>
    <html>
    <head>
      <script src="https://cdn.jsdelivr.net/npm/vega@6"></script>
      <script src="https://cdn.jsdelivr.net/npm/vega-lite@6"></script>
      <script src="https://cdn.jsdelivr.net/npm/vega-embed@7"></script>
    </head>
    <body>
      <div id="vis"></div>
      <script type="text/javascript">
        var spec = {
          "$schema": "https://vega.github.io/schema/vega-lite/v6.json",
          "config": {
            "view": {
              "continuousHeight": 300,
              "continuousWidth": 300
            }
          },
          "data": {
            "url": "https://vega.github.io/vega-datasets/data/cars.json"
          },
          "encoding": {
            "color": {
              "field": "Origin",
              "type": "nominal"
            },
            "x": {
              "field": "Horsepower",
              "type": "quantitative"
            },
            "y": {
              "field": "Miles_per_Gallon",
              "type": "quantitative"
            }
          },
          "mark": {"type": "point"}
        };
        var opt = {"renderer": "canvas", "actions": false};
        vegaEmbed("#vis", spec, opt);
      </script>
    </body>
    </html>

You can view the result here: `chart.html </_static/chart.html>`_.

By default, ``canvas`` is used for rendering the visualization in vegaEmbed. To 
change to ``svg`` rendering, use the ``embed_options`` as such:

.. code-block:: python

    chart.save('chart.html', embed_options={'renderer':'svg'})

If you need an HTML string object for further processing in custom HTML reports,
you can use the :meth:`Chart.to_html` method:

.. code-block:: python

    html_string = chart.to_html()
    # Use html_string in your custom HTML generation

The :meth:`Chart.to_html` method returns a string containing the HTML representation
of the chart, which can be embedded into larger HTML documents or processed
programmatically.


.. note::

   This is not the same as ``alt.renderers.enable('svg')``, what renders the 
   chart as a static ``svg`` image within a Jupyter notebook.


Offline HTML support
^^^^^^^^^^^^^^^^^^^^
By default, an HTML file generated by ``chart.save('chart.html')`` loads the necessary JavaScript dependencies from an online CDN location. This results in a small HTML file, but it means that an active internet connection is required in order to display the chart.

As an alternative, the ``inline=True`` keyword argument may be provided to ``chart.save`` to generate an HTML file that includes all necessary JavaScript dependencies inline. This results in a larger file size, but HTML files generated this way do not require an active internet connection to display.

.. code-block:: python

    chart.save('chart.html', inline=True)

.. note::

   Calling ``chart.save`` with ``inline=True`` requires :ref:`additional-dependencies`.


.. _saving-png:

PNG, SVG, and PDF format
~~~~~~~~~~~~~~~~~~~~~~~~
To save an Altair chart object as a PNG, SVG, or PDF image, you can use

.. code-block:: python

    chart.save('chart.png')
    chart.save('chart.svg')
    chart.save('chart.pdf')

.. note::

   :ref:`additional-dependencies` are required to save charts as images by running the javascript
   code necessary to interpret the Vega-Lite specification and output it in the form of an image.


altair_saver
^^^^^^^^^^^^

.. note::
   
   altair_saver was used in Altair 4 and earlier versions. It is no longer maintained and got superseded by vl-convert_ which provides a superior user experience and performance.


PNG Figure Size/Resolution
^^^^^^^^^^^^^^^^^^^^^^^^^^
When using ``chart.save()`` to create a PNG image, the resolution of the resulting image
defaults to 72 pixels per inch (ppi). To change the resolution of the image, while maintaining
the same physical size, the ``ppi`` argument may be provided to ``chart.save``. For example,
to save the image with a resolution of 200 pixels-per-inch::

    chart.save('chart.png', ppi=200)


To change the physical size of the resulting image while preserving the resolution, the
``scale_factor`` argument may be used. For example, to save the image at double the default
size at the default resolution of 72 ppi::

    chart.save('chart.png', scale_factor=2)

.. _additional-dependencies:

Additional Dependencies
~~~~~~~~~~~~~~~~~~~~~~~
Saving charts to images or offline HTML files requires the vl-convert_ package::

    conda install -c conda-forge vl-convert-python

or::

    pip install vl-convert-python

vl-convert_ does not require any external dependencies.
See the vl-convert documentation for information and for known
`limitations <https://github.com/vega/vl-convert#limitations>`_.

Sharable URL
~~~~~~~~~~~~
The :meth:`Chart.to_url` method can be used to build a sharable URL that opens the chart
specification in the online Vega editor_.

.. altair-plot::
    :output: repr

    import altair as alt
    from vega_datasets import data

    chart = alt.Chart(data.cars.url).mark_point().encode(
        x='Horsepower:Q',
        y='Miles_per_Gallon:Q',
        color='Origin:N'
    )

    chart.to_url()

.. _vl-convert: https://github.com/vega/vl-convert
.. _vegaEmbed: https://github.com/vega/vega-embed
.. _editor: https://vega.github.io/editor/
