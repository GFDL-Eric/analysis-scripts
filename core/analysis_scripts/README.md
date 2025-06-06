# analysis-scripts
A framework for analyzing GFDL model output.

### Motivation
The goal of this project is to provide a simple API to guide the development of
scripts that produce figures and tables from GFDL model output.  This work will be used
by a simple web application to provide users an easy interface to interact with model
output data.

### How to install this package
For now I'd recommend creating a virtual enviroment, and then installing the package inside
of it.

```bash
$ python3 -m venv env
$ source env/bin/activate
$ pip install --upgrade pip
$ pip install .
```

### How to use this framework
Custom analysis scripts classes can be created that inherit the `AnalysisScript` base
class, then override its contructor, `requires`, and `run_analysis` methods. For example:

```python3
from analysis_scripts import AnalysisScript


class NewAnalysisScript(AnalysisScript):
    """Analysis script for some task.

    Attributes:
       description: Longer form description for the analysis.
       title: Title that describes the analysis.
    """
    def __init__(self):
        """Instantiates an object.  The user should provide a description and title."""
        self.description = "This analysis does something cool."
        self.title = "Brief, but descriptive name for the analysis."

    def requires(self):
        """Provides metadata describing what is needed for this analysis to run.

        Returns:
            A json string describing the metadata.
        """
        return json.dumps({
            "settings": {
                "activity_id": "<fill this>",
                "institution_id": "<fill this>",
                "source_id": "<fill this>",
                "experiment_id": "<fill this>",
                "frequency": "<fill this>",
                "modeling_realm": "<fill this>",
                "table_id": "<fill this>",
                "member_id": "<fill this>",
                "grid_label": "<fill this>",
                "temporal_subset": "<fill this>",
                "chunk_freq": "<fill this>",
                "platform": "<fill this>",
                "cell_methods": "<fill this>"
            },
            "dimensions": {
                # These are just examples, you can put more/different ones.
                "lat": {"standard_name": "latitude"},
                "lon": {"standard_name": "longitude"},
                "time": {"standard_name": "time"}
            },
            "varlist": {
                "<fill this>": {
                    "standard_name": "<a standard name>",
                    "units": "<some units>",
                    "dimensions": ["time", "lat", "lon"]
                }, 
            }
        })

    def run_analysis(self, catalog, png_dir, config=None, reference_catalog=None):
        """Runs the analysis and generates all plots and associated datasets.

        Args:
            catalog: Path to a model output catalog.
            png_dir: Directory to store output png figures in.
            config: Dictionary of configuration options.
            reference_catalog: Path to a catalog of reference data.

        Returns:
            A list of png figures.
        """
        Do some stuff to create the figures.
        return ["figure1.png", "figure2.png",]
```

### Running a custom analysis script
In order to run a custom analysis script, you must first create a data catalog and
can then perform the analysis:

```python3
from analysis_scripts import available_plugins, plugin_requirements, run_plugin


# Create a data catalog.
# Some code to create a data "catalog.json" ...

# Show the installed plugins.
print(available_plugins())

# Run the radiative fluxes plugin.
name = "freanalysis_radiation"  # Name of the custom analysis script you want to run.
print(plugin_requirements(name))
figures = run_plugin(name, "catalog.json", "pngs")
```
