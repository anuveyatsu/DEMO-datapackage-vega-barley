This is an example dataset that demonstrates how to build visualizations using the "Vega Graph Spec". We are using Yields of Barley Based on the [Trellis Display][trellis] - one of the examples from [vega editor][editor] - and displaying it here, on DataHub with small modifications in vega-spec.

## Views

We assume that you are familiar with what [datapackage.json][datapackage.json] is and its specifications.

To create graphs for your tabular data, the `datapackage.json` should include the `views` attribute that is used for visualizations.

If you are familiar with [Vega][vega] specifications, you probably would like to use all its features. To use it, inside `views` you should set `specType` to `"vega"` and define some graph specifications in `spec` property. See below for more details.

### Vega Graph Specifications

This is the `views` property that is used for this page:

```
{
  ...,
  "views": [
    {
      "title": "DEMO Vega spec view",
      "resources": ["barley"],
      "specType": "vega",
      "spec": {
        "$schema": "https://vega.github.io/schema/vega/v3.0.json",
        "width": 200,
        "padding": 5,
        "signals": [
          {
            "name": "offset",
            "value": 15
          },
          {
            "name": "cellHeight",
            "value": 100
          },
          {
            "name": "height",
            "update": "6 * (offset + cellHeight)"
          }
        ],
        "data": [
          {
            "name": "barley"
          }
        ],
        "scales": [
          {
            "name": "gscale",
            "type": "band",
            "range": [
              0,
              {
                "signal": "height"
              }
            ],
            "round": true,
            "domain": {
              "data": "barley",
              "field": "site",
              "sort": {
                "field": "yield",
                "op": "median",
                "order": "descending"
              }
            }
          },
          {
            "name": "xscale",
            "type": "linear",
            "nice": true,
            "range": "width",
            "round": true,
            "domain": {
              "data": "barley",
              "field": "yield"
            }
          },
          {
            "name": "cscale",
            "type": "ordinal",
            "range": "category",
            "domain": {
              "data": "barley",
              "field": "year"
            }
          }
        ],
        "axes": [
          {
            "orient": "bottom",
            "scale": "xscale",
            "zindex": 1
          }
        ],
        "legends": [
          {
            "stroke": "cscale",
            "title": "Year",
            "padding": 4,
            "encode": {
              "symbols": {
                "enter": {
                  "strokeWidth": {
                    "value": 2
                  },
                  "size": {
                    "value": 50
                  }
                }
              }
            }
          }
        ],
        "marks": [
          {
            "name": "site",
            "type": "group",
            "from": {
              "facet": {
                "data": "barley",
                "name": "sites",
                "groupby": "site"
              }
            },
            "encode": {
              "enter": {
                "y": {
                  "scale": "gscale",
                  "field": "site",
                  "offset": {
                    "signal": "offset"
                  }
                },
                "height": {
                  "signal": "cellHeight"
                },
                "width": {
                  "signal": "width"
                },
                "stroke": {
                  "value": "#ccc"
                }
              }
            },
            "scales": [
              {
                "name": "yscale",
                "type": "point",
                "range": [
                  0,
                  {
                    "signal": "cellHeight"
                  }
                ],
                "padding": 1,
                "round": true,
                "domain": {
                  "data": "barley",
                  "field": "variety",
                  "sort": {
                    "field": "yield",
                    "op": "median",
                    "order": "descending"
                  }
                }
              }
            ],
            "axes": [
              {
                "orient": "left",
                "scale": "yscale",
                "tickSize": 0,
                "domain": false,
                "grid": true,
                "encode": {
                  "grid": {
                    "enter": {
                      "strokeDash": {
                        "value": [
                          3,
                          3
                        ]
                      }
                    }
                  }
                }
              },
              {
                "orient": "right",
                "scale": "yscale",
                "tickSize": 0,
                "domain": false
              }
            ],
            "marks": [
              {
                "type": "symbol",
                "from": {
                  "data": "sites"
                },
                "encode": {
                  "enter": {
                    "x": {
                      "scale": "xscale",
                      "field": "yield"
                    },
                    "y": {
                      "scale": "yscale",
                      "field": "variety"
                    },
                    "stroke": {
                      "scale": "cscale",
                      "field": "year"
                    },
                    "strokeWidth": {
                      "value": 2
                    },
                    "size": {
                      "value": 50
                    }
                  }
                }
              }
            ]
          },
          {
            "type": "text",
            "from": {
              "data": "site"
            },
            "encode": {
              "enter": {
                "x": {
                  "field": "width",
                  "mult": 0.5
                },
                "y": {
                  "field": "y"
                },
                "fontSize": {
                  "value": 11
                },
                "fontWeight": {
                  "value": "bold"
                },
                "text": {
                  "field": "datum.site"
                },
                "align": {
                  "value": "center"
                },
                "baseline": {
                  "value": "bottom"
                },
                "fill": {
                  "value": "#000"
                }
              }
            }
          }
        ]
      }
    }
  ]
}
```

<br>

You can use almost the same specifications inside `spec` attribute, that are used for setting the Vega graphs. Only difference is that in `data` property, `url` and `path` attributes are moved out. Instead we use `name` attribute to reference to a resource. Note, how we created a new object inside `data` property - `{"name": "flights-airport"}` to reference it to a resource (this names are identical to names of resources):

```
  ...
  "spec": {
    ...
    "data": [
      {
        "name": "barley"
      }
      ...
    ]
    ...
  }
```

Outside of `spec` attribute there are some other important parameters to note:

<table class="table table-bordered table-striped resource-summary">
  <thead>
   <tr>
     <th>Attribute</th>
     <th>Type</th>
     <th>Description</th>
   </tr>
  </thead>
  <tbody>
    <tr>
      <th>name</th>
      <td>String</td>
      <td>Unique identifier for view within list of views.</td>
    </tr>
    <tr>
      <th>title</th>
      <td>String</td>
      <td>Title for the graph.</td>
    </tr>
    <tr>
      <th>resources</th>
      <td>Array</td>
      <td>Data sources for this spec. It can be either resource name or index. By default it is the first resource.</td>
    </tr>
    <tr>
      <th>specType</th>
      <td>String</td>
      <td>Available options: simple, vega, plotly <strong>(Required)</strong>.</td>
    </tr>
  </tbody>
</table>

[vega]: https://vega.github.io/vega/
[trellis]: http://www.jstor.org/stable/1390777
[editor]: https://vega.github.io/editor/#/examples/vega/barley-trellis-plot
[datapackage.json]: http://specs.frictionlessdata.io/data-package/
